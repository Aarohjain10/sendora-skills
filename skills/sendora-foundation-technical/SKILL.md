---
name: sendora-foundation-technical
description: "Read first. How the Sendora MCP is organised, safety and compliance habits, how to audit a workspace, and how to troubleshoot. Every other Sendora guide assumes this one."
---

# Sendora: start here

Guide index: see the README. Technical guides explain the tools. Playbooks explain what to do for good business results. Pick the pair for the job: leads, voice, campaigns, channels, inbox, workspace, agency, plus the end to end GTM playbook.

## 1. How the MCP works and habits that help

Sendora runs outbound for a workspace: leads, voice agents, chat agents, campaigns across email, SMS, LinkedIn, WhatsApp and phone, the inbox, and the sending infrastructure underneath. You control all of it through the Sendora MCP.

### How the tools work

There are 27 tools. Most are grouped by resource and take an `action` field, for example `read_voice` with `action: "list_agents"` or `edit_leads` with `action: "import"`. The name tells you the effect:

| Prefix | Effect |
|---|---|
| `read_*`, `check_*`, `get_analytics` | Only reads or validates. It is better not to changes anything. |
| `edit_*`, `enrich_leads`, `find_prospects` | Creates or changes records. |
| `run_*`, `send_outreach` | Reaches real people or spends money. |
| `delete_resource` | Permanent. It helps to confirm first. |
| `launch_campaign`, `stop_campaign` | The only ways to start or stop a campaign. |
| `call_public_api` | Fallback for any documented API operation no tool covers. |

Resource families: `campaigns`, `leads`, `voice`, `conversations`, `platform` (workspace, numbers, billing reads), `channels` (sending accounts, domains, SMS registration, purchases), `agency` (partner clients and pricing).

### Habits that usually help

These are guidance, not commands. Every situation is different, so use judgement and adapt.

1. **Look before you change.** A `read_*` call first shows what already exists. Ids from results are safer than guessed ones.
2. **Tell the user what you plan to change.** It helps to check in right before a write, especially one that sends, calls, spends, publishes, deletes or changes a connected account, by saying the target and the effect.
3. **Drafts are low risk.** Editing a campaign does not start it. Only `launch_campaign` starts one. Saving a voice agent draft does not publish it.
4. **Ids are public ids** (short URL-safe strings). Passing them exactly as returned works best. A UUID is not the right input.
5. **Long work returns a job.** `read_platform` with `action: "jobs"` follows it. Reporting success is best left until the job is done.
6. **Writes can be replayed.** Where a tool accepts `idempotency_key`, a stable key for the action means a retry does nothing twice.
7. **When something blocks you,** such as a permission, a credit balance, a compliance rule or a missing connection, it usually works best to report the blocker and the fix instead of retrying in a loop.
8. **Keep secrets out of the conversation.** Printing API keys, tokens or full credentials rarely helps anyone.
9. **Check your work.** Reading the record back and reporting what you saw, rather than what you intended, keeps everyone on the same page.

### Picking where to start

| The user wants | Read this skill |
|---|---|
| A new or empty workspace, "set me up" | sendora-onboarding |
| "Is my account ready to send?" | sendora-foundation-technical |
| A full outbound plan | sendora-foundation-business |
| Leads in the system | sendora-leads-technical |
| A voice agent | sendora-voice-technical, sendora-voice-business |
| A campaign | sendora-campaigns-technical, sendora-campaigns-business |
| Replies and the inbox | sendora-inbox-technical |
| Something is broken | sendora-foundation-technical |

### Reporting style

Short and concrete. Say what you did, what you saw, and the single next step. Most users do not need the tool names unless they ask.

## 2. Compliance and do-not-contact

Compliance is enforced by the platform, and it goes more smoothly when your plans work with it.

### Do-not-contact list

- Read: `read_leads` `dnc`.
- Add: `edit_leads` `add_to_dnc` with the email or phone. Do this immediately when a lead says stop, unsubscribe, remove me, or similar, on any channel.
- It is better not to remove a DNC entry unless the user states the lead gave fresh consent and you can say where.

### Before a campaign

1. Check the list against DNC (a prepared import and campaign preflight both report suppressed leads).
2. Confirm the workspace has a mailing address set (`read_platform` `settings`). Email needs it.
3. Confirm channel consent rules for the region: SMS requires prior consent and registration; calls respect quiet hours.

