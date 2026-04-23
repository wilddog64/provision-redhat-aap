# Implementation Plan: Red Hat AAP Deployment & AD Integration

## Objective
Deploy Red Hat Ansible Automation Platform (AAP) 2.5 on RHEL 9.7 and integrate it with Active Directory (AD) for authentication.

## Background & Motivation
The goal is to provide a centralized, secure automation platform for the environment, leveraging existing AD infrastructure for user management and access control.

## Proposed Solution
The deployment will use the existing `provision-redhat-app` Ansible role to orchestrate the official Red Hat AAP installer. Post-installation, the role will configure the Automation Controller to use AD via LDAP.

## Initial Assessment (2026-04-23)

Current repository state is still pre-implementation:

- The role skeleton exists, but `defaults/main.yml`, `tasks/main.yml`, `handlers/main.yml`, and `vars/main.yml` still contain generator placeholders only.
- `meta/main.yml` still contains `ansible-galaxy init` placeholder metadata and has not been customized for this role.
- `tests/test.yml` is still the minimal localhost role-invocation stub and does not yet reflect target-host syntax-check or phased validation.
- The plan previously referenced `roles/provision-redhat-app/...` paths, but this repository root **is** the role and does not contain a `roles/` wrapper.
- Documentation is inconsistent on status: the Memory Bank and plan say implementation has not started, while `README.md` says implementation is in progress.

Assessment conclusion: planning exists, but the repository is still at **role scaffold + planning artifacts** rather than active implementation.

### 1. Phased Implementation Plan

#### Phase 1: Environment Preparation
- [ ] Verify system requirements (16GB+ RAM, 4+ vCPUs, 20GB+ disk space).
- [ ] Ensure `ansible-core` (2.14+) is available on the target node.
- [ ] Enable required Red Hat repositories (if available) or prepare for bundled installation.
- [ ] Confirm final AAP 2.5 installer source and delivery method (bundle URL/local artifact vs subscribed repository access).
- [ ] Confirm whether the controller database remains installer-managed or requires an external PostgreSQL decision.

#### Phase 2: AAP Installation (Role Development)
- [ ] Replace `ansible-galaxy init` placeholders in role metadata, defaults, vars, handlers, and task dispatcher.
- [ ] **Download/Fetch:** Retrieve the AAP setup bundle (tarball) or ensure repo access.
- [ ] **Configure Inventory:** Create a Jinja2 template for the AAP installer's `inventory` file.
- [ ] **Execute Setup:** Run the containerized installer from within the role with idempotent guards.
- [ ] **Handle Credentials:** Use Ansible Vault for sensitive passwords (admin, DB, AD bind).

#### Phase 3: AD Integration (Post-Install)
- [ ] **Collection Setup:** Ensure `infra.controller_configuration` or `redhat.ansible_automation_platform` collections are available.
- [ ] **LDAP Configuration:** Apply AD settings to the Automation Controller:
    - Server URI (LDAPS preferred).
    - Bind DN and Password.
    - User/Group search paths and filters.
    - Organization and Team mappings.
- [ ] **SSL/TLS:** Import the AD Domain Controller CA certificate into the system trust store if using LDAPS.

#### Phase 4: Validation
- [ ] Verify AAP services are running (`automation-controller-service`).
- [ ] Log in to the Web UI with the local admin account.
- [ ] Test AD login with a specific user.
- [ ] Verify AD group to Controller Team/Org mappings.
- [ ] Add `ansible-playbook tests/test.yml --syntax-check` validation that reflects the actual role variable contract.

## Key Files & Context
- `tasks/main.yml`: Primary orchestration entry point for the role.
- `templates/inventory.j2`: Planned AAP installer inventory template.
- `defaults/main.yml`: Public variable contract for versioning, paths, and feature toggles.
- `tests/test.yml`: Syntax-check and lightweight validation entry point.

## Alternatives Considered
- **Repository-based installation:** Rejected for now as repositories aren't confirmed as enabled on the host. The bundled installer is more reliable for restricted environments.
- **Manual configuration of AD:** Rejected in favor of automation to ensure consistency and repeatability.

## Open Questions / Risks

- Is AAP 2.5 definitively the required version, or must the role tolerate 2.4/2.5 artifact differences?
- Are Red Hat subscriptions and registry credentials already available on `pas01`, or must the role handle preflight failure messaging only?
- Which AD group mapping model is required (single org/team mapping vs multiple group-to-role mappings)?
- Does the target environment require custom CA trust bootstrapping for LDAPS before AAP configuration begins?

## Verification & Testing
- Use `ansible-playbook --check` for initial validation (limited for installation scripts).
- Monitor `/var/log/tower/setup.log` for installation details.
- Use `ldapsearch` (if available) to verify AD connectivity from the host before configuring AAP.
