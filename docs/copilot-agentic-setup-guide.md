# GitHub Copilot Agentic Mode — Setup Guide

> How this project uses GitHub Copilot's agentic features for AI-assisted development.  
> Use this as a reference for setting up your own projects.

---

## File Hierarchy

GitHub Copilot uses a layered instruction system. Each layer serves a different purpose:

```
.github/
├── copilot-instructions.md           # Layer 1: Global instructions (always loaded)
├── instructions/                     # Layer 2: File-type instructions (loaded by glob match)
│   ├── laravel-controllers.instructions.md
│   ├── laravel-services.instructions.md
│   └── ...
├── agents/                           # Layer 3: Custom agents (invoked on demand)
│   ├── scaffolder.agent.md
│   ├── tester.agent.md
│   └── ...
└── prompts/                          # Layer 4: Reusable prompts (one-click tasks)
    ├── new-module.prompt.md
    ├── generate-tests.prompt.md
    └── ...

AGENTS.md                             # Root context: single source of truth for all tools
```

### How Each Layer Works

| File | When Loaded | Purpose |
|---|---|---|
| `.github/copilot-instructions.md` | **Every** Copilot interaction | Project-wide rules, architecture, conventions |
| `.github/instructions/*.instructions.md` | When editing a file matching the `applyTo` glob | File-type-specific patterns and examples |
| `.github/agents/*.agent.md` | When user invokes `@agent-name` | Specialized multi-step workflows |
| `.github/prompts/*.prompt.md` | When user selects from prompt picker | Reusable one-click task templates |
| `AGENTS.md` | Referenced by agents and instructions | Full architecture, tech debt, directory map |

---

## Layer 1: Global Instructions

**File:** `.github/copilot-instructions.md`

This file is loaded into Copilot's context on **every interaction** — chat, inline completion, and agentic mode. Keep it concise (the model has a context window limit).

