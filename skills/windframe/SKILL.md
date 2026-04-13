---
name: windframe
description: Generate, edit, and convert production-ready UI pages and templates using the Windframe MCP server. Outputs complete HTML with Tailwind CSS — no frameworks, no build steps. Use when the user asks to build landing pages, dashboards, admin panels, marketing sites, SaaS UIs, or any HTML/Tailwind component. Also use when the user asks to change the visual style of an existing HTML design or needs a specific Windframe UI style like Linear UI, ShadCN, or Pandora. Handles installation and authentication automatically if the server is not yet connected.
version: 1.0.0
user-invocable: true
argument-hint: "[generate|edit|convert|install]"
---

# Windframe MCP — UI Generation Skill

Generate real, production-grade UI with Tailwind CSS. Not mockups. Not skeleton code. Actual HTML pages you can open in a browser right now.

**The core promise:** Give Windframe a specific description → get complete, styled HTML back. Edit it conversationally. Switch styles instantly.

**Mindset:** The difference between a generic AI-generated page and a great one is almost entirely in the prompt and the style choice. Be specific. Pick the right style for the use case. Iterate deliberately.

---

## Step 0: Check Installation

Before calling any Windframe tool, verify the MCP server is connected.

**Check:** Can you see tools named `generate_design`, `edit_design`, `convert_design_style` in your available tools? If yes — proceed.

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
   → 22 Tailwind palette options — match brand or mood
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

**Specificity checklist before calling `generate_design`:**
- [ ] Page type named explicitly
- [ ] Sections listed (hero, features, pricing, etc.)
- [ ] Headline copy direction or actual headline provided
- [ ] CTA text and hierarchy specified
- [ ] Key content counts given (3 features, 4 testimonials, 2 pricing tiers)
- [ ] Any brand constraints mentioned

---

## Step 3: Select Style and Theme

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

For theme: pick from the 22 Tailwind palette names. When in doubt — `slate` for neutral/professional, `blue` for trust/SaaS, `violet` for creative, `rose` for warm/consumer.

---

## Step 4: Generate

Call `generate_design` with a rich prompt, chosen style, and theme.

```json
{
  "tool": "generate_design",
  "arguments": {
    "prompt": "Your specific, detailed description",
    "uiStyle": "Default",
    "theme": "blue"
  }
}
```

**What comes back:**
- `html` — Complete, self-contained HTML file with Tailwind CDN included. Open directly in a browser.
- `fonts` — (Enterprise UI only) Font metadata
- `notice` — Free plan notice (expected, not an error)
- `warning` — Low-credit alert (prompt user to top up at https://windframe.dev/credits)

**Save the HTML immediately.** You'll pass it to `edit_design` for all subsequent changes.

> Full parameter docs and advanced examples → [references/tools.md](references/tools.md)

---

## Step 5: Iterate with Edit

Don't regenerate from scratch when something needs changing. Use `edit_design` — it preserves the visual language of the existing design while applying targeted changes.

```json
{
  "tool": "edit_design",
  "arguments": {
    "html": "<!-- current HTML from previous step -->",
    "prompt": "Add a dark mode toggle to the navbar. Make the hero CTA button larger with more padding.",
    "chatHistory": [
      {
        "type": "generation",
        "prompt": "Original generation prompt",
        "result": "<!-- original generated HTML -->"
      }
    ]
  }
}
```

**`chatHistory` is required** — it gives the model context on what was built and why. Without it, edits may not cohere with the original design intent.

**Build up chatHistory across a session:**

```
Turn 1: generate_design → html_v1
  chatHistory: []   (empty on first generation)

Turn 2: edit_design(html_v1, "add testimonials") → html_v2
  chatHistory: [{ type: "generation", prompt: "...", result: html_v1 }]

Turn 3: edit_design(html_v2, "change CTA color to green") → html_v3
  chatHistory: [
    { type: "generation", prompt: "...", result: html_v1 },
    { type: "edit", prompt: "add testimonials", result: html_v2 }
  ]
```

Each edit appends a new entry to chatHistory for the next call.

> Edit patterns, common requests, and chatHistory structure → [references/tools.md](references/tools.md)

---

## Step 6: Convert Style (Optional)

Want to see the same design in a different visual style or color scheme? Use `convert_design_style`. No need to regenerate or re-prompt.

```json
{
  "tool": "convert_design_style",
  "arguments": {
    "html": "<!-- current HTML -->",
    "uiStyle": "Pandora UI",
    "theme": "slate"
  }
}
```

Use this when:
- The user wants to explore style options (show 2 variants)
- The initial style choice wasn't right
- Switching from a light to dark aesthetic

---

## Quality Gates

Before presenting output to the user, check:

- [ ] **Opens in browser?** The HTML is self-contained with Tailwind CDN — it should render without any build step
- [ ] **All sections present?** Verify every section from the brief appears in the output
- [ ] **No placeholder content?** Real copy, not "Lorem ipsum" or "[Insert text here]"
- [ ] **Style coherent?** The visual style matches the brief (dark vs light, minimal vs bold)
- [ ] **CTAs are actionable?** Buttons have text, links make sense contextually
- [ ] **Free plan notice?** If present in the response, surface it to the user — it's informational, not an error

---

## Resources

| File | When to read |
|------|-------------|
| [references/tools.md](references/tools.md) | Full parameter specs, advanced tool usage, chatHistory patterns |
| [references/styles.md](references/styles.md) | Style decision tree, theme pairing, when to use which style |
| [references/anti-patterns.md](references/anti-patterns.md) | What goes wrong and how to avoid it |
| [references/workflow.md](references/workflow.md) | Complete end-to-end worked example (SaaS landing page) |

---

## Available Resources (MCP)

The server exposes two read-only resources — call these to get current data programmatically:

- **`windframe://styles`** — JSON object with all 5 styles, descriptions, and best-use cases
- **`windframe://themes`** — JSON array of all 22 color themes with Tailwind prefix names
