# Windframe MCP — End-to-End Workflow Example

A complete walkthrough: SaaS project management tool landing page, from blank slate to iterated final output. Follow this as a template for any generation session.

---

## The Brief

> "Build a landing page for **TaskFlow** — a project management SaaS targeting small engineering teams (5-20 people). The brand is clean, modern, trustworthy. We want users to sign up for a free trial."

This is a real-world brief. Vague in some places, specific in others. Here's how to work through it.

---

## Step 1: Clarify and Expand the Brief

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

## Step 2: Select Style and Theme

**Style decision:**
- SaaS marketing page → Default ✅
- Not a developer tool (no Linear UI)
- Not a dashboard (no ShadCN UI)
- Not bold/editorial agency site (no Pandora UI)

**Theme decision:**
- Trust + reliability + B2B SaaS → `blue` ✅

---

## Step 3: Write the Generation Prompt

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

## Step 4: Call `generate_design`

```json
{
  "tool": "generate_design",
  "arguments": {
    "prompt": "A SaaS landing page for TaskFlow... [full prompt above]",
    "uiStyle": "Default",
    "theme": "blue"
  }
}
```

**Store the result:**
```javascript
const v1 = response.html;
const chatHistory = [
  {
    type: "generation",
    prompt: "A SaaS landing page for TaskFlow...",
    result: v1
  }
];
```

**Check response:**
- `html` present? ✅
- `notice` field? If yes, surface it to the user: "Note: you're on the free plan."
- `warning` field? If yes: "You're running low on credits — top up at https://windframe.dev/credits"

---

## Step 5: Review and Plan Edits

User opens the HTML in browser. They say:

> "The hero is good. Can you make the features section more visual — maybe with icons? And I want the pricing table to have a monthly/annual toggle."

These are two focused, clear requests. Handle them in separate edit calls for cleaner output.

---

## Step 6: First Edit — Features Icons

```json
{
  "tool": "edit_design",
  "arguments": {
    "html": "<!-- v1 html -->",
    "prompt": "Update the features section: add an SVG icon before each feature title. Use a Heroicons-style outline icon — a checklist/clipboard icon for Sprint Planning, a code-branch/git icon for GitHub Integration, a chart/trending-up icon for Velocity Charts. Make the icons blue-500, 32x32px.",
    "chatHistory": [
      {
        "type": "generation",
        "prompt": "A SaaS landing page for TaskFlow...",
        "result": "<!-- v1 html -->"
      }
    ]
  }
}
```

**Store the result:**
```javascript
const v2 = response.html;
chatHistory.push({
  type: "edit",
  prompt: "Update the features section: add SVG icons...",
  result: v2
});
```

---

## Step 7: Second Edit — Pricing Toggle

```json
{
  "tool": "edit_design",
  "arguments": {
    "html": "<!-- v2 html -->",
    "prompt": "Add a monthly/annual billing toggle above the pricing cards. When 'Annual' is selected, show the discounted prices (Free stays free, Pro becomes $14/user/mo, Team becomes $11/user/mo) and add a 'Save 26%' badge on annual. Implement with a CSS/JS toggle — no React, no build tools. Default to Monthly.",
    "chatHistory": [
      {
        "type": "generation",
        "prompt": "A SaaS landing page for TaskFlow...",
        "result": "<!-- v1 html -->"
      },
      {
        "type": "edit",
        "prompt": "Update the features section: add SVG icons...",
        "result": "<!-- v2 html -->"
      }
    ]
  }
}
```

**Store the result:**
```javascript
const v3 = response.html;
chatHistory.push({
  type: "edit",
  prompt: "Add a monthly/annual billing toggle...",
  result: v3
});
```

---

## Step 8: Style Variant (Optional)

User says: "I want to see what it would look like in a darker, more developer-y style."

Use `convert_design_style` — same content, different aesthetic:

```json
{
  "tool": "convert_design_style",
  "arguments": {
    "html": "<!-- v3 html -->",
    "uiStyle": "Linear UI",
    "theme": "slate"
  }
}
```

Present both variants. Let the user choose. If they pick the dark variant, continue editing from there — update `chatHistory` with a new `conversion` entry:

```javascript
const v3_dark = response.html;
// If user picks this, start a new chatHistory or append:
chatHistory.push({
  type: "conversion",
  result: v3_dark
});
```

---

## Step 9: Final Delivery

Before handing over the HTML:

**Quality checklist:**
- [ ] Opens in browser without a server
- [ ] Hero with both CTAs present
- [ ] 3 feature cards with icons (after edit)
- [ ] Pricing toggle works (monthly/annual)
- [ ] FAQ accordion expands/collapses
- [ ] Footer links present
- [ ] No "Lorem ipsum" or `[placeholder]` text
- [ ] `<html>`, `<head>` with Tailwind CDN, `<body>` all present
- [ ] Free plan notice surfaced to user (if present in response)

**Deliver as:**
- Inline in the conversation (paste HTML into a code block)
- Saved as a `.html` file the user can open directly
- Or both — paste a preview of the structure, offer to save the file

---

## Full chatHistory at End of Session

```json
[
  {
    "type": "generation",
    "prompt": "A SaaS landing page for TaskFlow...",
    "result": "<!-- v1 full html -->"
  },
  {
    "type": "edit",
    "prompt": "Update the features section: add SVG icons...",
    "result": "<!-- v2 full html -->"
  },
  {
    "type": "edit",
    "prompt": "Add a monthly/annual billing toggle...",
    "result": "<!-- v3 full html -->"
  }
]
```

If a style conversion happened and was accepted, the chain continues with a `"type": "conversion"` entry.

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
Style: Default, theme: blue.
```

### Developer Tool Homepage
```
[Tool name] homepage for [developer audience].
Hero: dark background, monospace headline "[tagline]", install command in a code block,
single CTA "[CTA text]". Features: [N] items with code snippets. GitHub star count.
[Documentation | Quick start] section. Style: Linear UI, theme: slate.
```

### Admin Dashboard
```
Admin panel for [product]. Top navbar with logo, search, user avatar dropdown.
Left sidebar: [nav items]. Main content: [N] stat cards ([metric 1], [metric 2], ...),
[chart type] for [data], [table name] table with [columns] and pagination.
Style: ShadCN UI, theme: zinc.
```

### Agency / Portfolio
```
[Agency name] homepage. Full-bleed hero: large serif headline "[headline]", 
single CTA "[CTA]". Work grid: [N] case studies with cover, client, category tag.
About: [brief]. Contact form. Style: Pandora UI, theme: neutral.
```

### Pricing Page
```
Standalone pricing page for [product]. Header: "[headline about pricing]".
Toggle: monthly / annual (annual saves [X]%). [N] pricing tiers:
[tier 1 - free/starter], [tier 2 - pro/growth], [tier 3 - enterprise].
Feature comparison table below. FAQ: [N] questions. CTA banner at bottom.
Style: [match product style], theme: [match product theme].
```
