# Lidarr: Partial Album Import

**Status: UPSTREAM FEATURE PROPOSAL. Not implemented or accepted by Lidarr.**

## Problem

Lidarr can decline a completed download when some album tracks are missing even though the downloaded audio files may be recognizable. Users willing to keep an incomplete album then need repeated manual imports.

## Proposed improvement

An optional policy, off by default, would import only uniquely and safely matched existing tracks. Missing tracks remain Missing. Lidarr's standard Completed Download Handling stays responsible for the Activity Queue and the associated Transmission download, subject to seeding rules.

## Public documents

- [Detailed implementation and API proposal](automatic-partial-album-imports/PROPOSAL.md)
- [Sanitized finding and acceptance criteria](automatic-partial-album-imports/FINDING.md)
- [Suggested GitHub community comment](automatic-partial-album-imports/UPSTREAM_COMMENT.md)

Related: [Lidarr issue #5647](https://github.com/Lidarr/Lidarr/issues/5647) and [#5645](https://github.com/Lidarr/Lidarr/issues/5645).
