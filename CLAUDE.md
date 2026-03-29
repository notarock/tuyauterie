# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Project Is

Tuyauterie is a collection of reusable GitHub Actions workflows and composite actions. It is not a traditional application — there are no binaries to build, no test suite to run, and no local development server. Changes are purely to YAML workflow/action definitions.

## Architecture

The system uses a hub-and-spoke pattern centered on `.github/workflows/tuyauterie.yml`:

1. **`context` job** — Reads and parses `.tuyauterie.yaml` from the caller's repo using `actions/github-script` + `js-yaml`. Outputs all derived values (build type, artifact name, publish flag, GitOps config, etc.) consumed by downstream jobs.
2. **`build` job** — Dispatches to the appropriate composite action based on `build_type`.
3. **`build-docker` job** — Conditionally builds/publishes a Docker image if a Dockerfile is found.
4. **`release-log` job** — Generates commit log for releases on tags.
5. **`deployment-gitops` job** — Updates image tag in a remote Kubernetes manifest repo.

Composite actions live in `.github/actions/build-{golang,python,nix,node}/action.yml`. Each handles linting, building, testing, and optionally uploading artifacts for its language.

## Configuration Schema (`.tuyauterie.yaml`)

Client repos configure the pipeline via this file. See `.tuyauterie.example.yml` for the full reference. Key fields:

```yaml
version: 1
build:
  type: golang          # golang | python | nix | docker | node
  path: .               # subdirectory containing the project
  artifact_name: my-app # name for artifacts, packages, Docker image (defaults to repo name)
  dockerfile: Dockerfile
  upload_artifacts: false
release:
  rolling: false        # publish on every push to main
  on-tag: true          # publish on git tags
deploy:
  gitops:
    enable: false
    repository: org/repo
    manifest: k8s/deployment.yaml
```

## Adding New Features

Most changes fall into one of these patterns:

- **New config field**: Add parsing in the `context` job script in `tuyauterie.yml`, emit it with `core.setOutput(...)`, declare it as a job output, then pass it to the relevant downstream job.
- **New build type**: Add a composite action under `.github/actions/build-<type>/`, then add a conditional step in the `build` job of `tuyauterie.yml`.
- **New downstream job**: Add the job to `tuyauterie.yml`, wire it through `needs`, and gate it with an `if:` expression using context outputs.

The `context` job's `Summary` step writes the GitHub Actions job summary table — update it whenever new context outputs are added.

## Calling the Workflow

Client repos invoke the main workflow as:

```yaml
jobs:
  ci:
    uses: notarock/tuyauterie/.github/workflows/tuyauterie.yml@main
    with:
      tuyauterie-path: .tuyauterie.yaml
      github-event: ${{ github.event_name }}
    secrets:
      GITOPS_REPOSITORY_TOKEN: ${{ secrets.GITOPS_REPOSITORY_TOKEN }}
```
