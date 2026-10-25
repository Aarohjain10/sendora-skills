---
name: sendora-inbox-technical
description: "Technical guide to replies and conversations via the MCP: the inbox, sending messages, chat agents for SMS and WhatsApp, approvals, escalations, tasks, meetings."
---

# Inbox, chat agents, work items: technical guide

Strategy for answering replies is in `sendora-inbox-business`.

## 1. Conversations and the inbox

### Read

- `read_conversations` `list` (filter by channel, status, campaign, unread) and `get` for a full thread. A conversation is keyed by the lead.
- `needs_attention` lists everything waiting on a person, most urgent first.

### Act on a conversation

`edit_conversation` `conversation_step` on a lead, with `action`:

| Action | Use |
|---|---|
| `mark_read` | Handled |
| `discard_draft` | Throw away an AI draft |
| `archive` / `unarchive` | Clear or restore |
| `snooze` (with `snoozed_until`) / `unsnooze` | Come back later |
| `pause_outreach` / `resume_outreach` | Stop or restart sequences for this lead |

`set_traffic_light` sets who is in control of the thread (AI or human).

### Send

- `send_outreach` `message` sends a text channel message.
- `send_outreach` `email` sends an email.

Both reach a real person. Show the user the exact text, recipient and channel, and wait for a yes. Prefer not to send from a campaign's sending account in a way that breaks its pacing without saying so.

## 2. Chat agents

A chat agent answers messages for a lead on text channels. It starts switched off.

### Create

`edit_conversation` `create_chat_agent` with `name`, `template`, `business` (what the company does) and `channels`. Read back with `read_conversations` `get_chat_agent`, `chat_config`.

### Configure

`edit_conversation` `update_chat_config` changes only what you send, in `chat`:

- `channels`: which accounts and numbers it replies on
- `followup`: how and when it nudges quiet leads
- `settings`: tone, length, working hours
- `actions`: which built-in actions are on (book a meeting, update lead state, escalate)
- `custom_actions`: `add`, `update` (keyed by id) or `remove` calls to the customer's own URL

`update_chat_agent` changes the name and the instructions (`system_prompt`).

### Test

`edit_conversation` `chat_step` with `action: "test"` or `replay` (re-run a past conversation against the new instructions). `suggest_rules` proposes guardrails from past threads. `test_custom_action` checks a custom action.

### Go live

Ask first. Then `edit_conversation` `set_chat_agent_live`. Verify with `get_chat_agent`.

Start a conversation: `send_outreach` `chat_conversation` for one lead, or `chat_start_many` for up to 25 real chats (needs channel, phones and an opening). Both contact real people. Confirm the list.

### Approvals

If the agent needs a person to approve certain replies, review with `read_conversations` `approvals` and decide with `edit_conversation` `decide_approval` (sendora-inbox-technical).

## 3. Approvals, escalations, tasks

Campaign steps and agents raise items for a human. They block until decided.

### Read

`read_conversations`:

- `approvals` (status pending, approved, rejected)
- `escalations` (status)
- `tasks` (status, lead)
- `needs_attention` for all of them together

### Decide

`edit_conversation`:

- `decide_approval` with `decision: approved` or `rejected`. Show the user what is being approved (the draft message or action) before deciding. It is better not to approve on the user's behalf without a yes unless they set a standing rule.
- `update_escalation` with a `status` (acknowledge, resolve, reopen). Resolving needs `resolution_type`: `retry` (try again), `skip` (leave it out) or `manual` (a person handled it).
- `update_task` with `status`: open, in_progress, completed, cancelled.

## 4. Meetings

### Read

`read_conversations` `meetings` with `start` and `end` ISO times. `include_cancelled` shows cancelled ones.

### Record

`edit_conversation` `create_meeting` with `prospect_name`, `scheduled_at` and optional details. It records the meeting. **It sends no invite.** Tell the user to send one, or use the calendar connection if the workspace has one (sendora-workspace-technical).

### Change

`update_meeting` on a manual meeting, or set `status` to confirmed, rescheduled or cancelled. `delete_resource` `meeting` cancels a manual record.
