---
name: Restore files from a Slide snapshot
description: Create a file restore from a snapshot, browse its contents, and push selected files to a destination.
api: openapi/slide-openapi-original.json
operations: [Snapshots, SnapshotByID, FileRestoreCreate, FileRestores, FileRestoreByID, FileRestoreBrowse, FileRestorePushCreate, FileRestorePushStatus, FileRestoreDelete]
---

# Restore files from a snapshot

Recover individual files from a backup snapshot and push them to a target.

## Auth
`Authorization: Bearer YOUR_SECRET_TOKEN`. Base URL `https://api.slide.tech`.

## Steps
1. **Find a snapshot** — `Snapshots` (`GET /v1/snapshot`), optionally filtered
   by `agent_id`/time window; inspect one with `SnapshotByID`
   (`GET /v1/snapshot/{snapshot_id}`).
2. **Create the file restore** — `FileRestoreCreate` (`POST /v1/restore/file`)
   with the `snapshot_id`. Returns a `file_restore_id` (`fr_...`).
3. **Wait until ready** — `FileRestoreByID`
   (`GET /v1/restore/file/{file_restore_id}`) until the restore is mounted.
4. **Browse** — `FileRestoreBrowse`
   (`GET /v1/restore/file/{file_restore_id}/browse`) with a `path` to list the
   directory tree and locate files.
5. **Push files** — `FileRestorePushCreate`
   (`POST /v1/restore/file/{file_restore_id}/push`) to copy selected paths to a
   destination folder; poll `FileRestorePushStatus`
   (`GET /v1/restore/file/{file_restore_id}/push/{file_restore_push_id}`).
6. **Clean up** — `FileRestoreDelete`
   (`DELETE /v1/restore/file/{file_restore_id}`) to release the restore.

## Rules
- Browse with the `path` query param to walk the tree incrementally.
- Always delete the restore when finished to free resources.
- Errors return `{ codes, message, details }` (see errors/slide-error-codes.yml).
