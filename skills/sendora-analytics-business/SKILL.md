---
name: sendora-analytics-business
description: Business guide to turning Sendora numbers into decisions: the weekly review routine, metric definitions and benchmarks, how to read A/B tests, a diagnosis tree for drops, keep or cut or scale rules, and report formats for owners and clients.
---

# Analytics: business guide

The tools are in `sendora-analytics-technical`. This guide is how to read the numbers and what to do next.

## 1. The one question per report

Every report must end with an action: keep, fix, scale or stop. If you cannot say which, the report is not finished.

## 2. Metrics that matter, in order

| Metric | Plain meaning | Healthy cold email |
|---|---|---|
| Bounce rate | Sent to addresses that do not exist | under 2% |
| Reply rate | Replied divided by delivered | 3% to 8% |
| Positive reply rate | Interested divided by delivered | 1% to 3% |
| Meeting rate | Meetings booked per 100 contacted | 0.5 to 2 |
| Connect rate (calls) | Calls answered by a person | 15% to 35% |
| Meetings per 100 connects (calls) | Calls that turn into a booking | 2 to 8 |
| Cost per meeting | Credits and subscription per booking | falling over time |

These are rules of thumb. Compare a campaign with your own past results first, and with these second. Opens are unreliable (machines open mail), so never decide on opens alone.

## 3. Weekly review (30 minutes)

1. `overview` for the week. Write the headline in one sentence.
2. `top_campaigns` by meetings. The top two are your model. Read what they have in common (audience, offer, channel mix).
3. `funnel` for each live campaign. Find the step with the steepest drop.
4. `email_variants` and `variants` for any live A/B test. Decide if it has enough data (section 5).
5. `deliverability` and `mailboxes`. Any bounce band other than ok, or a mailbox with low health, is fixed before anything else.
6. `agents` and `calls` if voice is live. Look at connect rate and calls under 10 seconds.
7. `action_queue`: clear what is waiting on a person.
8. Pick three changes for next week. Write them down with the metric each should move.

## 4. Diagnosis tree

| Symptom | Check first | Likely cause | Fix |
|---|---|---|---|
| Bounces up | `deliverability`, list age | Stale or unverified list, DNS change | Pause, verify list, re-check DNS |
| Replies down, bounces fine | `funnel`, placement test | Landing in spam, tired copy | Placement test, rest mailboxes, new angle |
| Opens fine, no replies | `email_variants` | Weak ask or wrong person | Sharper ask, check titles |
| Replies but no meetings | reply handling speed | Slow or unclear follow up | Reply within the hour, offer two times |
| Calls connect but no bookings | call transcripts | Weak opener or ask | Rewrite opening, concrete time ask |
| Many calls under 10 seconds | `calls` | Opening problem, caller ID | Shorter opener, say company first |
| Good results then a cliff | `mailboxes`, `send_cap` | Warmup shortfall, cap hit | Slow down, add mailboxes |
| Spend rising, results flat | `ai_spend` | Heavy features unused value | Turn off low value AI steps |

## 5. Reading an A/B test

- Judge on positive replies (or meetings for calls), not opens.
- Wait for about 150 to 200 sends per variant; 300 or more for small differences. Under that you are reading noise.
- Run at least one full week so weekday effects average out.
- A winner beats control by about 30 percent relative and by more than a handful of replies. Otherwise keep the control.
- After a win: make the winner the baseline, then test the next single variable.
- Never end a test early because one variant looks ahead on day two.

## 6. Keep, fix, scale, stop

- **Scale:** meeting rate at or above your target, bounces under 2 percent, replies steady for two weeks. Raise volume about 25 percent a step.
- **Fix:** good delivery, weak response. Change one thing (audience, offer, then copy).
- **Stop:** under 0.3 percent positive replies after 200 well targeted sends and one fix attempt, or any deliverability alert you cannot clear.
- **Clone to template:** anything that beats target for three weeks.

## 7. Report formats

**Owner or founder (half a page):** headline, three numbers (meetings, cost per meeting, reply rate), what changed, what we do next, any risk.

**Client (agency):** the same plus a short plain-language explanation of each number, the A/B learnings, and next month's plan. No jargon, no supplier names.

**Quarterly:** trend of meetings and cost per meeting, best audience, best offer, best channel mix, what to stop.

## 8. Honest reporting

State the window. Report zero as zero. Say when a source was unavailable and show the last known value with its time. Do not compare periods of different length without saying so, and do not claim causes you have not tested.
