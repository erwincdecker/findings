# Findings

Public, shareable technical findings and upstream feature proposals. This repository contains **sanitized design documents**, not live system configurations, credentials, logs, or production-ready deployment scripts.

## Lidarr: automatic import of incomplete albums

**Proposal:** allow Lidarr to automatically import *confidently matched existing tracks* from an incomplete album download, keep other tracks marked **Missing**, and then use Lidarr's normal Completed Download Handling to manage its Activity Queue and the associated Transmission download under existing seeding/removal rules.

- [Full feature request and two proposed API integration routes](lidarr/automatic-partial-album-imports/PROPOSAL.md)
- [Ready-to-post comment for Lidarr issue #5647](lidarr/automatic-partial-album-imports/UPSTREAM_COMMENT.md)
- Upstream discussions: [Lidarr #5647](https://github.com/Lidarr/Lidarr/issues/5647) and [#5645](https://github.com/Lidarr/Lidarr/issues/5645)

**Preferred approach:** a built-in, optional import policy (default Off) exposed through Lidarr's existing configuration API and UI. A dedicated, validated preview/execute API could be a useful alternative for integrations, but would still need the native completed-download hook for fully automatic operation.

**Status:** community proposal, **not an upstream patch**, not accepted/merged by Lidarr, and not established by an end-to-end production test.

### Safety and contribution notes

The proposal deliberately avoids exposing any private download paths, server names, API tokens, internal repository contents or raw operational logs. It focuses on expected behavior, suggested entry points, safe cleanup, and regression testing.

Feedback and discussion belong on [Lidarr's existing issue #5647](https://github.com/Lidarr/Lidarr/issues/5647), subject to maintainers' direction.
