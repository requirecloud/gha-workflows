# GitHub Actions - Shared Workflows

## Zero Trust SSH

Workflow: `.github/workflows/zero-trust-ssh.yml`

This workflow establishes a Zero Trust SSH connection using either Tailscale or Twingate, and sets up SSH keys for secure access to remote hosts. It's designed to enable secure SSH connections in GitHub Actions workflows without exposing hosts to the public internet.

### Inputs

- `zerotrust`: Which Zero Trust service to use. Options: `tailscale` or `twingate`. Default value: `tailscale`
- `ssh_host`: SSH host to set in ssh config. Optional, only used when `KNOWN_HOSTS` secret is not provided

### Secrets

The workflow requires different secrets depending on the Zero Trust service selected:

**For Tailscale:**
- `TS_OAUTH_CLIENT_ID`: Tailscale OAuth client ID
- `TS_OAUTH_SECRET`: Tailscale OAuth secret

**For Twingate:**
- `TWINGATE_SERVICE_KEY`: Twingate service key

**SSH Configuration (required for both):**
- `PRIVATE_SSH_KEY`: Private SSH key for authentication
- `KNOWN_HOSTS`: (Optional) Known hosts file content. If not provided, `ssh_host` input must be set

### Usage

```yaml
name: Deploy via Zero Trust SSH

on:
  push:
  workflow_dispatch:

jobs:

  zero-trust-ssh:

    uses: requirecloud/gha-workflows/.github/workflows/zero-trust-ssh.yml@main
    with:
      zerotrust: tailscale
      ssh_host: myserver.internal
    secrets: inherit
```

## Docker - Build image

Workflow: `.github/workflows/docker-image-build.yml`

This workflow builds Docker images with optimized caching for faster builds. It supports multi-architecture builds for both linux/amd64 and linux/arm64 platforms using the "image" exporter type which properly handles manifest lists required for multi-architecture images.

### Inputs

- `image`: name of the docker image, e.g. `ghcr.io/requirecloud/some-image`
- `registry`: Docker Registry where to push the image. Default value: `ghcr.io`
- `push`: Should the image actually be pushed to Registry. Default value: `true`

### Caching

The workflow uses two types of caching to speed up builds:

1. **GitHub Actions Cache**: Always enabled, persists between workflow runs
   - Repository-wide cache: Shared across all branches
   - Branch-specific cache: Optimized for the current branch

2. **Registry Cache**: Enabled when `push` is set to `true`, stores cache in the registry with a `buildcache` tag

These caching mechanisms significantly reduce build times by reusing layers from previous builds.

#### Optimizing for Cache Effectiveness

For best caching results:

1. **Structure your Dockerfile efficiently**:
   - Place infrequently changing layers (e.g., base image, dependencies) at the beginning
   - Place frequently changing layers (e.g., application code) at the end

2. **Use .dockerignore file** to exclude unnecessary files from the build context

3. **Ensure consistent build environments** across different workflows that use the same image

4. **Be patient with initial builds** - caching benefits become apparent after the first few builds

### Usage

```yaml
name: Build my Docker image

on:
  push:
  workflow_dispatch:

jobs:

  docker-image-build:

    uses: requirecloud/gha-workflows/.github/workflows/docker-image-build.yml@main
    with:
      image: ghcr.io/requirecloud/some-image
    secrets: inherit
```
