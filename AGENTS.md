# AGENTS.md

Instructions for AI coding agents (Claude, Copilot CLI, Cline, Gemini, Cursor, etc.) working in this repository. This file follows the [AGENTS.md](https://agents.md) convention and is the canonical source of agent guidance. Agent-specific files (`CLAUDE.md`, `GEMINI.md`, `.github/copilot-instructions.md`) point here.

> **Also read:** [`.clinerules`](./.clinerules) and everything under [`memory-bank/`](./memory-bank/) before making changes. The Memory Bank is the project's canonical state; `.clinerules` expands on the guardrails in this document.

---

## 1. Project at a glance

- **What this is:** a single Ansible role, `provision-redhat-app`, that deploys **Red Hat Ansible Automation Platform (AAP) 2.5 (containerized installer)** to RHEL 9.x and integrates it with Active Directory via LDAPS.
- **Target host:** `pas01` (`us03l01pas01.dev.costcotravel.com`, RHEL 9.7). Details in `memory-bank/key_knowledge.md`.
- **Repository layout:** this directory **is** the role. There is no `roles/` wrapper. Consumers invoke it from external playbooks or from `tests/test.yml`.
- **Current phase:** planning complete; implementation not yet started. See `memory-bank/activeContext.md` and `docs/plans/` for the current plan.

## 2. How to work here

### 2.1 Read before you act
1. `memory-bank/activeContext.md` — what's happening right now and what's next.
2. `memory-bank/task_state.md` — canonical task list (progress.md holds milestones only).
3. `memory-bank/key_knowledge.md`, `techContext.md`, `systemPatterns.md` — facts you need.
4. `docs/plans/<latest>.md` — current implementation plan.
5. `.clinerules` — hard rules about scope and destructive operations.

### 2.2 Scope discipline (critical)
- **"Review", "look at", "analyze", "check"** → produce commentary/findings only. Do **not** modify files or run code changes.
- **"Plan"** → write or update a plan document only. Do **not** implement.
- **"Implement", "apply", "fix", "go ahead", "make the change"** → implement.
- If the scope is ambiguous (e.g., "yes, please" following a review), **ask before acting**.

### 2.3 Safe changes
- Prefer small, surgical edits. Keep a running mental list of every file you create or modify in a session.
- Update the Memory Bank when you change project state, but don't erase prior context that is still accurate.
- Update `docs/plans/` in place. One plan per initiative; do not create duplicate plan files.

### 2.4 Destructive operations — hard rules
- **Never run `git clean -fd` / `-fdx`** to revert your own work. It permanently deletes pre-existing untracked files and directories.
- To undo changes you made in a session, use this order:
    1. `git restore <specific paths>` for modified tracked files.
    2. `rm <specific files you created>` — list each one explicitly. No wildcards across directories you did not create.
    3. `git stash` if you need a broader rollback but are unsure what you touched.
- Never use `git reset --hard`, force-push, `rm -rf` on shared directories, or any `find … -delete` without explicit user authorization for that specific action.

### 2.5 Ask before assuming
- Version choices (AAP 2.4 vs 2.5), topology (single-node vs HA), auth backends, feature scope — confirm with the user before codifying them.

## 3. Conventions

### 3.1 Ansible
- FQCN modules everywhere (`ansible.builtin.copy`, not `copy`).
- Every task has a `name:`; phase task files are tagged (`preflight`, `install`, `configure`, `ad`).
- Idempotency is non-negotiable: guard expensive steps (e.g., installer re-runs), use reconcile-style post-install modules.
- Secrets come from Ansible Vault as `vault_*` vars, referenced via `default(omit)` in `defaults/main.yml`. **Never commit literal secret values.**
- Role structure:
    - `defaults/main.yml` — public variable contract.
    - `vars/main.yml` — internal constants.
    - `tasks/main.yml` — thin dispatcher using `import_tasks`.
    - `tasks/<phase>.yml` — phase-specific tasks.
    - `templates/` — Jinja2 templates (e.g., the AAP installer inventory).
    - `handlers/`, `meta/`, `tests/` — standard role layout.

### 3.2 Documentation
- **Memory Bank** (`memory-bank/*.md`) is the project's source of truth for state and context. Keep it accurate and concise.
- **Plans** live in `docs/plans/YYYY-MM-DD-<slug>.md`. One file per initiative. Supersede explicitly.
- Don't create scratch markdown files in the repo root for planning or notes. Use the agent's own session workspace.

### 3.3 Commits
- Imperative subject ≤ 72 chars. Conventional-commit prefixes welcome (`feat:`, `docs:`, `fix:`, `chore:`).
- Reference the plan or Memory Bank file in the body when the change implements a specific planned step.
- Include agent co-author trailers when appropriate (e.g., `Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>`).

## 4. Validation

Run the lightweight checks you can before handing back:

- `ansible-lint` and `yamllint` if available on the control node.
- `ansible-playbook tests/test.yml --syntax-check` for syntax validation.
- `ansible-playbook tests/test.yml --check` for a dry run (note: the containerized installer step cannot be fully dry-run; guard accordingly).

Do not invent new linting/testing tooling unless the user asks for it.

## 5. Things NOT to do

- ❌ Implement code when the user asked for a review or a plan.
- ❌ Run `git clean -fd`, `git reset --hard`, `rm -rf` on shared/untracked trees, or force-push without explicit per-operation consent.
- ❌ Create duplicate plan files or scratch markdown files in the repo root.
- ❌ Hard-code secrets or commit vault files.
- ❌ Add a `roles/` wrapper; this directory **is** the role.
- ❌ Silently upgrade versions, swap installers, or change auth backends — always confirm first.
- ❌ Delete or rewrite Memory Bank context that is still accurate.

## 6. Where to look

| Need | File |
|---|---|
| Current status / next steps | `memory-bank/activeContext.md` |
| Full task list | `memory-bank/task_state.md` |
| Milestones | `memory-bank/progress.md` |
| Target host facts | `memory-bank/key_knowledge.md` |
| Tech stack & constraints | `memory-bank/techContext.md` |
| Conventions & patterns | `memory-bank/systemPatterns.md` |
| Project brief | `memory-bank/projectbrief.md` |
| Active plan | `docs/plans/<latest>.md` |
| Hard rules | `.clinerules` |
