# Dockerfile Project

This project contains a Dockerfile to set up a development environment with various tools and dependencies.

## Tools and Dependencies

The Dockerfile installs the following tools and dependencies:

- curl
- wget
- python3
- python3-pip
- pipx
- git
- gnupg
- software-properties-common
- apt-transport-https
- ca-certificates
- openssl
- zip
- unzip
- sudo
- vim
- zsh
- fonts-powerline
- iputils-ping

## Additional Repositories

The Dockerfile adds the following repositories:

- Hashicorp
- Microsoft

## Installed Software

The Dockerfile installs the following software:

- Terraform
- PowerShell & Az Module
- Packer
- Azure CLI
- Ansible

## Clean Up

The Dockerfile performs clean-up operations to reduce the image size.

## Non-root User

The Dockerfile creates a non-root user with sudo privileges.

## Usage

To build the Docker image, run the following command:

```sh
docker build -t iac .
```

To run a container from the built image, use:

```sh
docker run -it iac
```

## Environment Variables
The following environment variables can be set to customize the installation:

- TERRAFORM_VERSION
- POWERSHELL_VERSION
- PACKER_VERSION
- AZ_CLI_VERSION
- ANSIBLE_DEV_TOOLS_VERSION
- USER_GID
- USER_UID
- USERNAME

## License
This project is licensed under the MIT License.