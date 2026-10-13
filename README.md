# Контакт

Private Russian-language client workspace. Built with React/TypeScript, Vinext, Cloudflare D1 and R2. The design follows the supplied screen reference. Starts with an empty database; no fictional client records or analytics.

## Implemented

- ChatGPT identity on every data request, server membership checks, owner-private Sites deployment, per-user request limits, origin checks.
- Shared relational database with immutable UUIDs, display IDs, unique normalized telephone numbers, original imported values, separate stages/results/priorities/owners/authors, persistent communication prohibitions.
- Paginated server search and filters, grid/table/processing modes, direct card edits, side panel and permanent client routes.
- Interaction attempts vs actual contacts, notes, tasks and completion, timezone planning, services/interests and amounts, chronological business history and technical audit log.
- Optimistic version checks and atomic dependent writes: no silent concurrent overwrite. Entered form state survives save errors.
- Import TXT/CSV/XLS/XLSX/DOCX/text-PDF; column mapping, preview, duplicate matching, editable names, batched saving, retry idempotency. Manual one-number creation.
- Actual aggregate statistics, employee attribution by event author, geography, direction demand, daily activity, configurable event period, metric navigation.
- CSV/XLSX filtered exports (formula-safe CSV), complete JSON snapshots stored in R2, atomic relational restore after a safety snapshot.
- Editable templates with manual preview/copy and deterministic suggestions. No external LLM or autonomous messaging.

## Runtime boundaries

Sites is owner-private. Adding an employee email in the app does NOT change platform access. Configure the specific employee in Sites before collaborative use; do not make the CRM public. Worker `/api/maintenance` relies on this owner-private platform boundary and supports the platform's service bearer path; if broadening audience, add narrower authorization before deployment.

Cloudflare storage is not a confirmed Russian-localized production deployment. The operator must resolve required data localization and processing bases before loading actual personal data.

## Backup automation

POST /api/maintenance creates a consistent D1 snapshot of all business tables in R2 under `backups/YYYY-MM-DD-automatic.json`. Repeat calls on the same UTC day reuse the saved snapshot. No customer records are returned. The linked Sites schedule should call it with service access obtained at runtime from Sites, never stored in source. Read the response key and then confirm the key with a second call (reused=true). Run daily at 03:00 Asia/Kamchatka. Maintain owner-private access.

## Known gaps against the full specification

- Browser QA is unavailable in this session; TypeScript, production build and SQLite-backed server-handler integration checks are provided.
- Automatic Russian data localization, external push delivery, OCR, arbitrary unknown binary formats, background PDF/OCR queues and geographical map are not connected.
- Only the first sheet of a workbook is imported. Duplicate fill updates missing names only; other fields remain untouched. Max 30 MB / 30,000 rows per import.
- Restore currently supports up to 10,000 rows across the snapshot; larger snapshots require a staged transactional restoration workflow. Live access accounts are retained to prevent owner lockout.
- Advanced multi-axis analytics, all exhaustive filters, persistent queue-session progress, editing category classifications, phone-primary reassignment and task rescheduling are not complete.
- Refresh synchronizes the open client list every 30 seconds; open detail editors retain their snapshot until refresh and use version conflict detection when saved.

## Validation

`node node_modules/typescript/bin/tsc --noEmit`
`node tests/core.mjs`
Build through Sites `build-site.mjs`.

The integration check uses an in-memory SQLite database with foreign keys and transaction-backed D1 compatibility. It exercises the actual API handler code: identity rejection, duplicate phone handling, stale-version protection, history, tasks, interests, forbidden-contact exclusion, import replay, backup/restore and aggregates. It does not simulate browser rendering or the hosted authentication proxy.
