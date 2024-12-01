# 🚀 Staging and Production Workflow

This GitHub Actions workflow automates the release process and deployment to both **Staging** and **Production** environments. It is designed to streamline the release pipeline, ensuring reliable and efficient deployment.

## 📝 Workflow Overview

1. **Release Creation**:
   - Automatically triggered when a pull request is closed or manually through `workflow_dispatch`.
   - Uses [release-please-action](https://github.com/googleapis/release-please-action) to generate a new release.

2. **Staging Deployment**:
   - Deploys the release to the **Staging environment**.
   - Ensures the release is validated in a pre-production environment.

3. **Production Deployment**:
   - Deploys the release to the **Production environment**.
   - Runs only after a successful Staging deployment.


## 📂 Workflow Structure

### Trigger
- Pull Request closure (`types: closed`).
- Manual dispatch (`workflow_dispatch`).

### Jobs
1. **`release`**:
   - Generates a release using `release-please`.
2. **`deploy-staging`**:
   - Deploys to the **Staging environment**.
   - Triggered only if a release is successfully created.
3. **`deploy-production`**:
   - Deploys to the **Production environment**.
   - Triggered only after a successful Staging deployment.


## 🔧 Configuration

### Secrets Required
Ensure the following secrets are configured in your repository:

| **Secret Name**             | **Description**                                |
|-----------------------------|-----------------------------------------------|
| `GITHUB_TOKEN`              | GitHub token for accessing the repository.    |
| `STAGING_SITE_URL`          | URL for the Staging environment.              |
| `STAGING_DEPLOY_PATH`       | Path for Staging deployment.                  |
| `STAGING_DEPLOY_HOST`       | Host for Staging environment.                 |
| `STAGING_DEPLOY_PORT`       | Port for Staging server.                      |
| `STAGING_DEPLOY_USER`       | Username for Staging server.                  |
| `STAGING_DEPLOY_KEY`        | SSH key for Staging server.                   |
| `PRODUCTION_SITE_URL`       | URL for the Production environment.           |
| `PRODUCTION_DEPLOY_PATH`    | Path for Production deployment.               |
| `PRODUCTION_DEPLOY_HOST`    | Host for Production environment.              |
| `PRODUCTION_DEPLOY_PORT`    | Port for Production server.                   |
| `PRODUCTION_DEPLOY_USER`    | Username for Production server.               |
| `PRODUCTION_DEPLOY_KEY`     | SSH key for Production server.                |
| `SLACK_WEBHOOK`             | Slack webhook for deployment notifications.   |



## 📋 Usage

### Automatic Trigger
- Close a pull request to trigger the release and deployment pipeline.

### Manual Trigger
- Use the **Run Workflow** button in the **Actions** tab to start the workflow manually.



## 🚀 Benefits

- **Streamlined Releases**: Automated release creation with `release-please`.
- **Environment Separation**: Staging deployment ensures validation before production.
- **Notifications**: Integrated Slack notifications keep the team informed.


Happy Deploying! 🎉
