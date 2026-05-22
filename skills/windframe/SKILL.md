---
name: windframe
description: Use this skill when a user asks to generate, redesign, restyle, or convert web UI components or pages with Windframe MCP. The skill must read Windframe MCP option resources, present available UI styles and color themes to the user, recommend the best choices for the user's intent, and wait for the user to choose before calling Windframe tools with uiStyle and primaryColor. Do not choose the UI style or primary color for the user.
version: 1.0.0
user-invocable: true
---

# Windframe MCP UI Skill

Use Windframe MCP to get style context for production-ready web UI generation or conversion. Windframe provides style guidance through MCP resources; the MCP client generates the actual UI code in the user's project framework.

Core rule: never choose `uiStyle` or `primaryColor` on the user's behalf. Read the options from Windframe resources, recommend the best matches, ask the user to select, then call the tool with the selected values.

---

## MCP Contract

The Windframe MCP server is named `windframe-mcp`.

Required resources:

- `windframe://styles` returns the currently available Windframe UI styles.
- `windframe://themes` returns the currently available Tailwind color themes as objects with `name` and `tailwindPrefix`.
- `windframe://style-context/{context_id}` returns the full style context after a tool call.

Required tools:

- `fetch_style_design_context` is for a new UI component or page.
- `fetch_style_conversion_context` is for converting an existing UI to another style.

Both tools return a `resource_uri`. Always read that resource before generating or converting UI code.

---

## Mandatory Workflow

### 1. Confirm The UI Request

Extract the user's intent:

- UI type: page, section, component, dashboard, admin panel, marketing site, etc.
- Target audience and tone.
- Required sections, content, interactions, and brand constraints.
- Project framework and styling stack when available.
- Whether this is a new design or a conversion/restyle of an existing design.

Ask only for missing details that materially affect the output. Style and color selection are always handled through the Windframe option flow below.

### 2. Read Windframe Options

Before any Windframe tool call, read:

- `windframe://styles`
- `windframe://themes`

Treat these resources as authoritative. Do not rely on a hard-coded style list when the resources are available.

### 3. Present Options And Recommendations

Show the user a compact selection prompt:

- List the available UI styles from `windframe://styles`.
- List practical theme options from `windframe://themes`, or group them if the list is long.
- Recommend up to three style/theme pairings that fit the user's request and the live resource data.
- Make clear that the user must choose the final `uiStyle` and `primaryColor`.

Do not proceed until the user selects both values.

Example:

> I read the current Windframe options.
>
> Available styles: [styles returned by windframe://styles]
> Available themes: [themes returned by windframe://themes]
>
> For your request, I recommend:
> 1. [live style] + [live theme]: [why it fits the user's intent and any live metadata].
> 2. [live style] + [live theme]: [why it fits the user's intent and any live metadata].
>
> Which style and primary color should I use?

### 4. Build A Specific Prompt

Write a detailed prompt for Windframe. Include:

- Page or component type.
- Audience and product context.
- Section-by-section content.
- Copy direction or exact copy for headings, CTAs, cards, tables, and forms.
- Interaction or state requirements.
- Existing UI details when converting.

### 5. Fetch Style Context

For new UI, call `fetch_style_design_context` with:

```json
{
  "prompt": "Specific UI brief",
  "uiStyle": "User-selected style",
  "primaryColor": "User-selected theme, current, or hex"
}
```

For conversion or restyling, call `fetch_style_conversion_context` with:

```json
{
  "prompt": "Specific conversion brief",
  "uiStyle": "User-selected target style",
  "primaryColor": "User-selected theme, current, or hex"
}
```

If a tool returns `user_input_required`, read the error, present the required options to the user, and retry only after the user chooses. Do not fill the missing values yourself.

### 6. Read The Returned Resource

The tool response has this shape:

```json
{
  "status": "ready",
  "resource_uri": "windframe://style-context/{context_id}",
  "message": "Style context is ready. Access the full context via the resource URI above."
}
```

Read `resource_uri`. Use the returned JSON style context to generate or convert UI code in the project framework.

### 7. Generate Or Convert The UI

Use the style context as design guidance. Generate code that fits the user's existing project structure, component patterns, framework, and Tailwind setup.

Do not paste the raw style context as the final result unless the user explicitly asks for it.

---

## Installation And Authentication

Before using Windframe, verify that the MCP server is connected and exposes the required resources and tools.

If it is not connected, add the HTTP MCP server:

```bash
claude mcp add --transport http windframe-mcp https://mcp.windframe.dev
```

Then restart the MCP client if needed. Authentication is handled through OAuth by the MCP transport. On first use, the user signs in through Windframe. Do not pass tokens or credentials into tool calls.

---

## Errors

- `user_input_required`: the MCP client cannot elicit interactively. Ask the user to choose `uiStyle` and `primaryColor`, then retry.
- `cancelled`: the user cancelled interactive selection. Stop and ask how they want to proceed.
- `pro_plan_required`: tell the user this Windframe feature requires Pro and point them to `https://windframe.dev/pricing`.
- `free_pass_expired`: tell the user their free calls are exhausted and point them to `https://windframe.dev/pricing`.

---

## Quality Gates

Before finishing:

- The style and primary color were chosen by the user.
- Options came from `windframe://styles` and `windframe://themes`.
- The returned `windframe://style-context/{context_id}` resource was read.
- The generated UI matches the user's framework and existing project conventions.
- Required sections, content, states, and interactions are present.
- No placeholder content remains unless the user requested placeholders.
- The result follows the selected Windframe style and theme.

---

## References

| File | When to read |
|------|-------------|
| [references/tools.md](references/tools.md) | Tool parameters, resources, responses, and errors |
| [references/styles.md](references/styles.md) | Style and theme recommendation guidance |
| [references/anti-patterns.md](references/anti-patterns.md) | Mistakes to avoid |
| [references/workflow.md](references/workflow.md) | End-to-end usage example |
