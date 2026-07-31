---
name: Author and deploy Nebulock detection rules
description: Validate, create, run, and manage Sigma / scheduled-SQL detection rules in Nebulock.
api: openapi/nebulock-openapi.yml
operations: [validate_rule_public_api, create_rule_public_api, list_rules_public_api, get_rule_public_api, update_rule_public_api, test_sql_rule_public_api, run_rule_scheduled_sase_public_api, get_rule_runs_scheduled_sase_public_api, get_rule_run_results_scheduled_sase_public_api, delete_rule_public_api]
---

# Author and deploy Nebulock detection rules

Manage detection rules of type `sigma`, `scheduled_sql`, or `signal_combination`.

## Auth
Headers `X-API-Key-ID` + `X-API-Key-Secret`. 60 req/min.

## Steps
1. **Validate first** — `validate_rule_public_api` (`POST /public/v1/rules/validate`). For Sigma
   send `content` as a YAML string; for SQL types send `content` as an object with `query`,
   `schedule`, and type-specific fields.
2. **Create the rule** — `create_rule_public_api` (`POST /public/v1/rules`). Omit
   `organization_id` (it is taken from your session). Start with `status: inactive` while
   iterating, then set `active` to deploy. `scheduled_sql` queries must contain
   `organization_id = ?`.
3. **List / inspect** — `list_rules_public_api` and `get_rule_public_api`
   (`GET /public/v1/rules/{rule_id}`; pass `version` for a specific revision).
4. **Iterate** — `update_rule_public_api` (`PATCH /public/v1/rules/{rule_id}`); send only changed
   fields. Updating `content` replaces the typed content for the rule's `rule_type`.
5. **Test SQL rules** — `test_sql_rule_public_api` for an on-demand run;
   `run_rule_scheduled_sase_public_api` for a retroactive run.
6. **Read results** — `get_rule_runs_scheduled_sase_public_api` (metadata) then
   `get_rule_run_results_scheduled_sase_public_api` for row-level results.
7. **Remove** — `delete_rule_public_api` deletes all versions of a rule.

## Rules
- Always `validate` before `create` to catch malformed Sigma YAML or SQL early (`422`).
- Rules are scoped to your session organization; you cannot list or run another tenant's rules.
