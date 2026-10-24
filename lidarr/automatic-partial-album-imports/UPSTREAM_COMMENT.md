# Suggested comment for Lidarr issue #5647

There is an opt-in feature proposal for automatically importing safely matched tracks from incomplete album downloads:

https://github.com/erwincdecker/findings/blob/main/lidarr/automatic-partial-album-imports/PROPOSAL.md

The suggestion is to reuse Lidarr's existing matching, library import, and Completed Download Handling. Missing tracks should remain missing. A successful import should still follow the current download-client cleanup and seeding rules.

The document compares two technical routes: an opt-in setting in the existing import pipeline and configuration API (preferred), and a possible read-only preview plus guarded execute API for integrations.

Would this fit best as a native import policy in Lidarr, or would maintainers prefer a dedicated API design? Happy to incorporate feedback before a scoped pull request.

This is a feature proposal rather than a tested upstream patch.
