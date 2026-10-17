# Adding playbooks

Use this guide when contributing a new runnable automation entry point.

## 1. Choose a location

Create a descriptive, hyphenated YAML file under `playbooks/`, grouped by use
case (for example, `playbooks/workstation/` or `playbooks/servers/`). Keep
playbooks as entry points. Put behavior reused by multiple playbooks into a
role under `roles/<role_name>/` and call it from the entry points.

## 2. Define scope and targeting

- Give the play and tasks clear names.
- Target a meaningful inventory group, not `all`, unless applying to every
  host is explicitly intended.
- Document supported OS families/versions and required privileges.
- Put environment-specific values in variables rather than hard-coding them.
- Use explicit opt-in variables for disruptive or security-sensitive changes.
- Prefer idempotent Ansible modules over shell commands.
- Never commit credentials, vault passwords, or real production inventories.

## 3. Add inventory examples only when useful

Keep sample inventory under `inventory/example/`. Examples must be safe to
inspect and must not contain real hostnames, addresses, or credentials. Put
local inventories in the ignored `inventory/local/` directory.

## 4. Validate before submitting

From the repository root, run:

```sh
ansible-playbook --syntax-check path/to/playbook.yml
ansible-playbook -i inventory/example/hosts.yml path/to/playbook.yml \
  --limit an-intended-group --check --diff
```

Check mode is a useful preview, not a guarantee that every task/module can
predict changes. Test changes against disposable hosts representative of the
supported platforms before applying them to important systems. If available,
run `ansible-lint` as well.

## Review checklist

- Is the inventory target narrow and intentional?
- Are supported platforms, privileges, variables, and impact clear?
- Is the play idempotent and safe to preview?
- Are potentially disruptive changes opt-in and documented?
- Are secrets and real infrastructure details excluded?
- Have syntax validation and an appropriate test run succeeded?

## Fedora CUSP playbook

`playbooks/workstation/fedora-common-user-security-workstation.yml` vendors the
generated SCAP Security Guide Ansible remediation for profile
`xccdf_org.ssgproject.content_profile_cusp_fedora` (benchmark version 0.1.82,
generated 2026-09-02). It contains approximately 1,659 upstream task entries.
This is a broad security baseline, not a small workstation bootstrap. The
upstream generator notes that it attempts to fix every selected rule and does
not first evaluate compliance. Inspect the changes and test on a non-operational
Fedora system before deploying.

The play targets only the `workstation` inventory group and has an explicit
opt-in guard. Preview the selected changes with:

```sh
ansible-playbook -i /path/to/fedora-inventory.yml \
  playbooks/workstation/fedora-common-user-security-workstation.yml \
  --limit workstation --check --diff \
  -e fedora_cusp_apply_remediation=true
```

The command above is a preview only; after reviewing the changes, apply with:

```sh
ansible-playbook -i /path/to/fedora-inventory.yml \
  playbooks/workstation/fedora-common-user-security-workstation.yml \
  --limit workstation -e fedora_cusp_apply_remediation=true
```

The profile ID is `xccdf_org.ssgproject.content_profile_cusp_fedora`. The
referenced 2023 guide documents SCAP Security Guide 0.1.68 and Fedora 36–40;
this vendored file is generated from SCAP Security Guide 0.1.82. Check
compatibility with the target Fedora release, review its changes, and test
before rollout. The upstream guide warns to test its settings in a
non-operational environment first.