### During a campaign

- Email carries a one-click unsubscribe. Prefer not to remove it from templates.
- Honour the traffic light and pause buttons in the inbox (`edit_conversation` `conversation_step` `pause_outreach`).
- Quiet hours and active hours are enforced per account. Prefer not to try to bypass them.
- Holidays: `edit_platform` `settings_step` `add_holiday` stops sending on that date.

### Replies that mean stop

"Not interested" is not always DNC, but "stop", "unsubscribe", "do not contact" and "take me off" are clear requests to be added. When unsure, pause outreach for that lead and ask the user.

### Things that tend to backfire

Contacting someone on a channel they did not give, buying or scraping lists to get around suppression, or turning off the AI reply pause (`ai_reply_paused`) just to push volume.

## 3. Audit a workspace before you start

Use before any launch, when onboarding a client, or when the user asks "are we ready?". This skill only reads. It changes nothing.

### Sequence

1. **Workspace basics.** `read_platform` `settings` with `include: "org,company_profile,ai_reply,mailing_address,holidays"`. Note a missing mailing address: email needs one for compliance.
2. **Sending accounts.** `read_channels` `accounts`. For each account that matters, read it with `include: "health,limits,strength,incidents"`. Flag status other than active, lifecycle problems, warmup not finished, open incidents.
3. **Domains.** `read_channels` `domains`, then one with `include: "dns"`. Flag failing SPF, DKIM or DMARC.
4. **Sender pools.** `read_channels` `sender_pools`. Flag empty pools and pools with paused members.
5. **Phone numbers.** `read_platform` `phone_numbers`, and `phone_details` for any number a voice agent will use. Flag numbers with failed setup.
6. **SMS readiness.** `read_channels` `sms_readiness`. It says whether the workspace can text yet and what step is next.
7. **Caller IDs.** `read_channels` `caller_ids`. Flag unverified.
8. **Voice agents.** `read_voice` `list_agents`, then `check_voice_agent` `validate` on each agent that will be used.
9. **Integrations.** `read_platform` `integration_health` and `crm_connections`. Flag anything unhealthy and report the last known state rather than a guessed one.
10. **Billing.** `read_platform` `billing` with `include: "billing.wallet,billing.subscription,billing.capacity"`. Flag low wallet balance and capacity limits.
11. **Waiting work.** `read_conversations` `needs_attention`. Flag approvals and escalations already waiting.

### Output

A table with three columns: area, state, fix. Group into **Blocking**, **Should fix**, **Fine**. End with the one action to do first. Prefer not to fix anything without being asked.

## 4. Troubleshooting

### First moves

1. Read the exact error. Validation errors carry a code, a path, a message and a repair hint. Fix the input and retry once.
2. Check the job if one exists: `read_platform` `jobs`.
3. Read the record back to see its real state.
4. Check `needs_attention`.

### Error classes

| Class | Meaning | Do |
|---|---|---|
| 401 / permission | Key or role lacks access | Say which scope or role is needed |
| 404 | Wrong id or wrong workspace | Re-read lists for the right id |
| 409 | In use or conflicting | Read what uses it, resolve, retry once |
| 422 | Input invalid | Use the hint, fix, retry |
| Credit or plan block | Not enough balance or capacity | Report. Prefer not to retry |
| 5xx / vendor down | Transient | Wait and retry later instead of looping |

### Symptoms

- **Campaign not sending:** preflight blockers, account paused or unhealthy, outside active hours, daily limit reached, leads suppressed, no leads enrolled.
- **High bounces:** unverified list, DNS failing (sendora-channels-business).
- **Calls not placed:** agent not published, no number or caller ID, quiet hours, DNC, wallet empty.
- **SMS not delivered:** registration not approved, number not attached, opt out.
- **Integration red:** read health, run `check_integration`, ask the owner to reconnect.
- **AI replies silent:** `ai_reply_paused`, chat agent not live, traffic light on human. For a chat agent the full list of causes is in `sendora-chat-agents-technical` section 8.
- **Purchase stuck:** read `purchases` with the operation id before retrying.

### Report

What you saw, the likely cause, what you changed (if anything), and the next step for the user. If you cannot fix it, say what is needed and from whom.
