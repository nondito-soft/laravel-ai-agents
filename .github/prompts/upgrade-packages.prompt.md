---
description: "Check for outdated Composer and npm packages, analyze compatibility, and safely upgrade"
agent: package-upgrader
---

Audit and upgrade project dependencies.

Use the `package-upgrader` agent workflow:

1. **Audit phase** (read-only):
   - `composer outdated --direct`
   - `npm outdated`
   - Build version compatibility matrix
   - Flag blockers (incompatible versions, abandoned packages)
2. **Present report** — categorize as Safe / Needs Code Changes / Cannot Upgrade / Abandoned
3. **Wait for approval** before making any changes
4. After approval: create branch, upgrade, run tests, verify build
