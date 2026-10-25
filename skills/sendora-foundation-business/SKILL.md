---
name: sendora-foundation-business
description: End to end go to market playbook on Sendora: the six phase plan and seven motion recipes (cold email, AI calling, LinkedIn, inbound response, appointment setting, reactivation, agency client launch) that point to the right technical guides and playbooks.
---

# GTM playbook

Use when the user says "set me up to sell X to Y" and has not specified the steps. This skill sequences the others.

### 0. Frame it (ask only what you cannot infer)

Capture in one short message: who the buyer is (title, industry, size, region), the offer in one sentence, the call to action (book a meeting, reply, visit), channels allowed, and volume per day. If something is missing, choose a safe default and state it.

### 1. Infrastructure (skill: sendora-foundation-technical)

Email needs a domain with healthy DNS and warmed mailboxes. Voice needs a number and a published agent. SMS needs approved registration. LinkedIn and WhatsApp need a connected account. Do not plan a channel the workspace cannot send on. Tell the user the lead time: warmup and carrier review take days.

### 2. Audience (skills: sendora-leads-technical, sendora-leads-technical, sendora-leads-technical)

Create a lead list named for the motion. Fill it by prospect search, Google Maps search or import. Preview before committing anything. Enrich emails and phones after import (sendora-leads-technical). Check suppression (sendora-foundation-technical).

### 3. Agents (skills: sendora-voice-technical, sendora-inbox-technical, sendora-voice-technical)

Create the voice agent if calls are in the plan, give it a knowledge base about the offer, and test it with a test call before any real lead is touched. Create a chat agent if replies on SMS or WhatsApp should be handled by AI.

### 4. Campaign (skills: sendora-campaigns-technical, sendora-campaigns-business)

Build the journey as a draft: trigger, first touch, waits, follow-ups on other channels, branches on replies, a human approval step where a person should check, an escalation for hot replies. Enrol a tiny test list first.

### 5. Test and launch (skill: sendora-campaigns-technical)

Run preflight. Send to the user's own address or phone first. Get an explicit yes. Launch with a small batch. Watch the first 24 hours.

### 6. Run (skills: sendora-inbox-technical, sendora-campaigns-technical)

Work the inbox daily, review analytics weekly, rewrite the weakest step, and move winners into a template.

### Principle

Small first, then scale. One list of 20 leads, one campaign, one channel working end to end beats five channels half built.

# Motion recipes

## Cold email campaign

Time: 3 to 4 weeks including warmup. Use these skills in order.

1. **Infrastructure** (sendora-channels-technical, sendora-channels-technical): a separate sending domain, healthy DNS, two to five mailboxes warming. Day 0.
2. **Audience** (sendora-leads-technical, sendora-leads-technical): 500 to 2,000 verified leads of one persona in a named list. Verify emails. Check DNC.
3. **Copy** (sendora-campaigns-business): four emails, plain text, one ask, friendly close.
4. **Build** (sendora-campaigns-technical): trigger, email 1, wait 3 days, email 2, wait 4, email 3, wait 5, email 4. Condition on reply at each wait. A task or escalation node for positive replies. Sender pool across mailboxes.
5. **Test** : enrol two internal addresses, read all four emails on a phone. Run a placement test (sendora-channels-business).
6. **Launch small** (sendora-campaigns-technical): 30 to 50 leads, 20 per mailbox per day at most.
7. **Work replies** (sendora-inbox-technical) every day.
8. **Review at day 7 and 14** (sendora-campaigns-technical). Replace the weakest email. Raise volume 25 percent a day if bounces stay low.

Success looks like: bounces under 2 percent, replies 3 percent or more, at least one meeting per 100 contacts.

## AI cold calling

1. **Number and caller ID** (sendora-voice-technical, sendora-voice-technical). Local presence helps. Check regulatory needs.
2. **Agent** (sendora-voice-technical, sendora-voice-business): opening with permission, one reason, two qualifiers, a concrete ask. Voicemail script.
3. **Knowledge and tools** (sendora-voice-technical, sendora-voice-technical): offer facts, propose meeting times, update lead state, escalate to human.
4. **Test** (sendora-voice-technical): ten browser calls, then a phone test to the user's own mobile.
5. **Audience** : leads with verified phones, in their local calling hours, DNC clean.
6. **Campaign** (sendora-campaigns-technical): trigger, `voice_call`, wait 1 day, `voice_call`, then `send_sms` or email for no answers, `escalation` for interested, `update_pipeline` for outcomes.
7. **Launch** with 20 leads, review all calls, then scale (sendora-voice-technical).

