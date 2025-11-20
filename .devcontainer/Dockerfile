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
        python3-yaml \
        wget \
        curl \
        xz-utils \
        fonts-powerline \
        libxrender1 \
        libxcb-render0 \
        libxcb-render-util0 \
        libxcb-shape0 \
        libxcb-randr0 \
        libxcb-xfixes0 \
        libxcb-sync1 \
        libxcb-shm0 \
        libxcb-icccm4 \
        libxcb-keysyms1 \
        libxcb-image0 \
        libxkbcommon0 \
        libxkbcommon-x11-0 \
        libfontconfig1 \
        libfreetype6 \
        libxext6 \
        libx11-6 \
        libxcb1 \
        libx11-xcb1 \
        libsm6 \
        libice6 \
    && wget -O /tmp/${ARM_GNU_TOOLCHAIN_ARCHIVE} ${ARM_GNU_TOOLCHAIN_URL} \
    && mkdir -p /opt/arm-gnu-toolchain \
    && tar -xJf /tmp/${ARM_GNU_TOOLCHAIN_ARCHIVE} -C /opt/arm-gnu-toolchain --strip-components=1 \
    && rm /tmp/${ARM_GNU_TOOLCHAIN_ARCHIVE} \
    && apt clean \
    && rm -rf /var/lib/apt/lists/*

ENV PATH="/opt/arm-gnu-toolchain/bin:${PATH}"

# Install Segger J-Link (deb package) using curl with resume+retries
RUN set -eux; \
    echo "Downloading ${JLINK_DEB_NAME} from ${JLINK_DEB_URL} with curl (resume + retries)"; \
    for i in 1 2 3; do \
        echo "curl POST attempt $i: accepting EULA and downloading"; \
        curl -fL --retry 8 --retry-delay 5 --retry-max-time 60 \
            --data "accept_license_agreement=accepted" \
            --data "submit=Download+software" \
            -o /tmp/${JLINK_DEB_NAME} \
            ${JLINK_DEB_URL} && break || echo "curl POST attempt $i failed, retrying..."; \
        sleep 3; \
    done; \
    if [ ! -f /tmp/${JLINK_DEB_NAME} ]; then echo "Failed to download ${JLINK_DEB_NAME} after retries" >&2; exit 1; fi; \
    # Ensure the downloaded file is a deb and not an HTML page (EULA)
    if file /tmp/${JLINK_DEB_NAME} | grep -qi 'html\|text'; then echo "Downloaded ${JLINK_DEB_NAME} appears to be HTML (EULA page). Abort." >&2; exit 1; fi; \
    apt-get update; \
    # Provide a no-op udevadm so the package post-install script can run inside a container
    printf '#!/bin/sh\nexit 0' > /usr/bin/udevadm; chmod +x /usr/bin/udevadm; \
    # Use apt to install the .deb so dependencies are resolved automatically
    apt-get install -y --no-install-recommends /tmp/${JLINK_DEB_NAME}; \
    rm -f /usr/bin/udevadm; \
    rm -f /tmp/${JLINK_DEB_NAME}; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*

# Configure zsh theme for the vscode user (keep zsh, switch theme to agnoster)
RUN if [ -f /home/vscode/.zshrc ]; then \
            if grep -q '^ZSH_THEME=' /home/vscode/.zshrc; then \
                sed -i 's/^ZSH_THEME=.*/ZSH_THEME="agnoster"/' /home/vscode/.zshrc; \
            else \
                echo 'ZSH_THEME="agnoster"' >> /home/vscode/.zshrc; \
            fi; \
            chown vscode:vscode /home/vscode/.zshrc; \
        fi
