# Proposed comment for Lidarr issue #5647

I have prepared a public proposal for an **opt-in automatic import of confidently matched tracks from incomplete album releases**:

https://github.com/erwincdecker/findings/blob/main/media/music/lidarr/automatic-partial-album-imports/PROPOSAL.md

There is also a sanitized problem analysis with expected outcomes and acceptance tests:

https://github.com/erwincdecker/findings/blob/main/media/music/lidarr/automatic-partial-album-imports/FINDING.md

**Proposed behavior:** when a completed download contains fewer tracks than the selected album release, Lidarr may still import those existing files if artist, release, and every present track match unambiguously. Remaining tracks stay Missing. The existing Completed Download Handling remains responsible for Lidarr Activity Queue and Transmission cleanup under the normal seeding/removal policy.

**Preferred route:** add a default-off native import policy to Lidarr's existing C# import decision flow, exposed through the existing configuration API and UI. The alternative is a server-validated preview/execute API, but this would still need the internal completion-policy hook to work automatically.

I would welcome maintainer feedback on the design before a scoped pull request. This is a feature proposal, **not a tested upstream patch**.
