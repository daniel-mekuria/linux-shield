# linux-shield

Ansible playbooks and scripts for hardening Linux systems against common vulnerabilities. Automates CIS benchmark compliance, firewall configuration, and access management.

## Overview

Securing Linux servers requires consistent application of hardening rules across many hosts. This project provides Ansible playbooks that automate CIS benchmark compliance, SSH hardening, firewall configuration, and audit logging — all idempotent and repeatable.

## Structure

```
harden.yml          - Main playbook (entry point)
tasks/              - Task files for each hardening category
vars.yml            - Configurable variables
templates/          - Configuration file templates
files/              - Static files (scripts, configs)
ansible.cfg         - Ansible configuration
requirements.yml    - Galaxy dependencies
molecule/           - Molecule tests for playbook validation
tests/              - Test inventory and scripts
legacy/             - Legacy/deprecated playbooks
```

## What It Hardens

- **Kernel parameters** — sysctl settings for network, memory, and process security
- **SSH** — disable root login, key-only auth, restrict ciphers
- **Firewall** — iptables/nftables rules for default-deny
- **User accounts** — password policies, session timeouts, sudo configuration
- **File permissions** — restrictive perms on sensitive files and directories
- **Audit logging** — auditd rules for security event tracking
- **Bootloader** — password-protected GRUB
- **Time sync** — secure NTP/chrony configuration

## Usage

### Run the full hardening playbook

```bash
ansible-playbook harden.yml -i inventory
```

### Run specific categories

```bash
# Harden SSH only
ansible-playbook harden.yml -i inventory -t ssh

# Apply firewall rules
ansible-playbook harden.yml -i inventory -t firewall

# Configure audit logging
ansible-playbook harden.yml -i inventory -t audit
```

### Check mode (dry run)

```bash
ansible-playbook harden.yml -i inventory --check --diff
```

## Configuration

```yaml
# vars.yml
ssh:
  permit_root_login: no
  password_auth: no
  max_auth_tries: 3

firewall:
  default_policy: drop
  allowed_ports:
    - 22
    - 443

audit:
  log_dir: /var/log/audit
  rules:
    - "-w /etc/passwd -p wa -k identity"
    - "-a always,exit -F arch=b64 -S unlink -S unlinkat -k delete"
```

## Testing

```bash
# Run molecule tests
molecule test

# Run with specific scenario
molecule test --scenario-name default
```

## OpenSCAP

The `openscap.yml` playbook runs an OpenSCAP scan to validate compliance:

```bash
ansible-playbook openscap.yml -i inventory
```

## License

MIT
