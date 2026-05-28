---
name: windframe
description: Use this skill when a user asks to generate, redesign, restyle, or convert web UI components or pages with the Windframe API. The skill must fetch live UI style options from the API, present available UI styles and practical color choices to the user, recommend the best choices for the user's intent, and wait for the user to choose before calling the design-context endpoint with uiStyle and primaryColor. Do not choose the UI style or primary color for the user.
user-invocable: true
compatibility: Requires WINDFRAME_API_KEY environment variable.
metadata:
  requires-env: WINDFRAME_API_KEY
---

# Overview

Use the Windframe API to get style context for production-ready web UI generation or conversion. Windframe API provides style guidance through authenticated HTTP endpoints; the coding agent generates the actual UI code in the user's project framework.

You are to interact with it via a REST API at `https://mcp.windframe.dev/api`, with endpoints:
- `GET /ui-styles` returns the currently available Windframe UI styles.
- `POST /design-context` returns style context for a prompt, `uiStyle`, and `primaryColor`.

And for Authorization use either `Authorization: Bearer $WINDRAME_API_KEY` or `x-api-key: $WINDRAME_API_KEY`


**Core rule**: never choose `uiStyle` or `primaryColor` on the user's behalf. Fetch the live options from Windframe, recommend the best matches, ask the user to select, then call the API with the selected values.

---

## Prerequisites: API Key

An API key created on [your account page](https://app.winframe.dev/account), store it in the `WINDFRAME_API_KEY` environment variable. Full keys are shown only once when created, so store them securely.

**Required plan**: Active Pro or Team account

---


## Mandatory Workflow

### 1. Confirm The UI Request

Extract the user's intent:

- UI type: page, section, component, dashboard, admin panel, marketing site, etc.
- Target audience and tone.
- Required sections, content, interactions, and brand constraints.
- Project framework and styling stack when available.
- Whether this is a new design or a conversion/restyle of an existing design.

Ask only for missing details that materially affect the output. Style and color selection are handled through the Windframe option flow below.

### 2. Confirm API Access

Before calling the API, make sure an API key is available in the user's environment, secret store, or current instructions.

Recommended environment variable:

```text
WINDFRAME_API_KEY
```

If no key is available, ask the user to create one from the Windframe Account page and provide it through a secure local mechanism. Do not hard-code API keys into project files.

### 3. Fetch Windframe Options

Before requesting design context, call:

```http
GET https://mcp.windframe.dev/api/ui-styles
Authorization: Bearer <WINDRAME_API_KEY>
```

Treat the response as authoritative. Do not rely on a hard-coded style list when the endpoint is available.

The endpoint returns:

```json
{
  "styles": ["..."]
}
```

or backend-controlled style objects. Preserve whatever shape the API returns and use any metadata it provides for recommendations.

### 4. Present Options And Recommendations

Show the user a compact selection prompt:

- List or summarize the available UI styles returned by `/ui-styles`.
- Offer practical primary color choices such as `"current"`, all Tailwind color names, or a valid custom hex value.
- Recommend up to three style/color pairings that fit the user's request and the live API data.
- Make clear that the user must choose the final `uiStyle` and `primaryColor`.

Do not proceed until the user selects both values.

Example:

> I read the current Windframe options.
>
> Available styles: [styles returned by /ui-styles]
> Practical primary colors: current, blue, emerald, violet, rose, amber, or a custom hex value.
>
> For your request, I recommend:
> 1. [live style] + blue: [why it fits].
> 2. [live style] + emerald: [why it fits].
>
> Which style and primary color should I use?

### 5. Build A Specific Prompt

Write a detailed prompt for Windframe. Include:

- Page or component type.
- Audience and product context.
- Section-by-section content.
- Copy direction or exact copy for headings, CTAs, cards, tables, and forms.
- Interaction or state requirements.
- Existing UI details when converting.

### 6. Fetch Design Context

For both new UI and conversion/restyling, call:

```http
POST https://mcp.windframe.dev/api/design-context
Authorization: Bearer <WINDRAME_API_KEY>
Content-Type: application/json
```

Request body:

```json
{
  "prompt": "Specific UI brief",
  "uiStyle": "User-selected style",
  "primaryColor": "User-selected color, current, or hex"
}
```

Required body fields are `prompt`, `uiStyle`, and `primaryColor`.

The endpoint returns:

```json
{
  "context": {},
  "fonts": {}
}
```

`fonts` may be omitted. Use the returned `context` and optional `fonts` to generate or convert UI code in the project framework.

### 7. Generate Or Convert The UI

Use the design context as guidance. Generate code that fits the user's existing project structure, component patterns, framework, and Tailwind setup.

Do not paste the raw design context as the final result unless the user explicitly asks for it.

---

## Errors

- `missing_api_key`: ask the user to provide a Windframe API key through a secure local mechanism.
- `invalid_api_key`: ask the user to verify or recreate the API key.
- `pro_plan_required`: tell the user this API feature requires Windframe Pro and point them to `https://windframe.dev/pricing`.
- `api_key_validation_unavailable`: tell the user Windframe could not validate the key and retry later.
- `ui_styles_unavailable`: tell the user style options could not be fetched and retry later.
- `missing_required_body_fields`: include `prompt`, `uiStyle`, and `primaryColor`, then retry.
- `design_context_unavailable`: tell the user design context could not be generated and retry later.

---

## Quality Gates

Before finishing:

- API access was handled through a secure key source, not hard-coded project files.
- Style options came from `GET /ui-styles`.
- The style and primary color were chosen by the user.
- `POST /design-context` returned design context successfully.
- The generated UI matches the user's framework and existing project conventions.
- Required sections, content, states, and interactions are present.
- No placeholder content remains unless the user requested placeholders.
- The result follows the selected Windframe style and primary color.

---

## References

| File | When to read |
|------|-------------|
| [references/styles.md](references/styles.md) | Primary color handling and style recommendation guidance |
| [references/anti-patterns.md](references/anti-patterns.md) | Mistakes to avoid with the HTTP API workflow |
