# Ansible Playbooks

Ansible playbooks for provisioning and managing my homelab systems.

## Contents

- **debian13/**: Debian 13 system setup - installs development tools, configures zsh with oh-my-zsh, adds Azure CLI and Terraform
- **win11/**: Windows 11 automated patching, activation, and personalization

Each directory contains its own README with detailed usage instructions.

## Prerequisites

- Ansible installed on your control machine
- SSH access to Debian systems
- WinRM configured on Windows systems

## Quick Start

```bash
# Debian 13 provisioning
cd debian13
ansible-playbook -i inventory.ini debian13.yml

# Windows 11 setup
cd win11
ansible-playbook -i inventory.ini win11.yml --ask-vault-pass
```

## Inventory Files

Inventory files (`inventory.ini`) contain IP addresses and credentials and are gitignored for security. Create them in each playbook directory as needed.

## General Notes

- All `.ini` files are gitignored to protect sensitive information
- `vault.yml` files containing secrets are gitignored
- Playbooks are idempotent - safe to run multiple times
- Test playbook syntax before running: `ansible-playbook --syntax-check -i inventory.ini <playbook>.yml`
