# Windframe MCP Anti-Patterns

Use this checklist to avoid behavior that violates the Windframe MCP workflow.

---

## Choosing Style Or Color For The User

Bad:

```json
{
  "uiStyle": "[hard-coded style]",
  "primaryColor": "[hard-coded color]"
}
```

when the user did not choose those values.

Fix:

1. Read `windframe://styles`.
2. Read `windframe://themes`.
3. Recommend suitable pairings.
4. Ask the user to choose `uiStyle` and `primaryColor`.
5. Retry or call the tool with the selected values.

---

## Skipping The Option Resources

Bad:

```text
Use a fixed style for a fixed UI category without checking the live resource data.
```

without checking what the server currently exposes.

Fix:

Treat `windframe://styles` and `windframe://themes` as authoritative. Local docs can guide recommendations, but resource output controls what can be offered.

---

## Treating Style Context As Finished Code

Bad:

```text
Here is your result: windframe://style-context/12345
```

Fix:

Read `windframe://style-context/{context_id}` and use the returned style context to generate or convert UI code in the user's project framework.

---

## Using The Wrong Parameter Name

Bad:

```json
{
  "theme": "[selected color]"
}
```

with `fetch_style_design_context` or `fetch_style_conversion_context`.

Fix:

Use `primaryColor`:

```json
{
  "primaryColor": "[user-selected theme, current, or custom hex]"
}
```

---

## Vague Prompts

Bad:

```text
A landing page for a startup.
```

Fix:

Include product, audience, goal, sections, copy direction, CTAs, content counts, interactions, and constraints:

```text
A landing page for a B2B contract management SaaS targeting legal teams. Hero headline: "Sign faster, dispute less". Sections: hero with demo CTA, 4 feature cards, security proof, 2 testimonials, 3-tier pricing, FAQ, footer.
```

---

## Overloading A Conversion

Bad:

```text
Convert this dashboard to a specific style, rewrite the product positioning, add pricing, change the nav, and create a mobile app screen.
```

Fix:

Separate content changes from style conversion. First clarify or update the UI structure, then use `fetch_style_conversion_context` for the selected target style and color.

---

## Ignoring Plan Or Credit Errors

If the tool returns `pro_plan_required`, tell the user the feature requires Windframe Pro and provide `https://windframe.dev/pricing`.

If the tool returns `free_pass_expired`, tell the user their free calls are exhausted and provide `https://windframe.dev/pricing`.

Do not keep retrying the same call without resolving the account state.

---

## Leaving Placeholder Content

Avoid final UI with:

- `Lorem ipsum`
- `[Insert text here]`
- `{{placeholder}}`
- Generic company names when the user provided a real product name

If the user did not provide copy, write reasonable copy from their brief and state any assumptions.

---

## Framework Mismatch

Do not generate React in a Vue project, Vue in a Svelte project, or static HTML inside an existing component app unless the user requested it.

Check project files before writing:

- `.jsx` or `.tsx`: React
- `.vue`: Vue
- `.svelte`: Svelte
- `app/`, `pages/`, or `next.config.*`: Next.js
- Otherwise, ask or generate framework-neutral HTML only if appropriate

---

## Auth Misuse

Authentication happens through MCP OAuth. Do not ask the user for tokens. Do not pass credentials into tool calls.

Only revisit authentication if the MCP server is missing, disconnected, or returns an authorization error.
