# Contributing to Laravel AI Agents

Thanks for your interest in improving this project! This repo is a template of agentic AI configurations (GitHub Copilot + Claude Code) for Laravel projects — contributions usually mean improving agents, instructions, prompts, or the docs around them.

## How to Contribute

1. **Fork** the repository and create a branch from `main`:

   ```bash
   git checkout -b feature/my-improvement
   ```

2. **Make your changes** (see guidelines below).
3. **Open a pull request** against `main` with a clear description of what changed and why.

## What We're Looking For

- **New or improved agents** — for either the `.github/agents/` (Copilot) or `.claude/agents/` (Claude Code) layer. Keep both layers in sync when the change applies to both.
- **File-type instructions** — backend conventions or additional frontend stacks under `instructions/`.
- **Reusable prompts** — workflow prompts under `prompts/` that solve a real, recurring Laravel task.
- **Documentation fixes** — corrections or clarifications to the setup guides in `docs/`.
- **Bug reports** — anything that doesn't work as documented.

## Guidelines

- **Mirror the two layers.** This repo maintains parallel setups for GitHub Copilot (`.github/`) and Claude Code (`.claude/`). If your change affects shared behavior, apply it to both.
- **Keep it generic.** Configurations should work for any Laravel project. Project-specific rules belong in the consuming project's own `AGENTS.md`, not here.
- **Respect the `FRONTEND` flag convention.** Frontend-stack-specific content must be gated behind the documented frontend stack options, not hardcoded to one stack.
- **Match existing structure and tone.** Look at neighboring files before adding new ones — naming, frontmatter, and formatting should be consistent.
- **Markdown style.** Use sentence-case headings, fenced code blocks with language tags, and tables where they aid scanning.

## Reporting Issues

Use the [issue templates](https://github.com/nondito-soft/laravel-ai-agents/issues/new/choose) — bug reports and feature requests each have one. For security issues, **do not open a public issue**; see [SECURITY.md](SECURITY.md).

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you agree to uphold it.

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
