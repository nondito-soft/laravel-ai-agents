---
description: "Analyze scope, break down features into tasks, estimate timeline, map dependencies, and create actionable roadmap"
agent: planner
---

Create a detailed project plan for **${input:featureDescription}**.

Use the `planner` agent workflow. Read `AGENTS.md` and `README.md` first to understand the project architecture, then:

1. **Scope Analysis** — Understand the feature, dependencies, and integrations
2. **Task Breakdown** — Break down into testability units (migrations, models, services, controllers, frontend, tests)
3. **Effort Estimation** — Estimate each task (1h, 2h, 4h, 8h, 1d, 2d, etc.) and total effort
4. **Timeline Calculation** — Apply buffers (15-20%) and estimate delivery dates
5. **Dependency Mapping** — Identify critical path, blockers, and parallel work
6. **Risk Assessment** — Call out unknowns, high-risk items, and mitigation
7. **Roadmap Creation** — Phase breakdown with deliverables for each phase

Provide output in structured markdown format with:
- Scope Summary
- Requirements Checklist
- Task Breakdown (organized by layer)
- Timeline Estimate
- Dependency Graph
- Risk & Mitigation
- Phased Roadmap
- Resource Requirements

Ask clarifying questions if scope is ambiguous (integration points, constraints, MVP vs. full feature, etc.).
