# GitHub Actions - Shared Workflows

## Docker - Build image

Workflow: `.github/workflows/docker-image-build.yml`

### Inputs

- `image`: name of the docker image, e.g. `ghcr.io/requirecloud/some-image`
- `registry`: Docker Registry where to push the image. Default value: `ghcr.io`
- `push`: Should the image actually be pushed to Registry. Default value: `true`

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
