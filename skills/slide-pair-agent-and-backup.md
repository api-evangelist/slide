---
name: Pair a Slide protection agent and start a backup
description: Register (pair) a newly installed Slide Agent to the account and kick off an on-demand backup, then poll it to completion.
api: openapi/slide-openapi-original.json
operations: [AgentPrePair, AgentPair, Agents, AgentByID, BackupStart, Backups, BackupByID]
---

# Pair a Slide Agent and start a backup

Use the Slide API to enroll a freshly installed Agent and trigger a backup.

## Auth
All requests use a Bearer API token created in the Slide Console:
`Authorization: Bearer YOUR_SECRET_TOKEN`. Base URL `https://api.slide.tech`.

## Steps
1. **Pre-pair the agent** — `AgentPrePair` (`POST /v1/agent`) to create the
   pairing record; the Agent installer shows a pairing code on the target host.
2. **Complete pairing** — `AgentPair` (`POST /v1/agent/pair`) with the pairing
   code to bind the agent to a device/client. Returns the `agent_id` (`a_...`).
3. **Confirm** — `AgentByID` (`GET /v1/agent/{agent_id}`) or `Agents`
   (`GET /v1/agent`) to verify the agent is paired and reporting.
4. **Start a backup** — `BackupStart` (`POST /v1/backup`) with the `agent_id`.
   Returns a `backup_id` (`b_...`).
5. **Poll** — `BackupByID` (`GET /v1/backup/{backup_id}`) or filter `Backups`
   (`GET /v1/backup`) until the backup reports complete; the completed backup
   references a `snapshot_id` (`s_...`) usable for restores.

## Rules
- Pagination is offset-based: pass `limit`/`offset`, follow `pagination.next_offset`.
- There is no idempotency-key; do not blindly retry `POST` calls — re-check
  state with the GET first to avoid duplicate backups.
- Errors return `{ codes, message, details }`; read `details[]` for the fix
  (see errors/slide-error-codes.yml).
