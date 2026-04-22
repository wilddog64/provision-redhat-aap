provision-redhat-app
====================

Ansible role to deploy **Red Hat Ansible Automation Platform (AAP) 2.5** (containerized installer) on RHEL 9.x and integrate it with Active Directory via LDAPS.

> ⚠️ **Status:** planning complete, implementation in progress. See [`memory-bank/activeContext.md`](./memory-bank/activeContext.md) for current state.

Quick Start
-----------

1. **Read the plan** in [`docs/plans/`](./docs/plans/) — it describes phases, the variable contract, and open questions.
2. **Provide secrets via Ansible Vault.** At minimum: `vault_aap_admin_password`, `vault_aap_pg_password`, `vault_aap_registry_password`, `vault_aap_ad_bind_password`.
3. **Point to the AAP installer bundle** by setting `aap_installer_src` (URL or local path) and `aap_installer_sha256`.
4. **Invoke the role** from a playbook:

   ```yaml
   - hosts: pas01
     become: true
     roles:
       - role: provision-redhat-app
         vars:
           aap_installer_src: "https://artifacts.example.com/aap-2.5-bundle.tar.gz"
           aap_installer_sha256: "…"
           aap_ad_server: "ad.dev.costcotravel.com"
           aap_ad_base_dn: "DC=dev,DC=costcotravel,DC=com"
           aap_ad_bind_dn: "CN=svc-aap,OU=ServiceAccounts,DC=dev,DC=costcotravel,DC=com"
   ```

5. **Run by phase** using tags: `preflight`, `install`, `configure`, `ad`.

Requirements
------------

- Control node: `ansible-core` ≥ 2.15 recommended (see plan for verification on `pas01`).
- Target: RHEL 9.x with AAP subscription entitlement, Podman-capable.
- Collections: `ansible.containerized_installer`, `ansible.platform`, `ansible.controller`, `ansible.hub` (installed on demand; see plan).
- Minimums: 16 GiB RAM, 4 vCPU, ~60 GiB free disk.

Role Variables
--------------

The full variable contract lives in [`defaults/main.yml`](./defaults/main.yml) and is documented in the [implementation plan](./docs/plans/). Secrets must be provided via Ansible Vault (`vault_*` keys) — never commit literal values.

Documentation
-------------

- **[AGENTS.md](./AGENTS.md)** — guidance for AI coding agents working in this repo.
- **[docs/plans/](./docs/plans/)** — date-stamped implementation plans (one per initiative).
- **[memory-bank/](./memory-bank/)** — project state, context, conventions, and task tracking.
- **[.clinerules](./.clinerules)** — hard rules on scope and destructive operations.

Testing
-------

```bash
ansible-playbook tests/test.yml --syntax-check
ansible-playbook tests/test.yml --check   # dry run; installer step is not fully dry-runnable
```

License
-------

BSD

Author Information
------------------

Maintained by the Costco Travel Automation team.