Watch for: quiet hours, answer rate, short calls under 10 seconds (opening problem), and the "are you a robot" reply (the agent must be honest).

## LinkedIn outreach

1. **Account.** The user connects their own LinkedIn account in the app. Check it is healthy (`read_channels` `accounts` with `include: "health,limits"`). Keep default limits. Never push them.
2. **Audience.** A list with LinkedIn URLs. Small and relevant beats large.
3. **Sequence** (sendora-campaigns-technical): `linkedin_connection` with a short note or none, wait 2 days after accept, `linkedin_message` that asks a question, wait 4 days, a second message with value, then an email touch if they have one.
4. **Copy** (sendora-campaigns-business): no pitch in the connection note.
5. **Pace.** 15 to 25 invites a day at most, spread through working hours.
6. **Watch.** Any account warning means pause the campaign at once.

Pull profile data with the lead's own fields. Do not scrape LinkedIn through other tools. Keep acceptance rate above 30 percent or change the target.

## Inbound lead response

Speed to lead wins. Target: contact within five minutes.

1. **Capture.** Leads arrive by form, widget or inbound call (sendora-voice-technical, sendora-voice-technical, sendora-workspace-technical for forms).
2. **Instant touch.** A campaign with trigger on new lead, then `send_sms` or email within a minute, and a `voice_call` after five minutes if no reply.
3. **Chat agent** (sendora-inbox-technical) handles the reply: confirm need, ask two qualifiers, offer meeting times.
4. **Voice agent** calls if they answer the phone call.
5. **Booking.** `propose_meeting_times` tool, record with `create_meeting`, move the pipeline stage.
6. **Escalate** to a person for large or unusual requests.
7. **After hours** behaviour set in inbound config.

Measure: median time to first touch, contact rate, booking rate.

## Appointment setting

1. Audience and infrastructure as in the cold email recipe in sendora-foundation-business.
2. Campaign with two goals only: a reply or a booked time.
3. Every positive reply goes to a `human_approval` or a chat agent that offers two specific times.
4. When a time is agreed: `create_meeting`, `set_pipeline_stage` meeting, send the invite from the calendar.
5. A reminder touch a day before (`send_sms` or email).
6. No show handling: a follow up message offering to rebook within an hour.
7. Report weekly: replies, meetings booked, held, no shows, by step.

Keep a short, friendly voice. The ask is always a specific time.

## Reactivation

1. **Pull** old leads: `read_leads` `list` filtered by last activity, stage lost or no reply, or a saved segment (sendora-leads-technical).
2. **Clean.** Re-verify emails and remove DNC (sendora-leads-technical, sendora-foundation-technical). Old data bounces.
3. **Angle.** Something genuinely new: a launch, a price change, a result. Never "just checking in".
4. **Sequence.** Two or three touches over ten days, shorter than a cold sequence. Mention the earlier conversation honestly.
5. **Warm channel first.** Email, then a call to anyone who engaged.
6. **Pace** gently. These addresses are risky for reputation.
7. **Report** reactivated count, meetings, and update the pipeline stages.

## Launching an agency client

1. **Create the client** and set package, limits, branding (sendora-agency-technical, sendora-agency-technical, sendora-agency-technical).
2. **Invite** the client contact. Give them the right portal mode.
3. **Kickoff questions:** ICP, offer, proof, volume, channels, compliance needs, approval preference.
4. **Infrastructure** inside the client workspace: domain, mailboxes, numbers, registration (sendora-foundation-technical). Start warmup day 1.
5. **Knowledge and agents:** knowledge base from their site and case studies, a voice or chat agent (sendora-voice-technical, sendora-voice-technical).
6. **First campaign** as a draft, reviewed with the client (sendora-campaigns-technical). Put a `human_approval` step in until they trust it.
7. **Launch small**, report after 7 days.
8. **Rhythm:** weekly report (sendora-campaigns-technical), monthly review and a template saved from what works.

Always work inside the right client workspace. State the client name before any write.

