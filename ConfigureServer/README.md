# Ansible Linux Server Configuration with Jenkins

This project provides a complete setup for configuring Linux servers using Ansible running in Docker, triggered from Jenkins CI/CD pipeline.

## Project Structure

```
.
├── Dockerfile                         # Ansible Docker image definition
├── Jenkinsfile                        # Jenkins pipeline configuration
├── ansible.cfg                        # Ansible configuration
├── inventory/                         # Server inventory files
│   └── hosts.yml                      # YAML format inventory
├── playbooks/                         # Ansible playbooks
│   ├── aws_server.yml                 # Main configuration playbook
│   └── roles/                         # Custom roles directory
└── ansible-playbooks/                 # Additional playbooks
```

## Prerequisites

### On Jenkins Server
- Docker installed and running
- Jenkins with Docker Pipeline plugin
- Git access to this repository
- SSH keys configured for server access

### On Target Servers
- SSH access enabled
- Python 3 installed
- Sudo privileges for the ansible user

## Setup Instructions

### 1. Configure Inventory

Edit `inventory/hosts.yml` to define your servers:

### 2. Configure SSH Keys on Jenkins

Use Jenkins credentials and modify the Jenkinsfile to mount them during pipeline execution.

### 3. Build Docker Image (For Local Testing)

```bash
# Build the Ansible Docker image
docker build -t ansible-server-config:v1 .


# Test running a playbook locally
docker run --rm \
  -v $(pwd):/ansible \
  -v ~/Downloads/serverkeys:/home/ansible/.ssh:ro \
  ansible-server-config:v1 \
  ansible-playbook -i /ansible/inventory/hosts.yml /ansible/playbooks/aws_server.yml --check
```

# Test running Ad-Hoc command on target server from local
ansible -i inventory/hosts.yml aws_server -m shell -a "yum list available wget-1.21.3 --showduplicates -b"

## Running Playbooks via Jenkins

### Via Jenkins UI
1. Go to your Jenkins pipeline job
2. Click "Build with Parameters"
3. Select options:
   - **PLAYBOOK**: Choose playbook to run.
   - **CHECK_MODE**: Enable for dry-run (no changes made).
   - **SSH_CREDENTIAL_ID**: Select correct creds for ansible to connect to server.
   - **SUB_FOLDER**: Select the folder location where Ansible configuration is stored.
4. Click "Build"

### Pipeline Features

The Jenkins pipeline includes:
- **Checkout**: Pulls latest playbooks from Git
- **Build**: Creates Ansible Docker image
- **Validate**: Checks playbook syntax
- **Execute**: Runs the selected playbook against target servers
- **Check Mode**: Dry-run option to preview changes

## Manual Ansible Execution

For local testing or manual runs:

```bash
# Check syntax
ansible-playbook --syntax-check playbooks/aws_server.yml

# Dry run (check mode)
ansible-playbook -i inventory/hosts.yml --check playbooks/aws_server.yml

# Run
ansible-playbook -i inventory/hosts.yml playbooks/aws_server.yml

# Run with verbose output
ansible-playbook -i inventory/hosts.yml -vvv playbooks/aws_server.yml
```

## Customization

### Adding New Playbooks
1. Create playbook in `playbooks/` directory
2. Update Jenkinsfile parameters to include new playbook
3. Commit, push, review, verify and merge changes


### Deploy Web Servers
```bash
# Via Jenkins: Select webserver.yml playbook:
ansible-playbook -i inventory/hosts.yml playbooks/aws_server.yml
```
