# Qwen Context File - Ansible VM Provisioning

## Project Overview

This project automates multi-tier application deployment using Ansible. The project provisions and configures Linux VMs for a complete application infrastructure including web, application, and database services, with CI/CD integration through GitHub Actions.

### Key Features:
- VM Provisioning using KVM/libvirt
- Docker setup and containerized application deployment
- NGINX reverse proxy configuration
- PostgreSQL database hardening
- Ansible Vault for secrets management
- GitHub Actions for linting and idempotency testing

## Architecture

The project implements a three-tier architecture:
- **Web Server VM** (WebServVM): Hosts reverse proxy and web applications
- **Application Server VM** (AppServVM): Hosts application containers via Docker
- **Database Server VM** (DataBaseServVM): PostgreSQL database with security hardening
- **Ansible Control Node VM** (AnsibleOneVM): Used to run Ansible playbooks

## Directory Structure

```
.
├── .github/                    # GitHub Actions workflows
├── cloud-init/                 # Cloud-init configuration for VM provisioning
│   ├── user-data              # User configuration for VM creation
│   └── meta-data              # Metadata configuration for VM creation
├── config/                     # Additional configuration files
├── images/                     # VM images and seed ISO files
├── inventories/                # Ansible inventory & variables
│   └── hosts.ini              # Host definitions
├── playbooks/                  # Ansible playbooks
│   ├── site.yml               # Main configuration playbook
│   ├── create-vms.yml         # VM creation playbook
│   ├── db_hardening.yml       # Database hardening playbook
│   └── docker_setup.yml       # Docker setup playbook
├── roles/                      # Ansible Galaxy roles
│   ├── db_hardening/          # PostgreSQL hardening
│   ├── docker_setup/          # Docker installation & app deployment
│   └── reverse_proxy/         # NGINX reverse proxy configuration
├── ansible.cfg                 # Ansible configuration file
├── .ansible.cfg               # Additional Ansible configuration
├── .gitignore                 # Git ignore patterns
└── README.md                   # Project documentation
```

## Configuration Files

### ansible.cfg
- Sets roles path to `./roles`
- Specifies default inventory as `inventories/hosts.ini`
- Disables host key checking
- Sets vault password file to `.vault_pass.txt`
- Configures SSH connection to use `/home/amw/.ssh/config`

### hosts.ini (Inventory)
Defines groups of servers:
- `[web]` for web servers (WebServVM)
- `[db]` for database servers (DataBaseServVM)
- `[app]` for application servers (AppServVM)
- `[infra]` for infrastructure servers (AnsibleOneVM)

## Playbooks

### Main Playbooks
- `site.yml`: Main orchestration playbook that applies roles to respective VMs
- `create-vms.yml`: Creates VMs using virt-install and cloud-init
- `db_hardening.yml`: PostgreSQL hardening playbook
- `docker_setup.yml`: Docker installation and application setup

### Execution
Main configuration command:
```bash
ansible-playbook -i inventories/hosts.ini playbooks/site.yml --vault-password-file .vault_pass.txt
```

## Ansible Roles

### Available Roles
1. `db_hardening`: Installs and hardens PostgreSQL database
2. `docker_setup`: Installs Docker and deploys application containers
3. `reverse_proxy`: Configures NGINX reverse proxy

## CI/CD Workflows

The project includes GitHub Actions workflows:
- `lint.yml`: Performs Ansible linting on code changes
- `idempotency.yml`: Tests playbook idempotency
- `docker_deploy.yml`: Automates Docker deployment processes

## Prerequisites

### System Requirements
- Ansible (latest version)
- Python 3 with pip
- libvirt/KVM (for VM provisioning)
- SSH access to target VMs

### Ansible Collections
Required collections:
- community.postgresql
- community.general

Installation command:
```bash
ansible-galaxy collection install community.postgresql community.general
```

## Secrets Management

Sensitive data is stored encrypted in:
- `inventories/group_vars/db/secrets.yml` (encrypted with Ansible Vault)

Manage vault files with:
- `ansible-vault edit inventories/group_vars/db/secrets.yml`
- `ansible-vault view inventories/group_vars/db/secrets.yml`

## Cloud-Init Configuration

VMs are provisioned using cloud-init with:
- `user-data`: Defines users, SSH keys, and packages to install
- `meta-data`: Provides instance metadata

## Development Conventions

- Use Ansible best practices for playbook and role structure
- Store sensitive data in encrypted vault files
- Test changes with idempotency workflows
- Follow role separation for different infrastructure components
- Use consistent variable naming across playbooks

## Working with This Project

1. Ensure VMs are created with correct IP addresses
2. Update inventory file with VM IP addresses
3. Configure SSH access in `~/.ssh/config`
4. Provide vault password file if using encrypted variables
5. Run main configuration with `site.yml` playbook

This project implements Linux automation with Ansible, following infrastructure-as-code principles and security best practices.