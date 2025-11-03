# Changelog

All notable changes to this project are documented in this file.

## [1.0.0] - 2025-11-03
- Role to manage `/etc/hosts` using Ansible modules.
- Update `127.0.0.1` (localhost) line with `FQDN`, hostname, and optional aliases.
- Render inventory-derived host entries and extra static entries via a managed block.
- Configurable defaults in `defaults/main.yml` for localhost and inventory management.
- Jinja template `templates/hosts_block.j2` for block rendering.
- Example inventory and playbook in `examples/`.

## Format
This changelog follows a simple, readable format. Future versions may adopt the
Keep a Changelog style and semantic versioning.