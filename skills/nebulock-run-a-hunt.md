---
name: Run a threat hunt and generate a report
description: Create a Nebulock hunt, drive it with follow-up directives, and generate a hunt report.
api: openapi/nebulock-openapi.yml
operations: [generate_hunt_suggestions, create_hunt_v2, get_hunt_v2, add_directive_v2, get_directive_v2, generate_hunt_report_v2, get_hunt_report, get_hunt_report_pdf]
---

# Run a threat hunt and generate a report

Drive the Nebulock agentic hunt loop end to end.

## Auth
Headers `X-API-Key-ID` + `X-API-Key-Secret`. Respect the 60 req/min limit.

## Steps
1. **(Optional) Get ideas** — `generate_hunt_suggestions`
   (`POST /public/v1/hunt_suggestions`) to seed hunts from threat-intel context.
2. **Create the hunt** — `create_hunt_v2` (`POST /public/v2/hunts`). Directive processing is
   async; the response returns the `hunt_id`.
3. **Poll the hunt** — `get_hunt_v2` (`GET /public/v2/hunts/{hunt_id}`) for directives, blocks,
   and enrichment until processing settles.
4. **Ask follow-ups** — `add_directive_v2` (`POST /public/v2/hunts/{hunt_id}/directives`); check a
   single directive with `get_directive_v2`
   (`GET /public/v2/hunts/{hunt_id}/directives/{directive_id}`).
5. **Generate the report** — `generate_hunt_report_v2`
   (`PATCH /public/v2/hunts/{hunt_id}/generate_report`).
6. **Fetch it** — `get_hunt_report` (markdown) or `get_hunt_report_pdf` (PDF).

## Rules
- Directive processing is asynchronous — poll, do not assume immediate completion.
- Use `stop_directive_v2` / `retry_directive_v2` to control a stuck or failed directive.
