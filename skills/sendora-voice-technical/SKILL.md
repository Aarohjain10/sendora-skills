---
name: sendora-voice-technical
description: "Technical guide to voice in Sendora via the MCP: create, configure, test and publish a voice agent, attach numbers and caller IDs, inbound calls, tools, knowledge bases, voices and cloning, widgets, reviewing calls."
---

# Voice: technical guide

How every voice tool works and the order to use them. For how to write the prompt and run good calls, read `sendora-voice-business`. Typical order: number and caller ID, voice, prompt, knowledge and tools, validate, draft, test, publish, verify, review.

## Quick path: working agent with a number, outbound and inbound

1. **Number.** `read_platform` `phone_numbers`. If none, `search_phone_numbers`, `phone_requirements`, `edit_channels` `quote_purchase`, confirm the price, `run_channels` `buy`, follow `purchases` with the `operation_id`. Check `phone_details` shows setup complete; `edit_platform` `number_step` `retry` resumes a failed setup.
2. **Caller ID** for outbound: `start_caller_id_verification`, `confirm_caller_id`. Set display name with `update_number_settings` (`caller_id_name`, `agent_caller_id_name`).
3. **Agent draft.** `check_voice_agent` `validate`, then `edit_voice` `save_draft`.
4. **Knowledge and tools.** `create_knowledge_base`, `add_kb_text` or `add_kb_url`, `assign_kb`; add tools and attach with `update_agent` (`tool_ids`, `knowledge_base_ids` replace the whole set).
5. **Attach the number to the agent.** `edit_platform` `update_number_settings` with `assigned_agent_id`. Then `save_channel_assignments` for the agent's channels (`phone`), and `read_voice` `channel_assignments` to confirm both sides agree.
6. **Inbound.** `update_inbound_config` (`capture_leads`, `lead_defaults`, `after_hours`).
7. **Test.** `test_config`, `browser_test`, then `phone_test` to the user's own phone, then call the number from another phone to test inbound.
8. **Publish** (needs a yes, and a confirmation id): `run_voice` `publish`; then `check_voice_agent` `verify`.
9. **Outbound use.** In a campaign `voice_call` step, or one off with `run_voice` `call`.

## Settings that make calls sound better

Every field below is on `save_draft` and `update_agent`. Ranges are enforced; the "start with" values are sensible defaults to test, not guarantees. Change one setting at a time and listen to a test call after each.

| Setting | Range and default | Start with | Why |
|---|---|---|---|
| `temperature` | 0 to 1, default 0.7 | 0.3 to 0.5 for sales and support; up to 0.7 for relaxed chat | Lower is steadier and less likely to invent; higher is more varied |
| `voice_speed` | 0.7 to 1.2 | 1.0; 0.95 to 1.05 for most; slower for older or non-native listeners | Fast speech reads as pushy, slow reads as sleepy |
| `voice_id` / `voice_model` | from `list_voices` | pick by listening to the same two lines on three voices | A clear, warm voice beats an unusual one |
| `first_speaker` | `agent` (default) or `user` | outbound cold: `user` (let them say hello first); inbound and widget: `agent` | Speaking over a "hello" sounds robotic |
| `first_message_delay_seconds` | 0 to 30 | about 1 second after pickup on outbound | A short beat avoids talking over the pickup |
| `first_message` | text | one short sentence with the permission opener | The first 5 seconds decide the call |
| `max_duration_seconds` | at least 120, default 300, platform maximum applies | 300 to 480 for cold calls; 600 and up for inbound support | Stops runaway calls; set `time_exceeded_message` to close politely |
| `join_timeout_seconds` | 5 to 300 | 30 to 60 | How long to wait for a person to join |
| `inactivity_messages` | up to 10 | a nudge after about 8 to 10 seconds of silence ("Are you still there?"), then a polite close | Handles silence and bad lines |
| `uninterruptible` | true or false | false; true only for a legally required statement | People must be able to cut in |
| `recording_enabled` | true or false | on where lawful, with a disclosure line in the opening | Needed for call review and training |
| `voicemail_enabled` and `voicemail_script` | true or false, text | on, 15 to 20 second script | Do not waste a dial |
| `language_hint` | language code | match the lead's language; test accents | Better recognition |
| `output_medium` | `voice` or `text` | `voice` for calls; `text` only for text-mode tests | |
| `model` | model name | leave the default unless tests show a need | |

