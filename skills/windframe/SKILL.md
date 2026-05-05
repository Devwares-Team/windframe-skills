---
name: windframe
description: Generate and convert UI components and pages using the Windframe MCP server. Returns style context for the MCP client to generate production-ready UI in your project's framework (React, Vue, Svelte, etc.) using Tailwind CSS. Use when the user asks to build landing pages, dashboards, admin panels, marketing sites, SaaS UIs, or any UI component. Also use when the user asks to change the visual style of an existing design or needs a specific Windframe UI style like Linear UI, ShadCN, or Pandora. Handles installation and authentication automatically if the server is not yet connected.
version: 1.0.0
user-invocable: true
argument-hint: "[generate|convert|install]"
---

# Windframe MCP — UI Generation Skill

Generate real, production-grade UI with Tailwind CSS in your project's framework. The MCP server provides style context; the MCP client handles the actual UI generation.

**The core promise:** Give Windframe a specific description → get style context back → MCP client generates framework-specific UI code.

**Mindset:** The difference between a generic AI-generated page and a great one is almost entirely in the prompt and the style choice. Be specific. Pick the right style for the use case. Iterate deliberately.

---

## Step 0: Check Installation

Before calling any Windframe tool, verify the MCP server is connected.

**Check:** Can you see tools named `fetch_style_design_context` and `fetch_style_conversion_context` in your available tools? If yes — proceed.

**If not connected**, install and authenticate:

```bash
# Add the Windframe MCP server (Claude Code CLI)
claude mcp add --transport http windframe-mcp https://mcp.windframe.dev
```

