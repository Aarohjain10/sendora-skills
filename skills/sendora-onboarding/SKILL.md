---
name: sendora-onboarding
description: "Get a new or empty Sendora workspace going for a customer. Learn the business first, then use the other Sendora skills to build what fits, as drafts for the user to review. Useful when the user says set me up, get me started or onboard me."
---

# Sendora: onboard a workspace

This is guidance for getting someone started. Every business is different, so treat the steps as a sensible order and change them to fit the person in front of you. Other Sendora skills do the detailed work, and this one points to them when they are needed. The habits in `sendora-foundation-technical` are worth a glance first.

## 1. Learn the business

The better you understand the company, the better everything built for it. Spend real effort here before building.

- Read what the workspace already holds: `read_platform` `settings` for the company profile, and the existing agents, knowledge bases, lead lists and campaigns through the read calls in `sendora-voice-technical`, `sendora-leads-technical` and `sendora-campaigns-technical`. If things already exist, it usually makes sense to build on them.
- Research the company with your own tools if you have them: their website and its main pages, what they sell, who they sell to, pricing or plans, case studies, reviews, recent news, competitors, and the language and tone they use. If you cannot browse, ask the user for the website and a short description.
- Notice the market too: where the customers are, how they buy, and what a busy buyer in that world cares about.

Then say back what you understood in a few sentences, and ask only for what you could not find, such as the real goal, the buyer you want most, or the channels they are comfortable with. One short message is usually enough.

## 2. Decide what to build

Work out what would help this business most, then offer two or three options in plain words: who it reaches, how, and why it fits. A goal might be new meetings, qualifying people who get in touch, winning back older contacts, or something else entirely. Let the user choose or reshape the idea before you build.

Some pointers for choosing:

- `sendora-foundation-business` has ideas on sequencing a go to market effort.
- `sendora-leads-business` helps define who to reach, and `sendora-campaigns-business` helps shape the journey.
- Channels work if their prerequisites exist. `sendora-channels-business` explains what each needs, which matters for email and texting in particular.

## 3. Build it

Build in whatever order the plan needs, and tell the user what you are doing as you go. Typical pieces, each with the skill that covers it:

- Facts about the business for agents to draw on: a knowledge base (`sendora-voice-technical`).
- A voice agent if calls are part of the plan: how to write the prompt is in `sendora-voice-business`, the setup in `sendora-voice-technical`. A chat agent for replies is in `sendora-inbox-technical`.
- The people to reach: `sendora-leads-technical` covers search, preview and import. Previewing before committing keeps spend under the user's control.
- The campaign: `sendora-campaigns-technical` and `sendora-campaigns-business`.

Drafts leave the user in charge. Publishing, launching, sending, calling, buying and spending can wait for a clear go ahead from them.

## 4. Hand over

Give the user a short picture of what now exists and what only they can do. Depending on the plan, that might be connecting a mailbox, getting a phone number, connecting a calendar, adding credits, or reviewing the drafts. `sendora-foundation-technical` has a read only audit that shows what is ready and what is blocking.

## Keep in mind

- Describe results in the customer's terms. The supplier and infrastructure names behind Sendora are not useful to them.
- Stay with facts you learned about the business. Invented customers, numbers or claims tend to cost trust later.
- If the user wants something different from this order, follow the user.
