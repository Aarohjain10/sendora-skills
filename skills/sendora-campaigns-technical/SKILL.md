---
name: sendora-campaigns-technical
description: "Technical guide to campaigns via the MCP: node types, building and validating a journey graph, A/B variants on nodes, journey rules, senders, enrolment, preflight, launch, pause, stop, folders, templates, clones, and reading analytics."
---

# Campaigns: technical guide

How to build, run and measure campaigns with the tools. For sequences, timing, branching and testing strategy, read `sendora-campaigns-business`.

## 1. Build a campaign

A campaign is a journey graph. Build it as a draft. Launching is a separate step.

### Node types

| Group | Types |
|---|---|
| Entry | `trigger` |
| Send | `send_email`, `send_sms`, `voice_call`, `linkedin_connection`, `linkedin_message`, `linkedin_inmail`, `whatsapp_message` |
| Flow | `wait`, `condition`, `route`, `branch` |
| AI and data | `ai_personalization`, `ai_decision`, `enrich`, `update_pipeline` |
| Coordination | `create_task`, `human_approval`, `escalation`, `crm_update` |
| Delivery | `send_webhook` |

Condition operators: equals, not_equals, contains, not_contains, is_null, is_not_null, is_empty, is_not_empty, greater_than, greater_or_equal, less_than, less_or_equal, is_true, is_false. Rotation strategies for sender choice: least_used, round_robin, weighted, sticky_by_lead.

### Steps

1. **Existing work.** `read_campaigns` `list`, and `templates` (a template may already match). Folders: `folders`.
2. **Create the draft.** `edit_campaign` `create` with a name.
3. **Design the graph on paper first.** Aim for 4 to 7 touches over 10 to 21 days. Mix channels only where the lead has them. Put a wait of 2 to 4 days between touches. After any reply, leave the sequence (use a condition on reply).
4. **Validate.** `read_campaigns` `validate_workflow` on the full graph. Every node needs a unique id, a supported type, numeric `position.x` and `position.y`, and a `data` object. Every edge needs a unique id, real source and target ids, and a valid handle. Fix every blocking issue by its path and hint.
5. **Save.** `edit_campaign` `build_workflow` validates, saves and preflights in one call, or `save_workflow` to only save. Neither launches.
6. **Rules.** `update_config` sets journey rules and per-step variables. `update_replies` sets how replies are handled.
7. **Sender setup.** Which accounts send, and how, is set per channel: `read_campaigns` `channel_settings` shows the saved settings, then `edit_campaign` `update_channel_settings` with `body.config.channels`. Send only what changes; it is merged into what is saved. Use the public ids from `read_channels` `accounts` (mailboxes and WhatsApp) or `read_platform` `phone_numbers` (voice and SMS) in `account_ids`. Keys by channel:

| Channel | Keys and limits |
|---|---|
| email | `account_ids` (mailboxes that are ready to send), `signature` (up to 2,000 characters), `default_subject` (one line, up to 255), `cc` and `bcc` (up to 10 valid addresses each), `track_opens`, `track_clicks`, `include_unsubscribe` (true or false) |
| sms | `account_ids` (numbers that can send texts), `max_length` (whole number from 40 to 480, default 160), `stop_keyword_reply` (up to 320 characters) |
| whatsapp | `account_ids` (connected WhatsApp accounts), `use_template` (true needs an approved `template_name` first), `template_name` |
| voice | `account_ids` (numbers that can place calls) |

A released or not-ready number, a number that cannot do the channel, or an account from another workspace is refused with a plain 422. Binding a sender pool to a campaign does not change who sends. Set active hours with `update_config` journey rules.
8. **Enrol a test list only.** `enroll_leads` with 2 or 3 internal leads. Do not enrol the real audience while building.
9. **Preflight.** `read_campaigns` `prepare_outreach` and `edit_campaign` `step` with `action: "preflight"`. Resolve every blocker.
10. **Hand over.** Summarise the journey in plain words, list what is untested, and wait for the user.

### Useful steps

`edit_campaign` `step` actions: `clone` (optional new name), `continue`, `preflight`, `export`. `import` creates a draft from an export file. `pause` and `resume` change run state.

## A/B testing is per step (message), inside the workflow

Each message step (email, SMS, WhatsApp, LinkedIn) can hold several variants of its copy. They live in the step's own data: `data.steps[i].variants[]`, and the sender reads them from there. A variant is:

