---
name: sendora-leads-technical
description: Technical guide to getting leads into Sendora through the MCP: lead lists, every way to add leads (single, import, B2B prospect search, Google Maps, company employees, lead requests, CRM import), exactly how previews and commits work, every filter, costs, limits and errors.
---

# Leads: technical guide

How the tools work. For how to choose *good* leads for a business, read `sendora-leads-business`.

## 1. The model

- A **lead** is one person or business. A **lead list** is a container; campaigns enrol from lists. Segments, tags and pipeline stages are slices on top.
- Every list-scoped call needs the list's public `list_id`. Create the list first.
- Ids are the public ids returned by tools. Never use or show a UUID.

## 2. Ways to get leads in

| Way | Tool and action | Costs | Writes leads? |
|---|---|---|---|
| One lead | `edit_leads` `create` | none | yes |
| Rows from a file or sheet | `check_lead_import` then `edit_leads` `import` (section 4) | none | yes, on `import` |
| B2B people, companies or hiring companies | `find_prospects` `preview_search`, then `commit_search` | allowance, then credits for contact lookups | only on commit |
| Local businesses (Google Maps) | `find_prospects` `preview_google_maps`, then `commit_google_maps` | credits | only on commit |
| Everyone at one company | `find_prospects` `preview_company_employees`, then `commit_search` | as B2B | only on commit |
| Leads sourced by the Sendora team | `edit_leads` `create_request` | per agreement | team adds them |
| From a connected CRM | `edit_platform` `crm_step` `import` | none | yes (use `dry_run` first) |

**Intent sources** (people already showing interest) run as one-time runs with two `find_prospects` actions, `prepare_intent_run` and `start_intent_run`. See section 2b. They work for LinkedIn post engagement, X reply mining, YouTube comments, competitor review authors, dormant CRM contacts and Google Maps. A connected LinkedIn account's own connections are switched off for now. Recurring schedules are off: each run is one time.

### 2b. Intent source runs (LinkedIn, X, YouTube, reviews, dormant CRM)

Two calls, both needing a lead list and the same prospect permission as search:

1. **Prepare:** `find_prospects` `prepare_intent_run` with `list_id`, `source_type` and `config`. The config is checked now (a bad config is a 422 that says what is missing). It saves the run and returns its id. No leads are added and nothing is charged.
2. **Start:** `find_prospects` `start_intent_run` with `list_id` and `source_id`. It collects people and adds them to the list. This writes leads and may cost credits. Confirm with the user first, and follow the job with `read_platform` `jobs`.

Config fields per `source_type`:

| `source_type` | Finds | Key `config` fields |
|---|---|---|
| `linkedin_post_engagers` | People who commented or reacted on chosen posts | `profile_urls` (up to 20, their best recent posts are found) and/or `post_urls` (up to 20; at least one required); `spike_threshold` (`min_reactions`, `min_comments`, `window_hours`); `capture` (`commenters` true, `likers` false); `limit` 1 to 500 |
| `x_reply_miner` | People who replied on chosen X posts or on a creator's recent posts | `post_urls` and/or `creator_handles` (up to 10); `posts_per_creator` 1 to 20; `max_replies` up to 1000 |
| `youtube_comment_engagers` | People who commented on chosen videos | `video_urls` (1 to 20); `max_comments` up to 500 |
| `zach` (review authors) | People who wrote reviews of named products on G2, Trustpilot or Capterra | `niche_description` (min 10 chars, used to qualify reviewers), plus at least one of `g2_product_slugs`, `trustpilot_company_urls`, `capterra_product_urls` (up to 5 each; the exact product must be named, it cannot be guessed); `use_g2`, `use_trustpilot`, `use_capterra`; `limit` 1 to 200 per product per site |
| `crm_dormant_reactivation` | Contacts in a connected CRM with no activity | `connection_id` (the CRM connection), `dormant_days` 1 to 730 (default 90), `limit` up to 2000 |
| `google_maps` | Local businesses | `query`, `locations` (up to 30), `limit` up to 500 per location |
| `prospect_search` | A B2B people search as a run | `filters` (section 6), `limit` up to 200 |
| `linkedin_connections` | Your connected account's connections | **Disabled for now** |

