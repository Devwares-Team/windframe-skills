# Windframe Style And Color Selection

This file defines the selection process only. Do not document specific Windframe styles here, because the style catalog changes over time.

The `GET /ui-styles` endpoint is the source of truth for available UI styles.

Style recommendations must happen during the user session after fetching live options from the API.

---

## Selection Rule

The user chooses the final `uiStyle` and `primaryColor`.

The agent may:

- Call `GET /ui-styles` to fetch available styles.
- Present the available options to the user.
- Recommend 1-3 pairings based on the user's current request and the live API data.
- Explain why each recommendation fits.

The agent must not:

- Pick a style or color silently.
- Call `POST /design-context` before the user selects both values.
- Use hard-coded style names, style descriptions, or default pairings from local docs.
- Assume the current Windframe style catalog is the same as a previous session.

---

## Recommendation Process

1. Call `GET /ui-styles`.
2. Inspect the user's request for UI type, audience, tone, density, content needs, and conversion goals.
3. Use only the styles returned by the API.
4. Recommend up to three style/color pairings.
5. Ask the user to choose both `uiStyle` and `primaryColor`.
6. Continue only after the user chooses.

If the API response includes style descriptions, best-use cases, tags, or metadata, use that information to make recommendations. If the response only contains names, make cautious recommendations from the names and ask the user to confirm.

---

## Presenting Options

Keep the user-facing prompt compact. Do not dump a long list unless the user asks for all options.

Recommended structure:

```text
I read the current Windframe options.

Available styles: [styles from /ui-styles]
Practical primary colors: current, blue, emerald, violet, rose, amber, or a custom hex value.

For this request, I recommend:
1. [style from API] + [color]: [why it fits this request].
2. [style from API] + [color]: [why it fits this request].

Which style and primary color should I use?
```

---

## Handling Missing Metadata

If `GET /ui-styles` returns only style names:

- Do not invent detailed style behavior.
- Use obvious naming cues only when they are clear.
- Present options neutrally when names are ambiguous.
- Ask the user which direction they prefer.

Example:

```text
The style endpoint returned names but no descriptions. I can make a cautious recommendation from the names, but you should choose the final style.
```

---

## Color Handling

`primaryColor` must be chosen by the user. Valid values:

- **A Tailwind color name**: e.g. `blue`, `emerald`, `violet`, `rose`, `amber`, `cyan`, `slate`, etc. Any standard Tailwind CSS color is accepted.
- **`"current"`**: inherits the project's existing primary color. Use this when the user wants to keep their current color scheme.
- **A custom hex value**: e.g. `"#3b82f6"`. Use this when the user has a specific brand color.

Do not invent unavailable color names. If the API or the user's project does not support a given color, fall back to a standard Tailwind color.
