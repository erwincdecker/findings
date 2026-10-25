# Finding: Incomplete Album Download Rejected During Import

**Status: OBSERVED SYMPTOM / EXTERNAL WORKAROUND VALIDATED / NATIVE REMEDY PROPOSED.**
This sanitized case study contains no private paths, credentials,
identifiers or raw logs. An external Python workaround has completed
one successful real-world nine-track pilot and a separate periodic
runner is active. The proposed native Lidarr feature has **not**
been implemented or accepted upstream.

See [external prototype validation](PROTOTYPE_VALIDATION.md) for
what was actually tested and what remains unverified.

## Symptom

The download client reports a finished transfer, but Lidarr declines automatic import because the selected album edition expects more tracks. The available files may still map correctly to individual tracks.

## Representative examples

| Audio files present | Tracks expected in selected edition | Missing |
| ---: | ---: | ---: |
| 13 | 15 | 2 |
| 9 | 24 | 15 |

These counts demonstrate an incomplete release, not proof that every file is correctly matched.
In the 9/24 example, nine tracks were imported and verified in Lidarr after
an externally initiated native ManualImport. In the 13/15 example, some
source files overlapped existing library tracks and two files were not
mapped unambiguously; that download was held for review.

## Technical finding

Album completeness and per-file matching confidence are separate dimensions. Globally lowering the release matching threshold risks importing the wrong artist, album release or individual tracks. Instead, a native opt-in policy should validate all present files individually and import only if the mapping is unambiguous.

## Desired lifecycle

1. Keep Lidarr's usual automatic path for complete releases.
2. Validate the incomplete download's artist, release, track IDs, file integrity and existing-file collisions.
3. Import confidently matched tracks to Lidarr's ordinary music library.
4. Leave absent tracks marked Missing and eligible for future acquisition.
5. Let **Lidarr itself** handle Activity Queue and Transmission cleanup under existing Completed Download Handling and seeding conditions.
6. Escalate ambiguous mappings or uncertain import attempts for manual
   review, without force-importing or issuing a separate torrent deletion
   from the workaround. Native Lidarr may legitimately clean up a
   completed download according to its configured policy.

## Acceptance criteria

- Off by default and no regression to complete-album imports.
- Only verified present tracks imported, with accurate Missing state afterward.
- Wrong artist/release, duplicate, collision or invalid audio blocks the partial path.
- A queued command alone cannot trigger completion or deletion.
- Interrupted processing never leads to double import or premature torrent removal.
- Source and library destinations are verified before declaring success.

## Field validation and key limitation

An external, version-pinned guardian has demonstrated a successful
missing-tracks-only import for one nine-file partial release. Its
periodic timer was independently observed enabled and waiting after
deployment. This proves the workaround is operational, **not** that
Lidarr contains a native feature or that every unattended album has
been exercised.

The external guardian rechecks target occupancy, but this is not
atomic against concurrent normal Lidarr imports. A native server-side
missing-only gate is needed to close that race and preserve the
user's FLAC/MP3 quality profile semantics. Complete releases should
continue through Lidarr's standard flow.

See the [sanitized real-world validation](PROTOTYPE_VALIDATION.md)
and [full design proposal](PROPOSAL.md). These are not tested or
deployed upstream Lidarr code.
