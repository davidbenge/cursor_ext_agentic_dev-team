---
name: app-builder-frontend-developer
description: Use when designing App Builder frontend Jamstack applications (unified shell / Workfront extensions / AEM assets / AEM CF extensions). Knows dx-excshell-1 application extension points, web-src/public layout, and Adobe React Spectrum UI development.
---

# App Builder Frontend Developer Skill

## When to Use This Skill

Use this skill when:
- During implementation planning (/dev-start-implementation-plan), when the story or tasks involve frontend, UI
- Designing or implementing App Builder extension-points apps as defined in this skill's references
- Implementing or refactoring React Spectrum components (layouts, forms, lists, navigation, overlays)
- Debugging or refactoring frontend components

**Do NOT use this skill when:**
- Working only on backend actions or APIs (use app-builder-actions-developer) or DB schema (use graph-db-specialist)
- Implementing App Builder MCP servers (use app-builder-mcp-developer)
- If this project is not an Adobe App Builder project, do not use this skill

## Role Boundary

**Does**: Write frontend section of task-plans. App Builder frontend component design using **Adobe React Spectrum only**. Knows application points and directory signatures (dx-excshell-1 = unified/experience shell; frontend under `web-src/`, `public/`).

**Does NOT**: Own story-level task breakdown (Architect + Dev Lead); write backend or DB code.

## React Spectrum & MCP

- **Stack**: Adobe React Spectrum only. Components from `@adobe/react-spectrum`; icons from `@spectrum-icons/workflow`. Wrap in `<Provider theme={defaultTheme}>`. Use dimension tokens; prefer `<Flex>`, `<Grid>`, `<View>`; controlled form props; label fields; use `IllustratedMessage` for empty/error states.
- **React Spectrum S2 MCP** (`project-0-pattern_atlas_publishing-React Spectrum S2`): `list_s2_pages`, `get_s2_page`, `search_s2_icons`, `search_s2_illustrations`, `get_style_macro_property_values` for component APIs, icons, tokens.
- **Context7 MCP** (`project-0-pattern_atlas_publishing-context7`): `resolve-library-id` then `query-docs` for latest React Spectrum docs/examples.

## References

- **Basic usage**: Load constitution and impl-log; use React Spectrum S2 / Context7 MCP when needed; read other specialist sections; write the Frontend Specialist section.
- **Unified shell**: See [references/unified-shell.md](references/unified-shell.md)
- **Application points**: See [references/application-points.md](references/application-points.md)
- **Constitution**: `docs/design-principles/frontend.md`, `docs/design-principles/react-spectrum-ui-patterns.md`
- **React Spectrum rule**: `../.cursor/rules/react-spectrum-ui.mdc`
- **System state**: `docs/impl-log/frontend/index.md`, `docs/impl-log/frontend/log.md`
- **Archive patterns** (screens/components): `docs/archive/pre-multi-persona-2026-02/docs/context-patterns/react-spectrum-ui-patterns.md` when building screens or components.

## Instructions

1. Load and read: this skill's references (unified-shell, application-points), `docs/design-principles/frontend.md`, `docs/impl-log/frontend/index.md`. For UI work, also load `../.cursor/rules/react-spectrum-ui.mdc` and, when building screens or components, the archive react-spectrum-ui-patterns path above.
2. Scan `docs/impl-log/frontend/log.md` entry headers for entries related to the current task; if a relevant entry exists and links to archived detail, load the linked artifact for context.
3. When implementing components: Use React Spectrum S2 MCP to look up component APIs, icons, or style tokens; use Context7 for latest docs/examples if needed.
4. Read other completed specialist sections first (Backend for API contract alignment; DB if present for data shapes or contracts consumed by the UI).
5. Write the Frontend Specialist section: components, application points. Follow design-principles and impl-log conventions. Use only React Spectrum components and tokens.

## Output Contract

Frontend section in task-plan; impl-log/frontend entries on completion.
