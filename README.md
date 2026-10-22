## gds-idea-workflows-catalogue
Centralised template repository for reusable CI/CD workflows

These workflows standardise:

- Python linting and testing
- Version enforcement
- AWS CDK infrastructure diff on PRs
- Branch-based deployment to DEV and PROD
- OIDC-based AWS authentication
- Terraform deployments

These workflows currently support AWS CDK application testing and deployment. Python package workflows will be added in a future release.

## Branching & Deployment Model

This catalogue assumes the following flow:

feature/* → PR → dev → merge → deploy to DEV  
dev → PR → prod → merge → deploy to PROD

- PRs trigger CI and infrastructure diff
- Merges trigger CD deployment
- `dev` branch deploys to the DEV AWS account
- `prod` branch deploys to the PROD AWS account

## Reusable Workflows

| Workflow | Purpose |
|---------|---------|
| `ci_build.yml` | Builds the project artifacts and validates that the application can compile/package successfully. |
| `ci_tests.yml` | Runs the automated test suite (unit/integration) to ensure code changes do not break functionality. |
| `ci_lint.yml` | Executes linting and static code analysis to enforce coding standards and detect potential issues. |
| `ci_dev_branch_policy.yml` | Enforces the branching policy by ensuring pull requests targeting the `prod` branch originate only from the `dev` branch. |
| `ci_cdk_diff.yml` | Runs `cdk diff` to compare proposed infrastructure changes against the currently deployed AWS stacks. |
| `ci_pyproject_version.yml` |  Checks the `pyproject.toml` version against the currently deployed production version and fails the pipeline if the version has not been incremented |
| `cd_workflow_cdk.yml` | Handles continuous deployment of AWS infrastructure using AWS CDK, deploying approved changes to DEV and PROD environments. |
| `pre-commit.yml` | Runs repository pre-commit file. |
| `terraform-deployment.yml` | Handles continuous deployment of AWS infrastructure using Terraform, deploying approved changes to DEV and PROD environments. |
| `branch-protection.yml` | Checks if action was triggered with allowed branch. |

## Queued deployments

`cd_workflow_cdk.yml`, `terraform-deployment.yml` (on `apply`) and `auto_tag_release.yml` queue their runs with `queue: max`. If a PR merges while the last deploy is still running, the new deploy waits and then runs. It is not cancelled. This lets you merge a stack of PRs one after another.

The group names are:

- `cdk-deploy-<repo>-<ref>`
- `tf-apply-<repo>-<workspace>`
- `release-<repo>`

Do not use these names in your own workflow-level `concurrency`. A matching group in the caller cancels the called job.

If a `cd_workflow_cdk.yml` deploy fails, it cancels the runs of the same workflow that are waiting on the same branch. Newer code does not deploy on top of a failure until someone has looked. To deploy anyway, re-run the cancelled run, or merge a fix.

The job that calls `cd_workflow_cdk.yml` must grant `actions: write`, which the cancel step needs. Without it, the run fails at startup:

```yaml
  cd:
    uses: co-cddo/gds-idea-workflows-catalogue/.github/workflows/cd_workflow_cdk.yml@main
    permissions:
      id-token: write
      contents: read
      actions: write
    secrets: inherit
```
