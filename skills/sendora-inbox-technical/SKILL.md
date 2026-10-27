---
name: sendora-inbox-technical
description: "Technical guide to replies and conversations via the MCP: the inbox, sending messages, approvals, escalations, tasks, meetings."
---

# Inbox and work items: technical guide

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

Everything about chat agents (creating one, the three ways a conversation starts, actions, follow-ups, limits, testing and going live) is in `sendora-chat-agents-technical`. When a chat agent runs in `draft` mode its replies wait here as drafts: read them with `read_conversations` `get` and send or discard them (`discard_draft`). Tool approvals a chat agent is waiting on are listed in its `chat_config` `approvals`.

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
