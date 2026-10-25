# Field validation: external missing-tracks-only Lidarr importer

**Status (2026-10-25): external workaround running; upstream feature not implemented or approved.**

This is a sanitized account of an independent, opt-in *external Python
workaround* for automatic partial-album imports. It is not an official Lidarr
release, an upstream patch or a claim that the normal Lidarr album-matching
threshold should be lowered. The underlying issue and the proposed native
design are covered in [FINDING.md](FINDING.md) and [PROPOSAL.md](PROPOSAL.md).

## What the external prototype actually does

1. Periodically checks Lidarr's Activity Queue for a **completed transfer**
   that Lidarr marked **`importFailed` specifically because tracks are
   missing**. Other import failures and normal/complete downloads stay in
   Lidarr's existing handling path.
2. Uses Lidarr's own manual-import preview and selected album edition to
   require a unique match for **every present audio file**: artist, album,
   release, track ID, track number and title. Rejects unexpected extra
   audio files, duplicate/occupied tracks, and ambiguous preview results.
3. Requires the candidate's recognized source quality to be allowed by
   the artist's existing Lidarr quality profile. Does not introduce a
   custom FLAC/MP3 ranking, relax profile restrictions or initiate upgrades
   of existing library tracks.
4. Immediately before submitting `ManualImport`, re-reads the tracked
   queue item, input file size/mtime, missing-track state and quality
   profile. Sets `importMode=copy`, `replaceExistingFiles=false`, and
   retains the original download ID for Lidarr's native completion handling.
5. Persists a one-shot execution fence before a potentially uncertain
   import request. Does **not** replay a timed-out or ambiguous POST
   automatically. Confirms the chosen track IDs appear in Lidarr after
   the import command reports completion.
6. Leaves torrent/download cleanup to existing Lidarr Completed Download
   Handling and the configured download client. The external runner
   does **not** invoke its own Transmission RPC deletion or queue DELETE.
7. Continues checking other eligible albums when one release cannot be
   matched safely. The external timer currently runs about every
   10–12 minutes. No archive of the downloaded media is required by
   this operator's local policy: a failed download may be acquired again.
   This is an operator choice, **not** a suggested upstream default.

## Sanitized observed results

| Case | Observed result | What it establishes |
| --- | --- | --- |
| Incomplete release with 9 files from a 24-track edition | 9 selected audio files imported, and 9 library track files independently present afterward | One real linked native `ManualImport` pilot completed and filled only the available track slots; 15 remained missing |
| Other incomplete release with 13 audio files versus 15 expected tracks | Two audio files could not be uniquely identified; other source tracks overlapped existing library files; held for review | The guard does not force ambiguous or duplicate track assignments |
| Generic timer after deployment | Timer reported enabled and active/waiting; installed source matched the intended source hash; last oneshot service reported success | The independent periodic workaround is operating; this alone is **not** proof of a *new* automatic import after deployment |
| Original download-client cleanup for the successful pilot | Operator observed that the download disappeared in the client; an independent RPC query received HTTP 401 | User confirmation only; the separate RPC check did **not** independently prove the removal |

Before the initial successful pilot, **26 deterministic regression tests
passed** in an earlier, quality-guarded revision. A later all-album revision
defines **32 tests**, but its full original activation stdout was lost
because the evidence Git push was rejected by a concurrent branch update.
A separate runtime audit verified the installed source hash, timer and
service status. **Do not misrepresent the 32 tests as independently
published PASS**, or infer that every future album will import successfully.
An isolated offline test of the evidence publisher's non-fast-forward
recovery passed 2/2 tests.

## Critical upstream safety gap

In the inspected Lidarr `v3.1.0.4875` implementation, the manual-import
path may invoke an existing-track upgrade operation even when the client
sends `replaceExistingFiles=false`. An external client's immediately
preceding track-state check cannot be atomic against another Lidarr
importer writing to the same track between GET and POST.

Therefore the workaround **reduces** the risk of unintentionally changing
an existing FLAC/MP3, but cannot honestly guarantee zero replacement in
every concurrency race. A built-in, server-side **atomic missing-only
validation**, integrated with the normal track import transaction and
quality profile logic, would be the stronger upstream solution.

Likewise, the native implementation must distinguish \"successfully
imported the available files\" from \"complete album\" and preserve
normal seeding, queue and removal semantics. It should not silently
remove a partially invalid release on a false-success path.

## What to ask Lidarr maintainers

- Would a **default-off**, native `import uniquely matched existing tracks
  when only album tracks are missing` setting fit the current import model?
- Where should the server-side atomic `only import into missing track
  slots` check live, to avoid a race with a concurrent quality upgrade?
- How can partial successes be recorded without marking the album fully
  acquired or breaking native Completed Download Handling?
- Which import preview or command result fields would best make
  per-track rejection and identity verification machine-readable?

The external service is useful field evidence, **not a substitute for
upstream design review or Lidarr's own integration tests**.

Related discussions: [#5647](https://github.com/Lidarr/Lidarr/issues/5647)
and [#5645](https://github.com/Lidarr/Lidarr/issues/5645).
