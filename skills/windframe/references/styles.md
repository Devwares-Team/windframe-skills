# Windframe MCP — Style & Theme Selection Guide

Choosing the wrong style is the single most common mistake. A great prompt with the wrong style produces a technically correct but tonally wrong result. Read this before every `generate_design` or `convert_design_style` call.

---

## The 7 UI Styles

### Default
**Character:** Modern, minimal, confident. Dual light/dark mode. Gradient blob backgrounds. Generous whitespace. Neutral palette with a colored accent.

**Visual signatures:**
- Soft gradient blobs in hero sections
- Cards with subtle shadows, not borders
- Large headings with normal-weight subtext
- Dual-mode (light/dark) layout patterns
- Smooth transitions and hover states

**Best for:**
- SaaS product landing pages
- Marketing and conversion pages
- General-purpose company websites
- Product homepages for B2B tools

**Not ideal for:**
- Pure developer tools (feels too "marketing")
- Anything that needs to look like a native app (no component primitives)
- Maximalist or editorial aesthetics

**Theme pairing:** `blue`, `indigo`, `violet` for tech/SaaS. `emerald`, `teal` for health/sustainability. `slate` for enterprise/neutral.

---

### Linear UI
**Character:** Uncompromisingly dark. Pure black (`bg-[#08090a]`). Inter font everywhere. Blurred glass effects. 3D perspective on UI cards. Engineered, not designed.

**Visual signatures:**
- Near-black background, never dark gray
- Monospace accents for code or terminal elements
- Custom drop shadows — not Tailwind defaults
- Animated elements with blur/backdrop-filter
- Bold but restrained — no unnecessary decoration

**Best for:**
- Developer tools, CLIs, APIs
- Technical documentation pages
- Dev productivity software (editor plugins, linters, CI tools)
- Products where the primary buyer is an engineer

**Not ideal for:**
- Consumer apps or B2C
- Anything with warm tones or natural imagery
- Healthcare, finance, or formal enterprise (trust signals differ)

**Theme pairing:** `slate`, `zinc`, `neutral`. Avoid warm colors — they fight the aesthetic. A subtle `indigo` or `cyan` accent works.

---

### ShadCN UI
**Character:** Mathematical precision. Component-first. Looks like it came out of a mature design system. Focus rings, semantic color tokens, consistent radius. Feels like a real app, not a landing page.

**Visual signatures:**
- Consistent 4px/8px/16px spacing grid — nothing feels arbitrary
- Rounded corners with a specific radius (not "rounded-full", not sharp)
- Subtle borders rather than shadows for card edges
- Focus states on every interactive element
- Typography that's readable, not decorative

**Best for:**
- Admin panels and internal dashboards
- SaaS product UI (not marketing page)
- Form-heavy interfaces (settings, onboarding flows)
- Analytics and data visualization pages
- Anything that needs to feel professional and trustworthy

**Not ideal for:**
- Marketing or conversion pages (too utilitarian)
- Bold brand statements (too subtle)
- Anything that needs to feel "fun" or "human"

**Theme pairing:** `zinc`, `slate`, `gray` for neutral/professional. `blue` for product SaaS. `emerald` for finance/success states.

---

### Pandora UI
**Character:** High-contrast, unapologetic. Black and white as primary palette. Bold serif or display typography. SVG grid or noise patterns in backgrounds. Gradient blurs as accent. Editorial energy.

**Visual signatures:**
- Heavy use of `black` and `white` as primary colors
- Large, expressive type — often 5xl-9xl for hero headlines
- SVG background patterns (grid, dots, noise)
- Gradient blur orbs as visual accents
- Strong grid — asymmetric layouts are fine

**Best for:**
- Design agencies, creative studios
- Portfolios and case study sites
- Bold marketing campaigns
- Startups that want to look different from every other SaaS
- Products in design, creative, or art-adjacent spaces

**Not ideal for:**
- Enterprise B2B (too bold, too risky)
- Apps with lots of content to read
- Anything needing warm, approachable tone
- Forms or utility-heavy pages

**Theme pairing:** `slate`, `neutral`, or `zinc` — Pandora's high-contrast B&W aesthetic means the theme color shows up in accents only. A bright accent (`rose`, `amber`, `lime`) can punch through effectively.

---

### Autumn
**Character:** Warm, seasonal, cozy. Amber, orange, and rust tones. Organic and soft — the opposite of cold tech.

**Visual signatures:**
- Warm palette — amber, orange, yellow, terracotta
- Softer, rounder shapes
- Warm background washes (not white, not pure black)
- Earthy, natural imagery cues

