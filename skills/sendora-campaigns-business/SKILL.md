---
name: sendora-campaigns-business
description: "Business and sales guide to designing campaigns that get replies: how to reach people across email, calls, LinkedIn, SMS and WhatsApp, ready sequences by scenario with timings, branching rules, how many A/B tests to run and how to read them, volume and pacing, reply handling, and a weekly improvement loop."
---

# Campaigns: sequence and outreach playbook

Tool usage is in `sendora-campaigns-technical`. This guide is what to build and why.

## 1. Principles

1. **Relevance beats volume.** A sequence to 200 well chosen people beats 5,000 poorly chosen. Fix the list before the copy.
2. **One idea per touch.** Each message adds one new reason to reply. It is better not to repeat "just following up".
3. **Multichannel beats single channel.** People respond on different channels. A call after an email open, a LinkedIn touch between emails, lifts replies, as long as each touch is respectful.
4. **Leave on reply.** The moment someone replies, they leave the sequence and a person or a chat agent takes over.
5. **Small first, then scale.** First run is a test: 30 to 100 leads.
6. **Protect the sender.** Domains and numbers are assets. Pace, verify, and pause at the first warning.

## 2. Pick the motion

| Situation | Sequence to use |
|---|---|
| Cold list, email only | Section 3A |
| Cold list with phones, you want meetings fast | 3B (call and email) |
| Senior buyers, active on LinkedIn | 3C |
| High value accounts, account based | 3D |
| Warm: engaged, replied before, intent signal | 3E |
| Inbound lead (form, widget) | 3F, first touch under 5 minutes |
| Old or lost leads | 3G |
| Local business owners | 3H |
| Event or webinar follow up | 3I |

## 3. Sequences

Day numbers are from the first touch. "Wait" is a `wait` node. Every wait is followed by a reply check. Send only in the lead's working hours, on working days.

### 3A. Cold email (21 days, 4 emails)
- Day 0: Email 1, short, about them, one question.
- Day 3: Email 2, a different angle, same thread.
- Day 7: Email 3, proof (one named result), same thread.
- Day 12: Email 4, a useful resource or insight, new thread.
- Day 18: Email 5, a polite close ("should I stop reaching out?").
Expected: positive replies 0.5 to 2 percent of contacts. If under 0.3 percent after 200 sends, fix list or offer, not the sequence.

### 3B. Call and email (14 days)
- Day 0: Email 1.
- Day 1: Call 1 (voice agent). If no answer, voicemail.
- Day 3: Email 2.
- Day 4: Call 2 at a different time of day.
- Day 7: SMS only if registered and consented, otherwise Email 3.
- Day 10: Call 3.
- Day 14: Close email.
Branch: if a call connects and the lead says "send me details", send a one-line email at once, then stop calling.

### 3C. LinkedIn plus email (18 days)
- Day 0: Profile view or connection request (short note or none).
- Day 2 after accept: message that asks a question about their work. No pitch.
- Day 5: Email 1 (if you have the address).
- Day 8: LinkedIn message with value (a short insight).
- Day 12: Email 2.
- Day 18: Close.
Keep to the account's safe daily limits.

### 3D. Account based (30 days, 3 to 5 contacts per account)
Stagger people across a company by two to three days. Different angle per role (economic buyer: outcome; user: how it helps daily). Prefer not to contact more than three people in the same company in the same week. Add a human approval step for every first touch.

### 3E. Warm intent (10 days)
- Day 0: Reference the signal in the first line ("saw your comment on ..."). One question.
- Day 2: Call or LinkedIn.
- Day 5: Email with proof.
- Day 10: Close.
Response rates here are several times higher. Move fast.

### 3F. Inbound lead (same day)
- Minute 0: SMS or email acknowledging the request and offering two times.
- Minute 5: Call if a phone exists and they have not replied.
- Hour 4: Second email.
- Day 1: Call again.
- Day 3: Close.

### 3G. Reactivation (10 days)
New reason to talk, honest tone. Email, then call, then email. See `sendora-foundation-business`.

### 3H. Local business owners
Call first (owners answer), then SMS (if consent) or email. Keep it very short. Best times: before opening or mid afternoon.

