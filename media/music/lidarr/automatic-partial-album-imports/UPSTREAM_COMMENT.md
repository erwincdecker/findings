# Suggested follow-up comment for Lidarr issue #5647

I wanted to add a field update to my [original opt-in partial-album
import proposal](https://github.com/erwincdecker/findings/blob/main/media/music/lidarr/automatic-partial-album-imports/PROPOSAL.md).

I have now deployed a **separate external Python workaround**, which
automatically picks up *finished* Lidarr downloads that failed specifically
because the album is missing tracks. Complete/ordinary downloads still
use Lidarr's usual import flow; no global matching threshold is changed.

For each candidate it checks the original tracked download, the
manual-import preview's artist, selected release and exact track IDs,
source file properties, absent existing track files and the artist's
native quality profile. It uses Lidarr's linked ManualImport command
with `importMode=copy` and `replaceExistingFiles=false`. A one-shot
execution fence prevents blind retries of uncertain requests. Ambiguous
or already-occupied tracks remain for review; no direct Transmission
cleanup is implemented.

**Sanitized results:** One pilot with 9 available tracks against a
24-track edition imported all 9 files; a later Lidarr readback confirmed
9 library tracks and the remaining 15 missing. Another 13/15 release
had ambiguous/overlapping source tracks and was correctly held for
review. The periodic external service is now running, but a further
*new*, unattended import has not yet been independently audited.

**Important limitation:** An external pre-import check for a missing
track is not atomic with Lidarr's native import command, whose upgrade
path can still operate on existing files even with
`replaceExistingFiles=false`. This is why I still prefer a native,
**opt-in, default-off, server-side atomic missing-only policy** in
Lidarr's import pipeline, rather than treating the workaround as the
long-term solution. It should respect existing artist quality profiles
and Completed Download Handling.

I've recorded the complete sanitized explanation and validation levels
here:
https://github.com/erwincdecker/findings/blob/main/media/music/lidarr/automatic-partial-album-imports/PROTOTYPE_VALIDATION.md

I'd welcome feedback about where such a guarded partial-import
eligibility rule and its concurrency-safe missing-only check belong
in the existing Lidarr C# import path.

---

**Publishing note:** This is a *draft* follow-up comment maintained in
the findings repository. Updating this file does **not** post it to the
upstream Lidarr issue.
