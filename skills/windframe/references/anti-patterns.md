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

### 2. Forgetting Style Context is Not UI Code
The tools return style context (via resource URI), not HTML/JSX/Vue code. The MCP client must read the resource and generate the actual UI code.

❌ **Bad:**
```
// Tool returns resource URI
// Agent pastes the JSON context directly to user
```

✅ **Fix:** Read the resource URI and use the context to generate framework-specific code:
```
1. Receive resource_uri: "windframe://style-context/{id}"
2. Read the resource to get JSON context
3. Generate React/Vue/Svelte/HTML code using that context
```

---

### 3. Missing Style or Color Selection
Starting generation without letting the user choose style and primary color.

❌ **Bad:**
```json
// Calling fetch_style_design_context without uiStyle or primaryColor
// Hoping the server will guess correctly
```

✅ **Fix:** Always read `windframe://styles` and `windframe://themes` first, present options to user, and get explicit confirmation. If you skip this, the server will either:
- Prompt the user via elicitation form (if client supports it)
- Return `user_input_required` error (if client doesn't support elicitation)

---


### 4. Overloading a Single Conversion
Requesting too many changes in one `fetch_style_conversion_context` call.

❌ **Bad:**
```
"Convert this page to Linear UI, change the headline, add testimonials,
fix the navbar, and make it mobile responsive"
```

✅ **Fix:** Do the content changes first with one tool call, then do the style conversion separately:
1. Use `fetch_style_design_context` for new content
2. Use `fetch_style_conversion_context` for style changes

---

### 5. Ignoring the Pro Plan Requirement
Trying to use `fetch_style_design_context` or `fetch_style_conversion_context` without a Pro plan.

❌ **Bad:**
```
// Calling fetch_style_design_context when user is on Free plan
// Tool returns: { "error": "pro_plan_required", "message": "..." }
```

✅ **Fix:** If you receive `pro_plan_required` error, direct the user to upgrade:
```
"This feature requires a Pro plan. Upgrade at https://windframe.dev/pricing"
```

---

### 6. Not Reading the Resource URI
Receiving a resource URI but never reading it to get the actual style context.

❌ **Bad:**
```
// Receive: { "resource_uri": "windframe://style-context/12345" }
// Agent: "Here's your style context: windframe://style-context/12345"
```

✅ **Fix:** Always read the resource to get the JSON context:
```
Read the resource at windframe://style-context/12345
// Then use that context to generate UI code
```

---

## Output Anti-Patterns

### Placeholder content
The generated UI should never contain:
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

### Framework Mismatch
Generating React JSX when the project uses Vue, or vice versa.

**Fix:** Check the project's framework before generating code:
- `.jsx`/`.tsx` files → React
- `.vue` files → Vue
- `.svelte` files → Svelte
- Otherwise → HTML with Tailwind CDN

---

## Authentication Anti-Patterns

### Re-running auth every session
OAuth tokens are stored locally and refreshed automatically (1-hour access tokens, 30-day refresh tokens). You don't need to re-authenticate each session.

**Only re-authenticate if:**
- The tool returns a `401 Unauthorized` error
- The user explicitly disconnects the MCP server
- More than 30 days have passed since last use

### Treating the auth URL as a tool parameter
Authentication happens at the MCP transport layer — not inside tool calls. You never pass API keys, tokens, or credentials into the tools.