### 3I. Event follow up
Day 0 (within 24 hours): thanks plus one takeaway. Day 2: relevant resource. Day 5: ask for a call. Day 10: close.

## 4. Branching that pays for itself

Build only branches you will use. Good ones:

- **Reply received:** leave the sequence. Positive reply: create a task or escalate. Negative: DNC or archive. Out of office: snooze to return date.
- **Opened 3+ times, no reply:** add a call or LinkedIn touch (treat opens as soft; they can be machines).
- **Clicked a link:** call within a day.
- **Email bounced:** drop the email touches, try phone or LinkedIn if available.
- **Call connected, no booking:** send the follow up email that matches what they said.
- **Replied "not now":** wait 60 to 90 days, then a fresh touch.
- **Voicemail left:** next touch within a day references it.
- **Lead has no phone:** skip the call step (condition on phone not empty).
- **Lead has no email:** skip email steps.

Avoid: more than three levels of nesting, branches nobody reads, and AI decisions on high stakes steps without a human approval node.

## 5. A/B testing: how many and how

You almost never need more than **one test at a time per step**. Small lists cannot support many tests.

Rules of thumb:
- Test **one variable**: subject line, opening line, offer, call to action, or send time. It is better not to two at once.
- Minimum sample: about **150 to 200 sends per variant** before drawing a conclusion for reply rate; **300 or more** for small differences. With less, you are reading noise.
- **Two variants** (control plus one challenger) is enough. Three only on lists above 1,000.
- Run each test for at least **one full week** to cover weekday effects.
- Judge on **positive replies**, not opens (opens are unreliable). For calls, judge on meetings per connect.
- A winner should beat control by a clear margin (about 30 percent relative) and by at least a handful of replies. Otherwise keep the control.
- After a win, promote the winner to control and test the next variable.

Test order that pays most: **audience first**, then **offer**, then **subject line and opening**, then **length**, then **call to action**, then **timing**.

Setup: add the challenger as a second variant of the message with `update_step` (keep the existing variant and its `variant_id` as the baseline), leave `distribution` off for an even split, and read results with `email_variants`. See `sendora-campaigns-technical`.

## 6. Volume and pacing

- New mailboxes: warm first. Start at 10 to 20 sends a day per mailbox, raise by about 25 percent every few days while bounces stay under 2 percent.
- Steady state: 30 to 50 cold emails per mailbox per day is a common ceiling. Spread across several mailboxes and a sender pool.
- LinkedIn: stay at or below the account's safe defaults.
- Calls: cap by connect rate and your ability to handle inbound callbacks.
- Pause at: bounces over 3 percent, any spam complaint, a sudden drop in replies.

## 7. Writing messages

Use `sendora-campaigns-business` principles (inside this section): short, specific, true, one ask. First line about them. No attachments or images in cold email. Plain text. Unsubscribe is automatic.

Subject line examples: "question about {{company}} onboarding", "{{first_name}}, quick one on hiring", "idea for {{company}}". Avoid all caps, emojis, "RE:", and fake urgency.

Follow up structure: new angle each time, with a reason (a result, a question, a resource), not "bumping this".

## 8. Replies: how to respond fast

- Positive: reply within the hour with two specific times. Move the lead's stage.
- Question: answer in two sentences, then ask.
- Objection: acknowledge, one line of value, a smaller ask.
- Not now: ask when to come back, snooze.
- Wrong person: ask who is right, thank them.
- Stop: DNC immediately.
See `sendora-inbox-business`.

## 9. Weekly improvement loop

1. Pull analytics (`get_analytics` `campaign`).
2. Find the weakest step (lowest reply or highest drop).
3. Form one hypothesis ("subject is vague").
4. Run one test on that step.
5. Review next week. Keep the winner.
6. Every month, clone the best campaign as a template and retire the worst.

## 10. Pre-launch checklist

- List fit checked, emails verified, DNC applied.
- Sender infrastructure healthy and warm.
- Messages read on a phone, variables have fallbacks.
- Test enrolment passed on every branch.
- Preflight clean, small batch chosen, user said launch.
