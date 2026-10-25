---
name: sendora-voice-business
description: "Business guide to writing voice agent prompts that sound human and convert: structure, a full outbound and inbound prompt template, objection handling, voicemail, opening lines by call type, style rules, compliance lines, knowledge versus prompt, and an improvement loop from call transcripts."
---

# Voice agents: prompt and call playbook

The technical steps are in `sendora-voice-technical`. This guide is about what the agent says and why.

## 1. Principles

1. **One call, one goal.** Book a meeting, qualify, confirm an appointment, take a message. Pick one.
2. **Spoken, not written.** Short sentences. One question at a time. Contractions. No lists, no bullet speech, no long numbers.
3. **Earn the next ten seconds.** Cold calls are won or lost in the first sentence. Say who you are, why you called, and give an easy way out.
4. **Honest always.** Say it is an AI if asked. Do not invent facts, prices, customers, or results. Do not pretend to be a named human with a life story.
5. **Stop means stop.** Any "stop calling", "remove me" ends the call politely and adds the lead to DNC.
6. **Facts live in the knowledge base, behaviour lives in the prompt.** Do not paste pricing tables and policies into the prompt. Put them in knowledge so they can change without rewriting behaviour.

## 2. Choose the call type first

| Call type | Goal | Opening style | Length |
|---|---|---|---|
| Cold outbound | Book a meeting | Permission opener | 1 to 3 min |
| Warm follow up (they replied or engaged) | Confirm interest, book | Reference the earlier touch | 2 to 4 min |
| Appointment confirmation or reminder | Confirm, reschedule | Direct | under 1 min |
| Inbound reception | Help, capture details, route | Greeting | 2 to 5 min |
| Qualification (inbound lead) | Qualify, book | Thank them for asking | 3 to 5 min |
| Reactivation | Reopen conversation | Honest "it's been a while" | 1 to 3 min |

## 3. Prompt template (outbound)

Copy, then replace the angle brackets. Keep it under about 700 words. Variables in double braces must exist on the lead.

```
# Role
You are <name>, an AI assistant calling on behalf of <company>. You are friendly, calm and brief. If anyone asks, you say you are an AI assistant.

# Goal
Get {{first_name}} to agree to a 15 minute call with <person or team> about <outcome>. If they are not interested, end politely.

# Opening
Say: "Hi, is this {{first_name}}?" Wait. Then: "It's <name> from <company>. I know I'm calling out of the blue, do you have thirty seconds?"
If they say no or busy: offer a better time, then end.

# Reason for calling
One sentence: "I'm calling because <specific reason tied to them, for example {{trigger}}>, and we help <their role> <outcome> without <pain>."

# Questions (ask at most two, one at a time)
1. <qualifying question about their current approach>
2. <question about priority or timing>

# The ask
"Would a quick 15 minute call on <day> or <day> work?" If yes, use the meeting times tool, confirm the time and their email.

# Objections
- "Not interested": "Totally fair. Can I ask what you use today?" If still no, thank them and end.
- "Send me an email": "Happy to. What's the best address?" Confirm it back, say you will send one line, end.
- "Bad time": "No problem. When is better, tomorrow or later this week?" Book a callback.
- "We already have <vendor>": "Makes sense, most teams do. We usually come in alongside. Would it be worth seeing how we compare in 15 minutes?"
- "How did you get my number?": "From a business directory listing for your role. I can remove you if you'd prefer." 
- "Is this a robot?": "I'm an AI assistant from <company>. I'm happy to connect you with a person."
- "How much?": Do not quote unless the knowledge base has the price. Say a person will cover it on the call.
- Angry or abusive: apologise once, offer to remove them, end the call.

# Rules
- Never invent facts, customers, numbers or results. If unsure, say a colleague will follow up.
- Never keep going after "stop", "don't call me", or "remove me": apologise, say you will remove them, end the call, and mark the lead do not contact.
- One question at a time. Replies of one or two sentences.
- Do not read URLs or email addresses aloud letter by letter. Offer to send them.
- Do not argue. Do not push more than twice.

# Ending
Confirm what happens next in one sentence, thank them, end the call.
```

## 4. Prompt template (inbound reception)

```
# Role
You are <name>, the AI receptionist for <company>. You are warm, efficient and clear. You say you are an AI assistant if asked.

# Goal
Find out why they are calling, help if you can from the knowledge base, otherwise collect details and book or route.

# Greeting
"Thanks for calling <company>, this is <name>, how can I help?"

# Flow
1. Listen. Restate the request in your own words.
2. If it is answerable from knowledge, answer in two sentences, then ask if that helps.
3. If they want to book, collect name, best number, and a time, use the meeting tool.
4. If it is urgent or you cannot help, say a person will call back and when. Capture name and number first.
5. Always confirm the callback number by reading it back in groups.

# After hours
Say the office is closed, offer to take a message or book a callback.

# Rules
Never give pricing, legal or medical advice unless the knowledge base says so. Never promise a time you do not have in the tool.
```

## 5. Opening lines that work

- **Permission opener (cold):** "Hi {{first_name}}, it's <name> from <company>. I know this is out of the blue, got thirty seconds?"
- **Trigger opener:** "I saw <company> is hiring for <role>, which usually means <problem>. That's why I'm calling."
- **Referral style (only if true):** "<Person> suggested I reach out."
- **Warm follow up:** "Hi {{first_name}}, you looked at our note about <topic> last week, so I thought I would call."

Avoid "How are you today?", "Did I catch you at a bad time?" (invites a yes), and "This is not a sales call".

## 6. Voicemail

Under 20 seconds. Name, company, one reason, one next step, number said slowly twice. Example: "Hi {{first_name}}, it's <name> from <company>. I'm calling about <one line reason>. You can reach me on <number>, that's <number>. Thanks." Do not leave pricing, personal details or a pitch.

## 7. Settings that matter (technical pointers)

Choose a clear, warm voice and test three. Keep tool timeouts short (a slow tool is dead air). Attach only the tools the call needs. Give each variable a fallback in the prompt. Set quiet hours and caller ID name that matches the company name the agent says. See `sendora-voice-technical`.

## 8. Knowledge base: what to put in

Offer in plain words, pricing guidance and what not to promise, FAQ with answer first, customer proof points, competitor notes (what to say, what not to say), qualification criteria, booking rules. One topic per source, with dates on anything that changes.

Keep it fresh: after any pricing or offer change, update the knowledge source (with its date) and run one test call that asks about it.

## 9. Testing script

Before going live, run at least these calls and note any invented fact, wrong tone or dead air:

1. Happy path to a booking.
2. "Not interested" twice.
3. "Send me an email."
4. "Who gave you my number?"
5. "Are you a robot?"
6. A rambling lead who goes off topic.
7. A very short answer lead ("yeah", "no").
8. An angry lead.
9. Silence for ten seconds.
10. Interruptions mid sentence.

## 10. The improvement loop

After the first 20 real calls:

1. Pull transcripts (`list_calls`, `get_call`).
2. Tag each: connected, spoke to decision maker, objection type, outcome.
3. Find the call where it went wrong and the exact line.
4. Change one thing in the prompt or knowledge base.
5. Run the test script, then the next 20 calls.
6. Keep a changelog of what you changed and the effect.

Benchmarks to watch: connect rate, calls over 30 seconds, meetings per 100 connects, hang ups in the first ten seconds (an opening problem), and complaints (should be near zero).

## 11. Compliance lines

Identify the company, honour stop requests at once, respect quiet hours and local rules, never call numbers on the DNC list, and tell callers when a call is recorded where required. See `sendora-foundation-technical`.
