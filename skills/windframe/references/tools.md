# Windframe MCP Tool Reference

This reference describes the MCP surface used by the Windframe skill. The MCP resources are the source of truth for available UI styles and color themes.

---

## Resources

### `windframe://styles`

Returns the currently available Windframe UI styles.

Use this before every design or conversion tool call. Present these live styles to the user and recommend only from the returned data.

### `windframe://themes`

Returns the currently available Tailwind CSS color themes.

The server currently returns an array of objects:

The exact values are backend-controlled and can change over time.

Use this before every design or conversion tool call. Present practical options to the user and recommend likely matches.

### `windframe://style-context/{context_id}`

Returns the full style context after `fetch_style_design_context` or `fetch_style_conversion_context`.

Always read this resource before generating or converting UI code. The tool response only gives the URI; it does not contain the full style context.

---

## Tool: `fetch_style_design_context`

Use this tool when the user wants a new UI component or page.

The tool fetches style context from Windframe. The MCP client then uses that context to generate code in the user's project framework.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `prompt` | string | Yes | Specific description of the UI component or page to design |
| `uiStyle` | string | Required by this skill | User-selected style from `windframe://styles` |
| `primaryColor` | string | Required by this skill | User-selected Tailwind theme name, `"current"`, or custom hex code |

The server can attempt interactive elicitation if `uiStyle` or `primaryColor` are omitted. The skill should still read the resources and ask the user to choose first, then call the tool with explicit values.

### Example

```json
{
  "prompt": "A SaaS analytics landing page for finance teams. Hero headline: 'Close the books with confidence'. Sections: hero with two CTAs, 4 feature cards, security proof, pricing table, FAQ, footer.",
  "uiStyle": "[user-selected style from windframe://styles]",
  "primaryColor": "[user-selected theme, current, or custom hex]"
}
```

### Response

```json
{
  "status": "ready",
  "resource_uri": "windframe://style-context/1234567890-abc123",
  "message": "Style context is ready. Access the full context via the resource URI above."
}
```

Read `resource_uri` next.

---

## Tool: `fetch_style_conversion_context`

Use this tool when the user wants to convert an existing UI component or page to a different Windframe style or color theme.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `prompt` | string | Yes | Description of what is being converted and what should change |
| `uiStyle` | string | Required by this skill | User-selected target style from `windframe://styles` |
| `primaryColor` | string | Required by this skill | User-selected Tailwind theme name, `"current"`, or custom hex code |

### Example

```json
{
  "prompt": "Convert the current admin dashboard while preserving the sidebar, metrics cards, table, filters, and empty states. Apply the user-selected Windframe style context.",
  "uiStyle": "[user-selected style from windframe://styles]",
  "primaryColor": "[user-selected theme, current, or custom hex]"
}
```

The response shape is the same as `fetch_style_design_context`. Read the returned `windframe://style-context/{context_id}` resource before modifying code.

---

## User Choice Requirement

Do not select `uiStyle` or `primaryColor` for the user.

Correct flow:

1. Read `windframe://styles`.
2. Read `windframe://themes`.
3. Present available options.
4. Recommend up to three pairings based on the user's intent and the live resource data.
5. Ask the user to choose both `uiStyle` and `primaryColor`.
6. Call the tool with the chosen values.

If the tool returns `user_input_required`, ask the user for the missing values and retry with explicit parameters.

---

## Errors

### `user_input_required`

The client does not support interactive elicitation. Ask the user to choose `uiStyle` and `primaryColor`, then retry.

### `cancelled`

The user cancelled interactive selection. Stop and ask how they want to proceed.

### `pro_plan_required`

The feature requires a Pro plan. Direct the user to `https://windframe.dev/pricing`.

### `free_pass_expired`

The user's free calls have expired. Direct the user to `https://windframe.dev/pricing`.

---

## Important Notes

- These tools return style context, not finished UI code.
- The MCP client must read the returned resource and generate code in the project framework.
- `primaryColor` accepts a Tailwind theme name, `"current"` for the project primary color, or a custom hex code such as `"#3b82f6"`.
- Available style names must come from `windframe://styles`, not from a hard-coded list.
