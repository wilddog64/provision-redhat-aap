# System Patterns: provision-redhat-app

## Architecture
- Role-based deployment.
- Modular task structure (split into `install.yml`, `configure.yml`, etc.).

## Standards & Conventions
- Ansible best practices (naming, modules, idempotency).
- Documentation-first approach via Memory Bank.

## Working-Agent Guardrails (see `.clinerules` and `AGENTS.md` for full rules)
- **Scope discipline:** "review" and "plan" requests produce commentary or plan docs only — never code changes. Implement only when the user explicitly authorizes it. When in doubt, ask.
- **No destructive git commands without explicit permission.** In particular, **never** use `git clean -fd`/`-fdx` to revert your own work — it deletes pre-existing untracked files. To undo changes, use targeted `git restore <paths>` for modified tracked files and explicit `rm <file>` for files you created. Track what you touched so a surgical revert is always possible.
- **One plan per initiative**, date-stamped in `docs/plans/`. No duplicate plan files.
- **Agent entry points:** `AGENTS.md` is the canonical agent guide; `CLAUDE.md`, `GEMINI.md`, and `.github/copilot-instructions.md` point to it.
