# Dev container image for ARM bare-metal C development
FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

ARG DEBIAN_FRONTEND=noninteractive
ARG ARM_GNU_TOOLCHAIN_VERSION=14.3.rel1
ARG ARM_GNU_TOOLCHAIN_ARCHIVE=arm-gnu-toolchain-${ARM_GNU_TOOLCHAIN_VERSION}-x86_64-arm-none-eabi.tar.xz
ARG ARM_GNU_TOOLCHAIN_URL=https://developer.arm.com/-/media/Files/downloads/gnu/${ARM_GNU_TOOLCHAIN_VERSION}/binrel/${ARM_GNU_TOOLCHAIN_ARCHIVE}
ARG JLINK_VERSION=888
ARG JLINK_DEB_URL=https://www.segger.com/downloads/jlink/JLink_Linux_V${JLINK_VERSION}_x86_64.deb
ARG JLINK_DEB_NAME=JLink_Linux_V${JLINK_VERSION}_x86_64.deb

RUN apt update \
    && apt install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        cmake \
        gdb-multiarch \
        git \
        ninja-build \
        python3 \
        python3-pip \
        wget \
        xz-utils \
        fonts-powerline \
    && wget -O /tmp/${ARM_GNU_TOOLCHAIN_ARCHIVE} ${ARM_GNU_TOOLCHAIN_URL} \
    && mkdir -p /opt/arm-gnu-toolchain \
    && tar -xJf /tmp/${ARM_GNU_TOOLCHAIN_ARCHIVE} -C /opt/arm-gnu-toolchain --strip-components=1 \
    && rm /tmp/${ARM_GNU_TOOLCHAIN_ARCHIVE} \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

ENV PATH="/opt/arm-gnu-toolchain/bin:${PATH}"

# Useful Python tools for debugging and flashing
RUN pip3 install --no-cache-dir \
        PyYAML \
        pyocd \
        west

# Install Segger J-Link (deb package)
RUN wget -O /tmp/${JLINK_DEB_NAME} ${JLINK_DEB_URL} \
    && dpkg -i /tmp/${JLINK_DEB_NAME} || true \
    && apt-get update \
    && apt-get install -y --no-install-recommends -f \
    && rm -f /tmp/${JLINK_DEB_NAME} \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Configure zsh theme for the vscode user (keep zsh, switch theme to agnoster)
RUN if [ -f /home/vscode/.zshrc ]; then \
            if grep -q '^ZSH_THEME=' /home/vscode/.zshrc; then \
                sed -i 's/^ZSH_THEME=.*/ZSH_THEME="agnoster"/' /home/vscode/.zshrc; \
            else \
                echo 'ZSH_THEME="agnoster"' >> /home/vscode/.zshrc; \
            fi; \
            chown vscode:vscode /home/vscode/.zshrc; \
        fi
