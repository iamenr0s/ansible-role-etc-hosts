# Ansible Role: Manage /etc/hosts

This role manages `/etc/hosts` using Ansible modules:

- Updates the `127.0.0.1` (localhost) line to include FQDN and hostname.
- Adds entries for hosts present in your inventory and optional extra variables.

## Features

- ...

## Requirements

- Ansible 2.9 or higher

## Supported Platforms

- Ubuntu 18.04, 20.04, 22.04
- Debian 10, 11, 12
- RHEL 7, 8, 9
- Rocky Linux 8, 9
- AlmaLinux 8, 9
- Fedora 35+

## Role Variables

- `etc_hosts_manage_localhost` (bool, default: `true`)
  - Enable managing the localhost line.
- `etc_hosts_localhost_include_fqdn` (bool, default: `true`)
  - Include `ansible_fqdn` on the localhost line.
- `etc_hosts_localhost_include_hostname` (bool, default: `true`)
  - Include `ansible_hostname` on the localhost line.
- `etc_hosts_localhost_add_localhost` (bool, default: `true`)
  - Keep `localhost` on the line.
- `etc_hosts_localhost_extra_names` (list, default: `[]`)
  - Additional aliases for the localhost line.
- `etc_hosts_manage_inventory_hosts` (bool, default: `true`)
  - Manage a block of host entries derived from the inventory and extra entries.
- `etc_hosts_groups_to_manage` (list, default: `[]` → means all groups)
  - Limit which inventory groups are rendered into `/etc/hosts`.
- `etc_hosts_entries` (list of dicts, default: `[]`)
  - Extra static entries to append. Example:
    ```yaml
    etc_hosts_entries:
      - { ip: "10.0.0.10", names: ["db01.example.com", "db01"] }
      - { ip: "10.0.0.11", names: ["web01.example.com", "web01"] }
    ```

## Dependencies

None. This role is self-contained.

## Example Playbook

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

## License

MIT

## Author Information

Author: iamenr0s
Galaxy: `iamenr0s.ansible_role_etc_hosts`

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## Changelog

See `CHANGELOG.md` for version history and release notes.