**What to include:**
- Tech stack summary
- Architecture rules (what goes where)
- Code conventions (naming, patterns, anti-patterns)
- Directory map
- Known tech debt (so the AI doesn't silently work around bugs)
- "Do not" list

**What NOT to include:**
- File-type-specific details (put those in `.instructions.md` files)
- Step-by-step workflows (put those in `.agent.md` files)
- Full API documentation (too long for context)

**Example structure:**
```markdown
# Project Name — Copilot Instructions

## Stack
- Framework: Laravel (any version)
- Frontend: Vue.js + Inertia.js
...

## Architecture Rules
- All business logic in app/Services/
- Controllers are thin: validate → call service → return response
...

## Code Conventions
- Use config('key') — never $_ENV
- Translation keys for all UI strings
...

## Known Tech Debt
- UserService::hasReferences() is empty
...
```

---

## Layer 2: File-Type Instructions

**Directory:** `.github/instructions/`

These files have YAML frontmatter with an `applyTo` glob. Copilot loads them **only when editing files that match the glob** — so you get relevant context without consuming tokens on unrelated rules.

**Frontmatter format:**
```yaml
---
description: "Brief description for Copilot to understand when this applies"
applyTo: "app/Http/**/*.php"
---
```

**Best practices:**
- **One file per domain** — controllers, services, models, routes, tests, etc.
- **Include code examples** — the AI produces better output when it sees the expected pattern
- **Keep each file focused** — 50-100 lines is ideal
- **Use the `description` field** — Copilot uses it to decide relevance

**Example globs:**

| Glob | Matches |
|---|---|
| `app/Http/**/*.php` | Controllers, middleware, form requests |
| `app/Services/**/*.php` | Service classes |
| `app/Models/**/*.php` | Eloquent models |
| `database/migrations/**/*.php` | Migrations |
| `database/seeders/**/*.php` | Seeders |
| `routes/**/*.php` | Route files |
| `resources/lang/**` | Translation files |
| `tests/**/*.php` | Test files |
| `resources/js/**` | Vue/JS/TS frontend |

---

## Layer 3: Custom Agents

**Directory:** `.github/agents/`

Agents are specialized AI personas invoked via `@agent-name` in Copilot Chat. Each agent has a defined role, reads specific context files, and follows a structured workflow.

**Frontmatter format:**
```yaml
---
name: scaffolder
description: "Use when adding a new module. Generates migration, model, service, controller, routes, etc."
tools: [search, read, edit, execute]
---
```

**Available tool sets** (per [official VS Code docs](https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features#_chat-tools)):
- `search` — Search the codebase (`search/codebase`, `search/fileSearch`, `search/textSearch`, `search/listDirectory`, `search/changes`, `search/usages`)
- `read` — Read files and diagnostics (`read/readFile`, `read/problems`, `read/terminalLastCommand`, `read/terminalSelection`)
- `edit` — Create and modify files (`edit/editFiles`, `edit/createFile`, `edit/createDirectory`)
- `execute` — Run terminal commands and tests (`execute/runInTerminal`, `execute/getTerminalOutput`, `execute/testFailure`)
- `web` — Fetch web content (`web/fetch`) — for CVE lookups, changelogs, etc.
- `agent` — Delegate to subagents (`agent/runSubagent`)

**Agent structure best practices:**

```markdown
# Role
You are the [Role Title]. Your job is to [clear 1-line purpose].

## Before [Action]
Read these files first:
1. AGENTS.md — root context
2. Relevant .instructions.md files
3. Existing code for pattern matching

## Workflow
### Step 1 — [Action]
...
### Step 2 — [Action]
...

## After [Action]
Verify: run tests, check syntax, etc.

## Do Not
- List of guardrails
```

**This project's agents:**

| Agent | Purpose |
|---|---|
| `scaffolder` | Generate a complete new module (10 files) |
| `documentor` | Sync all docs with codebase reality |
| `code-reviewer` | Audit code for standards, security, localization |
| `tester` | Write missing PHPUnit tests |
| `package-upgrader` | Safely upgrade Composer/npm dependencies |
| `security-auditor` | OWASP-aligned vulnerability scanning |
| `deployer` | Pre-deploy checks, deployment, rollback |
| `code-refactorer` | Dead code, duplication, complexity reduction |
| `pr-reviewer` | Review changesets against conventions |

---

## Layer 4: Reusable Prompts

**Directory:** `.github/prompts/`

Prompt files are one-click task templates. They appear in VS Code's prompt picker and can reference agents or run standalone.

**Frontmatter format:**
```yaml
---
description: "What this prompt does (shown in picker)"
agent: agent
---
```

**When to use prompts vs. agents:**
- **Prompt** — Common task with fixed steps, triggered from the picker
- **Agent** — Complex workflow that needs conversation and judgment

**This project's prompts:**

| Prompt | Purpose |
|---|---|
| `new-module` | Scaffold a new resource module |
| `generate-tests` | Run tests and generate missing ones |
| `review-changes` | Review staged changes before commit |
| `sync-docs` | Update all docs to match code |
| `security-audit` | Run a security audit |
| `upgrade-deps` | Audit and upgrade dependencies |

---

## AGENTS.md — The Root Context File

`AGENTS.md` is the **single source of truth** that all agentic tools read. It works across multiple AI tools:

| Tool | How it discovers context |
|---|---|
| GitHub Copilot | Agents and instructions reference it via `read` tool |

**What goes in AGENTS.md:**
- Full architecture documentation
- Detailed directory map
- All conventions (not just a summary)
- Known technical debt with file paths
- Build & test commands
- Module creation checklist
- "Do Not" rules

**AGENTS.md vs. copilot-instructions.md:**
- `AGENTS.md` is **comprehensive** — the full reference
- `copilot-instructions.md` is a **compact subset** — loaded on every interaction, so must be concise
- Agents read both; casual Copilot interactions only see `copilot-instructions.md`

---

## Adapting for Your Project

### Minimum viable setup (15 minutes)

1. Create `.github/copilot-instructions.md` with your stack, architecture rules, and conventions
2. Done. Copilot will follow these rules on every interaction.

### Intermediate setup (1 hour)

3. Add `.github/instructions/` files for your main file types (controllers, models, tests)
4. Create `AGENTS.md` with full architecture documentation

### Full setup (half a day)

5. Add custom agents in `.github/agents/` for your team's workflows
6. Add reusable prompts in `.github/prompts/` for common tasks
7. Add per-module feature docs in `docs/features/`

### Tips

- **Start small** — `copilot-instructions.md` alone gives 80% of the benefit
- **Include code examples** — AI mimics patterns better than prose descriptions
- **Document tech debt** — prevents AI from silently working around bugs
- **Keep `copilot-instructions.md` under ~200 lines** — it's loaded on every interaction
- **Test your setup** — ask Copilot to scaffold something and verify it follows your conventions
- **Iterate** — watch what Copilot gets wrong, then add rules to prevent it

---

## File Checklist

```
✅ .github/copilot-instructions.md    — Global rules (auto-loaded)
✅ .github/instructions/               — File-type conventions (auto-applied by glob)
✅ .github/agents/                     — Custom agent workflows (invoked on demand)
✅ .github/prompts/                    — Reusable task templates (one-click)
✅ AGENTS.md                           — Root context for all AI tools
✅ docs/features/                      — Per-module documentation (optional but recommended)
```
