# Windframe MCP — Anti-Patterns & Common Mistakes

What goes wrong, why it goes wrong, and exactly how to fix it.

---

## Prompt Anti-Patterns

### 1. The Vague Brief
The #1 cause of generic output. If your prompt could describe any website, it will produce any website.

❌ **Bad:**
```
"A landing page for a startup"
"A dashboard"
"A company website"
"A pricing page"
```

✅ **Fix:** Name the product, the audience, every section, and key copy direction.
```
"A landing page for a B2B contract management SaaS targeting legal teams at mid-size companies.
Hero: headline 'Sign faster, dispute less', subheadline about e-signature + audit trails,
CTA 'Request a demo'. Features: 4-column grid — Smart templates, e-Signatures, Audit logs,
Team permissions. Testimonials from 2 General Counsels. Pricing: Starter $99/mo, Business
$299/mo, Enterprise (custom). Security badges: SOC 2, GDPR, ISO 27001."
```

---

### 2. Style-Content Mismatch
Choosing a style that fights your content's purpose creates tonal dissonance — the design looks wrong even if technically correct.

❌ **Bad:**
```json
// A consumer recipe app homepage
{ "uiStyle": "Linear UI", "theme": "slate" }
```
Linear UI is built for developer tools. A recipe app will look like a GitHub clone.

```json
// An enterprise admin panel
{ "uiStyle": "Pandora UI", "theme": "slate" }
```
Pandora's bold, editorial style clashes with the precision and utility of an admin interface.

✅ **Fix:** Match style to use case. When in doubt, read [styles.md](styles.md).

| If you're building... | Use |
|----------------------|-----|
| Enterprise admin panel | ShadCN UI |
| Developer tool homepage | Linear UI |
| Bold agency site | Pandora UI |
| SaaS marketing page | Default |
| Seasonal promo page | Autumn |
| Content site or docs | Notion |

---

### 3. Regenerating Instead of Editing
Calling `generate_design` again when only minor changes are needed. This breaks continuity — the new output may look completely different even with the same prompt.

❌ **Bad:**
```
// User says "make the hero bigger"
// Agent calls generate_design again with same prompt
```

✅ **Fix:** Use `edit_design` for all modifications after the first generation. It preserves visual language.

```json
{
  "tool": "edit_design",
  "arguments": {
    "html": "<!-- current HTML -->",
    "prompt": "Make the hero section taller with more vertical padding. Increase headline size to 6xl.",
    "chatHistory": [{ "type": "generation", "prompt": "...", "result": "<!-- v1 -->" }]
  }
}
```

---

### 4. Missing chatHistory on Edit Calls
Passing `chatHistory: []` when calling `edit_design`. Without context, the model doesn't know what was generated before, why it was built that way, or how to maintain visual coherence.

❌ **Bad:**
```json
{
  "tool": "edit_design",
  "arguments": {
    "html": "<!-- html -->",
    "prompt": "Add a testimonials section",
    "chatHistory": []
  }
}
```

✅ **Fix:** Always pass the full chain of history. Every generation and edit appends a new entry:
```json
{
  "chatHistory": [
    { "type": "generation", "prompt": "original prompt", "result": "<!-- v1 html -->" },
    { "type": "edit", "prompt": "previous edit", "result": "<!-- v2 html -->" }
  ]
}
```

---

### 5. Single-Word Theme Mismatches
Using warm themes on cold styles, or cold themes on warm styles. The result looks disjointed.

❌ **Bad:**
```json
// Linear UI with warm orange — fights the cold dark aesthetic
{ "uiStyle": "Linear UI", "theme": "orange" }

// Autumn with cold slate — loses the warmth completely
{ "uiStyle": "Autumn", "theme": "slate" }
```

✅ **Fix:** Match theme temperature to style temperature.

| Cold styles | Cold/neutral themes |
|-------------|-------------------|
| Linear UI | `slate`, `zinc`, `cyan`, `neutral` |
| ShadCN UI | `zinc`, `slate`, `blue` |

