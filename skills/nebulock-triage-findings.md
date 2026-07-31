---
name: Triage Nebulock security findings
description: List, review, comment on, and update security findings surfaced by the Nebulock threat-hunting platform.
api: openapi/nebulock-openapi.yml
operations: [list_findings, get_finding_with_details, get_finding_comments, create_finding_comment, update_finding]
---

# Triage Nebulock security findings

Use the Nebulock Findings API to work a queue of security findings.

## Auth
Every request needs two headers: `X-API-Key-ID` and `X-API-Key-Secret` (issued by an
org admin in the Nebulock platform). Rate limit: 60 requests/minute — back off on `429`.

## Steps
1. **List open findings** — `list_findings` (`GET /api/v2/findings`). Page through using the
   returned pagination metadata (`meta` / `data`).
2. **Read the detail** — `get_finding_with_details` (`GET /api/v2/findings/{finding_id}`).
   Check `updated_at` to see if new signals have changed the finding since you last looked.
3. **Read discussion** — `get_finding_comments` (`GET /api/v2/findings/{finding_id}/comments`).
4. **Add your triage note** — `create_finding_comment`
   (`POST /api/v2/findings/{finding_id}/comments`).
5. **Update status/fields** — `update_finding` (`PATCH /api/v2/findings/{finding_id}`).
   Send only the fields you want to change.

## Rules
- The API returns only the most recent version of a finding; use `updated_at` to detect churn.
- No idempotency-key contract exists — do not blindly auto-retry the `PATCH`/`POST` writes.
