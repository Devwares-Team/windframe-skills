# Windframe MCP — End-to-End Workflow Example

A complete walkthrough: SaaS project management tool landing page, from blank slate to iterated final output. Follow this as a template for any generation session.

---

## The Brief

> "Build a landing page for **TaskFlow** — a project management SaaS targeting small engineering teams (5-20 people). The brand is clean, modern, trustworthy. We want users to sign up for a free trial."

This is a real-world brief. Vague in some places, specific in others. Here's how to work through it.

---

## Step1: Clarify and Expand the Brief

Before touching any tool, resolve the unknowns. Either ask the user or make defensible assumptions based on context. Document your assumptions.

**What we know:**
- Product: Project management SaaS
- Audience: Small engineering teams (5-20 people)
- Goal: Free trial signups
- Tone: Clean, modern, trustworthy

**Assumptions made (document these):**
- Primary CTA: "Start free trial" (no credit card)
- 3 core features that engineers care about: Sprint planning, GitHub integration, Team velocity charts
- Pricing: Free (up to 5 users), Pro ($19/user/mo), Team ($14/user/mo billed annually)
- No existing brand colors specified → will use blue (trust signal for SaaS)

**Generation brief:**
> "I'm building a **SaaS landing page** for **small engineering teams** using **Default** style with a **blue** color theme. Key sections: Hero with free trial CTA, 3-feature grid, social proof (logos), pricing (3 tiers), FAQ (4 questions), footer."

---

## Step2: Select Style and Theme

**Style decision:**
- SaaS marketing page → Default ✅
- Not a developer tool (no Linear UI)
- Not a dashboard (no ShadCN UI)
- Not bold/editorial agency site (no Pandora UI)

**Theme decision:**
- Trust + reliability + B2B SaaS → `blue` ✅

---

## Step3: Write the Generation Prompt

Take the brief and turn it into a rich, specific prompt. Every section gets content direction, not just a section name.

```
A SaaS landing page for TaskFlow, a project management tool for small engineering teams.

HERO: Bold headline "Ship sprints, not excuses". Subheadline: "TaskFlow gives engineering teams 
of 5-20 the sprint planning, GitHub sync, and velocity tracking they actually need — without 
the enterprise bloat." Two CTAs: "Start free trial" (primary blue button, no credit card) and 
"Watch 2-min demo" (ghost/outline button). Small trust line below: "Trusted by 1,200+ teams. 
No setup required."

FEATURES (3-column grid): 
1. Sprint Planning — "Drag-and-drop sprint boards that update in real time. Plan your next 
   sprint in minutes, not meetings."
2. GitHub Integration — "Auto-link PRs to tickets. See commit history, review status, and 
   deploy state without leaving TaskFlow."
3. Velocity Charts — "Understand your team's true pace. Predictable sprints, honest estimates, 
   fewer surprises at standups."

SOCIAL PROOF: "Trusted by teams at" + 5 company logo placeholders (use placeholder rectangles 
or text logos).

PRICING (3 tiers, monthly):
- Free: Up to 5 users, 3 active projects, basic reporting. CTA: "Get started free"
- Pro ($19/user/mo): Unlimited projects, GitHub integration, velocity charts, priority support. 
  CTA: "Start free trial"  
- Team ($14/user/mo billed annually): Everything in Pro + admin controls, SSO, SLA. 
  CTA: "Contact sales"
Highlight the Pro tier as recommended.

FAQ (4 questions):
1. "Do I need a credit card to start?" — No, free plan requires no payment info.
2. "Can I import from Jira or Linear?" — Yes, one-click import available.
3. "What happens when my team grows past 5 users?" — Automatically prompts to upgrade.
4. "Is my data secure?" — SOC 2 Type II certified, data encrypted at rest and in transit.

FOOTER: Logo, tagline "Built for builders", nav links (Product, Pricing, Docs, Blog, Status), 
legal links, "© 2024 TaskFlow Inc".
```

---

## Step4: Call `fetch_style_design_context`

```json
{
  "tool": "fetch_style_design_context",
  "arguments": {
    "prompt": "A SaaS landing page for TaskFlow... [full prompt above]",
    "uiStyle": "Default",
    "primaryColor": "blue"
  }
}
```

**What comes back:**
```json
{
  "status": "ready",
  "resource_uri": "windframe://style-context/1234567890-abc123",
  "message": "Style context is ready. Access the full context via the resource URI above."
}
```

**Read the resource:**
```
Read the resource at windframe://style-context/1234567890-abc123
```

The resource contains the style context in JSON format. Use this context to generate the UI in the project's framework (React, Vue, Svelte, etc.).

---

## Step5: Generate Framework-Specific UI

Using the style context from the resource, generate the UI code in the project's framework:

- **React:** Create components with Tailwind classes
- **Vue:** Create SFCs with Tailwind classes  
- **Svelte:** Create components with Tailwind classes
- **HTML:** Generate static HTML with Tailwind CDN (if no framework)

---

## Step6: Style Variant (Optional)

User says: "I want to see what it would look like in a darker, more developer-y style."

Use `fetch_style_conversion_context` — same content, different aesthetic:

```json
{
  "tool": "fetch_style_conversion_context",
  "arguments": {
    "prompt": "Convert this landing page to a dark developer-friendly style",
    "uiStyle": "Linear UI",
    "primaryColor": "slate"
  }
}
```

Present both variants. Let the user choose. Generate the framework-specific code from the new style context.

---

## Pattern Library — Common Page Recipes

Use these as starting prompt frameworks for recurring page types:

### SaaS Landing Page
```
[Product name] landing page for [audience].
Hero: "[headline]", subheadline about [value prop], CTA "[primary]" and "[secondary]".
Features: [N]-column grid — [feature 1], [feature 2], [feature 3].
Social proof: logos from [companies or count].
Pricing: [tier 1 ($X/mo)], [tier 2 ($Y/mo)], [tier 3 (custom/enterprise)].
FAQ: [N] questions. Footer with [links].
Style: Default, primaryColor: blue.
```

### Developer Tool Homepage
```
[Tool name] homepage for [developer audience].
Hero: dark background, monospace headline "[tagline]", install command in a code block,
single CTA "[CTA text]". Features: [N] items with code snippets. GitHub star count.
[Documentation | Quick start] section. Style: Linear UI, primaryColor: slate.
```

### Admin Dashboard
```
Admin panel for [product]. Top navbar with logo, search, user avatar dropdown.
Left sidebar: [nav items]. Main content: [N] stat cards ([metric 1], [metric 2], ...),
[chart type] for [data], [table name] table with [columns] and pagination.
Style: ShadCN UI, primaryColor: zinc.
```

### Agency / Portfolio
```
[Agency name] homepage. Full-bleed hero: large serif headline "[headline]", 
single CTA "[CTA]". Work grid: [N] case studies with cover, client, category tag.
About: [brief]. Contact form. Style: Pandora UI, primaryColor: neutral.
```

### Pricing Page
```
Standalone pricing page for [product]. Header: "[headline about pricing]".
Toggle: monthly / annual (annual saves [X]%). [N] pricing tiers:
[tier 1 - free/starter], [tier 2 - pro/growth], [tier 3 - enterprise].
Feature comparison table below. FAQ: [N] questions. CTA banner at bottom.
Style: [match product style], primaryColor: [match product theme].
```
