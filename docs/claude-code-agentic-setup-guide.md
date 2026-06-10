# Claude Code Agentic Mode — Setup Guide

> How this project uses Claude Code's agentic features for AI-assisted development.  
> Use this as a reference for setting up your own projects.

This is the Claude Code counterpart to the [Copilot Agentic Setup Guide](copilot-agentic-setup-guide.md). Both layers coexist — `.github/` drives GitHub Copilot, `.claude/` drives Claude Code — and both derive from the same root [`AGENTS.md`](../AGENTS.md).

---

## File Hierarchy

Claude Code uses a layered context system. Each layer serves a different purpose:

```
.claude/
├── CLAUDE.md                  # Layer 1: Project memory (auto-loaded, imports AGENTS.md)
├── rules/                     # Layer 2: File-type rules (read by agents/commands)
│   ├── laravel-controllers.md
│   ├── laravel-services.md
│   ├── frontend/              # One file per FRONTEND stack (only the active one applies)
│   │   ├── vue-inertia.md
│   │   ├── react-inertia.md
│   │   ├── livewire.md
│   │   └── blade.md
│   └── ...
├── agents/                    # Layer 3: Subagents (invoked on demand)
│   ├── scaffolder.agent.md
│   ├── tester.agent.md
│   └── ...
├── commands/                  # Layer 4: Slash commands (one-click tasks)
│   ├── scaffold-module.md
│   ├── generate-tests.md
│   └── ...
└── settings.json              # Harness config: permissions, hooks, env

AGENTS.md                      # Root context: single source of truth for all tools
```

### How Each Layer Works

| File | When Loaded | Purpose |
|---|---|---|
| `.claude/CLAUDE.md` | **Every** Claude Code session | Project memory; imports `AGENTS.md` + quick command reference |
| `.claude/rules/*.md` | Read by agents/commands for the relevant file type | File-type-specific patterns and examples |
| `.claude/agents/*.agent.md` | When a subagent is dispatched | Specialized multi-step workflows |
| `.claude/commands/*.md` | When the user types `/command-name` | Reusable one-click task templates |
| `AGENTS.md` | Imported by `CLAUDE.md` and referenced by agents | Full architecture, tech debt, directory map |

---

## Layer 1: Project Memory (CLAUDE.md)

**File:** `.claude/CLAUDE.md`

This file is loaded into context at the start of **every** Claude Code session. It uses an `@import` to pull in the root [`AGENTS.md`](../AGENTS.md), then adds a Claude-specific quick reference (slash commands + build commands).

**What to include:**
- `@AGENTS.md` import (so the full architecture is always present)
- A quick-reference table mapping tasks to slash commands
- Common build/test commands

**What NOT to include:**
- Duplicated architecture detail — it lives in `AGENTS.md`; import, don't copy
- File-type specifics — those belong in `.claude/rules/`
- Step-by-step workflows — those belong in `.claude/agents/`

---

## Layer 2: File-Type Rules

**Directory:** `.claude/rules/`

