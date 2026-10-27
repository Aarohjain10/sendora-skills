# Sendora skills

Skills that let an AI agent run outbound on Sendora through its MCP server: leads, voice agents, chat agents, campaigns, channels, the inbox, analytics, workspace admin and agency work.

Each area has two skills. The technical one says which tools and fields to use, in what order. The business one says what to do and say for good results. These are general guidance, not strict rules: every business and situation is different, so an agent should use judgement and adapt. Skills point to each other by name only where the pointer helps. Start with `sendora-onboarding` for a new workspace, or `sendora-foundation-technical` for the basics every other skill builds on.

## Install

Each tool has its own native steps. Signing in always opens a browser page where you choose your workspace and approve access. The same prompts are on the MCP page inside Sendora (Settings, Integrations, MCP), ready to paste into your agent.

**Claude Code** (skills and connection as one plugin):

```
claude plugin marketplace add Aarohjain10/sendora-skills
claude plugin install sendora@sendora-skills
claude mcp login plugin:sendora:sendora
```

**Codex:**

```
codex mcp add sendora --url https://api.sendora.ai/api/v1/mcp
codex mcp login sendora
```

**Cursor:** add `"sendora": { "url": "https://api.sendora.ai/api/v1/mcp" }` under `mcpServers` in `~/.cursor/mcp.json`, then sign in from Cursor's MCP settings.

**Windsurf:** add `"sendora": { "serverUrl": "https://api.sendora.ai/api/v1/mcp" }` under `mcpServers` in your Windsurf MCP config, then sign in from the MCPs section of the Cascade panel.

**Skills for Codex, Cursor and Windsurf** (copy the folders, no package needed):

```
git clone --depth 1 https://github.com/Aarohjain10/sendora-skills.git /tmp/sendora-skills
mkdir -p ~/.agents/skills && cp -R /tmp/sendora-skills/skills/. ~/.agents/skills/
```

Windsurf reads `~/.codeium/windsurf/skills/` too; use that folder if `~/.agents/skills` does not show them. Other agents: `npx -y skills add Aarohjain10/sendora-skills -g -y -a <agent id>`.

Restart your agent, then start with `sendora-onboarding`.

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
