# Jellyfin and Video Playback

**Status: GENERAL FUNCTIONAL FINDINGS.**

A video library service manages metadata, streams content to clients and may transcode media. Hardware acceleration can improve efficiency but introduces driver and device-access dependencies.

Check playback directly, test direct-play and transcoded content separately, verify permissions after container changes and compare memory/CPU consumption during realistic playback. A service process reporting healthy is not sufficient evidence that clients can stream correctly.

Do not publish personal library catalogs or media file paths.