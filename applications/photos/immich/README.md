# Immich Photo Library Management

**Status: DATA-HYGIENE AND INTEGRATION FINDINGS.**

Photo management workflows handle asset ingestion, duplicate detection, filenames, metadata and dates. A filesystem rename or metadata edit can affect database indexing and user-visible grouping.

For bulk operations: inventory first, create a deterministic dry-run manifest, detect naming collisions, preserve originals, perform a bounded migration, then verify asset counts, timestamps and representative albums.

Do not publish personal photographs, EXIF coordinates or a real asset inventory.