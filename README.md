# Windframe Agent Skills

[Windframe](https://sleek.design) Agent Skill helps your coding agent build better web interfaces by fetching live design-system context from the Windframe API authenticated HTTP endpoints on `https://mcp.windframe.dev/api` with a Windframe API key.

Use it for creating and restyling existing web interfaces.

## What It Does

Windframe Skill gives your agent a workflow for UI generation:

- fetches the current Windframe style catalog from the API
- recommends useful style and color directions based on your request
- fetches design context for the selected style and primary color
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

## Windframe API and Its Requirements
Base URL:

```text
https://mcp.windframe.dev/api
```

Endpoints:

- `GET /ui-styles` returns the currently available Windframe UI styles.
- `POST /design-context` returns style context for a prompt, `uiStyle`, and `primaryColor`.

Requirents:
- A [Windframe](https://windframe.dev) account on the Pro or Team plan
- An API key created on [your account page](https://app.winframe.dev/account), stored in the `WINDFRAME_API_KEY` environment variable. Full keys are shown only once when created, so store them securely.


## What's Inside

`SKILL.md` - core Windframe API workflow

- when to use the skill
- how the agent should call Windframe API endpoints
- how style and color selection should work
- how to fetch and apply Windframe style context

Reference guides:

- `references/workflow.md` - end-to-end API workflow
- `references/tools.md` - API endpoints, request bodies, responses, and errors
- `references/styles.md` - live style and color selection process
- `references/anti-patterns.md` - mistakes to avoid
