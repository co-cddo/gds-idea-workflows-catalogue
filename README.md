# gds-idea-workflows-catalogue
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
