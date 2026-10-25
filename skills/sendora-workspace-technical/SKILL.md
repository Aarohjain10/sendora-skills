---
name: sendora-workspace-technical
description: "Technical guide to workspace administration via the MCP: integrations and CRMs, webhooks, team and access, settings, billing reads."
---

# Workspace administration: technical guide

## 1. Integrations and CRM

### Health

`read_platform` `integration_health` shows every connected account with counts. One account with its incidents and recent checks by `id`. If a source is down, report the last known state with its time. Do not show a guessed healthy or zero.

`edit_platform` `check_integration` queues a fresh check (admins and owners).

### CRM

- `read_platform` `crm_connections` lists them, or one by provider name with `include` of `pipelines, contact_fields, opportunity_fields, calendars, logs, conflicts, mapping_conflicts`.
- `edit_platform` `update_crm`: sync `settings` and `field_mapping`.
- `edit_platform` `crm_step` with `action`: `test`, `sync` (optional lead, `force`) or `import` (`limit`, `dry_run`).
- `delete_resource` `crm_connection` disconnects.

Connecting a CRM or calendar needs the owner to sign in. Ask them to do that in the app, then read back.

### Safe sync routine

1. `test` the connection.
2. `import` with `dry_run` true and a small `limit`. Read the counts.
3. Check `mapping_conflicts`. Fix mapping.
4. Import for real. Verify a few leads.

### Rules

Know which direction data flows. Do not map one field two ways. Keep a CRM as the system of record for deals if the customer already uses one.

## 2. Webhooks

### Read

`read_platform` `webhooks` lists endpoints.

### Create and change

- `edit_platform` `create_webhook` with the HTTPS `url` and the event types to subscribe to.
- `update_webhook`: change `url`, `event_types`, `is_active`, `rotate_secret`, or `send_test`.
- `delete_resource` `webhook_endpoint`.

### Consumer rules (tell the customer's developer)

1. Read the raw body.
2. Verify the signature with the endpoint secret before parsing.
3. Deduplicate by the event `id`.
4. Queue the work and return 2xx fast.
Deliveries are at least once and may repeat.

### Setup routine

Create the endpoint, `send_test`, ask the customer to confirm receipt, then turn it live. Rotate the secret if it was ever pasted into chat or a ticket. Show a secret once, never store it in notes.

## 3. Team and access

### Read

`read_platform`:

- `team` lists members and pending invites.
- `team_access` with `include: "access_catalog"` shows roles and permissions, or `member_access` with `member_id` shows one member's role, grants and limits.

### Change

`edit_platform`:

- `team_step` with `action`: `invite` (email, role, full name), `resend_invite`, `revoke`, `suspend`, `reactivate`, `make_owner`. Owners and admins only.
- `set_team_access`: roles, extra `grants` and `denials`, and `scopes` that limit a member to certain campaigns or lead lists.
- `delete_resource` `team_member` removes a member.

## 4. Settings and billing

### Settings

`read_platform` `settings` with `include` of `org, company_profile, ai_reply, mailing_address, holidays`.

`edit_platform` `update_settings`: `org`, `company_profile`, `ai_reply_paused`, `mailing_address`. `settings_step` `add_holiday` (date, label) or `remove_holiday`.

Fill the company profile fully: agents and AI drafts use it. Mailing address is required for email compliance. `ai_reply_paused: true` stops AI replies everywhere, use it for emergencies and tell the user.

### Billing (read only)

`read_platform` `billing` with `include` of `billing.wallet, billing.subscription, billing.usage, billing.usage_daily, billing.capacity, billing.credit_summary` and the credit activity section. You cannot change plans or pay through the MCP. Direct the user to the app for that.

## Recorded consents (read only)

`read_platform` `consents` lists the workspace's feature consents (calling and recording, SMS, email on behalf, CRM, calendar and others), granted or withdrawn, with dates. Confirming or withdrawing a consent is a legal attestation and is done in the app by an admin or owner, not through the MCP.
