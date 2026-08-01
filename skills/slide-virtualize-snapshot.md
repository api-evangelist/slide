---
name: Virtualize a Slide snapshot for disaster recovery
description: Boot a virtual machine from a backup snapshot, manage its state, and tear it down after recovery.
api: openapi/slide-openapi-original.json
operations: [Snapshots, SnapshotByID, VirtCreate, Virts, VirtByID, VirtUpdate, VirtDelete]
---

# Virtualize a snapshot (instant recovery)

Spin up a VM from a snapshot so a protected machine can run while it is being
recovered.

## Auth
`Authorization: Bearer YOUR_SECRET_TOKEN`. Base URL `https://api.slide.tech`.

## Steps
1. **Pick a snapshot** — `Snapshots` (`GET /v1/snapshot`) then `SnapshotByID`
   (`GET /v1/snapshot/{snapshot_id}`) to confirm the recovery point.
2. **Create the virtual machine** — `VirtCreate` (`POST /v1/restore/virt`)
   with the `snapshot_id` (and optional CPU/memory/network settings). Returns a
   `virt_id` (`virt_...`).
3. **Monitor state** — `VirtByID` (`GET /v1/restore/virt/{virt_id}`) until the
   VM reaches a running state; list all with `Virts` (`GET /v1/restore/virt`).
4. **Adjust** — `VirtUpdate` (`PATCH /v1/restore/virt/{virt_id}`) to change
   resources or power/boot options as needed.
5. **Tear down** — `VirtDelete` (`DELETE /v1/restore/virt/{virt_id}`) once the
   underlying system is recovered.

## Rules
- A virt belongs to a snapshot, agent, and device (see data-model/slide-data-model.yml).
- No idempotency-key: check `Virts` before re-issuing `VirtCreate`.
- Errors return `{ codes, message, details }` (see errors/slide-error-codes.yml).
