ansible-playbook -i inventories/hosts.ini playbooks/site.yml --vault-password-file .vault_pass.txt
# Linux Automation Infrastructure Project (Prjct_02)

Automates Linux VM provisioning and configuration using Ansible, including web, app, and database services, with CI/CD integration.

## 1. Project Overview

This project automates multi-tier application deployment with the following components:

- **VM Provisioning:** KVM/libvirt VM creation
- **Configuration:** Docker, NGINX Reverse Proxy, PostgreSQL Hardening
- **Security:** Ansible Vault for secrets management
- **CI/CD:** GitHub Actions for linting and idempotency tests

## 2. Directory Structure

```
.
├── .github/                    # GitHub Actions workflows
├── inventories/                # Ansible inventory & variables
│   ├── hosts.ini              # Host definitions
│   ├── group_vars/            # Group variables
│   └── host_vars/             # Host-specific variables
├── playbooks/                 # Ansible playbooks
│   ├── site.yml              # Main configuration playbook
│   └── provisioning/
│       └── create-vms.yml    # VM creation playbook
└── roles/                     # Ansible Galaxy roles
    ├── db_hardening/         # PostgreSQL hardening
    ├── docker_setup/         # Docker installation & app deployment
    └── reverse_proxy/        # NGINX reverse proxy configuration
```

## 3. Prerequisites

### System Requirements
- **Ansible** (latest version)
- **Python 3** with **pip**
- **libvirt/KVM** (for VM provisioning)
- **SSH Access:** Ensure `~/.ssh/config` is configured for VM access

### Ansible Collections
Install required Ansible collections:

```bash
ansible-galaxy collection install community.postgresql community.general
```

## 4. Setup and Usage

### Step 1: VM Provisioning

Create the virtual machines:

```bash
ansible-playbook playbooks/provisioning/create-vms.yml
```

### Step 2: Inventory & SSH Configuration

1. Verify that `inventories/hosts.ini` contains the correct IP addresses for your VMs
2. Ensure `~/.ssh/config` has the correct SSH entries for:
   - `AppServVM`
   - `WebServVM`
   - `DataBaseServVM`

### Step 3: Running Configurations

Execute the main configuration playbook:

```bash
ansible-playbook -i inventories/hosts.ini playbooks/site.yml --ask-become-pass
```

### Step 4: Secrets Management (Ansible Vault)

Sensitive data is stored in `inventories/group_vars/db/secrets.yml`.

**Edit vault file:**
```bash
ansible-vault edit inventories/group_vars/db/secrets.yml
```

**View vault file:**
```bash
ansible-vault view inventories/group_vars/db/secrets.yml
```

### Step 5: CI/CD Workflows (GitHub Actions)

The project includes automated workflows:

- **`.github/workflows/lint.yml`:** Linting checks triggered on push
- **`.github/workflows/idempotency.yml`:** Idempotency tests triggered on push

## 5. Ansible Roles

### Available Roles

- `db_hardening` | Installs and hardens PostgreSQL database |
- `docker_setup` | Installs Docker and deploys application containers |
- `reverse_proxy` | Configures NGINX reverse proxy |

## 6. Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with the provided CI/CD workflows
5. Submit a pull request
