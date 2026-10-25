---
name: sendora-inbox-business
description: "Business guide to handling replies and conversations: triage order, response templates for every reply type, meeting booking, objection handling in writing, when to hand to a human, chat agent behaviour on SMS and WhatsApp, response time targets, and pipeline hygiene."
---

# Replies and conversations playbook

Tools are in `sendora-inbox-technical`.

## 1. Targets

| Reply type | Respond within |
|---|---|
| Positive or "interested" | 15 minutes in working hours, 1 hour max |
| Question | 1 hour |
| Objection | 2 hours |
| Not now | 4 hours |
| Out of office | no reply, snooze |
| Stop | immediate, no reply, add to DNC |

Speed on positive replies is the biggest lever you control.

## 2. Triage order

`needs_attention` first. Then unread conversations, newest positive first. Clear approvals and escalations. Then everything else.

## 3. Classify

Interested, question, objection, not now, wrong person, out of office, unsubscribe, hostile, automated. When unsure between interested and question, treat it as interested.

## 4. Response templates (adapt, do not copy blindly)

**Interested**
> Great, thanks {{first_name}}. Does <day> at <time> or <day> at <time> work for 20 minutes? If another time suits, tell me and I'll make it work.

**Question about pricing**
> Fair question. It depends on <one factor>. For <their size> teams it's usually <range if known and allowed>. Happy to walk through the details in 15 minutes, would <day> work?

**Objection: we already use someone**
> Makes sense, most teams do. We tend to be used alongside because <one reason>. Worth a short look at how it compares, or should I check back in a quarter?

**Objection: no budget**
> Understood. If it's useful, I can send a one page summary so it's ready when budget opens. When should I check back?

**Objection: bad timing**
> No problem. I'll leave it. Is next quarter a better time to reconnect?

**Not interested**
> Thanks for letting me know {{first_name}}. I'll close this out. If that changes, you know where I am.

**Wrong person**
> Thanks. Who would be the right person to speak to about <topic>? Happy to reach out directly.

**Referral to a colleague**
> Thank you, I'll reach out to <name>. Would you be happy for me to mention your name?

**Request for information**
> Sure. Here's a short summary: <two lines>. Would it help to see it on a quick call, or should I send examples first?

## 5. Writing guidelines

Match their length and formality. One ask per message. No walls of text. Answer the question they asked first. It is better not to promise what the knowledge base does not support. Keep the thread. Sign with a real sender name.

## 6. Booking meetings

Offer two specific times in their time zone. Confirm by restating the time, the length, and who is joining. Record it (`create_meeting`), move the pipeline stage, and send the invite from your calendar. Send a reminder a day before. Offer to rebook if they miss it.

## 7. Chat agents (SMS and WhatsApp)

Best for fast, short exchanges: confirming interest, answering simple questions, booking. Keep replies under two short sentences on SMS. It helps to stop on "stop". Hand to a person for pricing exceptions, complaints, legal, and anything the agent is unsure about. Review the first 50 conversations closely; use `replay` against new instructions before changing them live.

## 8. Escalation guidelines

Escalate: a buying signal with a large account, an angry reply, a legal or compliance question, a technical question the knowledge base cannot answer, anything involving money or contracts.

## 9. Pipeline hygiene

After every meaningful reply, update the lead's stage. Snooze with a date rather than leaving unresolved. Close lost with a reason. Review the pipeline weekly: stale replied leads get a nudge or are closed.

## 10. Learning from replies

Each week, read ten positive and ten negative replies. Note the words people use. Feed objections back into the voice prompt, chat agent instructions and campaign copy.
