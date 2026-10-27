---
name: sendora-chat-agents-business
description: "Business guide to chat agents: which job suits one, how to write instructions and an opening, qualifying and booking over text, follow-up cadence, when to hand to a person, outcomes worth recording, how to roll one out safely, and what to review each week."
---

# Chat agents playbook

How to build one is in `sendora-chat-agents-technical`. Replies in general are in `sendora-inbox-business`. These are guidance, not strict rules: adapt to the business.

## 1. Pick the job first

A chat agent is good at short written exchanges. Decide which of these it does before writing anything:

| Job | Starts how | Good for |
|---|---|---|
| Answer inbound | Someone texts or messages a number the business already publishes | Support questions, opening hours, pricing basics, quote requests |
| Follow up a campaign | A campaign opens the chat, the agent carries the replies | Qualifying and booking after cold outreach |
| Start a chat | A form, a CRM stage change, or a person starts it | Speed to lead, reminders, win back |

Do not use it for anything that needs judgement about money, contracts, complaints or legal questions. Hand those to a person.

## 2. Writing the instructions

The instructions are the agent's whole personality. The template gives a first draft; rewrite it for the business. Cover:

1. **Who it is and who it speaks for**, in one or two lines. It is an AI assistant for the company, and the first message should not pretend otherwise if the customer asks.
2. **What it knows.** Only what is in the instructions, the knowledge base and its tools. Tell it to say so when it does not know, and never to invent prices, availability or promises.
3. **What the goal is.** One main goal: answer, qualify, or book. Two at most.
4. **Tone and length.** Plain words, the way a thoughtful person texts. On SMS, one or two short sentences. Blank lines between thoughts send separate texts, so use them sparingly.
5. **The two or three things to find out** (see section 5) and the order that feels natural.
6. **When to bring in a person**, with examples.
7. **What not to do:** no pressure, no repeated pitches, stop when asked.

Keep it short and specific. A page of instructions beats three. Put business facts in the knowledge base, not in the prompt, so they are easy to change.

## 3. The opening message

When the agent or a campaign starts the chat, the first text decides everything.

- Name the person, say who it is from, and give one specific reason to reply.
- Ask one easy question, not two.
- No links in the first text unless the person asked for one.
- No mention of open times unless the agent can actually see a calendar. If it cannot, it should offer to have a teammate confirm a time.
- Check the contact's local time. The platform will refuse a chat outside 8 AM to 9 PM for them; do not try to schedule around it.

Let the agent write the opening when personalisation matters and the list is small. Write the opening yourself when you want exact wording for every lead, for example a compliance line.

## 4. Follow-ups

- A first nudge after 1 to 4 hours for sales, longer for support. Then roughly double. The maximum is three, and two is usually enough.
- A nudge should add something: a question, a useful detail, an easy way out. "Just checking in" repeated is how people stop answering.
- Anyone who has replied stops getting nudges. So does anyone who asked to stop or whose chat was handed to a person.
- Evenings and nights are held back automatically for SMS and WhatsApp, so a nudge may go out later than the number you set.

## 5. Qualifying without an interrogation

Ask for the details that decide the next step, as the conversation allows, one at a time. Typical set: what they need, when, a rough size, and the best email. Save each as a field so a person sees it on the contact. Required fields are a hint, not a gate: do not make the customer feel like they are filling in a form.

## 6. Booking meetings

Give two specific times, with the time zone, and book only a time the customer picked. Restate the time, the length and who is joining. Turn on calendar approval while the agent is new if a wrong booking would be costly: a person confirms each one. If no calendar is connected, say a teammate will confirm, and make that a handoff or an outcome so someone sees it.

## 7. When to hand to a person

Set handoff words and topics for the obvious ones: "refund", "cancel", "lawyer", "complaint", "speak to a human". These work in code before the AI is asked, so they always fire. Also hand off when the customer is angry, when they ask for something the agent cannot do, or when the answer involves money or a contract. After a handoff the AI stays off for that person until someone switches it back on, so a teammate must own the thread.

## 8. Outcomes worth recording

Define a small set, each with what should follow:

| Outcome | Typical follow-up |
|---|---|
| Booked | Tag, move the pipeline stage, notify the team, stop the campaign for this contact |
| Interested, needs a person | Notify, hand off |
| Not now | Tag, set status, stop the campaign |
| Not interested or asked to stop | Tag, stop the campaign (opt-outs are also handled by the platform) |
| Wrong person | Tag, stop |

Fewer, clearer outcomes are recorded more reliably.

## 9. Reply mode while it is new

Start in draft: a person approves each reply for the first dozen to fifty conversations. Read the drafts, fix the instructions, then switch to auto for SMS and WhatsApp. Email stays in draft longer by design, since an email is harder to take back.

## 10. Rolling out safely

1. Test in the agent's test chat, then replay a few real past conversations against the new instructions.
2. Restrict it to your own test number first.
3. Go live on one channel with a small group.
4. Read every conversation for the first few days: what it said, what it saved, what it handed off.
5. Widen only when it holds up.

## 11. What to review each week

- Handoffs and why, and what was asked that the agent could not answer. Move those answers into the knowledge base.
- Messages that ended the chat (people who went quiet after a specific line).
- Outcomes recorded against what a person would have chosen.
- Limits hit, and any approvals left pending too long.
- Opt-outs. A rise means the opening or the cadence is too pushy.

## 12. Compliance in short

Texting needs consent. The platform stops at opt-outs, keeps to calling hours, and holds nudges at night, but it cannot know whether you had permission in the first place. Confirm consent for any list before an agent texts it (sendora-foundation-technical covers the rules for the workspace).