Common optional fields on most sources: `ai_qualification` (`enabled`, `icp_prompt` up to 2000 chars, `only_qualified`) keeps only people who match your ideal customer description, and `auto_enrich` (default true) looks up contact data. Always turn `ai_qualification` on with a clear `icp_prompt` for social sources, because raw engagers include many people who are not buyers.

Notes: if a call is refused, report the exact message instead of retrying. Social data is public data; it is not consent to contact, so run DNC checks as usual.

## 3. Lists

- Read: `read_leads` `lists`; leads in a list: `read_leads` `list` with `list_id`, paginated.
- Create: `edit_leads` `create_list`. Rename: `update_list`. Delete: `delete_resource` `lead_list` (confirm, check the count first).
- One lead: `read_leads` `get`, `activities`, and `context` with `include` of `ai_state, outreach_state, callbacks, campaigns, recordings`.
- Bulk edits: `edit_leads` `bulk` with `lead_ids` and `action` `set_status`, `add_tags` or `guess_emails`.
- Pipeline: `read_leads` `pipeline_stages`; move a lead with `set_pipeline_stage`.
- Segments (saved filters): `read_leads` `segments`, `edit_leads` `create_segment` (`name`, `criteria`), `run_segment`.

## 4. Importing rows

Order: **map, validate, preview, import, follow the job, verify.**

1. `check_lead_import` `prepare` (validate plus preview in one call) or `validate` then `preview`.
2. Read each issue: it has a path, a message and a repair hint. Nothing is written yet.
3. Report to the user: rows that would be inserted, updated, skipped, rejected. Wait for a yes.
4. `edit_leads` `import` into a named list. It returns a job.
5. Follow it with `read_platform` `jobs`.
6. `check_lead_import` `verify_import`, then `verify_list`. Report the counts it returns.

Row rules: lowercase trimmed emails, phones in E.164 (`+14155550123`), one identity per lead (email, phone or LinkedIn URL), custom fields as extra columns. Duplicate emails already in the workspace are skipped or updated, not doubled.

## 5. B2B prospect search, step by step

Prospect search needs a lead list and a manager, admin or owner role (view-only and client roles are refused, and an API key or MCP session acts with the role of the person who made it).

**Step 1: turn the idea into filters.** `read_prospect_previews` `parse_criteria` with a plain-English `description` of the ideal customer. It returns a `filters` object. It never charges and never searches. Show the filters to the user and fix them together. You can also write the filters yourself (section 6).

**Step 2: preview.** `find_prospects` `preview_search` with:

| Field | Meaning |
|---|---|
| `list_id` | the target list |
| `mode` | `people` (default), `companies`, or `hiring` (companies hiring for a role) |
| `filters` | `{people: {...}, company: {...}, hiring: {...}}` |
| `page_size` | 1 to 50 (default 25) |
| `cursor` | from the previous page, to continue |
| `require_contact` | people mode only: keep only people for whom a work email and a US mobile were found. Slower. |

The call returns a **preview**: `id` (the `preview_id`), `status`, `results`, `total_matches`, `cursor`, `page`, `total_pages`, `dropped_without_profile`, `error_message`. **No leads are created by a preview.** If `status` is not finished, poll `read_prospect_previews` `search` with `list_id` and `preview_id`.

Each person in `results` has a `prospect_key`. That key is what you commit.

**Step 3: review and page.** Show the user a sample: name, title, company, location. Check it against the target. If the sample is off, change the filters and preview again rather than committing and cleaning up. To collect more, call again with the `cursor`.

**Step 4: commit.** `find_prospects` `commit_search` with `list_id`, `preview_id`, `prospect_keys` (only the people the user approved) and optionally:

