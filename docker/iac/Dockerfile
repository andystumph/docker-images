ARG IMAGE=ubuntu
ARG IMAGE_VERSION=24.04

FROM ${IMAGE}:${IMAGE_VERSION}

ARG USERNAME=iac
ARG USER_UID=1000
ARG USER_GID=$USER_UID

ARG TERRAFORM_VERSION=1.13.5-1
ARG POWERSHELL_VERSION=7.5.4-1.deb
ARG AZ_CLI_VERSION=2.79.0-1~noble
ARG PACKER_VERSION=1.14.2-1
ARG ANSIBLE_DEV_TOOLS_VERSION=25.10.0

ENV PIPX_HOME=/opt/pipx
ENV PIPX_BIN_DIR=/usr/local/bin
ENV LC_ALL=C.UTF-8

COPY ./files/Cisco_Umbrella_Root_CA.cer /etc/ssl/certs
COPY ./files/requirements.yml /tmp/requirements.yml

## Combined installation layer - this reduces image layers and enables cleanup in same layer
RUN apt-get update && \
    # Install basic tooling
    apt-get install -y --no-install-recommends \
    curl \
    wget \
    python3 \
    python3-pip \
    python3-dev \
    pipx \
    locales \
    git \
    gh \
    gnupg \
    gcc \
    build-essential \
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
    iputils-ping \
    libkrb5-dev \
    krb5-user && \
    # Set up locale
    locale-gen C.UTF-8 && \
    # Clean apt cache to free up space before adding more repositories
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    # Add Hashicorp Repository
    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list && \
    # Add Microsoft Repositories
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    rm packages-microsoft-prod.deb && \
    wget -O- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | tee /usr/share/keyrings/microsoft.gpg > /dev/null && \
    echo "deb [arch=amd64 signed-by=/usr/share/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/azure-cli.list && \
    # Update with new repos
    apt-get update && \
    # Download packages to /tmp (more space) then install with dpkg
    # This avoids apt's disk space check in /var/cache/apt/archives/
    cd /tmp && \
    apt-get download terraform=${TERRAFORM_VERSION} && dpkg -i terraform*.deb && rm terraform*.deb && \
    apt-get download packer=${PACKER_VERSION} && dpkg -i packer*.deb && rm packer*.deb && \
    apt-get download azure-cli=${AZ_CLI_VERSION} && dpkg -i azure-cli*.deb && rm azure-cli*.deb && \
    apt-get download powershell=${POWERSHELL_VERSION} && dpkg -i powershell*.deb && rm powershell*.deb && \
    # Download python3-pyvmomi and dependencies to /tmp
    apt-get download python3-pyvmomi python3-certifi python3-chardet python3-idna python3-requests python3-urllib3 && \
    dpkg -i *.deb && \
    rm *.deb && \
    cd / && \
    # Install Azure Developer CLI
    curl -fsSL https://aka.ms/install-azd.sh | bash && \
    # Install PowerShell Az Module
    pwsh -Command "Set-PSRepository -Name PSGallery -InstallationPolicy Trusted; Install-Module -Name Az -Repository PSGallery -Scope AllUsers -Force" && \
    # Install Ansible
    pipx install --include-deps ansible-dev-tools==${ANSIBLE_DEV_TOOLS_VERSION} && \
    pipx inject ansible-dev-tools pywinrm[kerberos] && \
    # Install pre-commit
    pipx install pre-commit && \
    # Remove wget (no longer needed after setup)
    apt-get purge -y --auto-remove wget && \
    # Clean up package caches and temporary files
    apt-get clean && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /root/.cache /root/.ansible/tmp

## Create Non-root User and configure environment
RUN userdel -r ubuntu 2>/dev/null || true && \
    groupadd --gid ${USER_GID} ${USERNAME} && \
    useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} && \
    echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} && \
    chmod 0440 /etc/sudoers.d/${USERNAME}

USER ${USERNAME}

COPY ./files/requirements.yml /tmp/requirements.yml

# Install Ansible collections and Oh-my-zsh in one layer
RUN ansible-galaxy collection install -r /tmp/requirements.yml -p /home/${USERNAME}/.ansible/collections/ansible_collections && \
    curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh | zsh && \
    sudo rm -rf /tmp/* /home/${USERNAME}/.cache

COPY ./files/.zshrc /home/${USERNAME}/.zshrc

WORKDIR /work
CMD ["zsh"]