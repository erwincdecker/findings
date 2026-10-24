# Finding: Incomplete Album Download Rejected During Import

**Status: OBSERVED SYMPTOM / PROPOSED REMEDY.** This sanitized case study contains no private paths, credentials, identifiers or raw logs.

## Symptom

The download client reports a finished transfer, but Lidarr declines automatic import because the selected album edition expects more tracks. The available files may still map correctly to individual tracks.

## Representative examples

| Audio files present | Tracks expected in selected edition | Missing |
| ---: | ---: | ---: |
| 13 | 15 | 2 |
| 9 | 24 | 15 |

These counts demonstrate an incomplete release, not proof that every file is correctly matched.

## Technical finding

Album completeness and per-file matching confidence are separate dimensions. Globally lowering the release matching threshold risks importing the wrong artist, album release or individual tracks. Instead, a native opt-in policy should validate all present files individually and import only if the mapping is unambiguous.

## Desired lifecycle

1. Keep Lidarr's usual automatic path for complete releases.
2. Validate the incomplete download's artist, release, track IDs, file integrity and existing-file collisions.
3. Import confidently matched tracks to Lidarr's ordinary music library.
4. Leave absent tracks marked Missing and eligible for future acquisition.
5. Let **Lidarr itself** handle Activity Queue and Transmission cleanup under existing Completed Download Handling and seeding conditions.
6. Escalate ambiguous mappings or partial execution failures for manual review rather than deleting source data.

## Acceptance criteria

- Off by default and no regression to complete-album imports.
- Only verified present tracks imported, with accurate Missing state afterward.
- Wrong artist/release, duplicate, collision or invalid audio blocks the partial path.
- A queued command alone cannot trigger completion or deletion.
- Interrupted processing never leads to double import or premature torrent removal.
- Source and library destinations are verified before declaring success.

See the [full design proposal](PROPOSAL.md), including the preferred built-in import-policy route and an optional dedicated preview/execute API. This is not yet a tested or deployed upstream feature.