| Warm styles | Warm themes |
|------------|------------|
| Autumn | `amber`, `orange`, `stone`, `yellow` |
| Default | `blue`, `indigo`, `emerald` (any) |

---

### 6. Overloading a Single Edit Call
Requesting 10 different changes in one `edit_design` call. This is the edit equivalent of a vague prompt — too many signals cause the model to prioritize some changes and miss others.

❌ **Bad:**
```
"Make the hero bigger, change the color scheme to green, add testimonials,
fix the navbar spacing, add a FAQ section, update all the copy, and make it
mobile responsive"
```

✅ **Fix:** Break large edits into focused calls. 2-4 related changes per call keeps outputs coherent:

```
Call 1: "Make the hero bigger, increase headline size, add a ghost secondary CTA"
Call 2: "Add a testimonials section below features with 3 cards"
Call 3: "Add a FAQ section with 6 questions in accordion style"
```

---

### 7. Presenting HTML Without Checking It
Handing the raw HTML to the user without verifying the output addresses the brief. Free plan responses always include a `notice` field — not surfacing this creates confusion.

❌ **Bad:**
```
// Receive response → immediately paste html to user
```

✅ **Fix:** Before presenting output, verify:
- All sections from the brief are present in the HTML
- No `warning` field (low credits) that should prompt the user to top up
- Acknowledge the `notice` field if present (free plan message)
- HTML is a complete document (has `<html>`, `<head>`, `<body>`, Tailwind CDN)

---

### 8. Converting When You Should Edit
Using `convert_design_style` to make content changes. This tool changes the visual style only — it won't add sections, change copy, or restructure layout.

❌ **Bad:**
```
// User wants to change headline AND switch to dark style
// Agent calls convert_design_style with hope it handles both
```

✅ **Fix:** Sequence the operations:
1. `edit_design` — make the content changes first
2. `convert_design_style` — then apply the style change

---

### 9. Ignoring the Prompt Resource
Skipping the MCP `generate_design` **prompt** (separate from the tool) when the user hasn't decided on a style or section structure yet. This prompt walks users through the selection process interactively.

The server exposes a guided prompt named `generate_design` — use it when the user says something vague like "build me a landing page" without further context. It asks for style preference and core content before any generation happens.

---

## Output Anti-Patterns

### Placeholder content
The generated HTML should never contain:
- "Lorem ipsum" — unless the user explicitly asked for filler text
- `[Insert text here]` or `{{placeholder}}`
- Generic company names like "Acme Corp" unless given no brand name

If the prompt was specific enough, the output will have real copy. If placeholders appear, the prompt lacked content direction.

**Fix:** Add explicit copy direction to the prompt:
```
"Hero headline: 'Automate your deployment pipeline'. Subheadline: 
'From push to production in under 5 minutes, without the ops overhead.'
CTA: 'Start for free'. Secondary CTA: 'View documentation'."
```

### Broken self-contained HTML
Windframe outputs complete HTML with Tailwind CDN loaded via script tag. If the output references external files, uses custom components, or requires a build step — something went wrong.

A valid Windframe output:
- Opens directly in any browser with no server
- Loads Tailwind from CDN (`<script src="https://cdn.tailwindcss.com"></script>`)
- Contains no `import`, `require`, or `@apply` directives
- Is a single `.html` file

---

## Authentication Anti-Patterns

### Re-running auth every session
OAuth tokens are stored locally and refreshed automatically (1-hour access tokens, 30-day refresh tokens). You don't need to re-authenticate each session.

**Only re-authenticate if:**
- The tool returns a `401 Unauthorized` error
- The user explicitly disconnects the MCP server
- More than 30 days have passed since last use

### Treating the auth URL as a tool parameter
Authentication happens at the MCP transport layer — not inside tool calls. You never pass API keys, tokens, or credentials into `generate_design`, `edit_design`, or `convert_design_style`. If a user asks you to embed their API key in a tool call, that's a misunderstanding of how the auth flow works.
