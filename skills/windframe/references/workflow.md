# Windframe MCP Workflow

This workflow is dynamic. It must work with live style and theme data returned by the Windframe backend through MCP resources.

Do not hard-code style names, style descriptions, intent mappings, or default style/color pairings in this workflow.

---

## User Request

> Build a landing page for TaskFlow, a project management SaaS for small engineering teams. It should feel clean, modern, trustworthy, and drive free trial signups.

---

## 1. Understand The Brief

Extract the important details:

- Product: TaskFlow
- UI type: landing page
- Audience: small engineering teams
- Goal: free trial signups
- Tone: clean, modern, trustworthy
- Likely sections: hero, features, social proof, pricing, FAQ, footer

If the project framework is not obvious, inspect the repository before generating code.

---

## 2. Read Live Option Resources

Read these MCP resources in the active user session:

```text
windframe://styles
windframe://themes
```

Use the returned data exactly as the backend provides it.

The style resource may return names only, or it may include metadata such as descriptions, tags, best-use cases, or visual notes. If metadata exists, use it to recommend suitable options. If only names exist, make cautious recommendations from the live names and ask the user to confirm.

The theme resource returns the color options available for `primaryColor`. It may also be valid for the user to choose `"current"` or a custom hex value if supported by the tool.

---

## 3. Present Live Options And Ask

Respond with a compact choice prompt based only on the live resource data.

Template:

```text
I read the current Windframe options.

Available styles: [style names returned by windframe://styles]
Available themes: [relevant theme names returned by windframe://themes]

For this request, I recommend:
1. [live style] + [live theme]: [reason based on the user's request and any live style metadata].
2. [live style] + [live theme]: [reason based on the user's request and any live style metadata].

Which style and primary color should I use?
```

Rules:

- Recommend at most three pairings.
- Do not recommend anything that was not returned by `windframe://styles` or `windframe://themes`, except `"current"` or a valid custom hex color.
- Do not treat any local example, previous session, or old style name as available.
- Stop here until the user chooses both `uiStyle` and `primaryColor`.

---

## 4. Build The Windframe Prompt

After the user chooses, write a specific prompt for the selected request.

Example prompt body:

```text
A landing page for TaskFlow, a project management tool for small engineering teams.

Hero: headline "Ship sprints, not excuses". Subheadline: "TaskFlow gives engineering teams of 5-20 the sprint planning, GitHub sync, and velocity tracking they need without enterprise bloat." CTAs: "Start free trial" and "Watch 2-min demo". Trust line: "Trusted by 1,200+ teams. No setup required."

Features: 3 cards for Sprint Planning, GitHub Integration, and Velocity Charts. Include concise benefit copy for each.

Social proof: logo row for 5 companies.

Pricing: Free, Pro, and Team tiers. Highlight Pro as recommended.

FAQ: 4 questions about credit card requirements, imports, team growth, and security.

Footer: product links, docs, blog, status, legal links, and copyright.
```

Do not embed hard-coded style guidance in the prompt unless it came from the live style context or the user's chosen style metadata.

---

## 5. Fetch Style Context

Call `fetch_style_design_context` only after the user has chosen both values.

Use the exact selected values:

```json
{
  "prompt": "[specific UI prompt]",
  "uiStyle": "[user-selected style from windframe://styles]",
  "primaryColor": "[user-selected theme, current, or custom hex]"
}
```

Do not call this tool before the user chooses the style and primary color.

---

## 6. Read The Returned Resource

The tool returns a resource URI:

```json
{
  "status": "ready",
  "resource_uri": "windframe://style-context/{context_id}",
  "message": "Style context is ready. Access the full context via the resource URI above."
}
```

Read the returned URI:

```text
windframe://style-context/{context_id}
```

Use the live JSON style context to generate UI code in the user's framework.

---

## 7. Generate Code

Generate code that fits the repository:

- React or Next.js: components with JSX/TSX and Tailwind classes.
- Vue: single-file components.
- Svelte: Svelte components.
- Static project: HTML with Tailwind only when appropriate.

Verify that the output follows the selected live Windframe style context and primary color, includes all requested sections, and contains real copy.

---

## 8. Conversion Workflow

When the user asks to convert, restyle, or redesign an existing UI:

1. Inspect the existing UI enough to describe what must be preserved.
2. Read `windframe://styles` and `windframe://themes` in the active session.
3. Present live options and recommend at most three pairings from the resource data.
4. Ask the user to choose `uiStyle` and `primaryColor`.
5. Call `fetch_style_conversion_context` with the selected values.
6. Read the returned `windframe://style-context/{context_id}`.
7. Apply the live style context to the existing UI code.

Example call shape after user choice:

```json
{
  "prompt": "Convert the current dashboard while preserving the sidebar, top navigation, KPI cards, filters, data table, pagination, empty states, and responsive behavior. Apply the user-selected Windframe style context.",
  "uiStyle": "[user-selected style from windframe://styles]",
  "primaryColor": "[user-selected theme, current, or custom hex]"
}
```
