## gds-idea-workflows-catalogue
Centralised template repository for reusable CI/CD workflows

These workflows standardise:

- Python linting and testing
- Version enforcement
- AWS CDK infrastructure diff on PRs
- Branch-based deployment to DEV and PROD
- OIDC-based AWS authentication

These workflows currently support AWS CDK application testing and deployment. Python package workflows will be added in a future release.

## Branching & Deployment Model

This catalogue assumes the following flow:

feature/* → PR → dev → merge → deploy to DEV  
dev → PR → main → merge → deploy to PROD

- PRs trigger CI and infrastructure diff
- Merges trigger CD deployment
- `dev` branch deploys to the DEV AWS account
- `main` branch deploys to the PROD AWS account

## Reusable Workflows

| Workflow | Purpose |
|---------|---------|
| `ci_build.yml` | Builds the project artifacts and validates that the application can compile/package successfully. |
| `ci_tests.yml` | Runs the automated test suite (unit/integration) to ensure code changes do not break functionality. |
| `ci_lint.yml` | Executes linting and static code analysis to enforce coding standards and detect potential issues. |
| `ci_cdk_diff.yml` | Runs `cdk diff` to compare proposed infrastructure changes against the currently deployed AWS stacks. |
| `ci_pyproject_version.yml` |  Checks the `pyproject.toml` version against the currently deployed production version and fails the pipeline if the version has not been incremented |
| `cd_workflow_cdk.yml` | Handles continuous deployment of AWS infrastructure using AWS CDK, deploying approved changes to DEV and PROD environments. |
