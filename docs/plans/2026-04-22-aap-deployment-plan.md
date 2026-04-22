# Implementation Plan: Red Hat AAP Deployment & AD Integration

## Objective
Deploy Red Hat Ansible Automation Platform (AAP) 2.4 on RHEL 9.7 and integrate it with Active Directory (AD) for authentication.

## Background & Motivation
The goal is to provide a centralized, secure automation platform for the environment, leveraging existing AD infrastructure for user management and access control.

## Proposed Solution
The deployment will use the existing `provision-redhat-app` Ansible role to orchestrate the official Red Hat AAP installer. Post-installation, the role will configure the Automation Controller to use AD via LDAP.

### 1. Phased Implementation Plan

#### Phase 1: Environment Preparation
- [ ] Verify system requirements (16GB+ RAM, 4+ vCPUs, 20GB+ disk space).
- [ ] Ensure `ansible-core` (2.14+) is available on the target node.
- [ ] Enable required Red Hat repositories (if available) or prepare for bundled installation.

#### Phase 2: AAP Installation (Role Development)
- [ ] **Download/Fetch:** Retrieve the AAP setup bundle (tarball) or ensure repo access.
- [ ] **Configure Inventory:** Create a Jinja2 template for the AAP installer's `inventory` file.
- [ ] **Execute Setup:** Run the `setup.sh` script from within the role.
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

## Key Files & Context
- `roles/provision-redhat-app/tasks/main.yml`: Primary orchestration.
- `roles/provision-redhat-app/templates/inventory.j2`: Installer configuration.
- `roles/provision-redhat-app/defaults/main.yml`: Default values for versions and settings.
- `playbook.yml`: Main playbook to execute the role on `pas01`.

## Alternatives Considered
- **Repository-based installation:** Rejected for now as repositories aren't confirmed as enabled on the host. The bundled installer is more reliable for restricted environments.
- **Manual configuration of AD:** Rejected in favor of automation to ensure consistency and repeatability.

## Verification & Testing
- Use `ansible-playbook --check` for initial validation (limited for installation scripts).
- Monitor `/var/log/tower/setup.log` for installation details.
- Use `ldapsearch` (if available) to verify AD connectivity from the host before configuring AAP.
