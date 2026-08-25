[![Molecule](https://github.com/iamenr0s/ansible-role-etc-hosts/actions/workflows/molecule.yml/badge.svg)](https://github.com/iamenr0s/ansible-role-etc-hosts/actions/workflows/molecule.yml) ![Ansible Role](https://img.shields.io/ansible/role/d/iamenr0s/ansible_role_etc_hosts) [![CodeFactor](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-etc-hosts/badge)](https://www.codefactor.io/repository/github/iamenr0s/ansible-role-etc-hosts)

Ansible Role: Manage /etc/hosts
================================

This role manages `/etc/hosts` using OS-agnostic Ansible modules (`lineinfile`/`blockinfile` — no per-distro branching needed):

- Updates the `127.0.0.1` (localhost) line to include the FQDN, hostname, and/or extra aliases.
- Renders a managed block of host entries derived from the inventory and optional static extra entries.

Features
--------
- Configurable localhost line: choose any combination of FQDN, short hostname, `localhost`, and extra aliases.
- Inventory-derived `/etc/hosts` block, optionally restricted to specific inventory groups.
- Static extra entries (`etc_hosts_entries`) appended alongside inventory-derived ones.
- Idempotent, marker-delimited managed block (`# BEGIN/END ANSIBLE MANAGED HOSTS`) — safe to re-run, and doesn't touch unrelated lines in `/etc/hosts`.

Requirements
------------
- Ansible 2.9 or higher.
- No external collections — this role only uses `ansible.builtin` modules.

Supported Platforms
--------------------
- AlmaLinux 8, 9, 10
- Debian 12, 13
- Fedora 42, 43, 44
- Rocky Linux 8, 9, 10
- Ubuntu 22.04, 24.04

Role Variables
---------------
Defined in `defaults/main.yml`:

- `etc_hosts_manage_localhost` (bool, default: `true`) — Enable managing the localhost line.
- `etc_hosts_localhost_include_fqdn` (bool, default: `true`) — Include `ansible_fqdn` on the localhost line.
- `etc_hosts_localhost_include_hostname` (bool, default: `true`) — Include `ansible_hostname` on the localhost line.
- `etc_hosts_localhost_add_localhost` (bool, default: `true`) — Keep `localhost` on the line.
- `etc_hosts_localhost_extra_names` (list, default: `[]`) — Additional aliases for the localhost line.
- `etc_hosts_manage_inventory_hosts` (bool, default: `true`) — Manage a block of host entries derived from the inventory and extra entries.
- `etc_hosts_groups_to_manage` (list, default: `[]` → all groups) — Limit which inventory groups are rendered into `/etc/hosts`.
- `etc_hosts_entries` (list of dicts, default: `[]`) — Extra static entries to append. Example:
  ```yaml
  etc_hosts_entries:
    - { ip: "10.0.0.10", names: ["db01.example.com", "db01"] }
    - { ip: "10.0.0.11", names: ["web01.example.com", "web01"] }
  ```

Behavior Overview
------------------
`tasks/main.yml` runs up to two independent steps:

- Localhost line (when `etc_hosts_manage_localhost`): computes the set of names to use (FQDN/hostname/`localhost`/extras per the toggles above) and updates the `127.0.0.1` line in place with `lineinfile`.
- Inventory block (when `etc_hosts_manage_inventory_hosts`): renders `templates/hosts_block.j2` — for each host in the selected groups (or all of `groups['all']` if `etc_hosts_groups_to_manage` is empty), resolves an IP from `etc_hosts_ip` (hostvar) → `ansible_host` → `ansible_default_ipv4.address`, then appends `etc_hosts_entries` — into a `blockinfile`-managed block at the end of `/etc/hosts`.

Tags
----
All tasks are tagged `etc_hosts`, allowing selective runs:
- `ansible-playbook ... --tags etc_hosts`
- `ansible-playbook ... --skip-tags etc_hosts`

Dependencies
------------
None. This role is self-contained.

Example Playbook
-----------------
### Basic Usage

```yaml
- name: Manage /etc/hosts
  hosts: all
  become: true
  gather_facts: true
  roles:
    - role: iamenr0s.ansible_role_etc_hosts
```

### Custom Configuration

```yaml
- name: Manage /etc/hosts with custom entries
  hosts: all
  become: true
  gather_facts: true
  vars:
    etc_hosts_groups_to_manage: ["basic_servers", "production_servers"]
    etc_hosts_localhost_extra_names:
      - "{{ inventory_hostname }}.local"
    etc_hosts_entries:
      - { ip: "10.0.0.10", names: ["db01.example.com", "db01"] }
  roles:
    - role: iamenr0s.ansible_role_etc_hosts
```

CI & Release (maintainers)
----------------------------
A single workflow (`.github/workflows/molecule.yml`) runs lint and the full Molecule distro matrix on pushes to `main`, PRs, and `v*` tags. On `v*` tags, a `release` job publishes to Ansible Galaxy after all tests pass.

The Galaxy API key lives in the `galaxy` GitHub environment, which only `v*` tags may target. One-time setup:

```bash
# Galaxy publishing key (environment-scoped, get it from galaxy.ansible.com/ui/token)
gh secret set GALAXY_API_KEY --env galaxy --repo iamenr0s/ansible-role-etc-hosts

# Code scanning notifications (Slack webhook URL; for Discord append /slack to the webhook URL)
gh secret set SECURITY_ALERT_WEBHOOK --env galaxy --repo iamenr0s/ansible-role-etc-hosts
```

`.github/workflows/code-scanning-notify.yml` polls the code-scanning API every 6 hours and posts new or updated open alerts to that webhook (GitHub Actions cannot trigger on `code_scanning_alert` directly).

## Contributing

Contributions are welcome — see [CONTRIBUTING.md](CONTRIBUTING.md) for the
local pipeline commands and pull request checklist. This project follows the
[Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md).

## Security

See [SECURITY.md](SECURITY.md) — GitHub private vulnerability reporting, no
public issues for security bugs.

## License

This project is licensed under the [MIT License](LICENSE).

## Author Information

Author: iamenr0s

Galaxy: `iamenr0s.ansible_role_etc_hosts`