**Best for:**
- Seasonal campaign pages
- Food, hospitality, lifestyle brands
- E-commerce with a warm/natural brand identity
- Event pages for seasonal occasions

**Not ideal for:**
- Tech products (tonally incongruent)
- Anything needing to feel modern or minimal
- Professional/enterprise contexts

**Theme pairing:** `amber`, `orange`, `yellow`, `stone`. Avoid cold themes (`blue`, `cyan`, `slate`) — they fight the warmth.

---

### Enterprise
**Character:** Refined, quiet, precision-first. Roobert typography. `#FAFAFA` canvas in light mode, near-black in dark. The interface chrome stays invisible so dashboards, workflows, and data tables can breathe. Looks like it was designed by an internal platform team that cares deeply about density and parity.

**Visual signatures:**
- `#FAFAFA` / `#0A0A0A` canvas — never pure white, never pitch black
- Roobert font family — structured and confident, not decorative
- `tracking-[-0.01em]` headings — tight but not cramped
- Border-led hierarchy for controls: thin `#E0E0E0` / `#333` dividers replace shadows
- Fine-grain 8px micro-grid for controls; 24–40px section spacing for breathability
- Crisp light/dark parity — same layout, same density in both modes

**Best for:**
- B2B SaaS product UIs (not marketing pages)
- Internal tools and operational dashboards
- Workflow-heavy applications (project management, CRM, ERP)
- Any product where the content IS the UI — no room for hero decoration
- Compliance, finance, or healthcare tools where trust is implied through restraint

**Not ideal for:**
- Consumer-facing marketing pages (too utilitarian)
- Creative or brand-heavy contexts (no personality surface area)
- Anything needing warmth or expressiveness

**Distinctive trait:** Where ShadCN UI looks like a component library demo, Enterprise looks like a shipped product. The difference is in density, font choice (Roobert vs generic sans), and the `#FAFAFA` off-white that signals "this was designed, not bootstrapped."

**Theme pairing:** `zinc`, `slate`, `neutral`, `gray`. Enterprise's palette is intentionally achromatic — the theme color shows up only in action states and status indicators. Avoid warm themes; they fight the neutral-first philosophy.

---

### Notion
**Character:** Content is the UI. Pure neutral palette — no brand color competes with the content. Bold Inter headings, soft border hierarchy, doodle-style illustrations, and black primary CTAs. Reads immediately as familiar and professional, like opening a well-kept Notion workspace.

**Visual signatures:**
- `bg-neutral-900` / `dark:bg-white` CTA buttons — high-contrast, no color
- Inter Variable, `font-bold tracking-tight` headings — authoritative without being heavy
- `rounded-xl` / `rounded-2xl` cards — softer than ShadCN, less clinical than Linear
- `max-w-screen-lg` / `max-w-5xl` container — content-width thinking, not full-bleed hero
- `shadow-lg` on featured cards; border-led hierarchy everywhere else
- Doodle or hand-drawn style illustration accents — playful without being loud
- `py-16` section spacing — generous, lets content breathe

**Best for:**
- Documentation sites and developer references
- Knowledge bases and wikis
- Blog platforms and writing tools
- Open-source project homepages
- Workspace apps and note-taking tools
- Any content-heavy page where the prose/data IS the product

**Not ideal for:**
- Marketing pages needing strong visual differentiation
- Anything requiring brand color dominance
- Dashboards or admin panels (too content-first, not app-first)
- Bold agency sites (too quiet)

**Distinctive trait:** Doodle illustrations. No other Windframe style uses hand-drawn imagery. When you see them in a prompt response, it confirms the Notion style applied correctly. If you want Notion without the doodles, specify "no illustrations" in the prompt.

**Theme pairing:** `neutral`, `zinc`, `slate`, `gray` — the palette is purely neutral by design. Notion's high-contrast black buttons don't need a theme color. Avoid any warm or vivid theme; the point is that color doesn't fight the content.

---

## Style Decision Tree

```
What are you building?

├── A page to MARKET or SELL a product?
│   ├── Product is a developer tool, API, or CLI?
│   │   └── → Linear UI
│   ├── Brand needs to STAND OUT, bold identity?
│   │   └── → Pandora UI
│   └── Standard SaaS, company site, general marketing?
│       └── → Default
│
├── A functional APP interface (not marketing)?
│   ├── Dense operational tool, workflow or data-heavy?
│   │   └── → Enterprise
│   ├── Dashboard, admin panel, settings, forms?
│   │   └── → ShadCN UI
│   └── Developer-facing tool UI (dark by default)?
│       └── → Linear UI
│
├── A CONTENT-FIRST site (docs, wiki, knowledge base)?
│   └── → Notion
│
├── A PORTFOLIO or AGENCY site?
│   └── → Pandora UI
│
└── SEASONAL or LIFESTYLE content?
    └── → Autumn
```