These mirror the Copilot `.github/instructions/*.instructions.md` files — same rules, Claude-native naming (`.md` instead of `.instructions.md`, and a `paths` frontmatter array instead of Copilot's `applyTo` glob). Agents and commands read the relevant rule file before acting on a given file type.

**Frontmatter format:**
```yaml
---
description: "Brief description of when this rule applies"
paths: ["app/Http/**/*.php"]
---
```

**Best practices:**
- **One file per domain** — controllers, services, models, routes, seeders, migrations, lang, tests
- **Include code examples** — the model produces better output when it sees the expected pattern
- **Keep frontend rules stack-split** — `rules/frontend/{stack}.md`; only the file matching the `FRONTEND` config in `AGENTS.md` applies
- **Keep backend rules stack-neutral** — state intent ("return the index view with `breadcrumbs` + `pageTitle`") and defer the mechanism to the active frontend rule

**This project's rules:**

| File | Applies to |
|---|---|
| `laravel-controllers.md` | `app/Http/**/*.php` |
| `laravel-services.md` | `app/Services/**/*.php` |
| `laravel-models.md` | `app/Models/**/*.php` |
| `laravel-migrations.md` | `database/migrations/**/*.php` |
| `laravel-seeders.md` | `database/seeders/**/*.php` |
| `laravel-routes.md` | `routes/**/*.php` |
| `laravel-lang.md` | `resources/lang/**` |
| `laravel-tests.md` | `tests/**/*.php` |
| `frontend/{stack}.md` | `resources/js/**`, `resources/views/**` (only the active `FRONTEND` stack) |

---

## Layer 3: Subagents

**Directory:** `.claude/agents/`

Agents are specialized personas Claude Code dispatches for focused, multi-step work. Each has a defined role, reads specific context files, and follows a structured workflow.

**Frontmatter format:**
```yaml
---
name: scaffolder
description: "Use when adding a new module. Generates migration, model, service, controller, routes, etc."
tools: [Read, Grep, Glob, Edit, Write, Bash]
---
```

**Tool vocabulary** (Claude Code native — the equivalent of Copilot's `search`/`read`/`edit`/`execute`/`web`):
- `Read`, `Grep`, `Glob` — read and search the codebase (Copilot `read` + `search`)
- `Edit`, `Write` — create and modify files (Copilot `edit`)
- `Bash` — run commands and tests (Copilot `execute`)
- `WebFetch`, `WebSearch` — fetch web content for CVE lookups, changelogs (Copilot `web`)

Grant only the tools an agent needs — read-only reviewers (e.g. `pr-reviewer`, `planner`) should not get `Edit`/`Write`.

**Agent structure best practices:**

```markdown
# Role
You are the [Role Title]. Your job is to [clear 1-line purpose].

## Before [Action]
Read these files first:
1. AGENTS.md — root context
2. Relevant .claude/rules/*.md files
3. Existing code for pattern matching

## Workflow
### Step 1 — [Action]
...

## After [Action]
Verify: run tests, check syntax, run `./vendor/bin/pint`.

## Do Not
- List of guardrails
```

**This project's agents** (mirror the `.github/agents/` set one-to-one):

| Agent | Purpose |
|---|---|
| `scaffolder` | Generate a complete new module (migration → feature doc) |
| `documentor` | Sync all docs with codebase reality |
| `code-reviewer` | Audit code for standards, security, localization |
| `code-formatter` | Format PHP (Pint) + frontend (Prettier/Blade) |
| `tester` | Write missing PHPUnit tests |
| `package-upgrader` | Safely upgrade Composer/npm dependencies |
| `security-auditor` | OWASP-aligned vulnerability scanning |
| `deployer` | Pre-deploy checks, deployment, rollback |
| `code-refactorer` | Dead code, duplication, complexity reduction |
| `pr-reviewer` | Review changesets against conventions |
| `planner` | Scope analysis, feature breakdown, roadmaps |

---

## Layer 4: Slash Commands

**Directory:** `.claude/commands/`

Command files are one-click task templates invoked by typing `/command-name`. They are the Claude Code counterpart to Copilot's `.github/prompts/*.prompt.md`.

**Frontmatter format:**
```yaml
---
description: "What this command does"
---
```

**Argument syntax:** Claude Code commands use `$ARGUMENTS` for user input (the Copilot equivalent is `${input:variableName}`).

**When to use commands vs. agents:**
- **Command** — Common task with fixed steps, triggered by `/name`
- **Agent** — Complex workflow that needs conversation and judgment (a command often delegates to an agent)

**This project's commands** (mirror the `.github/prompts/` set one-to-one):

| Command | Purpose |
|---|---|
| `/scaffold-module` | Scaffold a new resource module |
| `/generate-tests` | Run tests and generate missing ones |
| `/review-code` | Review staged changes before commit |
| `/format-code` | Format all code |
| `/update-docs` | Update all docs to match code |
| `/audit-security` | Run a security audit |
| `/upgrade-packages` | Audit and upgrade dependencies |
| `/refactor-code` | Analyze and refactor for health |
| `/plan-project` | Analyze scope, break down features, roadmap |
| `/deploy` | Execute safe deployment with verification |

---

## AGENTS.md — The Root Context File

`AGENTS.md` is the **single source of truth** that both layers read. Claude Code discovers it via the `@AGENTS.md` import in `.claude/CLAUDE.md`.

**AGENTS.md vs. CLAUDE.md:**
- `AGENTS.md` is **comprehensive** — the full reference (architecture, directory map, conventions, tech debt, "Do Not" rules)
- `CLAUDE.md` is a **thin wrapper** — it imports `AGENTS.md` and adds the slash-command + build quick reference
- Keep architecture in `AGENTS.md` only; `CLAUDE.md` should never duplicate it

---

## Keeping the Two Layers in Sync

The `.claude/` and `.github/` trees are intentional mirrors. When you change a rule, agent, or task in one, update its counterpart:

| Claude Code | GitHub Copilot |
|---|---|
| `.claude/CLAUDE.md` | `.github/copilot-instructions.md` |
| `.claude/rules/*.md` | `.github/instructions/*.instructions.md` |
| `.claude/agents/*.agent.md` | `.github/agents/*.agent.md` |
| `.claude/commands/*.md` | `.github/prompts/*.prompt.md` |

**Expected differences (not drift):**
- Tool vocabulary — `[Read, Grep, Glob, Bash]` vs `[search, read, execute]`
- Argument syntax — `$ARGUMENTS` vs `${input:name}`
- File naming — `.md` vs `.instructions.md` / `.prompt.md`
- Path references — `.claude/rules/` vs `.github/instructions/`

Everything else (the actual rules, checklists, and workflow steps) should stay identical.

---

## Adapting for Your Project

### Minimum viable setup (15 minutes)

1. Create `.claude/CLAUDE.md` that imports an `AGENTS.md` with your stack, architecture rules, and conventions.
2. Done. Claude Code loads it every session.

### Intermediate setup (1 hour)

3. Add `.claude/rules/` files for your main file types (controllers, models, tests).
4. Flesh out `AGENTS.md` with full architecture documentation.

### Full setup (half a day)

5. Add subagents in `.claude/agents/` for your team's workflows.
6. Add slash commands in `.claude/commands/` for common tasks.
7. Add per-module feature docs in `docs/features/`.

### Tips

- **Start small** — `CLAUDE.md` + `AGENTS.md` alone gives most of the benefit
- **Include code examples** — the model mimics patterns better than prose
- **Document tech debt** — prevents Claude from silently working around bugs
- **Grant least-privilege tools** — read-only agents should not get `Edit`/`Write`
- **Iterate** — watch what Claude gets wrong, then add rules to prevent it

---

## File Checklist

```
✅ .claude/CLAUDE.md          — Project memory (auto-loaded, imports AGENTS.md)
✅ .claude/rules/             — File-type conventions (read by agents/commands)
✅ .claude/agents/            — Subagent workflows (invoked on demand)
✅ .claude/commands/          — Slash-command task templates (one-click)
✅ .claude/settings.json      — Permissions, hooks, env
✅ AGENTS.md                  — Root context for all AI tools
✅ docs/features/             — Per-module documentation (optional but recommended)
```
