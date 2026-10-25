---
name: sendora-analytics-technical
description: Technical guide to reading performance through the MCP: every analytics report and its parameters, A/B results per email step and per step variant, call logs, deliverability and mailbox health, AI spend, activity and action queues, with ids, windows and limits.
---

# Analytics: technical guide

How to pull each report. For what the numbers mean and what to do about them, read `sendora-analytics-business`.

## Summaries

- `get_analytics` `overview`: workspace totals (contacted, replies, meetings, calls, channel mix). Takes `days` (1 to 365).
- `get_analytics` `campaign` with `campaign_id`: one campaign in detail.

## Reports (one action each, all on `get_analytics`)

All take `days`, or `start_date` and `end_date` (inclusive, YYYY-MM-DD). Ids in and out are public ids.

| Action | Needs | What it returns |
|---|---|---|
| `funnel` | `campaign_id` | Sent, delivered, opened, clicked, replied, meeting booked, with percent of the top step |
| `outcomes` | `campaign_id`, `outcome_type` call or reply | How calls or replies ended, with counts |
| `email_variants` | `campaign_id` | **A/B result of each email step**: sends, opens, replies per variant |
| `top_campaigns` | `metric` meetings, sends, replies or connect_rate; `window` 24h, 7d, 30d or 90d; `limit` up to 50 | Ranked campaigns |
| `calls` | optional `agent_id`, `limit` up to 200, `offset` | Call log: agent, duration, outcome, recording reference |
| `agents` | none | Calls, meetings, connect rate and average length per voice agent (every agent, so the answer can be long) |
| `meetings` | none | Meetings per day and the latest and upcoming ones |
| `geo` | `metric` leads, sends, replies or meetings | Where they come from, by country |
| `deliverability` | `window` 24h, 7d or 30d | Bounce rate with its band (ok, warn, alert) and DNS health of sending domains |
| `mailboxes` | `limit` | Each mailbox: warmup percent, sent today, cap today, health |
| `accounts_health` | `limit` up to 100 | Health of connected sending accounts (empty means none flagged) |
| `send_cap` | none | Sent today and in the period against the daily cap |
| `ai_spend` | `dimension` model or feature; `window` | Cost by model or by feature |
| `ai_decisions` | `window`, optional `campaign_id` | Decisions the campaign AI made: totals, cached, failed, latency, verdicts |
| `activity_feed` | `limit` | Recent sends, replies and bookings |
| `action_queue` | `limit` up to 100 | What needs attention now, most urgent first |

A wrong option value is a 422 that lists the allowed ones. A report that needs a campaign and has none is a 422.

## A/B results, in two places

- **Across a campaign:** `email_variants` (above). Empty until the campaign has sent.
- **One step:** `read_campaigns` `variants` with the campaign `id` and the step's `node_id`. Each variant: `variant_key`, `weight`, `is_control`, copy in `config`, `assignments` (leads given this variant) and `outcomes` (counts by outcome type).

How variants are edited is in `sendora-campaigns-technical` (A/B testing is per step, inside the workflow, edited with `update_step`).

## Other reads that feed reports

- `read_voice` `list_calls` and `get_call` for one agent's calls and a transcript.
- `read_conversations` `meetings` for booked meetings with details.
- `read_platform` `billing` for credit use (see `sendora-workspace-technical`).

## Habits

- Use the smallest window that answers the question. Always state the window in the answer.
- Page long lists with `limit` and `offset` instead of pulling everything.
- Zero is a real answer. A report that returns empty usually means nothing happened in the window, not an error.
- If a call fails with 503 or a timeout, say the report is unavailable and show the last number you have with its time. Never fill in a guess.
