# WebApp Release Deployer

## Description

The WebApp Release Deployer is a comprehensive GitHub Action designed to automate the deployment process of your web application. This action handles setting up the environment, installing dependencies, building the project, deploying to a remote server, and sending notifications to Slack.

## Inputs

| Input            | Description                                               | Required | Default                                                                                                          |
|------------------|-----------------------------------------------------------|----------|------------------------------------------------------------------------------------------------------------------|
| `github-token`   | GitHub Token                                              | Yes      | N/A                                                                                                              |
| `deploy-path`    | Remote deploy path                                        | Yes      | N/A                                                                                                              |
| `deploy-host`    | Remote deploy host                                        | Yes      | N/A                                                                                                              |
| `deploy-port`    | Remote deploy port                                        | Yes      | N/A                                                                                                              |
| `deploy-user`    | Remote deploy user                                        | Yes      | N/A                                                                                                              |
| `deploy-key`     | Remote deploy key                                         | Yes      | N/A                                                                                                              |
| `path`           | Path to the build directory                               | Yes      | `build/trunk/`                                                                                                   |
| `switches`       | Rsync switches for deployment                             | No       | `-avzr --delete --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"` |
| `slack-webhook`  | Slack webhook URL for notifications                       | No       | N/A                                                                                                              |
| `slack-channel`  | Slack channel for notifications                           | No       | `general`                                                                                                        |
| `slack-title`    | Slack notification title                                  | No       | `Web Application Deployed`                                                                                       |
| `slack-message`  | Slack notification message                                | No       | `Deployment process completed. Check logs for details.`                                                          |
| `slack-username` | Slack notification username                               | No       | `WebApp Deploy Bot`                                                                                              |
| `slack-footer`   | Slack notification footer                                 | No       | `Web Application Update Status`                                                                                  |

## Usage

To use this action in your workflow, include the following step in your GitHub Actions workflow file:

### Example Workflow

```yaml
name: 🚀 Release Deployer
on:
  pull_request:
    types:
      - closed
  workflow_dispatch:

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Run release-please
        uses: google-github-actions/release-please-action@v3
        id: release
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          command: manifest
          default-branch: master

      - name: Run Custom Deployer Action
        if: ${{ steps.release.outputs.releases_created }}
        uses: devuri/rd-web-app-release-deployer-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          deploy-path: ${{ secrets.DEPLOY_PATH }}
          deploy-host: ${{ secrets.DEPLOY_HOST }}
          deploy-port: ${{ secrets.DEPLOY_PORT }}
          deploy-user: ${{ secrets.DEPLOY_USER }}
          deploy-key: ${{ secrets.DEPLOY_KEY }}
          path: build/trunk/
          switches: '-avzr --delete --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"'
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}
          slack-channel: general
          slack-title: "Web Application Deployed"
          slack-message: "Deployment process completed."
          slack-username: "WebApp Deploy Bot"
          slack-footer: "Web Application Update Status"
```


## How It Works

1. **Setup Environment**: The action sets up the necessary environment, including PHP and Node.js.
2. **Install Dependencies**: It installs the required PHP and NPM dependencies.
3. **Build Project**: The action runs the build process for your project.
4. **Deploy Using Rsync**: It deploys the built artifacts to the remote server using `rsync` with the specified switches.
5. **Remote Commands Execution**: Optionally, it runs additional commands on the remote server using SSH to complete the deployment process.
6. **Upload Release Assets**: It uploads the built artifacts to the GitHub release.
7. **Slack Notification**: Finally, it sends a notification to Slack with the deployment details.

### Caveats

- **Permissions**: Ensure that the SSH key and GitHub token have the necessary permissions to access the remote server and GitHub repository respectively.
- **Network Configuration**: The remote server must be accessible from the GitHub Actions runner. Ensure that firewalls and security groups allow this access.
- **Error Handling**: The action stops execution on errors (`set -eo`), which ensures that subsequent steps are not executed if a previous step fails.
- **Dependencies**: Ensure that all dependencies (PHP extensions, Node.js modules, etc.) are correctly specified and available.

## Secrets

| Secret             | Description                  |
|--------------------|------------------------------|
| `GITHUB_TOKEN`     | GitHub token for authentication |
| `DEPLOY_PATH`      | Remote deploy path           |
| `DEPLOY_HOST`      | Remote deploy host           |
| `DEPLOY_PORT`      | Remote deploy port           |
| `DEPLOY_USER`      | Remote deploy user           |
| `DEPLOY_KEY`       | Remote deploy key            |
| `SLACK_WEBHOOK`    | Slack webhook URL for notifications |

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).
