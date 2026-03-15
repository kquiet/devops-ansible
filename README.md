# Ansible Infrastructure Provisioning

This repository contains Ansible playbooks and roles used for provisioning and managing infrastructure. It serves as the single source of truth for infrastructure configuration and is also used as a project source for Ansible AWX.

## Prerequisites

To run the playbooks in this repository locally, ensure your environment meets the following requirements:

- **Ansible**: 2.17 or higher
- **Python**: 3.10 or higher
- **Collections & Roles**: Required dependencies listed in `requirements.yml`.

## Repository Structure

- `playbooks/`: Contains the main entry points for various infrastructure tasks (e.g., OS upgrades, Docker installation, backups).
- `roles/`: Contains custom Ansible roles developed for this project.
- `inventory/`: Defines the hosts and groups for different environments.
- `collections/`: Local or vendored Ansible collections.
- `requirements.yml`: Lists external roles and collections required.
- `ansible.cfg`: Local Ansible configuration.

## Setup

Before running any playbooks, install the required external roles and collections:

```bash
ansible-galaxy install -r requirements.yml
```

The `ansible.cfg` is configured to store these in `./galaxy_roles` and `./galaxy_collections` to keep the environment isolated.

## Secrets Management

This project uses HashiCorp Vault for managing sensitive information. When running playbooks locally, you must provide your Vault credentials via a variables file.

**Important:** Never commit secrets to the repository.

1. Create a directory named `dev_vars/` (this should be ignored by git).
2. Create a file `dev_vars/vault_secrets.yml` with the following structure:

```yaml
---
vault_url: "https://vault.svc.internal"
vault_role_id: "your-role-id"
vault_secret_id: "your-secret-id"
vault_extra_options: " validate_certs=false"
```

## Usage

To execute a playbook, use the `ansible-playbook` command and include your secret variables:

```bash
ansible-playbook playbooks/<playbook_name>.yml -e @dev_vars/vault_secrets.yml
```

For example, to ping all hosts:

```bash
ansible-playbook playbooks/ping.yml -e @dev_vars/vault_secrets.yml
```

## Contribution Process

We welcome contributions from other crews! To maintain the quality and stability of our infrastructure code, please follow this workflow:

1. **Create a Feature Branch**: Branch off from `main` using a descriptive name (e.g., `feature/add-k8s-monitoring` or `fix/ssh-config`).
2. **Develop and Test**: Implement your changes in your branch. Test your playbooks against a development or staging environment if available.
3. **Commit Changes**: Use clear and concise commit messages. Ensure no sensitive data is included in your commits.
4. **Submit a Merge Request (MR)**: Push your branch to GitLab and create a Merge Request targeting the `main` branch.
5. **CI/CD Validation**: Every MR triggers a GitLab CI pipeline that runs security scans (SAST, Secret Detection). Ensure all checks pass.
6. **Code Review**: A maintainer will review your changes. Address any feedback provided during the review.
7. **Merge**: Once approved and all checks have passed, your changes will be merged into the `main` branch and become available for scheduled jobs in AWX.

## Maintenance

Currently, there are no specific recurring maintenance tasks. Please ensure that `requirements.yml` is kept up to date if new dependencies are added.
