---
description: "Run a security audit covering OWASP Top 10, dependency vulnerabilities, and Laravel-specific anti-patterns"
agent: security-auditor
---

Perform a full security audit of this Laravel project.

Use the `security-auditor` agent workflow:

1. `composer audit` and `npm audit` for dependency vulnerabilities
2. Check authentication: rate limiting, session security, Sanctum config
3. Scan for mass assignment: `$guarded = []`, `$request->all()` in create/fill
4. Scan for SQL injection: raw queries without bindings
5. Scan for XSS: unescaped user input, `v-html` with user content
6. Verify every controller action has permission middleware
7. Check for exposed secrets, `dd()`/`dump()`, `$_ENV` usage
8. Report findings with severity (Critical/High/Medium/Low) and remediation steps