| Field | Meaning |
|---|---|
| copy | `subject` and `html_body` for email, `message` for SMS and WhatsApp, `note` for LinkedIn |
| `enabled` | false switches the variant off without deleting it |
| `distribution` | optional share from 1 to 100. Leave it off on all variants for an even split |
| `variant_id` | set by the system. **Keep it on a variant you keep** so its results survive; leave it off a new one |

Each lead gets one variant for good (picked from the lead's id), so results stay clean.

**Edit one step's variants without resending the workflow:** `edit_campaign` `update_step` with `body.nodes`:

`{"<step id>": {"step_variants": {"0": [ {existing variant with its variant_id}, {"enabled": true, "subject": "New subject", "html_body": "...", "distribution": 40} ]}}}`

`step_variants` replaces that message's whole variant list, so send every variant you want to keep. `data` in the same patch merges other step settings (a label, a wait time). The whole updated workflow is validated and saved through the normal path, which also updates the variant results table. A saved variant that is missing from the list is deleted along with its assignments.

**Read results:** `get_analytics` `email_variants` (email steps across a campaign) or `read_campaigns` `variants` (query `id` and `node_id`) for one step: each variant's key, assignments and outcomes.

**Do not** try to add variants by writing the results table directly: the sender never reads it and the next save removes rows that are not in the workflow.

## Journey rules and per-step variables

`edit_campaign` `update_config` with `body.config.journey_rules` sets: exit on reply, send window and send days, timezone, and the weekly touch cap. `body.config.node_variables` sets per-step variable values. Read them back with `read_campaigns` `config` and `include` of `config.journey`, `config.ai`, `config.triggers`, `config.condition_fields`, `config.dialer_eta` (pass `node_id` for one step's variables). `config.condition_fields` lists the lead fields a `condition` node can test, including custom fields.

## Safe testing, history and housekeeping actions

| Need | Tool and action |
|---|---|
| Run the saved test contact through the whole campaign (sends real messages to that contact) | `send_outreach` `campaign_test_run` (`body.action` test_run, optional `body.lead_id`) |
| Send one step to the test contact | `send_outreach` `campaign_send_test` (`body.action` send_test, `body.node_id`) |
| See saved workflow versions | `read_campaigns` `versions` (add `version_no` to read one with its graph) |
| Roll the workflow back | `edit_campaign` `restore_version` (`body.config.restore_version`, draft or paused only) |
| Set the AI provider and model | `edit_campaign` `update_ai_settings` (`body.config.ai`; a key is never set here) |
| Move to a folder | `edit_campaign` `move_to_folder` (`body.folder_id`, null removes from folders) |

Tests use the workspace's one saved test contact. Set that contact in the app first. Ask the user before any test: it sends a real message or call to that contact.

## 2. Launch, pause, stop and monitor

### Before launch

1. `read_campaigns` `prepare_outreach` for the preflight and next actions. Clear every blocker.
2. Confirm out loud with the user: campaign name and id, audience size, channels, sending accounts and daily limits, schedule and time zone, first message text, and what happens on a reply.
3. Check infrastructure is healthy (sendora-foundation-technical) and DNC is applied.
4. Start with a small batch if the campaign has not run before. Enrol 20 to 50 real leads, not thousands.

### Launch

`launch_campaign` with the campaign id, only after an explicit "launch" or "go live". If it returns a job, follow it with `read_platform` `jobs`.

### First 24 hours

Check at 1 hour, 4 hours, next day:

- `get_analytics` `campaign`: sends, delivered, bounces, replies.
- Bounce rate above 3 percent or any spam complaint: pause and investigate (sendora-channels-business).
- `read_channels` `accounts` with `include: "health,incidents"`: any account flagged.
- `needs_attention`: replies and approvals waiting.

### Control

- `edit_campaign` `pause` stops future work. It does not recall messages already sent.
- `resume` restarts it.
- `stop_campaign` is permanent and highly destructive. Only on a clear instruction.

### After a timeout

Read the campaign state before repeating any transition.

## 3. Folders, templates, clones

### Folders

- `read_campaigns` `folders`.
- `edit_campaign` `create_folder` (name), `rename_folder`.
- `delete_resource` `campaign_folder` removes the folder. Its campaigns stay.

Folder per client, quarter or motion.

### Templates

`read_campaigns` `templates` lists ready made campaigns (filter by search text, category or channel) or reads one by slug. Start from a template when one fits, then adapt the copy and targeting.

### Reuse your own

- `edit_campaign` `step` `clone` copies a campaign (give a new name).
- `step` `export` produces an export file. `edit_campaign` `import` creates a draft from it, useful for moving a winner between workspaces.

## Results

Reports, funnels and A/B results are read with `get_analytics`; see `sendora-analytics-technical`.
