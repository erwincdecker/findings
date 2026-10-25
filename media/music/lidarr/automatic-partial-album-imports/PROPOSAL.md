# Upstream proposal: Opt-in automatic import of incomplete album downloads

> **Upstream feature proposal.** Not yet accepted or implemented in Lidarr.
>
> This design is proposed for discussion in [Lidarr issue #5647](https://github.com/Lidarr/Lidarr/issues/5647), or a separate feature request if maintainers prefer. It is **not an upstream-approved or deployed Lidarr patch**. An independently deployed *external* prototype is described in [field validation](PROTOTYPE_VALIDATION.md).

## Suggested issue title

Feature request: Opt-in automatic import of matched tracks from incomplete album downloads

## Problem

Lidarr can correctly identify some tracks of a completed download yet reject automatic import of the entire download because the release is incomplete. Users who knowingly accept partial collections must then run manual import, match the available tracks, and repeat this for subsequent partial downloads.

For example, a downloaded release contains 13 audio files against an album edition with 15 expected tracks, or 9 files against a 24-track edition. Those available files may match exact track IDs correctly even though the album as a whole is incomplete. This is distinct from accepting wrong artist, wrong release or ambiguous track mappings.

A similar user experience was reported in [#5647](https://github.com/Lidarr/Lidarr/issues/5647), which specifically asks about automating ManualImport for missing-track cases, and [#5645](https://github.com/Lidarr/Lidarr/issues/5645), which concerns the effort of repetitive manual imports below the existing album-match threshold.

## Proposed user-facing behavior

An **opt-in** advanced setting, e.g. `Automatically import matched tracks from incomplete album downloads`, default **Off**. When enabled:

1. Use ordinary automatic import for complete, confidently matched albums. Do not change that path.
2. If a completed download fails **only** because expected tracks are missing, evaluate the available audio files individually using Lidarr's **existing** artist, album-release and track matching primitives.
3. If all available audio files can be mapped **unambiguously and safely**, import those files to Lidarr's normal artist/album library location. Do **not** require the downloaded set to equal the complete album edition.
4. Keep all absent tracks **Missing/monitored**, so that future suitable downloads can fill gaps. Do not declare the album complete solely because all submitted files were imported.
5. Keep existing `Completed Download Handling` behavior for Lidarr Activity Queue and the download client. For torrents, respect existing **seeding completion, paused/stopped state, and Remove Completed Downloads** policies rather than implementing a second queue/torrent deletion mechanism.
6. On wrong/unknown artist, title, track IDs, edition, duplicate/colliding trackfile, unexpected rejections, corrupt/unsupported audio, or uncertain API/import state: **do not force import**. Keep the item for manual review.

The setting is about *per-track import eligibility on incomplete albums*, not a blanket lower album matching threshold. Albums missing only some tracks should remain eligible for future improvement/repair.

## Implementation direction (for maintainer discussion)

An external **Python prototype has now been deployed by an operator** (independent from Lidarr upstream). It:
- inspects completed downloads rejected specifically for missing tracks;
- retrieves Lidarr manual-import preview and checks unique artist/album/release/track identity;
- submits `ManualImport` with the **original tracked download ID** so Lidarr's native completion handling remains responsible for any downstream cleanup;
- verifies imported track relationships while leaving missing tracks missing; and
- stops rather than guessing if mapping, eligibility, or execution outcome is ambiguous.

This is **not upstream C# code** or an accepted Lidarr patch. A controlled
real-world pilot **did** import nine present tracks from a 24-track edition;
the nine imported library tracks were independently confirmed afterward.
The operator confirmed that native client cleanup took place, but a
separate authenticated RPC check was blocked (HTTP 401), so deletion
was **not independently verified** by the audit. Another ambiguous
13/15 partial release remains blocked for review. The external
periodic timer was independently verified active, but new scheduled
imports after deployment have not yet been separately confirmed.

The prototype deliberately does not back up torrent source files because
that operator is willing to redownload them. This is not a proposal to
remove Lidarr's existing safety checks or impose this retention choice on
other users. A source-quality allowlist follows the artist's own Lidarr
quality profile; no automatic FLAC/MP3 quality ranking is invented.

**Remaining fundamental risk:** checking that a track is absent via a
separate API call is not atomic with the following ManualImport POST.
The native import path may still enter the quality upgrade branch, even
with `replaceExistingFiles=false`, if another writer changes the track
between those steps. A built-in server-side, atomic missing-only gate
should therefore be part of the upstream design. A well-verified
external workaround is evidence for the need, not proof that the
upstream design is already safe or finished.

See the [sanitized validation and limitations](PROTOTYPE_VALIDATION.md)
for the exact proof levels and unresolved checks.

If the maintainers support the feature, the preferred final implementation
is inside Lidarr's existing C# import decision/track-import path, not a
per-user external timer/service.

In `v3.1.0.4875`, the relevant path includes `NzbDrone.Core.MediaFiles.TrackImport.Manual.ManualImportService` and `DownloadCompletedEvent` for successfully processed tracked downloads. A linked ManualImport may also remove the original output folder under certain `ShouldDeleteFolder`/`CanMoveFiles` conditions; any upstream patch must preserve correct torrent seeding semantics and avoid premature deletion.

## Proposed upstream integration routes

I recommend **an opt-in feature in the existing C# import pipeline, exposed through Lidarr's existing configuration REST API**, rather than an additional daemon or a separate Transmission cleaner. The endpoint and parameter names below are **proposals, not currently supported API contracts**.

### Option A: native import policy + existing REST API (preferred)

1. **Configuration and UI.** Add an advanced setting such as `partialAlbumImportPolicy = "reject" | "importConfidentTracks"` (default: `"reject"`) to the existing download-handling/import configuration, exposed through its normal GET/PUT settings endpoint and React settings UI. This changes *eligibility of incomplete albums*, not the global ~80% match threshold.
2. **Completed-download decision path.** When a completed, tracked download would otherwise fail solely due to missing album tracks, and this policy is enabled, run a narrow second pass through the *existing* parser/track-matcher. Reuse the artist, MusicBrainz release, file inspection, track ID, quality, and rejection checks. Each present file must have a unique and confident mapping. Unknown artist/release, duplicate track, wrong titles, collision with existing music or invalid audio means **REVIEW, no forced import**.
3. **Native library import.** Import the confidently matched subset using the normal track-import service and library naming. Preserve the album's true track count: undownloaded songs remain **Missing/monitored**. A success for all *supplied* tracks must never falsely mark the entire release complete.
4. **Native completion and cleanup.** Use Lidarr's original tracked download identity and completed-download lifecycle. Only mark a download imported once the selected audio was actually imported and verified. Let the existing Completed Download Handling decide when to clear Activity Queue and remove the download/torrent from Transmission, respecting seeding goals, stopped/paused state, and Remove Completed Downloads. **No new Transmission RPC worker** and no premature deletion if a file import fails.
5. **Idempotence and reporting.** Keep a unique command/download correlation, report per-file successes/rejections, and handle process restart or uncertain POST without importing twice or deleting a partially imported source.

### Option B: a dedicated API for partial-import preview + execution

An API-first alternative would expose the *same underlying, server-side validator* to users and integrations. **Illustrative only**, subject to maintainers' naming and authorization model:

**Read-only request**

```http
POST /api/v1/import/partial/preview

{"downloadId":"<tracked-download-id>","policy":"confident-tracks-only"}
```

**Read-only response**

```json
{"eligible":true,"albumId":456,"albumReleaseId":789,
 "matchedTrackIds":[1,2,3],"missingTrackCount":2,
 "rejections":[],"planToken":"<short-lived-idempotency-token>"}
```

**Guarded execution**

```http
POST /api/v1/import/partial

{"downloadId":"<tracked-download-id>","planToken":"<short-lived-idempotency-token>"}
```

The execution endpoint must **revalidate all server-side data** (original download, artist, album release, selected track IDs, existing library files and file identity) and use native import/completion handling. It must not blindly trust submitted file paths or track IDs. A server-side idempotency key or existing command model can replace the illustrative plan token.

**Trade-off:** This new endpoint alone would **not** automate importing every completed partial album. Option B still needs the in-process completed-download policy hook from Option A. Therefore **Option A is the smaller and more maintainable design**, and Option B can be added only if integrations really need a safe explicit preview/import flow.

### Relevant code-level integration points

- The existing `ManualImportController` under `src/Lidarr.Api.V1/ManualImport` already exposes GET manual-import preview. The existing command endpoint dispatches ManualImport.
- `ManualImportService` under `src/NzbDrone.Core/MediaFiles/TrackImport/Manual` already correlates tracked download IDs and emits `DownloadCompletedEvent` when import results are successful.
- For `v3.1.0.4875`, `ManualImportService` may delete the original output folder under `ShouldDeleteFolder` + `CanMoveFiles`. The upstream change needs careful tests for seeding, retries, data-loss safety and source-folder handling (also see #5846).
- Prefer implementing the eligibility rule inside normal downloaded-track decision/import services, **not** calling the UI manual-import endpoint from an external timer. Documentation of ManualImport payloads and readable rejection results would additionally help #5647.

## Acceptance tests to add

- [ ] Setting disabled: existing import behavior unchanged.
- [ ] Complete download: imports using ordinary path.
- [ ] Incomplete album with all available tracks uniquely mapped: imports existing files; absent tracks stay Missing.
- [ ] Single wrong artist or ambiguous album release: rejects, no forced/incorrect imports.
- [ ] Duplicated or already present track: does not overwrite without the usual explicit upgrade policy.
- [ ] Partially invalid or unreadable audio: does not create a false-success completion.
- [ ] Usenet completed item and torrent still seeding: correct native cleanup timings.
- [ ] Torrent meets seed requirements and completed-download removal enabled: Lidarr cleans up its queue/client as it ordinarily does, only after import completes.
- [ ] Idempotence/restart/timeout: no duplicate or unsafe imports, and no premature source deletion.
- [ ] No regressions in normal quality profile, release profile, track-file upgrades or manual import.

## Why discuss upstream first?

The official [Lidarr contribution guide](https://github.com/Lidarr/Lidarr/blob/develop/CONTRIBUTING.md) requests discussing unrequested features first, submitting a single scoped feature per PR, targeting `develop`, and adding tests. It also expects at least 80% test coverage for new code. The feature would be useful beyond one user's setup, but maintainer guidance is needed on UX, how to represent partial success, and safe native completion semantics.

## Related upstream discussions

- [#5647: API information regarding ManualImport command](https://github.com/Lidarr/Lidarr/issues/5647) (open at drafting)
- [#5645: Multiple manual import](https://github.com/Lidarr/Lidarr/issues/5645) (open at drafting)
- [#779: Options to tweak album matching engine](https://github.com/Lidarr/Lidarr/issues/779) (closed, broader scope)
- [#5846: Manual/upgrade import data-loss report](https://github.com/Lidarr/Lidarr/issues/5846) (closed; relevant safety/regression risk)