Practical rules:
- Put facts in the knowledge base and behaviour in the prompt (see `sendora-voice-business`). Do not tune settings to fix a wrong answer.
- Attach only the tools the call needs, and keep tool timeouts short. A slow tool is dead air.
- Give each prompt variable a fallback so a missing value is never read aloud as "blank".
- Test inbound and outbound separately: they want different `first_speaker` values.
- After any change, run `check_voice_agent` `validate`, then a browser test call.

## Agent fields (every one can be set)

`name`, `display_name`, `system_prompt`, `first_message`, `first_speaker`, `first_message_delay_seconds`, `language_hint`, `voice_id`, `voice_asset_id`, `voice_model`, `voice_speed`, `model`, `temperature`, `max_duration_seconds`, `join_timeout_seconds`, `time_exceeded_message`, `inactivity_messages`, `uninterruptible`, `output_medium`, `recording_enabled`, `voicemail_enabled`, `voicemail_script`, `tools`, `direction` (inbound or outbound, set when created), `is_active`. The shortcut `create_agent` only takes name, prompt, voice, language and first message; use `save_draft` with a full config for the rest, and `update_agent` to change them later. Start from a template: `read_voice` `agent_templates`. For the booking tool, list a calendar's meeting types with `read_voice` `meeting_types` (query `calendar_id`).

## 1. Create, configure and retire an agent

A voice agent makes and takes calls. Build it as a draft, test it, then publish.

### Flow