Then restart Claude Code. On first tool call, the OAuth flow triggers automatically — a browser window opens to the Windframe login page. Sign in with your Windframe account (or create one at https://windframe.dev). After login, the token is stored locally and refreshes silently.

**Other clients:**

| Client | Config location | Entry |
|--------|----------------|-------|
| Claude Code | `~/.claude.json` | `"mcpServers": { "windframe-mcp": { "type": "http", "url": "https://mcp.windframe.dev" } }` |
| VS Code | `.vscode/settings.json` | Same structure under `"mcp.servers"` |
| Cursor | `.cursor/mcp.json` | Same structure under `"mcpServers"` |

> **Auth is automatic.** MCP clients handle the full OAuth 2.0 + PKCE flow. You don't exchange tokens manually — just connect the URL and sign in when prompted.

---

## Step 1: Discover — Know What You're Building

Never generate blind. Settle these before picking a tool:

```
1. WHAT is this UI?
   → Page type: landing page, dashboard, admin panel, form, marketing section, component

2. WHO uses it?
   → Audience shapes tone: developers want density + precision, consumers want warmth + clarity

3. WHAT should it contain?
   → Sections, components, copy direction (even rough)

4. WHAT visual style fits?
   → Dark/developer-grade? Minimal/SaaS? Bold/marketing? Warm/seasonal?
   → Read [references/styles.md](references/styles.md) for the full decision tree

5. WHAT color theme?
   → Tailwind color name, "current" for project primary, or custom hex code
   → See [references/styles.md](references/styles.md) § Theme Selection
```

**Output a generation brief:**
> "I'm building a **[PAGE TYPE]** for **[AUDIENCE]** using **[STYLE]** style with a **[THEME]** color theme. Key sections: **[SECTION LIST]**."

---

## Step 2: Write a Specific Prompt

This is the single highest-leverage action. Windframe's output quality scales directly with prompt specificity.

**The gap between bad and good:**

❌ **Bad:** "A landing page for a SaaS product"

✅ **Good:** "A SaaS landing page for a project management tool targeting remote engineering teams. Hero: bold headline 'Ship without the chaos', subheadline about async-first workflows, two CTAs: 'Start free' (primary, blue) and 'Watch demo' (ghost). Features grid (3 columns): AI task routing, real-time presence, GitHub integration — each with icon, title, 2-line description. Testimonials from 3 CTOs. Pricing: Free, Pro ($29/mo), Team ($99/mo) with feature comparison table. Footer with newsletter signup and social links."

**Specificity checklist before calling `fetch_style_design_context`:**
- [ ] Page type named explicitly
- [ ] Sections listed (hero, features, pricing, etc.)
- [ ] Headline copy direction or actual headline provided
- [ ] CTA text and hierarchy specified
- [ ] Key content counts given (3 features, 4 testimonials, 2 pricing tiers)
- [ ] Any brand constraints mentioned

---

## Step 3: Select Style and Theme

**IMPORTANT:** Before calling `fetch_style_design_context`, you MUST read the available styles from `windframe://styles` and themes from `windframe://themes`, present the options to the user, and explicitly ask them to choose a style and theme. Do not assume defaults or proceed without user confirmation.

If `uiStyle` or `primaryColor` are not provided, the server will interactively prompt the user via an elicitation form (if client supports it). Clients that don't support elicitation will receive a `user_input_required` error.

Read [references/styles.md](references/styles.md) for the full guide. Quick reference:

| Style | Best for | Visual character |
|-------|----------|-----------------|
| `Default` | SaaS, marketing, landing pages | Clean dual-mode, gradient blobs, generous spacing |
| `Linear UI` | Dev tools, technical products | Pure dark, Inter font, blur effects, 3D transforms |
| `ShadCN UI` | Dashboards, admin panels, forms | Mathematical spacing, semantic tokens, focus states |
| `Pandora UI` | Agency sites, bold marketing, portfolios | High-contrast B&W, bold type, SVG patterns |
| `Autumn` | Seasonal pages, warm-toned designs | Warm amber/orange palette |
| `Enterprise` | Operational tools, workflow apps, B2B product UI | Roobert font, `#FAFAFA` canvas, border-led, crisp light/dark parity |
| `Notion` | Docs, knowledge bases, content-first sites | Neutral-first, Inter bold headings, doodle images, black CTAs |

For theme: pick a Tailwind color name (e.g., `"blue"`), use `"current"` to inherit the project's primary color, or provide a custom hex code (e.g., `"#3b82f6"`). When in doubt — `slate` for neutral/professional, `blue` for trust/SaaS, `violet` for creative, `rose` for warm/consumer.

---

## Step 4: Fetch Style Context

Call `fetch_style_design_context` with a rich prompt, chosen style, and primary color. This returns a resource URI containing the style context.

```json
{
  "tool": "fetch_style_design_context",
  "arguments": {
    "prompt": "Your specific, detailed description",
    "uiStyle": "Default",
    "primaryColor": "blue"
  }
}
```

**What comes back:**
- `status` — "ready" when context is available
- `resource_uri` — URI to access the style context (e.g., `windframe://style-context/{id}`)
- `message` — Instructions to read the resource

**Read the resource:**
The MCP client should read the resource URI to get the full style context, then use that context to generate the UI in the project's framework.

**Note:** These tools require a Pro plan. Free users receive a `pro_plan_required` error with upgrade link to https://windframe.dev/pricing.

> Full parameter docs and advanced examples → [references/tools.md](references/tools.md)

---

## Step 5: Convert Style (Optional)

Want to see the same design in a different visual style or color scheme? Use `fetch_style_conversion_context`. No need to regenerate or re-prompt.

```json
{
  "tool": "fetch_style_conversion_context",
  "arguments": {
    "prompt": "Convert this dashboard to Enterprise UI",
    "uiStyle": "Enterprise UI",
    "primaryColor": "slate"
  }
}
```

Use this when:
- The user wants to explore style options (show 2 variants)
- The initial style choice wasn't right
- Switching from a light to dark aesthetic

The tool returns a resource URI with the converted style context. The MCP client then generates the UI in the new style.

---

## Quality Gates

Before presenting output to the user, check:

- [ ] **Framework-appropriate?** The generated code should match the project's framework (React, Vue, Svelte, etc.)
- [ ] **All sections present?** Verify every section from the brief appears in the output
- [ ] **No placeholder content?** Real copy, not "Lorem ipsum" or "[Insert text here]"
- [ ] **Style coherent?** The visual style matches the brief (dark vs light, minimal vs bold)
- [ ] **Pro plan check?** If `pro_plan_required` error received, direct user to https://windframe.dev/pricing

---

## Resources

| File | When to read |
|------|-------------|
| [references/tools.md](references/tools.md) | Full parameter specs, advanced tool usage |
| [references/styles.md](references/styles.md) | Style decision tree, theme pairing, when to use which style |
| [references/anti-patterns.md](references/anti-patterns.md) | What goes wrong and how to avoid it |
| [references/workflow.md](references/workflow.md) | Complete end-to-end worked example (SaaS landing page) |

---

## Available Resources (MCP)

The server exposes resources — call these to get current data programmatically:

- **`windframe://styles`** — JSON object with all 7 styles, descriptions, and best-use cases
- **`windframe://themes`** — JSON array of all color themes
- **`windframe://style-context/{id}`** — Style context data for client-side framework generation
