---
name: sendora-agency-business
description: Business guide for agencies reselling outbound on Sendora: packaging and pricing the service, onboarding a client in a fixed timeline, reporting cadence, protecting reputation across clients, margins, and when to suspend or upsell.
---

# Agency playbook

Tools are in `sendora-agency-technical`.

## 1. Offer design

Sell outcomes (meetings, pipeline), not tools. Three packages is enough:

| Package | For | Includes | Typical shape |
|---|---|---|---|
| Starter | Testing the channel | One channel, one campaign, small volume, weekly report | low price, short term |
| Growth | Steady outbound | Two or three channels, voice or chat agent, monthly strategy call | middle price |
| Scale | Full motion | All channels, multiple campaigns, account based work, dedicated manager | high price |

Include credits that fit the package. Charge setup for infrastructure and onboarding.

## 2. Pricing logic

Start from your cost per delivered outcome, add the markup (`save_pricing`, in basis points), then check retail rates per credit before you publish. Do not undercut yourself: price on value (a meeting is worth hundreds). Leave room for usage past the included credits. Review pricing quarterly.

## 3. Client onboarding timeline

| Day | Step |
|---|---|
| 0 | Kickoff call: ICP, offer, proof, guardrails. Create the client workspace, set limits and branding |
| 1 to 3 | Domains and mailboxes bought, warmup started, numbers and registration begun |
| 3 to 7 | Lead list built, knowledge base written, voice or chat agent drafted |
| 7 to 10 | Campaign drafted, client reviews every message |
| 10 to 14 | Test enrolment, placement test, approvals on |
| 14 to 21 | Soft launch, small batch |
| 21+ | Scale, weekly reporting |

Set expectations: email needs warmup, carrier registration takes days, results take four to six weeks.

## 4. Reporting cadence

Weekly: sends, replies, positive replies, meetings, issues, next week's change. Monthly: pipeline value, cost per meeting, what you tested, plan for next month. Quarterly: strategy and pricing review. Use `get_analytics` and the client's spend in `client_details`.

## 5. Protecting reputation across clients

Separate domains and mailboxes per client. Never share a sending account across clients. Apply the same pause thresholds to every client. If one client ruins a domain, it must not touch others. Keep a shared standard checklist (`sendora-foundation-technical`).

## 6. Margin and capacity

Know the cost to serve a client per month: infrastructure, credits, your time. Cap clients per manager. Use usage caps (`update_client_limits`) so one client cannot overspend credits.

## 7. Suspending and upselling

Suspend (with a clear message) when payment fails or when a client violates compliance. Upsell triggers: reply rate strong and list exhausted (add a segment), meetings booked but no follow up (add chat or voice agent), client asks for a new region or channel.

## 8. Client communication

Explain what is happening in plain words. Share bad news early with a plan. Give them a short dashboard of the numbers that matter. Never expose supplier or infrastructure names; use "Sendora".
