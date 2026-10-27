---
name: sendora-chat-agents-technical
description: "Technical guide to chat agents through the MCP: what a chat agent is, the three ways a conversation starts (inbound to a number or account, a campaign step, or a chat the agent starts itself), creating and configuring one, actions and approvals, follow-ups, safety limits, testing, going live, and troubleshooting."
---

# Chat agents: technical guide

Strategy and wording are in `sendora-chat-agents-business`. Replies to campaigns in general are in `sendora-inbox-technical`.

A chat agent is an AI agent that answers customers in writing on SMS, WhatsApp and email. It is not a voice agent: there is no call, no voice and no provider agent behind it. It is a normal agent of kind `chat` with one row per text channel. A voice agent that is also set up to text can appear in the same list, but only a `chat` agent can be edited with the tools below.

## 1. Before anything

- **Chat agents are switched on per workspace by Sendora.** Until then every chat agent tool answers 404 "Not found". Call `read_conversations` `list_chat_agents` first. A 404 means the workspace does not have them yet: tell the user, and do not try to work around it.
- Writes need a key or sign-in with write access.
- Ids are public ids. A phone number, a mailbox or a WhatsApp account is also chosen by its public id.

## 2. The three ways a conversation starts

Know which one the user means. They are set up differently.

### A. Inbound: someone writes to the agent first

The agent answers on an account it owns. One account belongs to one agent at a time.

- **SMS:** a workspace phone number is assigned to the agent. A text to that number creates the conversation, stamps it to the agent and the agent replies. An agent has one SMS number: assigning another releases the first.
- **WhatsApp:** a connected WhatsApp account is assigned to the agent. A stranger who writes to it becomes a contact, tagged as a ghost lead so cold outreach counts leave it out. Group chats are ignored. WhatsApp can hide a sender's number; the contact then keeps WhatsApp's masked id and is reachable on WhatsApp only.
- **Email:** a connected mailbox can be assigned. Replies to an email the agent sent itself (its send email action) come back to the same agent. A first email from a stranger starting a conversation is behind a separate switch that is off unless Sendora turns it on, so do not promise it.

### B. A campaign step: the campaign texts first, the agent carries the replies

- An SMS or WhatsApp step can name a `chat_agent_id`. Leave the step's `message` empty and the chat agent writes the opening for each lead from its own instructions, one lead at a time, just before sending. Write a `message` and it is sent as written; the agent then only handles replies.
- Replies in a campaign follow the campaign's own reply setting, not the number's: `edit_campaign` `update_replies` with `mode` (`off`, `draft` or `auto`), `agent_id` (the chat agent, needed for draft and auto) and `note` (extra instructions for this campaign only; a line "SMS replies:" or "WhatsApp replies:" starts a part for that channel). One setting covers email, SMS and WhatsApp together.
- Which agent answers a reply is decided in this order: the campaign's reply agent, then the agent that owns the conversation (the number or account it came in on), then the workspace's oldest active agent. Set `agent_id` on the campaign so it is never left to the last one.
- While a campaign is running or paused and replies are `off`, a person answers. After it finishes, a reply is handled like any other inbound conversation.
- Inside a campaign the agent keeps its own personality and tools. Its instructions come first, then "For this campaign:" and the note.
- `read_campaigns` `prepare_outreach` (preflight) errors if a step names a chat agent that is not found, or the workspace has chat agents off. It warns if replies do not follow that agent: fix with `update_replies`.
- Campaign texts leave from the campaign's sending numbers (`update_channel_settings`), which is separate from the agent's own number. The agent's number is where inbound and chats it starts leave from.

### C. The agent starts a chat itself

- `send_outreach` `chat_conversation` with `agent_id`, `channel` (`sms` or `whatsapp`) and either `phone` (with country code) or `lead_id`. `opening` is the first message; left out, the agent writes it.
- `send_outreach` `chat_start_many`: up to 25 phones with `channel` and `opening` in one call, three seconds apart. A number that fails is reported with its reason and the rest carry on. It stops early only if the agent is missing or its sending limit is hit.
- A webhook link per agent: `read_conversations` `chat_config` with `include: "trigger"` shows the secret URL, and `edit_conversation` `chat_step` `rotate_trigger` makes a new one and kills the old. Another system POSTs `phone`, optional `channel`, `name` and `opening` to it.
- A chat never starts for: an opted-out or do-not-contact person, a contact with no phone, a time outside 8 AM to 9 PM for that contact (worked out from the phone number), a second start to the same person within a minute, an agent that is not switched on for the channel, a contact who is in a running or paused campaign (its replies follow that campaign), or when the agent hit its sending limit. Each returns a plain sentence and an error code (409 for most, 429 for a limit).

## 3. Set up an agent, in order

