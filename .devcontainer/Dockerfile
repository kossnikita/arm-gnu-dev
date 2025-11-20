# Dev container image for ARM bare-metal C development
FROM mcr.microsoft.com/devcontainers/base:ubuntu-22.04

ARG DEBIAN_FRONTEND=noninteractive
ARG ARM_GNU_TOOLCHAIN_VERSION=14.3.rel1
ARG ARM_GNU_TOOLCHAIN_ARCHIVE=arm-gnu-toolchain-${ARM_GNU_TOOLCHAIN_VERSION}-x86_64-arm-none-eabi.tar.xz
ARG ARM_GNU_TOOLCHAIN_URL=https://developer.arm.com/-/media/Files/downloads/gnu/${ARM_GNU_TOOLCHAIN_VERSION}/binrel/${ARM_GNU_TOOLCHAIN_ARCHIVE}

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
