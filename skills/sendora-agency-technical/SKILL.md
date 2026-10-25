---
name: sendora-agency-technical
description: "Technical guide for agencies via the MCP: client workspaces, limits and access, packages, markup pricing, branding and onboarding."
---

# Agency: technical guide

Business advice for running an agency on Sendora is in `sendora-agency-business`.

## 1. Clients

For agencies reselling Sendora. Needs agency scopes and client clearance.

### Read

`read_agency`: `list_clients` (returns every client at once; `limit` is not applied), `client_details` (status, plan, owner, health, 30 day usage and revenue), `client_users`, `client_limits` (caps, access, activity and your changes with `include` of `limits, audit`), `client_features`.

### Create and change

`edit_agency`:

- `create_client`, then `client_branding`, `client_features`.
- `update_client_profile`: name, owner, domain, tier label, private notes, account manager, renewal.
- `update_client_limits`: monthly `usage_caps` and channel or tab `access`.
- `invite_client_user`, `update_client_user` (portal mode, visible tabs).
- `client_step`: `activate`, `suspend`, `trial`. Owners and admins only.

### Remove

`delete_resource` `client` closes a client (needs `confirm_name`, the exact name). It is restorable for 30 days in the app. `client_user` removes people.

### Suspend

Suspending stops the client using the app. Confirm the client name, say that running work may stop, and get a yes.

## 2. Packages and pricing

### Read

`read_agency` `packages` lists packages with every version's price and included credits. `pricing` shows your published markup, any draft, and each rate's retail price.

### Create

- `edit_agency` `create_package` (name, `monthly_price_cents`, `included_credits`).
- `create_package_version` publishes a new version (`package_id` plus fields). Existing clients keep their version until moved.
- `save_pricing` saves a draft markup (`default_markup_bps`). `publish_pricing` makes it live. Package creation needs published pricing.

### Rules

Never edit a live version in place. Always make a new version. Explain to the user how many clients a change touches.

## 3. Branding and onboarding

### Read

`read_agency`: `signup_onboarding`, `launch_configuration`, `experience_draft`, `experience_versions`.

### Change

`edit_agency`:

- `signup_onboarding`: the signup and onboarding configuration for the agency's clients.
- `update_experience_draft`: the look and wording of the client experience (name, logo, colours, domain text).
- `publish_experience_draft`: makes the draft live.
- `rollback_experience`: return to an earlier version.
- `client_branding`: branding for one client.

### Flow

1. Read the current draft and the live version.
2. Change the draft only.
3. Show the user the difference.
4. Publish on a yes, then read back.
5. If something looks wrong, roll back first, investigate second.
