# Windows 11 Playbook

Automated Windows 11 patching, activation, and personalization using Ansible.

## Usage

```bash
cd win11
ansible-playbook -i inventory.ini win11.yml --ask-vault-pass
```

**Note**: This can take a long time on systems that haven't been patched recently. The playbook requires `--ask-vault-pass` to decrypt the product key.

## What This Playbook Does

### Windows Update
- Installs PSWindowsUpdate PowerShell module from PSGallery
- Installs PSReadLine module for improved PowerShell CLI experience
- Configures TLS 1.2 for PowerShell web requests
- Runs Windows Update for all categories (Security, Critical, Updates, etc.)
- Automatically reboots as needed during patching
- Loops until no updates remain AND no reboot required (up to 6 iterations)

### Windows Activation
- Reads product key from encrypted `vault.yml` file
- Checks current activation status
- Installs product key and activates Windows if not already activated
- Uses batch mode to prevent GUI popups

### System Configuration
- Sets time zone to Eastern Standard Time
- Enables Developer Mode
- Sets PowerShell execution policy to RemoteSigned

### SSH Key Generation
- Creates `.ssh` directory for the specified user
- Generates RSA 4096-bit SSH key with passphrase
- Only generates key if one doesn't already exist

### Package Management
- Installs Chocolatey package manager
- Installs Git via Chocolatey
- Configures Git with user name and email from inventory
- Installs GitHub CLI via Chocolatey
- Installs Azure CLI via Chocolatey
- Installs Azure PowerShell module (Az) from PSGallery
- Installs Microsoft Graph PowerShell module from PSGallery
- Installs Visual C++ Redistributable packages (all versions)

### Taskbar Personalization
- Detects logged-in user's SID dynamically
- Modifies registry settings for the active user (not the ansible service account)
- Hides taskbar search, Task View button, Widgets, and Chat
- Sets accent color to automatic (picks color from background)

### Lock Screen and Start Menu
- Disables Windows Spotlight on lock screen (uses Picture mode)
- Hides recently added apps in Start menu
- Hides recently opened items in Start menu, Jump Lists, and File Explorer
- Automatically reboots if personalization settings changed

## Inventory File

Create `inventory.ini` in this directory with your Windows host details:

```bash
# Copy the example and customize it
cp inventory.ini.example inventory.ini
# Edit with your actual details
nano inventory.ini
```

**Note**: All `.ini` files are gitignored for security. The `ansible_password` references an encrypted variable from `vault.yml`.

## Vault File

Create `vault.yml` in this directory with encrypted secrets:

```bash
# Copy the example and add your actual secrets
cp vault.yml.example vault.yml
# Edit with your actual values
nano vault.yml
# Encrypt it
ansible-vault encrypt vault.yml
```

**Note**: `vault.yml` is gitignored for security.

## Design Notes

- All registry changes use `HKU:\<SID>` to target the logged-in user, not the ansible account
- All registry changes are idempotent and tracked
- Conditional reboot only triggers if settings actually changed
- `win_reboot` module includes retry logic and connection testing
- Windows Update loop includes 1-hour timeout per reboot