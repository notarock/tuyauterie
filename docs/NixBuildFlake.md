# Reusable Nix Flake Build Workflow

This GitHub Actions workflow provides a reusable setup to build Nix flakes. It automates the process of setting up Nix, caching, and building the flake for your project.

## Features

- Automatically installs Nix using `nix-installer-action`.
- Caches Nix builds using `magic-nix-cache-action` to speed up subsequent builds.
- Runs the `nix build flake` command to build the flake.

## Usage

To use this workflow in another repository, reference it in your GitHub Actions configuration:

```yaml
jobs:
  nix-flake-build:
    permissions:
      contents: read
      id-token: write
    uses: notarock/tuyauterie/.github/workflows/NixBuildFlake.yml@main
```

## Inputs

This workflow does not require any inputs, and it triggers on `workflow_call`.

## Example

Here's how you can include this reusable workflow in your own GitHub Actions file:

```yaml
jobs:
  nix-flake-build:
    permissions:
      contents: read
      id-token: write
    uses: notarock/tuyauterie/.github/workflows/NixBuildFlake.yml@main
```

