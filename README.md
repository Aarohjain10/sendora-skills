# Sendora skills

Skills that let an AI agent run outbound on Sendora through its MCP server: leads, voice agents, campaigns, channels, the inbox, analytics, workspace admin and agency work.

Each area has two skills. The technical one says which tools and fields to use, in what order. The business one says what to do and say for good results. These are general guidance, not strict rules: every business and situation is different, so an agent should use judgement and adapt. Skills point to each other by name only where the pointer helps. Start with `sendora-onboarding` for a new workspace, or `sendora-foundation-technical` for the basics every other skill builds on.

## Install

Connect the Sendora MCP server first (`https://api.sendora.ai/api/v1/mcp`, sign in with OAuth), then install the skills with the skills tool:

```
npx skills add Aarohjain10/sendora-skills -a claude-code -g -y
```

Use `-a codex` or `-a cursor` for those tools. The repository is private for now, so set `GITHUB_TOKEN` or `GH_TOKEN` first. Use `--list` to preview what is in it, or `--skill sendora-onboarding` to install one skill.

## Skills

| Skill | For |
|---|---|
| sendora-onboarding | Learn the business, then use the other skills to build a fitting first setup as drafts |
| sendora-foundation-technical | How the MCP works, helpful habits, compliance, workspace audit, troubleshooting. Read first |
| sendora-foundation-business | Go to market plans and recipes |
| sendora-leads-technical, sendora-leads-business | Lists, import, prospect search, filters |
| sendora-voice-technical, sendora-voice-business | Voice agents, numbers, tools, knowledge, prompts |
| sendora-campaigns-technical, sendora-campaigns-business | Graph, A/B per step, launch, sequences |
| sendora-channels-technical, sendora-channels-business | Mailboxes, domains, pools, SMS registration, deliverability |
| sendora-inbox-technical, sendora-inbox-business | Conversations, chat agents, approvals, replies |
| sendora-analytics-technical, sendora-analytics-business | Reports and A/B results, weekly review |
| sendora-workspace-technical, sendora-workspace-business | Integrations, webhooks, team, settings, billing |
| sendora-agency-technical, sendora-agency-business | Clients, packages, branding, running an agency |

## Rules for contributors

A fact lives in one place. How a tool works goes in a technical skill. Why and when goes in the business skill. Skills link to each other by name. When an MCP tool is renamed or removed, change the skills that name it in the same change.

## Known gaps

Purchases, number buying, SMS submit and voice cloning have not been exercised live. Benchmarks and volume numbers are rules of thumb to confirm. These are stated inside the skills.
