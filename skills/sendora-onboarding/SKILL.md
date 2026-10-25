---
name: sendora-onboarding
description: "Set up a new Sendora workspace for the customer, as drafts only. Use when the workspace is new or empty, or the user says \"set me up\", \"get me started\" or \"onboard me\". Reads first, asks three questions, proposes plays, builds a knowledge base, voice agent, lead list and campaign draft, and lists what only the user can do next."
---

# Sendora: onboard a new workspace

Read `sendora-foundation-technical` first. Its rules apply here. This skill only ever creates drafts. It never launches, sends, calls, buys or spends.

## 1. Look before you ask

Read what already exists so you never duplicate it.

1. `read_platform` `settings` for the company profile and mailing address.
2. `read_voice` `list_agents` and `read_voice` `list_knowledge_bases`.
3. The lead lists and campaigns, using the list calls in `sendora-leads-technical` and `sendora-campaigns-technical`.

If agents, lists or campaigns already exist, say what you found and ask whether to build on it. Do not start over.

## 2. Ask three questions

Ask only what you cannot read from the workspace. Keep it to three, in one message.

1. What do you sell, and to whom? Ask for the website too. If there is no website, one line about the business is enough.
2. What is the goal: book meetings with new people, answer and qualify people who contact you, or win back older contacts?
3. Which channels do you want to use? Offer email, calls, WhatsApp and text. Tell them text needs a business registration that cannot be done during setup, so plan without it and add it later.

Where the people are decides the chat channel. Europe: WhatsApp, not text. The United States: text, once registered. Elsewhere: WhatsApp.

## 3. Propose two or three plays

One line each: who it reaches, the channels in order, and why it fits this business. Name only facts you were given, never invent customers or numbers. Reasonable defaults:

- New people, email then a call, spread over about a week.
- Local businesses (restaurants, clinics, contractors): find them on Google Maps and plan around calls, with no email steps.
- Older leads or people already in the pipeline: suggest voice calls, and say so.
- People who contact them: a voice agent answers, a chat agent follows up by message.

Wait for the user to pick one. Do not build before they do.

## 4. Build the picked play, as drafts

Say what you are about to do before each step, then do it, then read it back.

1. **Knowledge base.** `edit_voice` `create_knowledge_base`, then `add_kb_url` with the website (or `add_kb_text` with the one line). Keep facts here, not in prompts (`sendora-voice-technical`).
2. **Voice agent, only if the play has a call.** Write the prompt with `sendora-voice-business`. Save a draft with `edit_voice` `save_draft`. Do not publish and do not assign a number. `assign_kb` to attach the knowledge base.
3. **Lead list.** `edit_leads` `create_list`, then find people with `find_prospects`. Preview first (`preview_search`, or `preview_google_maps` for local businesses), show the user what you found, and commit only the people they approve. Credits are spent at commit, so say so first (`sendora-leads-technical`).
4. **Campaign draft.** Build the steps with `sendora-campaigns-technical` and write the messages with `sendora-campaigns-business`. Validate, save, run preflight. Do not enroll leads and do not launch.

## 5. Finish with what only the user can do

List the next steps in order, each with the reason:

- Connect a mailbox and set a mailing address (email needs both).
- Get a phone number (the voice agent needs one to call). Quote the price first, buy nothing without a yes.
- Connect a calendar so booked meetings really book.
- Add credits if the lead list needs contact lookups.
- Review the drafts, then ask you to launch when ready (`sendora-campaigns-technical`).

End by running the read only audit in `sendora-foundation-technical` and report blockers in the Blocking, Should fix, Fine format.

## Never

Launch, send, call, publish, buy or spend. Enroll leads. Skip the preview before a lead commit. Say something was created before you have read it back. Ask for or repeat passwords, keys or codes.
