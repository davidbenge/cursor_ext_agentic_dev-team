# Frontend — [Your Frontend Stack]

<!-- Replace "[Your Frontend Stack]" with e.g. "React + Tailwind", "Next.js", "React Spectrum", "App Builder Unified Shell", etc. -->

## Stack

<!-- Define the approved frontend technologies -->
- **[UI Framework]** only — [constraint on alternatives]
- [Delivery mechanism, e.g., "Static served via CDN", "SSR via Next.js", "App Builder web-src"]

## Component Rules

<!-- Define component-level conventions -->
- [Rule 1 — e.g., "Wrap app in `<Provider>` with theme at root"]
- [Rule 2 — e.g., "Prefer composed library components over custom implementations"]
- [Rule 3 — e.g., "All components must be accessible (WCAG AA)"]

## State Management

<!-- Define how state is managed -->
- [Rule 1 — e.g., "Server state via React Query; no manual fetch boilerplate"]
- [Rule 2 — e.g., "UI state via hooks; no global store unless justified"]
- [Rule 3 — e.g., "No DOM APIs in server-rendered components"]

## Tech Stack

<!-- Define the exact technologies this project's frontend uses.
     The frontend-specialist reads this to know which MCP domain skills to load and what patterns to follow. -->

- **UI framework**: [e.g., React 18 / Vue 3 / Svelte]
- **Component library**: [e.g., Adobe React Spectrum / Material UI / Tailwind CSS]
- **Extension point**: [e.g., App Builder dx-excshell-1 (Unified Shell) / Workfront workfront-ui-1 / standalone]
- **Auth**: [e.g., Adobe IMS via imslib / OAuth 2.0 / session cookie]
- **State management**: [e.g., React Query + hooks / Redux / Zustand]
- **Build tooling**: [e.g., Webpack / Vite / Parcel]

<!-- After filling in the stack above, update the MCP Domain Skills section below
     to reflect exactly which domains apply to this project. -->

## MCP Domain Skills

The `frontend-specialist` persona **must** check the `adobe-extensibility-mcp` MCP server before starting any domain-specific task. Call `list_skills` to confirm available domains, then `load_skill` for each that applies to this project.

**Domains relevant to this project:**

> Not sure which domains you need? You can simply prompt the agent for guidance. Explain the type of frontend work, or describe your UI surface, and the agent will recommend which MCP domain skills apply.

> During project setup, the relevant skills can be selected interactively via the setup commands.
> - When running setup, you will be given the option to either:
>   - Select the domains from a checklist, **or**
>   - Describe your frontend needs and let the AI recommend the correct domain skills for you.

If you're unsure, just ask in natural language—AI will help you select the right MCP domain skills.



<!-- Check all that apply and remove the rest -->
- [ ] `app-builder-frontend` — App Builder frontend apps (Unified Shell / dx-excshell-1, React Spectrum)
- [ ] `workfront-extension` — Workfront UI extensions (workfront-ui-1 extension point)

Source: [github.com/davidbenge/adobe_extensibility_mcp — skills](https://github.com/davidbenge/adobe_extensibility_mcp/tree/stage/actions/skills-mcp/skills)

## Ownership

The `frontend-specialist` persona is responsible for maintaining **docs/impl-log/frontend/index.md** (the canonical frontend implementation log and component inventory).

**Component Documentation Policy:**
Whenever a new component or page is added, the impl-log/frontend/index.md **must also be updated** to reflect the addition. Keeping the component inventory in sync with the implemented UI is required for all additions and changes.
