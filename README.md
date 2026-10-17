# SRE Ansible Playbooks

A home for repeatable, reviewable Ansible automation. Playbooks are organized by
use case; shared behavior belongs in roles rather than being copied between
plays.

## Requirements

- Ansible Core installed on the control node (`ansible-playbook` on `PATH`).
- SSH access and Python on managed hosts, as required by Ansible modules.
- Privilege escalation access for plays that configure system state.

## Repository layout

```text
ansible.cfg
playbooks/                 # Runnable entry points, grouped by use case
  workstation/
roles/                     # Shared, reusable roles
inventory/
  example/                 # Safe inventory examples; no real credentials
group_vars/                # Shared group defaults (when needed)
docs/                      # Contributor and operational guidance
```

## Run a playbook

Review the playbook and variables before running it. For the workstation
baseline, start with syntax validation and a check-mode run against an
intentionally selected inventory:

```sh
ansible-playbook --syntax-check playbooks/workstation/linux-pre-setup.yml
ansible-playbook -i inventory/example/hosts.yml \
  playbooks/workstation/linux-pre-setup.yml --limit workstation \
  --check --diff
```

For a real run, remove `--check --diff` only after reviewing the proposed
changes. Supply a real inventory outside this repository or create a local,
untracked inventory. Do not commit secrets or production host details.

## Add a playbook

See [docs/adding-playbooks.md](docs/adding-playbooks.md) for the structure,
conventions, validation steps, and review checklist for new playbooks.

## Safety

Examples under `inventory/example/` are not intended to identify real hosts.
Playbooks should state their supported platforms and operational impact, use
variables for environment-specific values, and avoid destructive or
security-sensitive changes without explicit opt-in.