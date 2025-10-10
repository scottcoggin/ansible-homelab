# Debian 13 Playbook

Automated Debian 13 system provisioning with development tools, zsh configuration, Azure CLI, and Terraform.

## Usage

```bash
cd debian13
ansible-playbook -i inventory.ini debian13.yml
```

## What This Playbook Does

- Updates and upgrades all packages
- Installs base tools (curl, git, vim, zsh, LaTeX)
- Configures zsh as default shell with oh-my-zsh and plugins
- Installs Azure CLI from Microsoft's apt repository
- Installs Terraform from HashiCorp's apt repository

## Inventory File

Create `inventory.ini` in this directory with your Debian host details:

```ini
[debian]
10.0.0.50 ansible_user=your_user ansible_become=yes
```

**Note**: All `.ini` files are gitignored for security.

## Architecture Notes

The playbook handles architecture and codename mapping for third-party repositories:

### Azure CLI
- Maps Debian codenames (trixie→bookworm) since Microsoft doesn't serve all Debian versions
- Maps architecture (x86_64→amd64, aarch64→arm64, etc.)

### HashiCorp/Terraform
- Uses Debian LSB codename directly
- Maps architecture using the same logic as Azure CLI
- Requires GPG key dearmoring via `gpg --dearmor`

### Design Patterns
- Removes stale repository files before adding new ones (prevents conflicts)
- Uses `signed-by` for GPG keys (modern Debian best practice)
- Idempotent: safe to run multiple times