- `lookup_email: true`: finds a work email. 2 credits each, only when found.
- `lookup_phone: true`: finds a US mobile number. 8 credits each, only when found.

The result reports: `new_leads_count`, `updated_leads_count`, `already_in_other_lists_count`, `blocked_by_lead_limit`, `emails_found`, `phones_found`, and `contacts_blocked_message` (set when paid lookups stopped part way for lack of credits or a daily limit; the people were still added). A single commit accepts a limited number of keys. If it is refused for size, commit in batches.

**Step 5: verify.** `read_leads` `list` on the list, then verify emails (`enrich_leads` `verify_emails`).

**Allowance and costs.** Each plan includes a monthly allowance of people whose contact details are found (Launch 6,000, Growth 20,000). Searching and previewing does not use it. Past the allowance, and on pay as you go, contact lookups use credits as above. There are daily fair use caps per workspace. Tell the user before a commit that will use credits.

**Errors:** 402 empty balance (report, do not retry), 403 role cannot search, 503 search switched off, 422 a filter value is not allowed (the message names it).

### Company employees

`find_prospects` `preview_company_employees` with `company` (LinkedIn URL or website), `style` (`best` ranks the strongest contacts for a `role`; `everyone` lists the team), optional `titles`, `countries`, `max_results` (up to 50) and `page`. Commit chosen people with `commit_search`.

## 6. Filters

Full allowed values: `reference/filter-values.md`, and the 495 industries in `reference/industries.md`. Lists hold at most 50 values. Countries are two letter codes (`US`, `GB`). Unset fields are ignored.

**people** (who): `titles`, `exclude_titles`, `include_headline` (also match the profile headline), `levels`, `functions`, `countries`, `continents`, `sales_regions`, `cities`, `min_connections` (0 to 500), `education`, `education_exclude`.

**company** (where they work): `linkedin_urls`, `name_includes`, `name_excludes`, `industries`, `industries_exclude`, `company_types`, `employee_ranges`, `countries`, `cities`, `sales_regions`, `keywords`, `exclude_keywords`, `funding_types`, `lead_investors`, `last_funding_year_min/max`, `total_funding_min/max`, `revenue_min/max`, `founded_year_min/max`, `web_traffic_min/max`, `ad_spend_min/max`, `min_linkedin_followers`.

**hiring** (job postings as a buying signal): `titles`, `exclude_titles`, `description_keywords` (a tool or a pain named in the posting, the stand-in for tech stack), `themes`, `seniority` (`0-2`, `2-5`, `5-10`, `10+`), `employment_types`, `work_arrangements`, `countries`, `cities`, `posted_within_days` (1 to 365), `min_roles_per_company` (1 to 25), `exclude_agencies`.

A search needs at least one filter in `people`, `company` or `hiring`. Ranges: a minimum above a maximum is rejected.

## 7. Google Maps (local businesses)

`find_prospects` `preview_google_maps` with `list_id`, `query` (the business type, for example `roofing contractor`), `locations` (a list of up to 30 cities or areas) and `limit` (up to 500 **per location**; 50 locations of 50 is up to 2,500). It previews without writing. Commit with `commit_google_maps` and the chosen `place_ids`, `auto_enrich` true to find contact data. Read the preview with `read_prospect_previews` `google_maps`.

Use it for local services. Use B2B search for roles at companies.

## 7b. Tags, export

- `read_leads` `tags_in_use` lists the tags used in the workspace with counts. One lead's tags come with `get`. Add or remove tags with `edit_leads` `update` (`add_tags`, `remove_tags`) or `bulk` `add_tags`.
- `read_leads` `export` returns leads as CSV text (`lead_list_id` for one list). Over 500 leads it returns only the count and columns, so export one list at a time.

## 8. After leads land

Verify emails, enrich missing phones (`enrich_leads` `enrich`, `verify_emails`), research a high value lead (`research_web`), check DNC, tag by source and batch. A lead already on the DNC list is not contacted even if you import it again.
