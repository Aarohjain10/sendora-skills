---
name: sendora-leads-business
description: Business and sales guide to finding good leads for any business with Sendora: define the ICP, choose between cold fit-based and intent-led outbound, signal ladder, ready-made filter recipes, list sizing and quality checks, qualification, and how to ask for help when a signal source is not in the MCP.
---

# Leads: business playbook

Good outbound is mostly good targeting. A mediocre message to the right person beats a great message to the wrong one. Read `sendora-leads-technical` for how each tool works.

## 1. Before any search: the ICP in six lines

Ask the user (or infer and confirm) in one short message:

1. **What do they sell, and to whom?** One sentence each.
2. **Who buys?** Job titles and level. Who signs, who uses, who influences.
3. **What kind of company?** Industry, size band, region, stage.
4. **What problem is urgent for them right now?** The trigger that makes them buy this quarter.
5. **Who is a bad fit?** Sizes, regions, industries, competitors, agencies.
6. **What does a win look like?** Meetings per month, deal size. That sets how many leads you need.

If the user cannot answer 4, say so: no urgent trigger means cold, fit-based outbound only, and expect lower reply rates.

## 2. Two strategies

**Fit-based (cold).** Everyone who matches the profile. Easy to scale, lower response, needs strong copy and volume. Reply rates are typically low single digits.

**Intent-led (warm).** Only people showing a sign they care right now. Smaller lists, much higher response, easier conversations. Always prefer it when a good signal exists, then add fit-based for volume.

### The signal ladder (strongest to weakest)

| Signal | What it tells you | How to get it in Sendora |
|---|---|---|
| They asked or visited you | Highest intent | Inbound: widget, inbound calls, forms through webhooks (`sendora-inbox-technical`) |
| Competitor review authors | They already buy this category and have opinions | MCP via source runs: `source_type zach` with the competitor's product page names (`leads-technical` 2b) |
| Engaged with a relevant post (commented, reacted) | Active interest in the topic | MCP via source runs: `linkedin_post_engagers` with creator profiles or post URLs, plus `ai_qualification` |
| Replied on a relevant creator's X posts, commented on a relevant YouTube video | Interest in the topic | MCP via source runs: `x_reply_miner` (X) or `youtube_comment_engagers` (YouTube) |
| **Hiring for a role that means they need you** | Budget exists, a pain is live | MCP: `preview_search` with `mode: hiring` |
| **Recent funding** | Cash to spend, growth pressure | MCP: company `funding_types`, `last_funding_year_min`, `lead_investors` |
| **Growth indicators** | Web traffic, ad spend, follower count rising | MCP: company `web_traffic_min`, `ad_spend_min`, `min_linkedin_followers` |
| **Named a tool or a pain in a job post** | Tech stack and pain signal | MCP: hiring `description_keywords` |
| Old leads or lost deals gone quiet | Warm if the reason to talk is new | CRM import or a segment of old leads (`sendora-campaigns-business`, reactivation) |
| Plain fit (title, industry, size, region) | Lowest, but scalable | MCP: people and company filters |

Combine: fit plus one signal beats either alone. Example: SaaS companies 50 to 500 staff (fit) that are hiring a Head of RevOps (signal).

How to run an intent-led play: pick the right people or content to watch (a creator your buyers follow, a post about the problem you solve, the review page of a competitor), set a clear `icp_prompt` so only buyers are kept, run once with a small `limit` (50), review the leads, and write a first line that references what they did ("saw your comment on ..."). Treat engagement as interest in the topic, not as consent: still follow the compliance rules. The exact calls and config fields are in `sendora-leads-technical` section 2b.

## 3. Recipes (starting filters)

Treat these as starting points, then preview and adjust. Values must be exact (see `reference/filter-values.md` in the technical guide).

