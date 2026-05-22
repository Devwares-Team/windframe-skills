# Windframe Style And Theme Selection

This file defines the selection process only. Do not document specific Windframe styles here, because the style catalog changes over time.

The MCP resources are the source of truth:

- `windframe://styles` for available UI styles.
- `windframe://themes` for available color themes.

Style recommendations must happen during the user session after the MCP client reads those resources.

---

## Selection Rule

The user chooses the final `uiStyle` and `primaryColor`.

The agent may:

- Read the available styles and themes from Windframe resources.
- Summarize the options returned by the server.
- Recommend 1-3 pairings based on the user's current request and the live resource data.
- Explain why each recommendation fits.

The agent must not:

- Pick a style or color silently.
- Call `fetch_style_design_context` or `fetch_style_conversion_context` before the user selects both values.
- Use hard-coded style names, style descriptions, theme mappings, or default pairings from local docs.
- Assume the current Windframe style catalog is the same as a previous session.

---

## Recommendation Process

1. Read `windframe://styles`.
2. Read `windframe://themes`.
3. Inspect the user's request for UI type, audience, tone, density, content needs, and conversion goals.
4. Use only the styles and themes returned by the resources.
5. Recommend up to three style/theme pairings.
6. Ask the user to choose both `uiStyle` and `primaryColor`.
7. Continue only after the user chooses.

If the resource output includes style descriptions, best-use cases, tags, or metadata, use that information to make recommendations. If the resource output only contains names, make cautious recommendations from the names and ask the user to confirm.

---

## Presenting Options

Keep the user-facing prompt compact. Do not dump a long list unless the user asks for all options.

Recommended structure:

```text
I read the current Windframe options.

Available styles: [styles from windframe://styles]
Available themes: [short grouped or trimmed list from windframe://themes]

For this request, I recommend:
1. [Style from resource] + [theme from resource]: [why it fits this request].
2. [Style from resource] + [theme from resource]: [why it fits this request].

Which style and primary color should I use?
```

If there are many themes, show the most relevant theme candidates and say that more options are available. If the user asks for every theme, list every theme returned by `windframe://themes`.

---

## Handling Missing Metadata

If `windframe://styles` returns only style names:

- Do not invent detailed style behavior.
- Use obvious naming cues only when they are clear.
- Present options neutrally when names are ambiguous.
- Ask the user which direction they prefer.

Example:

```text
The style resource returned names but no descriptions. I can make a cautious recommendation from the names, but you should choose the final style.
```

---

## Color Handling

`primaryColor` must be chosen by the user. It can be:

- A theme returned by `windframe://themes`.
- `"current"` when the user wants to inherit the project's existing primary color.
- A custom hex value such as `"#3b82f6"` if the tool supports it.

Do not invent unavailable theme names. If the user provides a color that is not listed in `windframe://themes`, only use it if it is `"current"` or a valid custom hex value.
