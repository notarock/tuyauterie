# Reusable Docker Build and Publish Workflow

This GitHub Actions workflow provides a reusable setup to build and publish Docker images to a container registry. It's designed to simplify CI/CD pipelines for containerized applications.

## Features

- Build and push Docker images using Buildx.
- Supports multi-platform builds with QEMU.
- Customizable registry settings and pre-build commands.
- Extracts metadata (tags and labels) for the image.

## Usage

This workflow can be used as a reusable workflow in other GitHub repositories. To include it, reference it in your workflow file:

```yaml
jobs:
  build-and-push:
    permissions:
      contents: read
      packages: write
    uses: notarock/tuyauterie/.github/workflows/docker-build-publish.yml@main
    with:
      REGISTRY_URL: ghcr.io
      REGISTRY_USERNAME: ${{ github.actor }}
    secrets:
      REGISTRY_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name                | Description                                                                     | Required | Default     |
|---------------------|---------------------------------------------------------------------------------|----------|-------------|
| `REGISTRY_URL`      | URL of the container registry where the image will be pushed.                  | No       | `ghcr.io`   |
| `REGISTRY_USERNAME` | Username for registry authentication. Defaults to the GitHub actor.            | No       | `${{ github.actor }}` |
| `EXTRA_COMMAND`     | Optional command to run before building the Docker image.                      | No       | `""`        |

## Secrets

| Name                 | Description                                      | Required |
|----------------------|--------------------------------------------------|----------|
| `REGISTRY_TOKEN`     | Authentication token for the container registry. | Yes      |
| `EXTRA_COMMAND_SECRET` | Optional secret to be passed to `EXTRA_COMMAND`. | No       |

## Outputs

| Name      | Description                     |
|-----------|---------------------------------|
| `version` | Version of the Docker image.   |

## Example with `EXTRA_COMMAND`

If you need to run an additional command before building the Docker image (e.g., setting environment variables or downloading dependencies), you can specify it in `EXTRA_COMMAND`:

```yaml
jobs:
  build-and-push:
    uses: notarock/tuyauterie/.github/workflows/docker-build-publish.yml@main
    permissions:
      contents: read
      packages: write
    with:
      REGISTRY_URL: ghcr.io
      EXTRA_COMMAND: "echo Pre-build step"
    secrets:
      REGISTRY_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      EXTRA_COMMAND_SECRET: ${{ secrets.MY_SECRET }}
```

## Notes

- Ensure your container registry token has the necessary permissions to push images.
- This workflow assumes the repository is already configured for Docker builds i.e. a valid `Dockerfile` was placed at the root of the repository.