**B2B SaaS selling to sales leaders**
- people: `titles` ["Head of Sales", "VP Sales", "Chief Revenue Officer"], `levels` ["VP", "C-Team"], `functions` ["Sales & Business Development"], `countries` ["US", "GB"]
- company: `employee_ranges` ["51-200", "201-500"], `industries` pick 2 or 3 specific ones, `exclude_keywords` ["staffing"]
- add signal: company `last_funding_year_min` this year minus 1, `funding_types` ["Series A", "Series B"]

**Agency or service selling to founders**
- people: `titles` ["Founder", "Co-Founder", "CEO"], `levels` ["C-Team"]
- company: `employee_ranges` ["1-10", "11-50"], `company_types` ["Privately Held"], specific `industries`
- signal: hiring for the function you replace (for example `hiring.titles` ["Marketing Manager"], `posted_within_days` 45)

**Recruiting or HR tech**
- mode `hiring`: `titles` the roles you place, `posted_within_days` 30, `min_roles_per_company` 3 (a company hiring many roles hurts), `exclude_agencies` true.
- then `employee-preview` on each company with `role` "Head of Talent" or "HR".

**Security, IT or infra tool**
- people: `functions` ["Information Technology"], `levels` ["Director", "VP", "C-Team"]
- hiring: `description_keywords` the tool you replace or integrate with, `titles` like "Security Engineer"

**Local services (roofing, dental, restaurants)**
- Google Maps: `query` the trade, `locations` several nearby cities. Filter in review by rating and review count (a business with many reviews is established).
- Expect no job titles: the owner often answers. Call first.

**Ecommerce / DTC**
- company: `keywords` the platform and category, `web_traffic_min` and `ad_spend_min` to find ones that spend, `employee_ranges` ["11-50", "51-200"]
- people: `titles` ["Head of Growth", "Marketing Director", "Founder"]

**Enterprise (account based)**
- Build a named account list of 30 to 100 companies (`company.linkedin_urls` or `name_includes`).
- Per company, `preview_company_employees` with `style: best` and a `role`, then pick 3 to 5 contacts per account across functions.

## 4. Sizing the list

Work backwards: target meetings per month, divided by meeting rate (cold email about 0.5 to 1 per 100 contacts, warm and intent-led 3 to 10 per 100), gives contacts needed. Add 30 percent for bounces and disqualifications. Do not buy more than one cycle of leads at a time.

Start with 50 to 100 leads, send, learn, then widen. Do not build a 10,000 list before the first 100 get answers.

## 5. Quality checks before a campaign

Run these on a sample of 20 from the preview, in the preview, before committing:

- **Role fit:** does every title look like a buyer? If more than 2 in 20 do not, tighten titles and add `exclude_titles` ("assistant", "intern", "student", "consultant", "freelance").
- **Company fit:** right size, right industry, not a competitor, not an agency (unless they are the target).
- **Region fit:** the right country, and time zone for calling.
- **Duplicates:** a person at two companies, or already in another list (`already_in_other_lists_count` after commit).
- **Contactability:** after commit, verify emails. Cut invalid ones. For calling, confirm a phone is present.

If quality is below 85 percent fit, do not scale. Fix the filters first.

## 6. Personalisation data (this is what makes messages good)

Collect while building the list, so the campaign can use it: first name, company, title, one trigger fact (funding round, the job they are hiring for, the post they engaged with, the review they wrote), and the reason they are on the list. For high value leads use `enrich_leads` `research_web` for a true recent fact. If no fact exists, write a role-based opening instead of inventing one.

## 7. Budget discipline

Previewing does not use the plan allowance. Contact lookups count against it when a contact is found, and cost credits past it. So: preview more, commit less. Ask for contact lookups only on the people the user approved. Tell the user the expected cost before each commit.

## 8. Compliance

Outreach only to people who can lawfully be contacted for this purpose. Check DNC before launch. Honour opt outs at once. Where calling or texting, check regional rules (quiet hours, registration). Scraped or bought data is not consent. See `sendora-foundation-technical` for the standing rules.

## 9. Asking the right follow up questions (use sparingly)

If the user says "find me leads" and gives nothing else, ask only: who is the buyer, what is the company profile, and which region. Offer a default and proceed if they say "your call".