1. **Create.** `edit_conversation` `create_chat_agent` with `name`, `template`, `business` (a short line about what the company does) and `channels` (leave out for all three). Templates: `answer_questions` (answers from its knowledge, hands off when unsure) and `book_appointments` (also finds a time and books it). The template is only a first draft of the instructions. It starts switched off.
2. **Read it back.** `read_conversations` `get_chat_agent`. The public view never returns the instructions. Use `chat_config` for setup, including the instructions through the app screens.
3. **Instructions.** `update_chat_agent` with `name` and `system_prompt`. Write them with `sendora-chat-agents-business`.
4. **Knowledge.** The agent answers from the knowledge bases linked to it and says it does not know when nothing is found. Link them on the agent's Configure screen in the app: the MCP chat setup does not take `knowledge_base_ids`. Say this to the user instead of guessing.
5. **Channels and accounts.** `update_chat_config` with `chat.channels`, keyed by `sms`, `whatsapp` or `email`, each with:
   - `account_id`: for SMS the number's public id (`read_platform` `phone_numbers`); for WhatsApp or email the connection's public id (`read_channels` `accounts`). Null removes it. An account another agent answers on is refused with 409.
   - `mode`: `auto` replies by itself, `draft` writes a draft that waits in the inbox for a person. Defaults: SMS and WhatsApp `auto`, email `draft`.
6. **Details to collect, outcomes, rules, tool rules.** In `chat.settings`, send only what changes. A block replaces the whole of that key (see section 5).
7. **Actions.** `chat.actions` keyed by action id (section 4). `chat.custom_actions` takes `add`, `update` (keyed by id) and `remove`.
8. **Follow-ups.** `chat.followup` with `nudge_after_minutes` (0 turns it off, up to 1440) and `nudge_max` (1 to 3). It applies to every text channel of the agent.
9. **Test.** Section 7.
10. **Go live.** Ask the user first. `edit_conversation` `set_chat_agent_live` with `live: true`. It switches on every channel that has an account and lists the rest in `channels_missing`. Needs at least one connected channel (422 otherwise). Verify with `get_chat_agent`: `live` is true only when a channel is both enabled and connected. Turning off does not delete anything.

## 4. Actions an agent can be given

Set with `chat.actions`, for example `{"calendar": {"enabled": true, "config": {"calendar_id": "...", "duration_minutes": 30}}}`.

| Id | What it does | Notes |
|---|---|---|
| `calendar` | Offers times (`propose_meeting_times`), books (`book_meeting`), cancels (`cancel_meeting`) | Needs a connected calendar (sendora-workspace-technical). To move a meeting it cancels and books again. A booking is allowed only for a time the agent actually showed in a reply, and an offered time expires after 30 minutes |
| `contacts` | Reads the contact and pipeline always. A switch lets it update the contact, status, pipeline stage, snooze and enrich | Off by default |
| `email`, `sms` | Sends an email or text to the contact | Choose the sending mailbox or number with `from_id`. `fixed` plus `body` (and `subject` for email) sends exactly that text. Email can be `tracked` or `instant` |
| `whatsapp` | Sends a WhatsApp message to the contact | A switch. Opens from the agent's own WhatsApp account |
| `handoff` | Stops replying and tells the team | On by default |
| `team` | Notifies the team, or pauses the chat until a time and leaves a note | A switch |
| `resources` | Gives the customer a saved https link or file | Saved in `settings.resources`, up to 20 |
| `web` | Searches and reads public pages | A switch |
| `custom` | The owner's own HTTPS call: name, `http_url`, `http_method`, up to 12 parameters, optional `auth_tokens` | Name in lowercase letters, numbers and underscores, not one already used. Test with `chat_step` `test_custom_action` and `tool_id` |

Write tools (details, outcomes, hand off, team, contact updates and so on) are only offered when the channel is `auto`. In `draft` the agent can only write text.

### Per-tool rules and approvals

`settings.tool_rules`, keyed by tool group (`calendar`, `email`, `sms`, `whatsapp`, `contacts`, `team`, `resources`, `web` or a custom action name):

- `when`: plain words for when to use it.
- `channels`: where it may run (`sms`, `whatsapp`, `email`, `web`). Empty means everywhere.
- `approve`: a person approves each use first (calendar, email, sms and whatsapp only). The agent tells the customer someone will confirm and never claims it is done. Pending approvals appear in `read_conversations` `chat_config` with `include: "approvals"`. Decide with `chat_step` `approve` or `reject` and `approval_id`. Approve runs it once.
- `guidance`: how to write the message. `fixed_text`: send exactly this (WhatsApp).
- `web_without_approval`: calendar only, lets website visitors book and cancel without approval.

## 5. Settings the agent obeys

`chat.settings` keys. Each one replaces the whole key.

