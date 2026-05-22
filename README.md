# Windframe Skill

Windframe Skill helps your coding agent build better web interfaces by connecting it to Windframe MCP. Instead of guessing spacing, typography, colors, and component patterns from generic training data, your agent can request live design-system context from Windframe and use it while generating UI in your project.

Use it for landing pages, dashboards, admin panels, SaaS screens, marketing pages, UI components, and restyling existing interfaces.

## What It Does

Windframe Skill gives your agent a workflow for UI generation:

- fetches Windframe style context for design direction
- recommends useful style and color directions based on your request
- helps the agent generate production-ready UI in your project

## Install

Install the skill into your agent's skills directory.

If you use the `skills` CLI:

```bash
npx skills add https://github.com/Devwares-Team/windframe-skill --skill windframe
```

Or clone the skill manually:

```bash
git clone https://github.com/Devwares-Team/windframe-skill ~/.claude/skills/windframe

```

## Windframe MCP

Windframe MCP is the server behind the skill. It plugs into MCP-compatible coding agents and gives them access to design tokens, Tailwind styling guidance, components, and style context.

The skill is the local instruction layer. Windframe MCP is the live design-system backend.

Official MCP page: https://windframe.dev/mcp

Windframe MCP endpoint: https://mcp.windframe.dev/mcp


## What's Inside

`SKILL.md` — core Windframe workflow

- when to use the skill
- how the agent should read live MCP resources
- how style and color selection should work
- how to fetch and apply Windframe style context

Reference guides:

- `references/workflow.md` — end-to-end dynamic workflow
- `references/tools.md` — MCP resources, tools, parameters, and errors
- `references/styles.md` — live style and theme selection process
- `references/anti-patterns.md` — mistakes to avoid