1. **Brief.** Capture: what the agent is for (outbound cold call, appointment setting, inbound reception, follow-up), who it speaks as, the company, the offer, the goal of the call, what it must never do, and where to hand off to a human.
2. **Check what exists.** `read_voice` `list_agents`. Reuse or clone thinking rather than duplicating.
3. **Pick a voice.** `read_voice` `list_voices` (catalogue) and `custom_voices` (the workspace's own). `agent_voices` shows what a given agent can use. See sendora-voice-technical.
4. **Write the prompt** (sendora-voice-business).
5. **Validate.** `check_voice_agent` `validate` on the candidate config. Fix every issue by path.
6. **Save a draft.** `edit_voice` `save_draft`, or `create_agent` with `draft: true`. Nothing is published and no number is assigned.
7. **Attach knowledge and tools** (sendora-voice-technical, sendora-voice-technical): `assign_kb`, `create_tool`, or `update_agent` with `knowledge_base_ids` and `tool_ids` (these replace the current set, so send the full list).
8. **Test** (sendora-voice-technical).
9. **Publish** only after the user says yes: `run_voice` `publish`.
10. **Verify.** `check_voice_agent` `verify` after publishing.

### Changing an agent

`edit_voice` `update_agent` takes only the fields that change. Related actions:

- `update_inbound_config`: capture leads from inbound calls, defaults for new leads, after hours behaviour (sendora-voice-technical).
- `save_channel_assignments`: which sending accounts and numbers the agent uses.
- `update_test_context`: the variable values used when testing.

### Variables

Prompts can use per-lead variables such as the lead's first name and company. Every variable used must have a value at call time or the call quality drops. The validator lists the variables an agent needs. Give test values with `update_test_context`.

### Deleting

`delete_resource` `voice_agent`. Confirm first. Check the agent is not assigned to a live campaign.

### Read a full agent

`read_voice` `get_agent`, `configuration`, `inbound_config`, `channel_assignments`, and `context` with `include: "tools,tool_catalog,test_context"`.

## 2. Voices and cloning

### Choose

- `read_voice` `list_voices` is the catalogue. Filter by language, gender, accent.
- `custom_voices` lists the workspace's own imported or cloned voices.
- `agent_voices` lists what a given agent can use.

Pick for clarity and warmth, not novelty. Test the same two sentences on three voices.

### Add or clone

- `edit_voice` `add_custom_voice` registers a voice from the customer's own speech provider (`provider`, `external_id`, `name`).
- `clone_voice` creates a voice from a speech sample (`name`, `gender`, `nationality`, `consent_confirmed`, and `audio_url` or `audio_base64`).

**Cloning needs consent.** Only clone a voice if the speaker has agreed in writing. Set `consent_confirmed` to true only when the user states that. Cloning is charged by the provider: say so before you do it.

Sample tips: 1 to 3 minutes of clean speech, one speaker, no music, no echo.

### Change and remove

- `update_custom_voice` changes settings (`canonical_settings`, `confirm_assignments`).
- `delete_resource` `custom_voice` fails with 409 while an agent uses it. Reassign the agent first.

### After a change

Run a browser test call so the user can hear it.

## 3. Knowledge bases

A knowledge base is the facts an agent may use: offer, pricing rules, FAQs, policies, case studies.

### Read

- `read_voice` `list_knowledge_bases`.
- `get_knowledge_base` with an `id` shows the base and its sources. Pass `source_id` for one source's editable copy and version.

### Create and fill

1. `edit_voice` `create_knowledge_base` with a name and description.
2. `add_kb_text` for written facts, `add_kb_url` to pull a web page.
3. `update_knowledge_base` renames it and can add, edit or remove sources in one call (`add_sources`, `update_sources` with `expected_version`, `remove_source_ids`). Use `expected_version` so two edits do not overwrite each other.
4. Provider connected sources (for example a document drive or a note workspace) refresh on their own. `accept_provider_updates` takes the latest, `restore_snapshots` goes back.

### Assign

`assign_kb` to an agent, `unassign_kb` to remove. Or set `knowledge_base_ids` in `update_agent`.

### Delete

`delete_resource` `knowledge_base`. Check which agents use it first.

## 4. Tools

Tools let the agent act during a call.

### Kinds

- **Built in**: hang up, transfer to voicemail and similar. Present on most agents.
- **Sendora tools**: propose meeting times, update lead state (not interested, snoozed, DNC, qualified) with a reason, escalate to a human.
- **Custom HTTP tools**: call the customer's own URL with parameters the model fills in.

### Read

- `read_voice` `list_tools` for one agent, `tool_catalog` for what can be added, `workspace_tools` for tools shared across the workspace.
- `read_voice` `context` with `include: "tools,tool_invocations"` and a `tool_id` shows how a tool was used.

### Create and change

- `edit_voice` `create_tool` (for an agent) or `create_workspace_tool` (reusable, set `agent_id` to limit to one agent).
- `update_tool`, `update_workspace_tool` change a definition.
- `test_tool`, `test_workspace_tool` run a safe test. Always test before attaching.
- Attach to an agent with `update_agent` `tool_ids` (replaces the set, send the full list).

### Designing a custom tool

- Name it as a verb: `check_availability`, `create_ticket`.
- Describe when to use it in one sentence. The model reads this to decide.
- Keep parameters few, each with a clear description and type.
- Return short, speakable results. Long JSON confuses the call.
- Set a short timeout. A slow tool is a long silence on the phone.
- Keep secrets in the tool's auth settings. Never paste a key into a description or prompt.

### Remove

`delete_resource` `voice_tool` (from one agent) or `skill` (the workspace tool). Confirm first.

## 5. Phone numbers and attaching them to agents

### Read

- `read_platform` `phone_numbers` lists numbers.
- `phone_details` with `id` and `include` of `history, usage, alpha_sender, documents`.
- `search_phone_numbers` with country, type, features and pattern filters. It shows prices. It may return more results than `limit` (it searches in best-effort mode), so show the user a shortlist of five to ten.
- `phone_requirements` for a number: what the carrier needs before purchase.
- `purchases` for history and capacity.

### Buy (money)

1. Search. Show the user the shortlist with prices.
2. `phone_requirements`. Some countries need an address or documents first.
3. If required, `edit_platform` `add_regulatory`: `regulatory_address`, then `regulatory_document` (filename and `content_base64`).
4. `edit_channels` `quote_purchase` with `kind: phone_number`. This charges nothing.
5. Read the price back and get a yes.
6. `run_channels` `buy` with the `quote_id`. Check status through `purchases` with `operation_id`.

### Bring your own numbers

`edit_platform` `import_numbers` (steps search, confirm, outbound_profile) or `import_twilio` (steps `twilio_search`, then `twilio_confirm` with the account details). Ask the user to provide credentials directly in the app if you cannot enter them safely.

### Configure

`update_number_settings`: assigned agent, caller ID name, verified caller ID, alphanumeric sender ID. `number_step`: `retry` (resume failed setup), `test_message`, `send_documents`.

### Release

`delete_resource` `phone_number` releases it. `confirm_unassign` also removes it from what uses it. An imported number is only removed from the workspace. Confirm twice: a released number may be lost.

### Rules

Calls need a caller ID the lead will trust. Texting needs registration (sendora-channels-technical). Keep one number per agent role.

## 6. Caller IDs

### Read

`read_channels` `caller_ids`.

### Verify

1. `edit_channels` `start_caller_id_verification` with `phone_e164` and `method` call or sms.
2. Ask the user for the code they receive.
3. `confirm_caller_id` with the `code`.

### Use

Set the verified caller ID on a number or agent with `edit_platform` `update_number_settings` (`verified_caller_id`, `caller_id_name`).

### Remove

`delete_resource` `caller_id`.

### Notes

Only verify numbers the customer owns. A mismatched caller ID can be flagged as spam. Keep the display name short and the same as the company name the agent says.

## 7. Inbound calls

### Setup

1. Have a number (sendora-voice-technical).
2. Create a published voice agent written for inbound (greeting, ask what they need, collect name and callback number, route).
3. `read_voice` `inbound_config` to see current settings.
4. `edit_voice` `update_inbound_config` with:
   - `capture_leads`: create a lead for each new caller
   - `lead_defaults`: list, tags, pipeline stage for new leads
   - `after_hours`: what to do outside working hours (take a message, book a callback, route to voicemail)
5. Assign the number to the agent with `edit_platform` `update_number_settings` (`assigned_agent_id`).
6. `read_voice` `channel_assignments` to verify.

### Test

Call the number from a phone you own. Test: a normal enquiry, a wrong number, an angry caller, a request for a human, a call outside hours.

## 8. Web widgets

A widget lets a website visitor talk to an agent from the browser.

### Read

`read_voice` `list_widgets`, `get_widget`, and `widget_embed` for the snippet to paste.

### Create and change

`edit_voice` `create_widget` for an agent, with allowed domains, label, colours and position. `update_widget` changes them.

### Rules

- Restrict the widget to the customer's own domains.
- Use a published agent. Test with `run_voice` `browser_test` first.
- Tell visitors it is an AI call and that it may be recorded.
- Give the widget an after hours behaviour if no one should be transferred to.

### Install

Give the user the embed snippet and where to paste it (before the closing body tag). Then ask them to load the page and place a test call.

### Remove

`delete_resource` `voice_widget`. Confirm first. The snippet stops working at once.

## 9. Test and publish

Never publish untested. Never test on real leads.

### Tests, cheapest first

1. `check_voice_agent` `validate`: config is complete.
2. `test_config`: runs the config checks without a call.
3. `run_voice` `browser_test`: a live call in the browser. Use `start_test_call` through `edit_voice` `agent_step` if you need `template_context` or text only mode.
4. `run_voice` `phone_test`: rings a phone the user owns. Confirm the number with the user first.
5. `read_voice` `list_calls` and `get_call` to read the transcript. `context` with `include: "test_call"` and a `call_id` reads the test call. End a stuck test with `agent_step` `hangup_test_call`.

### Publish

Ask: "Publish <name> now? It will be able to place and take calls." On yes, `run_voice` `publish` (a confirmation id may be required). Then `check_voice_agent` `verify` and read the agent back.

### Real calls

`run_voice` `call` places a real call to a lead. Only on an explicit instruction naming the lead and agent. Check the caller ID, quiet hours and DNC first.

## 10. Review calls

### Pull calls

`read_voice` `list_calls` (filter by agent, date, outcome), `get_call` for one. A recording can be fetched by its download link in the call record. Treat recordings as sensitive.
