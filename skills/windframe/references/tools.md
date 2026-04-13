# Windframe MCP — Tool Reference

Complete parameter specs, usage patterns, and examples for all three Windframe tools.

---

## Tool: `generate_design`

**When to use:** Starting a new design from scratch. First call in any session.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `prompt` | string | ✅ | Detailed description of the UI to generate |
| `uiStyle` | enum | ✅ | Visual style — one of 5 options (see below) |
| `theme` | string | ✅ | Tailwind color name (e.g. `"blue"`, `"slate"`, `"rose"`) |

**`uiStyle` values:** `"Default"` | `"Linear UI"` | `"ShadCN UI"` | `"Pandora UI"` | `"Autumn"` | `"Enterprise"` | `"Notion"`

### Response Shape

```json
{
  "html": "<html>...</html>",
  "fonts": ["Inter", "Poppins"],
  "notice": "You are on a free plan. Visit https://windframe.dev/pricing...",
  "warning": "You have 2 credits remaining. Top up at https://windframe.dev/credits"
}
```

| Field | Always present? | Notes |
|-------|----------------|-------|
| `html` | ✅ | Complete self-contained HTML — open directly in browser |
| `fonts` | ❌ | Only on Enterprise UI style — font stack used |
| `notice` | ❌ | Free plan users only — informational, not an error |
| `warning` | ❌ | Low credit alert — prompt user to top up |

### Example Calls

**Landing page (Default style):**
```json
{
  "prompt": "A SaaS analytics platform landing page. Hero: headline 'Turn data into decisions', subheadline about real-time dashboards, CTA 'Start free trial'. Features: 3-column grid — Live dashboards, AI insights, Team sharing. Social proof: logos of 5 companies. Pricing: Free (3 dashboards), Pro ($49/mo, unlimited), Enterprise (custom). Footer with links.",
  "uiStyle": "Default",
  "theme": "indigo"
}
```

**Developer tool (Linear UI style):**
```json
{
  "prompt": "A CLI tool homepage for developers. Dark hero with monospace font, terminal animation showing the tool in action, single CTA 'npm install -g mytool'. Below: 3 feature blocks with code snippets. GitHub star count badge. Installation guide section.",
  "uiStyle": "Linear UI",
  "theme": "slate"
}
```

**Admin dashboard (ShadCN UI):**
```json
{
  "prompt": "An admin panel dashboard page. Top navbar with logo, search, user avatar. Left sidebar with nav links: Overview, Users, Orders, Products, Settings. Main area: 4 stat cards (revenue, orders, users, conversion rate), a line chart for monthly revenue, a recent orders table with status badges and pagination.",
  "uiStyle": "ShadCN UI",
  "theme": "zinc"
}
```

**Bold agency homepage (Pandora UI):**
```json
{
  "prompt": "A design agency homepage. Full-bleed hero with large black serif headline 'We make things people remember', white background, single 'See our work' CTA. Case studies grid: 6 projects with cover images, client name, category tag. About section: team photo + manifesto text. Contact form.",
  "uiStyle": "Pandora UI",
  "theme": "slate"
}
```

**Operational B2B product UI (Enterprise):**
```json
{
  "prompt": "A project management SaaS product page — not a marketing page, the actual app UI. Top navbar: logo, global search, notifications bell, user avatar. Left sidebar: nav items for Dashboard, Projects, Tasks, Team, Reports, Settings. Main content: dashboard overview with 4 stat cards (active projects, open tasks, overdue, team velocity), a Gantt-style timeline for the current sprint, and a recent activity feed. Dense, information-first layout.",
  "uiStyle": "Enterprise",
  "theme": "zinc"
}
```

**Content-first workspace or docs site (Notion):**
```json
{
  "prompt": "A knowledge base homepage for an open-source developer tool. Hero: bold Inter headline 'Everything you need to build with X', subheadline about the docs, black CTA 'Read the docs' and ghost CTA 'View on GitHub'. Feature blocks: 3 content-first cards — Quick start, API Reference, Examples — with border-led hierarchy and doodle-style illustrations. Recent changelog section as a simple list. Footer with links.",
  "uiStyle": "Notion",
  "theme": "neutral"
}
```

---

## Tool: `edit_design`

**When to use:** Modifying an existing design. Always prefer this over regenerating — it preserves visual coherence.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `html` | string | ✅ | The current HTML to be modified |
| `prompt` | string | ✅ | Description of the changes to apply |
| `chatHistory` | array | ✅ | Conversation history from all previous calls |

### chatHistory Structure

Each entry in `chatHistory` represents one previous generation or edit:

```typescript
type ChatHistoryEntry = {
  type: "generation" | "edit" | "conversion";
  prompt?: string;     // The prompt used in that step
  result: string;      // The HTML output from that step
}
```