---

## Theme Selection Guide

### The 22 themes grouped by character

**Cool / Professional / Tech**
- `slate` — The universal neutral. Works with everything. Safe default.
- `zinc` — Slightly warmer than slate. Good for ShadCN UI.
- `gray` — Neutral, forgettable. Use when theme shouldn't matter.
- `neutral` — Completely achromatic. Minimal and precise.
- `stone` — Warm-neutral. Good for earthy/organic contexts.

**Blue family (trust, reliability, SaaS)**
- `sky` — Lighter, airy. Consumer-friendly.
- `blue` — The SaaS default. Competent, professional, safe.
- `cyan` — Technical, cool. Works well with dark themes.
- `teal` — Blue-green. Health, environment, calm.

**Purple family (creativity, premium, modern)**
- `indigo` — Premium tech. Slightly warmer than blue.
- `violet` — Creative, modern. Startup energy.
- `purple` — Bold. Fashion, premium, luxury.
- `fuchsia` — Vibrant. Consumer apps, creative tools.

**Green family (growth, health, success)**
- `lime` — Energetic. Young brands, sustainability.
- `green` — Nature, health, environment.
- `emerald` — Finance, success states, premium green.

**Warm family (energy, action, warmth)**
- `yellow` — Attention-grabbing. Use sparingly.
- `amber` — Warm, confident. Pairs well with Autumn style.
- `orange` — Energetic. Consumer, food, lifestyle.
- `red` — Urgency, strength. Use intentionally.
- `rose` — Warm, approachable. Consumer-friendly tech.
- `pink` — Playful, feminine. Fashion, beauty, consumer.

### Quick pairing matrix

| Style | Recommended themes | Avoid |
|-------|-------------------|-------|
| Default | `blue`, `indigo`, `emerald`, `slate` | `yellow`, `pink` |
| Linear UI | `slate`, `zinc`, `neutral`, `cyan` | `orange`, `pink`, `yellow` |
| ShadCN UI | `zinc`, `slate`, `blue`, `emerald` | `fuchsia`, `pink`, `lime` |
| Pandora UI | `slate`, `neutral` + one bold accent | *Any* — accent only |
| Autumn | `amber`, `orange`, `stone` | `blue`, `cyan`, `slate` |
| Enterprise | `zinc`, `slate`, `neutral`, `gray` | `orange`, `pink`, `lime` |
| Notion | `neutral`, `zinc`, `slate`, `gray` | `orange`, `pink`, `fuchsia` |

---

## When the User Doesn't Specify a Style

Ask before generating if the project context doesn't make the choice obvious. One question is enough:

> "What style fits best — clean SaaS marketing (Default), dark developer-grade (Linear UI), component-precise (ShadCN UI), bold editorial (Pandora UI), warm seasonal (Autumn), refined enterprise product (Enterprise), or content-first workspace (Notion)?"

If asking is impractical (batch generation, automated context), apply these defaults:

- Landing page + no style specified → **Default** + `blue`
- Dashboard or admin + no style → **ShadCN UI** + `zinc`
- Dense operational / workflow app + no style → **Enterprise** + `zinc`
- Developer tool + no style → **Linear UI** + `slate`
- Docs site or knowledge base + no style → **Notion** + `neutral`
- Portfolio or agency + no style → **Pandora UI** + `neutral`

---

## Style Coherence — Don't Mix

Each style has a strong internal logic. When writing prompts for a given style, align your prompt's aesthetic cues with the style's character:

**For Linear UI:** Use words like "monospace", "terminal", "API docs", "dark", "minimal". Avoid "colorful", "bright", "illustration".

**For Pandora UI:** Use "bold", "editorial", "full-bleed", "large type". Avoid "subtle", "small", "delicate".

**For ShadCN UI:** Use "dashboard", "panel", "table", "form", "sidebar". Avoid "hero", "blob", "gradient background".

**For Default:** You can use "hero", "gradient", "light/dark", "marketing", "conversion". Most prompts work well here.

**For Enterprise:** Use "operational", "workflow", "dense", "Roobert font", "border-led". Avoid "hero", "marketing", "bold colors".

**For Notion:** Use "content-first", "neutral", "doodle illustrations", "black CTAs", "workspace". Avoid "hero", "marketing", "colorful".

**For Autumn:** Use "warm", "seasonal", "cozy", "earthy". Avoid anything that sounds cold or technical.
