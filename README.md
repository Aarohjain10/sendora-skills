# Sendora skills

Skills that let an AI agent run outbound on Sendora through its MCP server: leads, voice agents, chat agents, campaigns, channels, the inbox, analytics, workspace admin and agency work.

Each area has two skills. The technical one says which tools and fields to use, in what order. The business one says what to do and say for good results. These are general guidance, not strict rules: every business and situation is different, so an agent should use judgement and adapt. Skills point to each other by name only where the pointer helps. Start with `sendora-onboarding` for a new workspace, or `sendora-foundation-technical` for the basics every other skill builds on.

## Install

Works with Claude Code, Codex, Cursor, Windsurf and most other coding agents. Use your agent's id: `claude-code`, `codex`, `cursor` or `windsurf` (`npx -y add-mcp list-agents` shows the rest).

1. Connect the Sendora MCP server, then sign in when your agent asks (a browser page opens to approve access to your workspace):

```
npx -y add-mcp https://api.sendora.ai/api/v1/mcp -n sendora -g -a <id> -y
```

2. Install the skills:

```
npx -y skills add Aarohjain10/sendora-skills -g -y -a <id>
```

Reload your agent, then start with `sendora-onboarding`. Use `--list` to preview the skills, or `--skill sendora-onboarding` to install one.

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
| sendora-inbox-technical, sendora-inbox-business | Conversations, approvals, escalations, tasks, meetings, replies |
| sendora-chat-agents-technical, sendora-chat-agents-business | Chat agents on SMS, WhatsApp and email: inbound, campaign replies, agent-started chats, actions, follow-ups, testing |
| sendora-analytics-technical, sendora-analytics-business | Reports and A/B results, weekly review |
| sendora-workspace-technical, sendora-workspace-business | Integrations, webhooks, team, settings, billing |
| sendora-agency-technical, sendora-agency-business | Clients, packages, branding, running an agency |

## Rules for contributors

A fact lives in one place. How a tool works goes in a technical skill. Why and when goes in the business skill. Skills link to each other by name. When an MCP tool is renamed or removed, change the skills that name it in the same change.

## Known gaps

Purchases, number buying, SMS submit and voice cloning have not been exercised live. Chat agents are written from the product code, not from a live end to end run through the MCP: starting a first chat on WhatsApp, and a stranger emailing an agent first, have not been proven live. Benchmarks and volume numbers are rules of thumb to confirm. These are stated inside the skills.
