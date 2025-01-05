ARG IMAGE=ubuntu
ARG IMAGE_VERSION=24.04

FROM ${IMAGE}:${IMAGE_VERSION}

ARG USERNAME=iac
ARG USER_UID=1010
ARG USER_GID=$USER_UID

ARG TERRAFORM_VERSION=1.10.3-1
ARG POWERSHELL_VERSION=7.4.6-1.deb
ARG AZ_CLI_VERSION=2.67.0-1~noble
ARG PACKER_VERSION=1.10.2-1
ARG ANSIBLE_DEV_TOOLS_VERSION=24.12.0

ARG PIPX_HOME=/opt/pipx
ARG PIPX_BIN_DIR=/usr/local/bin

## Install basic tooling
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    python3 \
    python3-pip \
    pipx \
    git \
    gnupg \
    software-properties-common \
    apt-transport-https \
    ca-certificates \
    openssl \
    zip \
    unzip \
    sudo \
    vim \
    zsh \
    fonts-powerline \
    iputils-ping

## Add Repositories
# Hashicorp
RUN wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list && \
# Microsoft
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    rm packages-microsoft-prod.deb && \
    #
    wget -O- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | tee /usr/share/keyrings/microsoft.gpg > /dev/null && \
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/azure-cli.list && \
    #
    apt-get update

COPY ./files/Cisco_Umbrella_Root_CA.cer /etc/ssl/certs

## Install Terraform
RUN apt-get update && apt-get install -y terraform=${TERRAFORM_VERSION}

## Install PowerShell & Az Module
RUN apt-get update && apt-get install -y powershell=${POWERSHELL_VERSION} && \
pwsh -Command Set-PSRepository -Name PSGallery -InstallationPolicy Trusted && \
pwsh -Command Install-Module -Name Az -Repository PSGallery -Scope AllUsers -Force

## Install Packer
RUN apt-get update && apt-get install -y packer=${PACKER_VERSION}

## Install Azure CLI
RUN apt-get update && apt-get install -y azure-cli=${AZ_CLI_VERSION}

## Install Ansible
RUN pipx install --include-deps ansible-dev-tools==${ANSIBLE_DEV_TOOLS_VERSION} && \
    pipx install --include-deps pywinrm && \
    apt-get install -y python3-pyvmomi

## Clean Up
RUN apt-get clean autoclean && \
    apt-get autoremove --yes && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

## Create Non-root User
RUN groupadd --gid ${USER_GID} ${USERNAME} \
    && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} \
    && echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} \
    && chmod 0440 /etc/sudoers.d/${USERNAME}

USER ${USERNAME}

# Install Oh-my-zsh
RUN wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O - | zsh || true

COPY ./files/.zshrc /home/${USERNAME}/.zshrc

WORKDIR /work
CMD ["zsh"]