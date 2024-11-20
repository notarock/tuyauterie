# Rolling Deployment Workflow

This GitHub Actions workflow automates the process of performing a rolling deployment using GitOps principles. It allows you to update the deployment configuration with a new version and push the changes to your GitOps repository.

## Features

- Check out a GitOps repository.
- Modify deployment files with the specified version.
- Commit and push the changes back to the repository.

## Usage

To use this workflow, reference it in another GitHub Actions workflow file:

```yaml
jobs:
  rolling-deployment:
    uses: notarock/tuyauterie/.github/workflows/UpdateDeploymentVersion.yml@main
    with:
      version: "v1.0.0"
      repository: "your-gitops-repo"
      deployment-path: "path/to/deployment/file"
    secrets:
      gitops-token: ${{ secrets.GITOPS_TOKEN }}
```

## Inputs

| Name              | Description                                                         | Required | Default            |
|-------------------|---------------------------------------------------------------------|----------|--------------------|
| `version`         | The version to be deployed.                                          | Yes      | N/A                |
| `repository`      | The GitOps repository to check out and update.                       | Yes      | `notarock/gitops`   |
| `deployment-path` | Path to the deployment file to be updated in the repository.         | Yes      | N/A                |

## Secrets

| Name            | Description                                                    | Required |
|-----------------|----------------------------------------------------------------|----------|
| `gitops-token`  | GitHub token to authenticate with the GitOps repository. Must have Read & Push permisions       | Yes      |

## Example

This is an example of how to set up the workflow:

```yaml
jobs:
  rolling-deployment:
    uses: notarock/tuyauterie/.github/workflows/UpdateDeploymentVersion.yml@main
    with:
      version: "v1.0.1"
      repository: "you/your-gitops-repo"
      deployment-path: "path/to/deployment.yaml"
    secrets:
      gitops-token: ${{ secrets.GITOPS_TOKEN }}
```

