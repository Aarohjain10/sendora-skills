---
name: sendora-workspace-business
description: "Business guide for running a Sendora workspace well: who gets which role, onboarding and offboarding people, choosing and governing integrations and webhooks, controlling credit spend, keeping settings and consents healthy, and a quarterly workspace review."
---

# Workspace: business guide

The tools are in `sendora-workspace-technical`.

## 1. Roles: least privilege

The workspace has one owner and three roles you can assign (read the live list with `read_platform` `team_access`):

| Role | Can | Cannot |
|---|---|---|
| owner | Everything, including transferring ownership and deleting the workspace | |
| admin | Manage the workspace, billing, team access, integrations and API access | Transfer ownership, delete the workspace |
| manager (the default for invites) | Run campaigns, work leads, reply in the inbox, view reports, use the API | Manage billing, team access, integrations |
| viewer (view only) | See campaign analytics and workspace summaries | Open campaign configuration, edit or export leads, read the inbox, use the API |

Who gets what: the founder or head of growth is owner; an operations lead is admin; anyone who runs campaigns day to day is manager; a stakeholder who only needs numbers is viewer. Scope contractors to their own campaigns or lead lists with `set_team_access`.

Rules: keep two owners if you can, no more than two or three admins, give access for a purpose and review it quarterly. It is better not to make someone owner without an explicit instruction from an existing owner.

## 2. People lifecycle

- **Join:** invite with the lowest role that works, scope contractors, and tell them which campaigns and lists are theirs.
- **Move:** change role and scope the same day. Prefer not to stack old access.
- **Leave:** suspend at once, reassign their campaigns, agents and lists, then remove. Check what they owned first.

## 3. Choosing integrations

- Name the **system of record** for each thing: leads (CRM), deals (CRM), meetings (calendar), outreach activity (Sendora). Data flows toward the system of record, not both ways for the same field.
- Connect one thing at a time, run the connection test, then a dry-run import with a small limit, then read the mapping conflicts before the real import.
- Keep field mapping simple: one source field per target field.
- Treat an unhealthy integration as an incident: read the last known state, tell the owner, be cautious with numbers that depend on it.

## 4. Webhooks that earn their keep

Subscribe only to events someone acts on. Typical sets:

| Use | Events to subscribe |
|---|---|
| Notify sales of interest | positive reply, meeting booked |
| Keep a CRM in step | lead created, stage changed |
| Ops alerting | send failures, account problems |

Send a test event first. Rotate the secret if it was ever shared in chat or a ticket. The receiving system should verify the signature and deduplicate by event id.

## 5. Controlling spend

- Know what burns credits: contact lookups on prospects, email verification, AI personalisation and research, calls, SMS, number and mailbox purchases.
- Preview before committing, verify before sending, and enrich only approved leads.
- Watch wallet cover weekly (`billing.wallet`, `billing.capacity`). Warn at under a week of cover.
- Set usage caps for client workspaces (agency) so one client cannot drain credits.
- Pause high cost steps first if cover is short; keep compliance steps (unsubscribe handling, DNC) running.

## 6. Settings that quietly matter

- **Company profile:** complete it; agents and AI drafts use it.
- **Mailing address:** required for email compliance.
- **AI reply pause:** use it for incidents only, and tell the team when it is on.
- **Holidays:** add public holidays for the main markets so nothing sends on them.

## 7. Consents (read only here)

The consent ledger records permissions such as call recording, SMS, email on behalf, CRM and calendar. Check it before turning on a feature that depends on one. If a consent is missing or withdrawn, the owner or admin confirms it in the app; do not work around it.

## 8. Quarterly workspace review (45 minutes)

1. Team: remove stale access, recheck roles.
2. Integrations: all healthy, mappings still right, unused ones disconnected.
3. Webhooks: still needed, secrets recent.
4. Spend: trend, biggest driver, anything wasteful.
5. Compliance: consents current, mailing address correct, DNC process working.
6. Agents and campaigns: archive what is dead, fold winners into templates.
Write the result as three lists: fine, fix this month, decide later.
