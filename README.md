# ARM GNU Bare-metal Dev Container

This repository defines a VS Code Dev Container that ships with the Arm GNU Toolchain 14.3.rel1 (`arm-none-eabi`), CMake, Ninja, and helpful debugging utilities for bare-metal development. It is meant to be built automatically on GitHub and published to the GitHub Container Registry (GHCR).

## Using the Dev Container in VS Code

1. Install the **Dev Containers** extension.
2. Open this repository in VS Code and run **Dev Containers: Reopen in Container**.
3. The container will run `arm-none-eabi-gcc --version` and `cmake --version` after creation so you can verify the toolchain is ready.
4. Recommended extensions (C/C++, Cortex-Debug, CMake Tools, Docker) are installed automatically by the dev container spec.

## Building the Image Manually

```pwsh
# Authenticate to GHCR if you plan to push the image
# echo YOUR_TOKEN | docker login ghcr.io -u YOUR_GITHUB_USER --password-stdin

# Build the dev container image locally
docker build -f .devcontainer/Dockerfile -t ghcr.io/<owner>/<repo>/arm-baremetal-devcontainer:local .
```

## GitHub Actions Publishing Flow

- The workflow in `.github/workflows/devcontainer-image.yml` runs on pushes to `main` that touch the dev container or the workflow itself.
- It authenticates with GHCR using the built-in `GITHUB_TOKEN`, builds the image, tags it with branch/tag names plus `latest`, and pushes it to `ghcr.io/<owner>/arm-baremetal-devcontainer`.
- Trigger the workflow manually via **Run workflow** if you need to publish on demand.

## Consuming the Published Image

After the workflow runs successfully, you can pull and use the prebuilt image anywhere Docker is available:

```pwsh
docker pull ghcr.io/<owner>/arm-baremetal-devcontainer:latest
```

Replace `<owner>` with your GitHub username or organization. Grant `read:packages` permissions to anyone needing access to the image if the repository is private.
