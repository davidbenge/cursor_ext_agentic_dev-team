# Quick Start — Adopting the Framework

Two paths depending on whether you are starting fresh or retrofitting an existing project.

---

## Path A: New Project

Open your new project in Cursor and type:

```
/setup-dev-process-new
```

The command will walk you through a phased, interactive setup:
1. **Project identity** — name, repo URL, Jira key
2. **Vision and constitution** — fill in design-principles files interactively
3. **Quality and security** — testing and security standards
4. **System memory** — create the `docs/impl-log/` scaffolding
5. **Personas** — pick which specialist skills your project needs
6. **Commands and rules** — generates and validates all workflow files

At the end of setup, your project has a full AI dev team ready to work.

---

## Path B: Existing Project

Open your existing project in Cursor and type:

```
/setup-dev-process-existing
```

The command inspects your codebase first, then proposes design-principles, impl-log indexes, and persona skills based on what it finds — rather than asking from scratch.

---

## Manual Copy (Advanced)

If you prefer to copy the framework files manually into an existing project:

### Step 1 — Copy the framework files

```bash
# From the cursor-agentic-framework repo root, copy into your project
cp -r .cursor/ /path/to/your/project/.cursor/
cp -r docs/ /path/to/your/project/docs/
```

Or selectively:
- `.cursor/commands/` — all 28 slash commands
- `.cursor/skills/` — the 6 core personas (skip `examples/` for now)
- `.cursor/rules/multi-persona-workflow.mdc` — required workflow hygiene rule
- `.cursor/AGENTS.md` — Cursor auto-loads this; update the project name
- `.cursor/mcp.json` — update or extend with your MCP servers

### Step 2 — Fill in the constitution

Edit the template files in `docs/design-principles/`. Start with `vision.md` and `architecture.md` — the others will follow naturally. See [`docs/design-principles/_INSTRUCTIONS.md`](docs/design-principles/_INSTRUCTIONS.md) for guidance on each file.

### Step 3 — Update AGENTS.md

Edit `.cursor/AGENTS.md` and replace `{{PROJECT_NAME}}` with your actual project name.

### Step 4 — Add domain specialist skills (optional)

Copy any of the example skills from `.cursor/skills/examples/` into `.cursor/skills/` and adapt the `SKILL.md` for your domain. See [`.cursor/skills/examples/README.md`](.cursor/skills/examples/README.md) for the authoring guide.

### Step 5 — Start working

Use the slash commands to begin:

```
/plan-1    # Start a new story from an idea or Jira ticket
/epic-1    # Start a new epic from an epic ID or feature brief
/dev-1     # Start implementation from an approved story plan
```

---

## Placeholder Reference

These tokens appear in template files — replace them during setup:

| Token | Replace With |
|---|---|
| `{{PROJECT_NAME}}` | Your project name (e.g. "Acme Platform") |
| `{{JIRA_PREFIX}}` | Your Jira project key (e.g. `PROJ`, `ACME`) |
| `{{APP_EXTENSION_POINT}}` | Your app extension/module name (if applicable) |

---

## Full Process Reference

Once setup is complete, the canonical workflow guide is:
[`docs/developer-setup/developer-process.md`](docs/developer-setup/developer-process.md)

Persona and command reference:
[`docs/developer-setup/persona-and-command-reference.md`](docs/developer-setup/persona-and-command-reference.md)
