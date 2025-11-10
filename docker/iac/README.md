# Infrastructure as Code (IaC) Docker Image

A comprehensive Docker image designed for Infrastructure as Code development and operations. This Ubuntu 24.04-based image includes all essential tools for cloud infrastructure management, automation, and DevOps workflows.

## 🚀 Quick Start

### Building the Image

```bash
docker build -t iac .
```

### Running the Container

```bash
# Interactive shell
docker run -it --rm iac

# With volume mount for your projects
docker run -it --rm -v "$(pwd):/work" iac

# With custom user ID (to match host permissions)
docker run -it --rm --user $(id -u):$(id -g) iac
```

### Pre-built Image

```bash
docker pull astumph/iac:latest
docker run -it --rm astumph/iac:latest
```

## 📦 Installed Tools & Versions

### Infrastructure Tools
- **Terraform**: v1.13.5 - Infrastructure provisioning
- **Packer**: v1.14.2 - Machine image building
- **Ansible**: v25.10.0 (dev-tools) - Configuration management and automation

### Cloud Platforms
- **Azure CLI**: v2.79.0 - Azure command-line interface
- **Azure Developer CLI**: Latest - Azure development workflows  
- **PowerShell**: v7.5.4 with Az module - Azure PowerShell management

### Development Environment
- **Python**: 3.x with pip and pipx
- **Git**: Version control
- **Zsh**: Enhanced shell with Oh My Zsh
- **Vim**: Text editor
- **Standard utilities**: curl, wget, zip, unzip, sudo, etc.

### Ansible Collections

The image includes a comprehensive set of Ansible collections:
- `community.general` - General community modules
- `servicenow.servicenow` - ServiceNow integration
- `community.windows` - Windows system management  
- `chocolatey.chocolatey` - Windows package management
- `netscaler.adc` - Citrix NetScaler management
- `azure.azcollection` - Azure cloud automation
- `purestorage.flasharray` - Pure Storage management
- `ansible.windows` - Core Windows support
- `awx.awx` - AWX/Ansible Tower integration
- `community.vmware` - VMware infrastructure
- `kubernetes.core` - Kubernetes orchestration
- `ansible.posix` - POSIX system utilities
- `microsoft.ad` - Active Directory management

## ⚙️ Configuration

### Build Arguments

Customize the image during build with these arguments:

```bash
docker build \
  --build-arg USERNAME=myuser \
  --build-arg USER_UID=1001 \
  --build-arg USER_GID=1001 \
  --build-arg TERRAFORM_VERSION=1.13.5-1 \
  --build-arg POWERSHELL_VERSION=7.5.4-1.deb \
  --build-arg AZ_CLI_VERSION=2.79.0-1~noble \
  --build-arg PACKER_VERSION=1.14.2-1 \
  --build-arg ANSIBLE_DEV_TOOLS_VERSION=25.10.0 \
  -t iac .
```

### Environment Variables

- `PIPX_HOME=/opt/pipx` - Pipx installation directory
- `PIPX_BIN_DIR=/usr/local/bin` - Pipx binary directory  
- `LC_ALL=C.UTF-8` - Locale configuration

### Security Features

- **Non-root user**: Runs as `iac` user (UID 1000) with sudo privileges
- **Certificate management**: Cisco Umbrella Root CA certificate pre-installed
- **Kerberos support**: For Windows authentication scenarios

## 🏗️ Image Architecture

### Base Image
- **Ubuntu 24.04 LTS**: Stable, secure, and well-supported base

### Package Repositories
- **HashiCorp**: Official Terraform and Packer packages
- **Microsoft**: PowerShell, Azure CLI, and related tools  
- **Standard Ubuntu**: System packages and utilities

### User Setup
- Default user: `iac` (UID 1000, GID 1000)
- Sudo access without password prompts
- Zsh with Oh My Zsh configuration
- Working directory: `/work`

## 🔧 Advanced Usage

### Docker Compose

```yaml
version: '3.8'
services:
  iac:
    image: astumph/iac:latest
    volumes:
      - ./projects:/work
      - ~/.aws:/home/iac/.aws:ro  # AWS credentials
      - ~/.azure:/home/iac/.azure:ro  # Azure credentials
    environment:
      - TERM=xterm-256color
    tty: true
    stdin_open: true
```

### CI/CD Integration

```yaml
# GitHub Actions example
- name: Run Infrastructure Tasks
  run: |
    docker run --rm \
      -v ${{ github.workspace }}:/work \
      -e ARM_CLIENT_ID=${{ secrets.ARM_CLIENT_ID }} \
      -e ARM_CLIENT_SECRET=${{ secrets.ARM_CLIENT_SECRET }} \
      -e ARM_TENANT_ID=${{ secrets.ARM_TENANT_ID }} \
      astumph/iac:latest \
      terraform plan
```

### Volume Mounts for Credentials

```bash
# AWS credentials
docker run -it --rm \
  -v ~/.aws:/home/iac/.aws:ro \
  -v "$(pwd):/work" \
  iac

# Azure credentials  
docker run -it --rm \
  -v ~/.azure:/home/iac/.azure:ro \
  -v "$(pwd):/work" \
  iac

# SSH keys
docker run -it --rm \
  -v ~/.ssh:/home/iac/.ssh:ro \
  -v "$(pwd):/work" \
  iac
```

## 🧪 Testing

The image undergoes automated testing via GitHub Actions:

- Image builds successfully
- All tools are accessible and report versions correctly
- Container starts and runs commands properly
- Ansible collections are properly installed and accessible

## 📋 Troubleshooting

### Permission Issues
If you encounter permission issues with mounted volumes:

```bash
# Match host user ID
docker run -it --rm --user $(id -u):$(id -g) iac

# Or use Docker's --userns-remap feature
```

### Tool Versions
Check installed tool versions:

```bash
docker run --rm iac terraform --version
docker run --rm iac ansible --version  
docker run --rm iac az version
docker run --rm iac pwsh -c '$PSVersionTable'
```

### Ansible Collections
List installed collections:

```bash
docker run --rm iac ansible-galaxy collection list
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Test changes locally with `docker build` and `docker run`
4. Submit a pull request
5. Automated tests will validate the changes

## 📄 License

This project is licensed under the MIT License.