**CRITICAL:** `chatHistory` is required. Passing an empty array `[]` on edit calls causes the model to lose design context — edits may conflict with the existing visual language.

### Building chatHistory Across a Session

Track the full chain — each entry is the result of the *previous* call:

```
Session start:
  chatHistory = []

After generate_design(prompt="landing page", html=v1):
  chatHistory = [
    { type: "generation", prompt: "landing page", result: v1 }
  ]

After edit_design(html=v1, prompt="add testimonials", html=v2):
  chatHistory = [
    { type: "generation", prompt: "landing page", result: v1 },
    { type: "edit", prompt: "add testimonials", result: v2 }
  ]

After edit_design(html=v2, prompt="change nav to sticky", html=v3):
  chatHistory = [
    { type: "generation", prompt: "landing page", result: v1 },
    { type: "edit", prompt: "add testimonials", result: v2 },
    { type: "edit", prompt: "change nav to sticky", result: v3 }
  ]
```

After `convert_design_style`, use `type: "conversion"`.

### Example Calls

**Add a section:**
```json
{
  "html": "<!-- current HTML -->",
  "prompt": "Add a testimonials section below the features grid. Include 3 testimonials with avatar placeholder, name, job title, and 2-sentence quote. Use card layout with subtle shadow.",
  "chatHistory": [
    { "type": "generation", "prompt": "SaaS landing page...", "result": "<!-- v1 html -->" }
  ]
}
```

**Modify existing elements:**
```json
{
  "html": "<!-- current HTML -->",
  "prompt": "Make the hero section full-screen height. Add a subtle animated gradient background. Increase the headline font size. Add a secondary ghost button 'Watch demo' next to the primary CTA.",
  "chatHistory": [...]
}
```

**Layout and spacing tweaks:**
```json
{
  "html": "<!-- current HTML -->",
  "prompt": "Increase the spacing between all sections. Make the features grid 2 columns on mobile, 4 on desktop. Add a subtle top border to the footer.",
  "chatHistory": [...]
}
```

**Copy and content changes:**
```json
{
  "html": "<!-- current HTML -->",
  "prompt": "Change the headline to 'Build faster, ship smarter'. Update the pricing section: rename tiers to Starter, Growth, Enterprise. Add annual/monthly billing toggle above pricing cards.",
  "chatHistory": [...]
}
```

### Edit Prompt Quality

Apply the same specificity discipline as generation prompts:

❌ "Make it look better"
✅ "Increase headline font weight to 800. Add 8px letter-spacing to section labels. Give feature cards a 1px border instead of shadow."

❌ "Fix the colors"
✅ "Change the primary CTA from blue to emerald. Make the hero background a dark gradient from slate-900 to slate-800. Lighten the body text to slate-400."

---

## Tool: `convert_design_style`

**When to use:** Transforming an existing design into a completely different visual style or color palette — without re-prompting from scratch.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `html` | string | ✅ | The current HTML to convert |
| `uiStyle` | enum | ✅ | Target style |
| `theme` | string | ✅ | Target Tailwind color theme |

### Response Shape

Same as `generate_design` — returns `{ html, fonts?, notice?, warning? }`.

### When to Convert vs. When to Regenerate

**Use `convert_design_style` when:**
- The content and structure are right, but the visual style isn't
- User wants to see 2 style variants of the same page
- Switching from light to dark (Default → Linear UI)
- Moving from marketing (Pandora) to product (ShadCN) aesthetic

**Regenerate when:**
- The entire page structure needs to change
- The target style has fundamentally different layout requirements
- The content brief has changed significantly

### Example Calls

**Light → Dark:**
```json
{
  "html": "<!-- existing light Default-style page -->",
  "uiStyle": "Linear UI",
  "theme": "slate"
}
```

**Bold marketing → Clean product:**
```json
{
  "html": "<!-- Pandora UI page -->",
  "uiStyle": "Default",
  "theme": "blue"
}
```

**Explore palette options:**
```json
{
  "html": "<!-- same base design -->",
  "uiStyle": "Default",
  "theme": "rose"
}
```

---

## MCP Resources

Two read-only resources — call these when you need to know what options exist:

### `windframe://styles`

Returns JSON with all styles. Use this to present style options to users or to verify a style name.


### `windframe://themes`

Returns array of all color themes. Use this when you need to present theme options or verify a theme name.

---

## Credits and Plans

Every `generate_design`, `edit_design`, and `convert_design_style` call consumes AI credits.

| Signal | Meaning | Action |
|--------|---------|--------|
| `notice` field in response | Free plan — limits apply | Inform user; link to https://windframe.dev/pricing |
| `warning` field in response | Credits running low | Prompt user to top up at https://windframe.dev/credits |
| Auth error | Token expired or invalid | Re-authenticate via `claude mcp add` or restart Claude Code |

Credits are scoped to the user's Windframe account — not the client application. Each authenticated user has their own credit balance.
