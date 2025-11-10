[![Build](https://github.com/andystumph/docker-images/actions/workflows/iac.yml/badge.svg)](https://github.com/andystumph/docker-images/actions/workflows/iac.yml)

# Docker Images

This repository contains Docker images for various development and operational scenarios. Each image is designed to provide a complete, ready-to-use environment with pre-installed tools and dependencies.

## Available Images

### Infrastructure as Code (IaC) Image

The `iac` image provides a comprehensive development environment for Infrastructure as Code workflows, including:

- **Infrastructure Tools**: Terraform, Packer
- **Cloud Platforms**: Azure CLI, Azure Developer CLI, PowerShell with Az module
- **Automation**: Ansible with extensive collection support
- **Development Tools**: Git, Vim, Zsh with Oh My Zsh, Python 3 with pip and pipx
- **Security**: Cisco Umbrella Root CA certificate integration
- **User Experience**: Non-root user with sudo privileges, customized Zsh environment

📁 **Location**: `docker/iac/`  
📖 **Documentation**: See [docker/iac/README.md](docker/iac/README.md) for detailed information

## Quick Start

### Building Images

```bash
# Build the IaC image
docker build -t iac ./docker/iac
```

### Running Containers

```bash
# Run IaC container interactively
docker run -it --rm iac

# Run with volume mount for your projects
docker run -it --rm -v "$(pwd)/projects:/work" iac
```

## Pre-built Images

Pre-built images are automatically published to Docker Hub when changes are pushed to the main branch:

- `astumph/iac:latest` - Latest stable version
- `astumph/iac:<version>` - Specific tagged versions

## CI/CD

This repository uses GitHub Actions for automated testing and deployment:

- **Testing**: Each image is built and tested for tool availability on pull requests
- **Release**: Successful builds on the main branch are automatically published to Docker Hub
- **Scheduling**: Weekly builds ensure images stay updated with latest base image updates

## Contributing

1. Create feature branches for changes
2. Test locally using `docker build` and `docker run`
3. Submit pull requests for review
4. All tests must pass before merging

## License

This project is licensed under the MIT License.
