# Lidarr: automatic partial-album imports

**Status (2026-10-25): external prototype operational; native upstream feature remains a proposal.**

## Problem

Lidarr may reject a completed transfer when its selected album edition
has missing tracks, even where the available files can be correctly
identified. Manually repeating the partial import is unnecessarily
labor-intensive, but lowering the global album match threshold would
create artist/release matching and file-quality risks.

## Public field evidence

A standalone, independently deployed Python guardian has now demonstrated
a **successful real nine-track import from a 24-track album**, with nine
library track files confirmed afterward and fifteen still missing.
It also correctly held an ambiguous 13/15 source for review. A generic
scheduled guardian is active; this is not proof that *every future*
scheduled import succeeds.

The guardian accepts only downloads that are **finished but rejected for
missing album tracks**. Complete albums continue through Lidarr's normal
import path. Exact artist/release/track mappings, the artist's native
FLAC/MP3 quality profile and absence of existing track files are
checked before a native linked `ManualImport`. It does not issue its own
Lidarr queue DELETE or Transmission removal command. The owner
reports normal client cleanup, although independent RPC verification was
not possible in the observed pilot.

The external checks cannot be atomic with native import, so
**the upstream design still needs a server-side atomic missing-only guard**
to eliminate potential concurrency upgrades/overwrites. This is a
workaround, **not an upstream Lidarr implementation**.

## Documents

- [Sanitized prototype validation and its limitations](automatic-partial-album-imports/PROTOTYPE_VALIDATION.md)
- [Problem finding and acceptance criteria](automatic-partial-album-imports/FINDING.md)
- [Native implementation and API proposal](automatic-partial-album-imports/PROPOSAL.md)
- [Draft follow-up for upstream maintainers](automatic-partial-album-imports/UPSTREAM_COMMENT.md)

Related Lidarr discussions: [#5647](https://github.com/Lidarr/Lidarr/issues/5647),
[#5645](https://github.com/Lidarr/Lidarr/issues/5645).

**No private hostnames, network addresses, API tokens, download IDs,
unredacted paths or private execution logs are published here.**
