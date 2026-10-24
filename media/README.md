# Music, Video and Media Automation

**Status: FUNCTIONAL FINDINGS AND UPSTREAM PROPOSALS.**

## Functional domains
- **Music library automation:** search, downloading, release identification, tagging, import, missing tracks and completed-download cleanup.
- **Video library automation:** discovery, acquisition, organization, indexing, transcoding and playback.
- **Download clients:** completed-download handling, queue state and seeding policy.

## Engineering findings
- Match quality and album completeness are different dimensions. Missing album tracks should not automatically imply that every present file is unusable.
- The application that owns an import should generally own its follow-up queue cleanup.
- Automation should not loosen matching thresholds for wrong artist, release edition or track identity.
- Library and downloader state must be verified independently, including any delayed seeding/removal policy.

See [Lidarr: partial album import proposal](music/lidarr/README.md) for a public feature request and API design.