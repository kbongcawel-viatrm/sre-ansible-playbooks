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