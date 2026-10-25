---
name: sendora-channels-business
description: "Business guide to getting email and calls delivered and trusted: domain strategy, mailbox counts, warmup schedule, volume ceilings, list hygiene, content rules, monitoring thresholds, recovery from a damaged reputation, and SMS and calling reputation basics."
---

# Deliverability and sender reputation playbook

Technical steps are in `sendora-channels-technical`. This is the strategy that keeps messages out of spam.

## 1. Principles

1. Reputation is the asset. It builds slowly and breaks fast.
2. It is better not to send cold email from the main company domain. Use separate, similar looking sending domains.
3. Spread volume. Many small senders beat one big one.
4. Clean lists and relevant targeting are the best deliverability tools.
5. Pause early. A paused account recovers; a burned one is replaced.

## 2. Infrastructure plan

| Daily cold volume | Domains | Mailboxes |
|---|---|---|
| up to 150 | 1 to 2 | 3 to 5 |
| 150 to 500 | 3 to 5 | 8 to 15 |
| 500 to 1,500 | 6 to 12 | 20 to 40 |

Starting points: two to three mailboxes per domain, 30 to 50 sends per mailbox per day at most once warm, separate domains for separate campaigns when audiences differ. Domain names close to the brand (a different TLD or a prefix like `get` or `try`), never misleading.

## 3. Warmup schedule (typical)

| Week | Sends per mailbox per day |
|---|---|
| 1 | 5 to 10 (warmup only) |
| 2 | 10 to 20 |
| 3 | 20 to 30 |
| 4 | 30 to 40, real campaigns allowed at a low share |
Keep warmup running alongside real sends. It helps to keep it, even for a mailbox that "looks" established if it never sent cold mail.

## 4. DNS and authentication

SPF with one record, DKIM passing, DMARC present (`p=none` with reports, tighten after a clean month), working forwarding of the sending domain to the main site, and a reverse path that matches. Verify with `domain_step` `verify_dns` after every change. Re-check weekly.

## 5. List hygiene

Verify every address before sending. Remove role addresses (info@, sales@) unless you sell to small businesses. Remove catch-all risky addresses on cold sends, hard bounces and unsubscribes immediately, and anyone who complained. Keep list age under 90 days for cold.

## 6. Content guidelines

Plain text. 50 to 120 words. One link at most, preferably none in the first email. No attachments or images. No spam trigger phrases ("guarantee", "act now", all caps). Personalise the first line. Vary the wording across mailboxes so messages are not identical. Include a clear way to opt out (automatic) and a real sender name.

## 7. Monitoring thresholds

| Signal | Healthy | Act at |
|---|---|---|
| Bounce rate | under 2% | over 3%: pause the campaign |
| Spam complaints | under 0.1% | any spike: pause the mailbox |
| Reply rate | stable | drops by half: run a placement test |
| Placement test | inbox | spam or promotions: rest the mailbox |
| Blacklist test | clean | listed: stop that domain, request removal |

Run `start_deliverability_test` (placement and blacklist) before launch, then weekly. Read `accounts` with `include: "health,incidents,strength"` daily in the first month.

## 8. Recovery

1. Pause sends from the affected mailboxes and campaigns.
2. Find the cause: list quality, volume jump, copy, DNS change, blacklist.
3. Fix the cause. Re-verify the list.
4. Restart warmup at a low level for one to two weeks.
5. Return to campaigns at a quarter of the previous volume and climb back.
6. If a domain stays listed after fixes, retire it and move volume to fresh domains.

## 9. Calls and SMS reputation

**Calls:** use a verified caller ID with a name that matches the company. Rotate numbers sparingly, not aggressively. Keep call windows reasonable. A number flagged as spam likely made too many short unanswered calls: lower volume, add a voicemail, and call at different times. Honour stop requests.

**SMS:** register brand and campaign, keep sample messages truthful, include opt out on the first message, avoid sending to people who have not consented, and keep opt out processing instant.

## 10. LinkedIn and WhatsApp accounts

Treat as personal accounts. Stay under safe daily limits, mix in manual style messages, avoid running at the limit, and pause on any warning.

## 11. SMS registration: writing what the carriers read

Use the company's real legal name, address and website. Sample messages work best when they are truthful, in your own voice, name the brand, include opt out wording on the first message, and avoid shortened links and restricted topics (gambling, loans, adult). Describe the opt in flow exactly as it happens. A mismatch between the registration and real messages is the usual cause of rejection. Plan for days, not hours, of review.
