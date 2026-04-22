# Copilot Instructions for this Repository

The canonical agent guidance for this repository is in [`../AGENTS.md`](../AGENTS.md). Read it before taking any action.

Also read, in order:
1. [`../.clinerules`](../.clinerules) — hard rules on scope discipline and destructive operations.
2. [`../memory-bank/activeContext.md`](../memory-bank/activeContext.md) — current state.
3. [`../memory-bank/task_state.md`](../memory-bank/task_state.md) — task list.
4. [`../docs/plans/`](../docs/plans/) — latest implementation plan.

**Critical reminders:**
- "Review" / "plan" requests do **not** authorize code changes. Ask if unsure.
- **Never** use `git clean -fd` to revert your own work. See `AGENTS.md` §2.4 for the safe-revert procedure.
- This directory **is** the `provision-redhat-app` Ansible role. No `roles/` wrapper.
- Secrets via Ansible Vault (`vault_*` vars) only. Never commit literal secrets.