- `fields`: details to find out, each `key` (lowercase, underscores), `label`, `type` (`text`, `number`, `date`, `choice`, `yes_no`), `choices`, `required`, `hint`. The agent saves them on the contact as they come up. `first_name`, `last_name`, `email`, `phone`, `company` and `title` write the standard fields, any other key goes to custom fields. Up to 20.
- `outcomes`: how a chat can end, each `key`, `label`, `when` and `effects`: `tag`, `stop_campaign` (removes the contact from running campaigns and pauses automation), `notify`, `set_status` (`GREEN`, `YELLOW` or `RED`), `move_stage`, `hand_off`. Up to 12.
- `rules.hours`: `enabled`, `timezone`, `days` (0 Monday to 6 Sunday), `start`, `end`, `away_message`. Outside the hours the agent stays quiet, sends the away message once per conversation per day, and does not queue an answer. A person has to pick the thread up.
- `rules.handoff`: `words` (whole words), `topics` (match anywhere), `message`, `notify`. When one matches, a person is told, the agent steps back and the customer gets the message. This runs in code before the model, so it holds however the instructions are written.
- `allow_only`: test mode. A list of phone numbers: the agent talks to those and no one else, and starting a chat to anyone else is refused.
- `widget`: the website chat window (`enabled`, `title`, `greeting`, `color`, `contact_mode`, `allowed_sites`). The window runs from Sendora's own address. By default it cannot change data; `tools` lets it use allowed tools, each needing approval.
- `skill_ids`: workspace skills the agent may use.

## 6. What the agent does by itself

- **Several quick messages get one answer.** It waits a few seconds, and if a newer message arrived the newer one answers.
- **SMS and WhatsApp replies are split** into up to three short texts at blank lines, with a typing pause. Email is one message. No em or en dashes ever reach a customer.
- **Quiet customer:** with follow-ups on, a sweep every minute checks conversations where the agent spoke last in the past 24 hours. The gap grows (N, then 2N, then 3N minutes). It stops at `nudge_max`, after any reply, an opt-out, a human takeover, a handoff, the safety limit, or outside decent hours. It sends a short check-in written from the thread, or nothing if it decides a nudge would not help. Replies to something the customer just wrote are never held for quiet hours, only messages the agent starts.
- **Safety limits:** by default at most 12 messages from the agent per conversation per hour, a flood stop at 30 inbound per hour in one conversation, and an optional daily cap per agent. Sendora sets these server side and may change them. A hit is recorded in `activity` and the reply is not sent.
- **Silent turn:** if the model returns nothing, the customer gets a short holding line and a person is told.
- **A person replying in the inbox switches the AI off** for that contact and channel. A handoff by the agent does the same. Turn it back on with `edit_conversation` `set_traffic_light` for that lead.
- **STOP and opt-outs** go on the do-not-contact list. The agent never starts or restarts a chat with them.
- **No claims without a tool:** the agent is told never to say it booked, cancelled, sent or changed something unless a tool did it in that reply.

## 7. Test before real people

1. `edit_conversation` `chat_step` `test` with `channel` and `messages` (up to 24, each `{role, content}`). It answers from the real instructions, knowledge and channel. It sends nothing.
2. `chat_step` `replay` with `conversation_id` runs a past conversation against the current instructions. Use it before editing a live agent.
3. `chat_step` `suggest_rules` with `text` proposes `fields`, `outcomes` and rules from a plain sentence. Review before saving.
4. For a real send to a real number, set `allow_only` to the test number first.
5. Read what happened: `read_conversations` `chat_config` with `include` of `history`, `activity` (replies, nudges, handoffs, outcomes, limits hit, approvals), `conversations` or `transcript` (needs `conversation_id`). Each turn also records the tools it used and any checks that sent the model back.

## 8. Troubleshooting

| Symptom | Likely cause |
|---|---|
| Every chat agent tool says Not found | Chat agents are not switched on for this workspace |
| Texts to the number get no reply | Agent not live, channel not enabled, no number assigned, mode is `draft`, AI switched off for that contact, outside `rules.hours`, `allow_only` set, opted out, or a safety limit |
| A campaign reply is answered by the wrong agent or by nobody | `update_replies` not set to the chat agent, or set to `off` while the campaign is live |
| Preflight says replies do not follow the agent | `update_replies` with this agent's id |
| Start refused with 409 | Opt-out, outside 8 AM to 9 PM for the contact, agent not on for the channel, contact in a running campaign, or a duplicate within a minute |
| Live fails with 422 or 409 | No channel has an account assigned, or the account belongs to another agent |
| Agent offers times it cannot see | Calendar action is off or not connected. It should offer a teammate to confirm |
| Email stays unsent | Email is `draft` by default: approve the draft in the inbox, or set `auto` |
| Nudges never go out | `nudge_after_minutes` is 0, the customer replied last, it is outside decent hours, or the max was reached |

## 9. Verify, never assume

After any change read it back with `get_chat_agent` and `chat_config`. After going live, `chat_step` `test` once more and watch the first real conversations in `activity`. Say what was and was not checked.
