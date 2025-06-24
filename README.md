# GitHub Actions - Shared Workflows

## Docker - Build image

Workflow: `.github/workflows/docker-image-build.yml`

This workflow builds Docker images with optimized caching for faster builds.

### Inputs

- `image`: name of the docker image, e.g. `ghcr.io/requirecloud/some-image`
- `registry`: Docker Registry where to push the image. Default value: `ghcr.io`
- `push`: Should the image actually be pushed to Registry. Default value: `true`

### Caching

The workflow uses two types of caching to speed up builds:

1. **GitHub Actions Cache**: Always enabled, persists between workflow runs on the same branch
2. **Registry Cache**: Enabled when `push` is set to `true`, stores cache in the registry with a `buildcache` tag

These caching mechanisms significantly reduce build times by reusing layers from previous builds.

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
