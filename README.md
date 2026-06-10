# Laravel AI Agents

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Laravel](https://img.shields.io/badge/Laravel-FF2D20?logo=laravel&logoColor=white)](https://laravel.com)
[![PHP](https://img.shields.io/badge/PHP-777BB4?logo=php&logoColor=white)](https://www.php.net)
[![Copilot](https://img.shields.io/badge/GitHub_Copilot-Agentic-000?logo=github&logoColor=white)](https://github.com/features/copilot)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Agentic-D97706?logo=anthropic&logoColor=white)](https://claude.ai/code)

> **12 agents** · **12 file-type instructions** (8 backend + 4 frontend stacks) · **11 reusable prompts** · **GitHub Copilot + Claude Code**

The production-ready agentic setup for Laravel projects. Custom agents, file-type instructions, reusable prompts, and cross-tool context files — extracted from a real Laravel codebase with actual architecture rules, conventions, and tech debt tracking that the AI follows.

Not a generic config pack. Not starter templates. A complete system that makes AI understand your Laravel project's architecture and enforce your team's conventions — works with both **GitHub Copilot** and **Claude Code**.

---

## The Guides

The setup guides explain each tool's layer system, with examples and patterns for each component.

| | |
|---|---|
| **[Copilot Agentic Setup Guide](docs/copilot-agentic-setup-guide.md)** | GitHub Copilot setup — file hierarchy, glob patterns, agent design, prompt templates. |
| **[Claude Code Agentic Setup Guide](docs/claude-code-agentic-setup-guide.md)** | Claude Code setup — agents, slash commands, rules, CLAUDE.md wiring. |

---

## Quick Start

Get up and running in under 2 minutes:

### Step 1: Clone the repo

```bash
git clone https://github.com/nondito-soft/laravel-ai-agents.git
```

### Step 2: Copy to your Laravel project

**GitHub Copilot:**
```bash
cp -r laravel-ai-agents/.github your-laravel-project/
```

**Claude Code:**
```bash
cp -r laravel-ai-agents/.claude your-laravel-project/
```

**Universal context file (required for both):**
```bash
cp laravel-ai-agents/AGENTS.md your-laravel-project/
```

> You can copy one tool's layer or both — they are fully independent.

### Step 3: Customize for your project

Edit the files to match your actual stack, architecture, and conventions. See [What to Customize](#what-to-customize) below.

### Step 4: Start using

**GitHub Copilot:**
```
# Invoke an agent in Copilot chat
@scaffolder Add a Department module

# Or use a reusable prompt from the prompt picker
> scaffold-module
```

**Claude Code:**
```
# Invoke an agent via slash command
/scaffold-module Department

# Or use any command from the command palette
/review-code
```

That's it. You now have 12 agents, 12 instruction sets (8 backend + 4 frontend stacks), and 11 prompts/commands working with your codebase.

> **Alternative:** Don't want to copy? Browse the files and create your own from scratch using the patterns shown here.

### ⚠️ Important: Customize for Your Project

These files encode your specific architecture and conventions. Before running agents, update:

**GitHub Copilot:**
- `.github/copilot-instructions.md` — stack, rules, directory map
- `.github/instructions/*.instructions.md` — your code patterns and examples
- `.github/agents/*.agent.md` — your team's workflows

**Claude Code:**
- `.claude/CLAUDE.md` — references AGENTS.md; add any Claude-specific overrides
- `.claude/rules/*.md` — your code patterns (mirrors `.github/instructions/`)
- `.claude/agents/*.agent.md` — your team's workflows (mirrors `.github/agents/`)

**Both tools:**
- `AGENTS.md` — your complete architecture guide and known tech debt

See [What to Customize](#what-to-customize) for details.

---

## How It Works

Both tools use a layered instruction system. Each layer is loaded at a different time and serves a different purpose.

### GitHub Copilot

| Layer | Files | When Loaded | Purpose |
|---|---|---|---|
| **1. Global** | `.github/copilot-instructions.md` | Every Copilot interaction | Stack, architecture rules, conventions |
| **2. File-type** | `.github/instructions/*.instructions.md` | When editing files matching the `applyTo` glob | Per-filetype patterns and constraints |
| **3. Agents** | `.github/agents/*.agent.md` | When you invoke `@agent-name` in chat | Specialized multi-step workflows |
| **4. Prompts** | `.github/prompts/*.prompt.md` | When you select from the prompt picker | One-click task templates |

### Claude Code

| Layer | Files | When Loaded | Purpose |
|---|---|---|---|
| **1. Global** | `AGENTS.md` + `.claude/CLAUDE.md` | Every Claude Code session | Architecture rules, conventions, quick reference |
| **2. Rules** | `.claude/rules/*.md` | Attached to relevant file edits | Per-filetype patterns and constraints |
| **3. Agents** | `.claude/agents/*.agent.md` | When Claude Code spawns a subagent | Specialized multi-step workflows |
| **4. Commands** | `.claude/commands/*.md` | When you run `/command-name` | One-click slash command templates |

---

## What's Inside

```
laravel-ai-agents/
|
|-- .github/                                   # GitHub Copilot layer
|   |-- copilot-instructions.md               #   Global rules (auto-loaded on every interaction)
|   |
|   |-- instructions/                          #   File-type conventions (auto-applied by glob)
|   |   |-- laravel-controllers.instructions.md    # app/Http/**/*.php
|   |   |-- laravel-services.instructions.md       # app/Services/**/*.php
|   |   |-- laravel-models.instructions.md         # app/Models/**/*.php
|   |   |-- laravel-migrations.instructions.md     # database/migrations/**/*.php
|   |   |-- laravel-seeders.instructions.md        # database/seeders/**/*.php
|   |   |-- laravel-routes.instructions.md         # routes/**/*.php
|   |   |-- laravel-lang.instructions.md           # resources/lang/**
|   |   |-- laravel-tests.instructions.md          # tests/**/*.php
|   |   |-- frontend/                              #   One per stack — only the active FRONTEND applies
|   |       |-- vue-inertia.instructions.md            # resources/js/** (Vue)
|   |       |-- react-inertia.instructions.md          # resources/js/** (React)
|   |       |-- livewire.instructions.md               # resources/views/** (Livewire)
|   |       |-- blade.instructions.md                  # resources/views/** (Blade)
|   |
|   |-- agents/                                #   Specialized agents (invoked via @agent-name)
|   |   |-- developer.agent.md
|   |   |-- scaffolder.agent.md
|   |   |-- documentor.agent.md
|   |   |-- code-reviewer.agent.md
|   |   |-- tester.agent.md
|   |   |-- package-upgrader.agent.md
|   |   |-- security-auditor.agent.md
|   |   |-- deployer.agent.md
|   |   |-- code-refactorer.agent.md
|   |   |-- pr-reviewer.agent.md
|   |   |-- planner.agent.md
|   |   |-- code-formatter.agent.md
|   |
|   |-- prompts/                               #   One-click reusable task templates
|       |-- develop.prompt.md
|       |-- scaffold-module.prompt.md
|       |-- generate-tests.prompt.md
|       |-- review-code.prompt.md
|       |-- format-code.prompt.md
|       |-- update-docs.prompt.md
|       |-- audit-security.prompt.md
|       |-- upgrade-packages.prompt.md
|       |-- refactor-code.prompt.md
|       |-- plan-project.prompt.md
|       |-- deploy.prompt.md
|
|-- .claude/                                   # Claude Code layer
|   |-- CLAUDE.md                             #   Auto-loaded by Claude Code; references AGENTS.md
|   |-- settings.json                         #   Harness config: permissions, hooks, env
|   |
|   |-- rules/                                 #   File-type conventions (same rules, Claude format)
|   |   |-- laravel-controllers.md
|   |   |-- laravel-services.md
|   |   |-- laravel-models.md
|   |   |-- laravel-migrations.md
|   |   |-- laravel-seeders.md
|   |   |-- laravel-routes.md
|   |   |-- laravel-lang.md
|   |   |-- laravel-tests.md
|   |   |-- frontend/                           #   One per stack — only the active FRONTEND applies
|   |       |-- vue-inertia.md
|   |       |-- react-inertia.md
|   |       |-- livewire.md
|   |       |-- blade.md
|   |
|   |-- agents/                                #   Specialized subagents (same agents, Claude format)
|   |   |-- developer.agent.md
|   |   |-- scaffolder.agent.md
|   |   |-- documentor.agent.md
|   |   |-- code-reviewer.agent.md
|   |   |-- tester.agent.md
|   |   |-- package-upgrader.agent.md
|   |   |-- security-auditor.agent.md
|   |   |-- deployer.agent.md
|   |   |-- code-refactorer.agent.md
|   |   |-- pr-reviewer.agent.md
|   |   |-- planner.agent.md
|   |   |-- code-formatter.agent.md
|   |
|   |-- commands/                              #   Slash command templates (invoked via /command)
|       |-- develop.md
|       |-- scaffold-module.md
|       |-- generate-tests.md
|       |-- review-code.md
|       |-- format-code.md
|       |-- update-docs.md
|       |-- audit-security.md
|       |-- upgrade-packages.md
|       |-- refactor-code.md
|       |-- plan-project.md
|       |-- deploy.md
|
|-- AGENTS.md                                  # Root context file (all AI tools read this)
|-- docs/
    |-- copilot-agentic-setup-guide.md         # GitHub Copilot setup guide
    |-- claude-code-agentic-setup-guide.md     # Claude Code setup guide
```

---

## Which Agent Should I Use?

Not sure where to start? Use this quick reference:

| I want to... | Copilot | Claude Code | What it does |
|---|---|---|---|
| Add a new module/resource | `@scaffolder` | `/scaffold-module` | Generates the full stack: migration → model → service → FormRequests → controller → routes → permissions → translations → feature doc |
| Write or fix tests | `@tester` | `/generate-tests` | Writes missing PHPUnit Feature and Unit tests with proper assertions |
| Review my code | `@code-reviewer` | `/review-code` | Audits for standards, security, localization, and theme compliance |
| Review a PR/changeset | `@pr-reviewer` | — | Checks architecture, naming, permissions, translations, and tests |
| Update documentation | `@documentor` | `/update-docs` | Scans codebase and updates all docs to match reality |
| Find security issues | `@security-auditor` | `/audit-security` | OWASP-aligned vulnerability scanning across the full stack |
| Upgrade dependencies | `@package-upgrader` | `/upgrade-packages` | Safely upgrades Composer and npm packages with compatibility checks |
| Clean up code | `@code-refactorer` | `/refactor-code` | Dead code removal, duplication reduction, complexity analysis |
| Format code | `@code-formatter` | `/format-code` | Runs Pint (PHP), Prettier (JavaScript/Vue), validates formatting consistency |
| Deploy the app | `@deployer` | `/deploy` | Pre-deploy checks, deployment execution, post-deploy verification, rollback |
| Plan a feature | `@planner` | `/plan-project` | Scope analysis, task breakdown, timeline, dependency mapping |

### Common Workflows

**Adding a new feature:**

```
# GitHub Copilot
@scaffolder Add a Department module with name, code, and parent_id
@tester Generate tests for DepartmentController and DepartmentService
@code-reviewer Review the Department module

# Claude Code
/scaffold-module Department
/generate-tests
/review-code
```

**Preparing for production:**

```
# GitHub Copilot
@security-auditor Run a full security audit
@tester Run all tests and generate missing ones
@deployer Run pre-deploy checks

# Claude Code
/audit-security
/generate-tests
/deploy
```

**Maintaining the codebase:**

```
# GitHub Copilot
@code-refactorer Find dead code and unused dependencies
@documentor Sync all docs with the current codebase
@package-upgrader Check for outdated packages

# Claude Code
/refactor-code
/update-docs
/upgrade-packages
```

---

## File-Type Instructions

When you edit a file, the tool automatically loads the matching instruction set based on the file path. No manual invocation needed. Both tools have identical rules — just in their native format.

| Applies To | Copilot (`.github/instructions/`) | Claude Code (`.claude/rules/`) | What It Enforces |
|---|---|---|---|
| `app/Http/**/*.php` | `laravel-controllers.instructions.md` | `laravel-controllers.md` | HasMiddleware, route model binding, Inertia responses, thin controllers |
| `app/Services/**/*.php` | `laravel-services.instructions.md` | `laravel-services.md` | BaseModelService, DB transactions, activity logging, business logic |
| `app/Models/**/*.php` | `laravel-models.instructions.md` | `laravel-models.md` | `$fillable`, SoftDeletes, relationships, model constraints |
| `database/migrations/**/*.php` | `laravel-migrations.instructions.md` | `laravel-migrations.md` | Required columns, SoftDeletes, naming conventions |
| `database/seeders/**/*.php` | `laravel-seeders.instructions.md` | `laravel-seeders.md` | Permission seeder structure, required fields |
| `routes/**/*.php` | `laravel-routes.instructions.md` | `laravel-routes.md` | Auth group structure, named routes, breadcrumbs |
| `resources/lang/**` | `laravel-lang.instructions.md` | `laravel-lang.md` | Translation key conventions, file organization |
| `tests/**/*.php` | `laravel-tests.instructions.md` | `laravel-tests.md` | RefreshDatabase, permission seeding, render assertions per stack |
| `resources/js/**`, `resources/views/**` | `frontend/{stack}.instructions.md` | `frontend/{stack}.md` | Per the active `FRONTEND` stack (`vue-inertia` · `react-inertia` · `livewire` · `blade`) — navigation, forms, shared data, Tailwind; only the matching file applies |

---

## Reusable Prompts & Commands

One-click task templates. Select from the prompt picker in Copilot, or run as a slash command in Claude Code.

| Task | Copilot prompt | Claude Code command | What it does |
|---|---|---|---|
| `scaffold-module` | `> scaffold-module` | `/scaffold-module` | Scaffold a complete new module |
| `generate-tests` | `> generate-tests` | `/generate-tests` | Run existing tests, then generate missing ones |
| `review-code` | `> review-code` | `/review-code` | Review staged/uncommitted changes before commit |
| `update-docs` | `> update-docs` | `/update-docs` | Update all documentation to match the current code |
| `audit-security` | `> audit-security` | `/audit-security` | Run a full OWASP-aligned security audit |
| `upgrade-packages` | `> upgrade-packages` | `/upgrade-packages` | Check and upgrade Composer + npm dependencies |
| `format-code` | `> format-code` | `/format-code` | Run PHP and JavaScript formatters |
| `refactor-code` | `> refactor-code` | `/refactor-code` | Analyze and refactor code for health |
| `plan-project` | `> plan-project` | `/plan-project` | Scope analysis, task breakdown, timeline estimation |
| `deploy` | `> deploy` | `/deploy` | Execute safe deployment with verification |

---

## Cross-Tool Compatibility

`AGENTS.md` is the **universal context file** — a single source of truth read by all major AI coding tools:

| Tool | Context File | Instructions / Rules | Agents | Prompts / Commands |
|---|---|---|---|---|
| **GitHub Copilot** | `AGENTS.md` (auto) + `.github/copilot-instructions.md` (auto) | `.github/instructions/` (auto by glob) | `.github/agents/` (via `@agent-name`) | `.github/prompts/` (prompt picker) |
| **Claude Code** | `AGENTS.md` (auto) + `.claude/CLAUDE.md` (auto) | `.claude/rules/` | `.claude/agents/` (subagents) | `.claude/commands/` (via `/command`) |

> **Key insight:** Write your architecture rules, conventions, and tech debt in `AGENTS.md` once. Every AI tool picks it up automatically. Tool-specific features layer on top in their own directories.

---

## Architecture Patterns Demonstrated

This setup demonstrates patterns you can adapt for any Laravel project:

| Pattern | How it works |
|---|---|
| **Service layer** | All business logic in `app/Services/`, thin controllers that validate → call service → return response |
| **File-type instructions** | Different AI rules for controllers vs models vs migrations — each file type gets contextual guidance |
| **Permission middleware** | Convention-driven RBAC with Spatie: `can-{view\|create\|edit\|delete}-{resource}` |
| **Activity logging** | Manual logging via `activity()` helper — granular control, no observers |
| **Translation conventions** | Key patterns for all user-facing strings: `__('message.custom.resource.action.outcome')` |
| **Tech debt tracking** | Known issues documented in `AGENTS.md` — AI knows about them and fixes properly instead of working around |
| **Cross-tool context** | Single `AGENTS.md` feeds all AI tools; tool-specific files layer on top |

---

## Stack This Was Built For

| Layer | Technology |
|---|---|
| Framework | Laravel (any version) / PHP 8.1+ |
| Frontend | Configurable via `FRONTEND` in `AGENTS.md` — `vue-inertia` (default) · `react-inertia` · `livewire` · `blade` · all + Tailwind CSS |
| Auth (web) | Laravel Breeze (session) |
| Auth (API) | Laravel Sanctum (token) |
| RBAC | Spatie laravel-permission |
| Audit | Spatie laravel-activitylog |
| Login tracking | Rappasoft laravel-authentication-log |
| Breadcrumbs | Diglactic laravel-breadcrumbs |
| DB | SQLite (dev) · MySQL (prod) |

> **Important:** These files contain conventions from this specific stack. You **must** adapt them to your project. See [What to Customize](#what-to-customize) below.

---

## What to Customize

These are **not drop-in configs** — they encode a specific project's architecture and conventions. **Every organization must adapt them to match their own:**

### 1. Root Context File (`AGENTS.md`)

**This is your authoritative architecture guide — update it first.** Both tools read it automatically.

1. **Architecture** section with your actual patterns
2. **Directory Map** with your project structure
3. **Key Conventions** with your team's rules
4. **Adding a New Module** checklist tailored to your process
5. **Known Technical Debt** with YOUR actual issues (not generic examples)
6. **Do Not** section with your team's actual anti-patterns

### 2. GitHub Copilot Layer

**Global instructions** (`.github/copilot-instructions.md`) — update Stack, Architecture Rules, Directory Map, and Code Conventions to match your project.

**File-type instructions** (`.github/instructions/`) — update code examples in each file to match your actual patterns. If your controllers use a `BaseController`, show that. If your services follow a different pattern, update the examples.

**Agents** (`.github/agents/`) — customize each agent for your team's workflow. Update file paths, tool names, and verification steps. Delete agents you don't use.

### 3. Claude Code Layer

**CLAUDE.md** (`.claude/CLAUDE.md`) — by default it imports `AGENTS.md` via `@AGENTS.md`. Add any Claude Code-specific overrides below that import.

**Rules** (`.claude/rules/`) — mirrors `.github/instructions/`. Update with the same changes you made there, in Claude's rule format (no frontmatter glob, just markdown).

**Agents** (`.claude/agents/`) — mirrors `.github/agents/`. Key difference: agent files reference `.claude/rules/` paths instead of `.github/instructions/` paths. Update those internal references when customizing.

**Commands** (`.claude/commands/`) — mirrors `.github/prompts/`. Update the prompt text to match your workflow.

### Checklist for Customization

Essential changes **before using agents:**

**Both tools:**
- [ ] Update `AGENTS.md` — architecture, directory map, conventions, known tech debt
- [ ] Remove or replace the example Known Technical Debt entries with your own

**GitHub Copilot:**
- [ ] Update `.github/copilot-instructions.md` Stack section with your actual stack
- [ ] Update `.github/instructions/*.instructions.md` — replace code examples with YOUR patterns
- [ ] Customize `.github/agents/*.agent.md` for your workflow (update file paths, steps)
- [ ] Delete agents you don't use
- [ ] Test with `@code-reviewer` to verify customization

**Claude Code:**
- [ ] Update `.claude/rules/*.md` — keep in sync with `.github/instructions/` changes
- [ ] Customize `.claude/agents/*.agent.md` — keep rule file paths pointing to `.claude/rules/`
- [ ] Test with `/review-code` to verify customization

### Why This Matters

AI agents work best when they understand YOUR project's specific rules. Generic instructions lead to:

❌ Code that doesn't match your architecture  
❌ Suggestions that contradict your conventions  
❌ Agents that reference non-existent patterns  
❌ False positives in code reviews and audits  

With proper customization:

✅ Generated code matches your standards  
✅ Agents understand your technical debt and work around it  
✅ Code reviews enforce YOUR conventions  
✅ Automated tasks save actual time  

---

## FAQ

<details>
<summary><strong>Do I need both Copilot and Claude Code layers?</strong></summary>

No. Copy only the layer you use. If you use GitHub Copilot, copy `.github/` and `AGENTS.md`. If you use Claude Code, copy `.claude/` and `AGENTS.md`. Both layers are fully independent.
</details>

<details>
<summary><strong>Do I need all 12 agents?</strong></summary>

No. Start with 2-3 that match your workflow (e.g., `scaffolder` + `tester` + `code-reviewer`) and add more as needed. Delete the agent file from whichever tool directory you're using.
</details>

<details>
<summary><strong>Does this work with projects other than Laravel?</strong></summary>

The structure works for any project. The file hierarchy (global instructions → file-type instructions → agents → prompts/commands) is framework-agnostic. Replace the Laravel-specific content with your stack's conventions.
</details>

<details>
<summary><strong>What's the difference between AGENTS.md, copilot-instructions.md, and CLAUDE.md?</strong></summary>

`AGENTS.md` is the full architecture reference — both tools read it automatically. `copilot-instructions.md` is a compact subset auto-loaded by Copilot on every interaction. `CLAUDE.md` is loaded by Claude Code and by default just imports `AGENTS.md` via `@AGENTS.md`. Keep `AGENTS.md` as the single source of truth and derive the other two from it.
</details>

<details>
<summary><strong>Do Copilot agents work in VS Code only?</strong></summary>

Copilot agents (`.github/agents/`) and prompts (`.github/prompts/`) work in VS Code and GitHub.com. Claude Code agents (`.claude/agents/`) and commands (`.claude/commands/`) work in the Claude Code CLI, VS Code extension, and JetBrains extension. `AGENTS.md` and instruction/rule files work across all supported tools.
</details>

<details>
<summary><strong>How do I add a new file-type instruction?</strong></summary>

For Copilot: create a file in `.github/instructions/` with the `.instructions.md` extension and add YAML frontmatter with an `applyTo` glob pattern. For Claude Code: create a matching file in `.claude/rules/` with a `paths` array in its frontmatter. Frontend rules go in the `frontend/` subfolder (one per `FRONTEND` stack). See the setup guides for the full format.
</details>

<details>
<summary><strong>If I update a rule, do I need to update it in both places?</strong></summary>

Yes — the two directories are kept in sync manually. The same rule lives in `.github/instructions/` for Copilot and `.claude/rules/` for Claude Code. When you change one, update the other. The content is identical; only the file format differs slightly (Copilot uses `applyTo` frontmatter, Claude rules use `paths` frontmatter).
</details>

---

## Contributing

Contributions are welcome and encouraged. If you have:

- Better patterns for Laravel-specific agents
- Additional instruction/rule files (e.g., Livewire, Filament, API Resources, Queues)
- Prompt or command templates for common Laravel tasks
- Improvements to either setup guide
- Agents for other frameworks (adapt the structure)

Please open a PR. When contributing agent or instruction improvements, update **both** the `.github/` and `.claude/` versions so both tools stay in sync.

### Ideas for Contributions

- **Framework-specific agents** — Livewire, Filament, Nova instruction files (for both tools)
- **Additional prompts/commands** — Database optimization, API versioning, queue management
- **Language expansions** — Translate agents and prompts for multilingual teams
- **CI/CD integration** — Agents that work with GitHub Actions workflows

---

## Links

- Copilot Setup Guide: **[docs/copilot-agentic-setup-guide.md](docs/copilot-agentic-setup-guide.md)**
- Claude Code Setup Guide: **[docs/claude-code-agentic-setup-guide.md](docs/claude-code-agentic-setup-guide.md)**
- Root Context: **[AGENTS.md](AGENTS.md)** (architecture reference for all AI tools)

---

## License

MIT — Use freely, modify as needed, contribute back if you can.

---

Built by [Nondito Soft](https://github.com/nondito-soft). Extracted from a production Laravel project.
