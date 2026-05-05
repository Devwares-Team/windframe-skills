# Windframe MCP — Tool Reference

Complete parameter specs, usage patterns, and examples for Windframe MCP tools.

---

## Tool: `fetch_style_design_context`

**When to use:** Starting a new UI design. Returns style context for the MCP client to generate framework-specific code.

**Plan required:** Pro

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `prompt` | string | ✅ | Description of the UI component or page to design |
| `uiStyle` | enum | ❌* | Visual style — one of 7 options (see below) |
| `primaryColor` | string | ❌* | Tailwind color name, `"current"` for project color, or hex code |

*`uiStyle` and `primaryColor` are optional. If omitted, the server will interactively prompt the user via elicitation form (if client supports it). Clients that don't support elicitation receive a `user_input_required` error.

**`uiStyle` values:** `"Default"` | `"Linear UI"` | `"ShadCN UI"` | `"Pandora UI"` | `"Autumn"` | `"Enterprise"` | `"Notion"`

### Response Shape

```json
{
  "status": "ready",
  "resource_uri": "windframe://style-context/1234567890-abc123",
  "message": "Style context is ready. Access the full context via the resource URI above."
}
```

### How to Use the Response

1. Read the resource URI: `windframe://style-context/{id}`
2. The resource contains the style context in JSON format
3. Use that context to generate UI code in the project's framework (React, Vue, Svelte, etc.)

### Example Calls

**Landing page (Default style):**
```json
{
  "prompt": "A SaaS analytics platform landing page. Hero: headline 'Turn data into decisions', subheadline about real-time dashboards, CTA 'Start free trial'. Features: 3-column grid — Live dashboards, AI insights, Team sharing. Social proof: logos of 5 companies. Pricing: Free (3 dashboards), Pro ($49/mo, unlimited), Enterprise (custom). Footer with links.",
  "uiStyle": "Default",
  "primaryColor": "indigo"
}
```

**Developer tool (Linear UI style):**
```json
{
  "prompt": "A CLI tool homepage for developers. Dark hero with monospace font, terminal animation showing the tool in action, single CTA 'npm install -g mytool'. Below: 3 feature blocks with code snippets. GitHub star count badge. Installation guide section.",
  "uiStyle": "Linear UI",
  "primaryColor": "slate"
}
```

---

## Tool: `fetch_style_conversion_context`

**When to use:** Converting an existing UI to a different visual style while keeping the same content structure.

**Plan required:** Pro

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `prompt` | string | ✅ | Description of what is being converted and any specific conversion instructions |
| `uiStyle` | enum | ❌* | Target visual style |
| `primaryColor` | string | ❌* | Target color: Tailwind name, `"current"`, or hex code |

*`uiStyle` and `primaryColor` are optional. If omitted, the server will prompt interactively via elicitation.

### Response Shape

Same as `fetch_style_design_context` — returns a resource URI with the converted style context.

### How to Use the Response

1. Read the resource URI: `windframe://style-context/{id}`
2. The resource contains the converted style context
3. Use that context to generate the UI in the new style within the project's framework

### Example Calls

**Light → Dark:**
```json
{
  "prompt": "Convert this landing page to a dark developer-friendly style",
  "uiStyle": "Linear UI",
  "primaryColor": "slate"
}
```

**Bold marketing → Clean product:**
```json
{
  "prompt": "Convert this agency site to a clean SaaS product style",
  "uiStyle": "Default",
  "primaryColor": "blue"
}
```

---

## MCP Resources

Two read-only resources — call these when you need to know what options exist:

### `windframe://styles`

Returns JSON with all styles. Use this to present style options to users or to verify a style name.

### `windframe://themes`

Returns array of all color themes. Use this when you need to present theme options or verify a theme name.

### `windframe://style-context/{id}`

Returns the style context data for client-side framework generation. Access this after receiving a resource URI from `fetch_style_design_context` or `fetch_style_conversion_context`.

---

## Credits and Plans

- **Pro plan required:** Both `fetch_style_design_context` and `fetch_style_conversion_context` require a Pro plan
- If `pro_plan_required` error received → direct user to https://windframe.dev/pricing
- The MCP server handles credit consumption automatically for Pro users
