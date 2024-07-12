# WebApp Release Deployer

## Description

The WebApp Release Deployer is a GitHub Action designed to automate the deployment process of your web application. This action handles setting up the environment, installing dependencies, building the project, deploying to a remote server, and sending notifications to Slack.

## Inputs

| Input            | Description                                               | Required | Default                                                                                                          |
|------------------|-----------------------------------------------------------|----------|------------------------------------------------------------------------------------------------------------------|
| `github-token`   | GitHub Token. This is provided automatically by GitHub. Use the default value `${{ secrets.GITHUB_TOKEN }}`. [More info](https://docs.github.com/en/actions/security-guides/automatic-token-authentication) | Yes      | N/A                                                                                                              |
| `deploy-path`    | The path on the remote server where the application will be deployed.                      | Yes      | N/A                                                                                                              |
| `deploy-host`    | The hostname or IP address of the remote server.                                         | Yes      | N/A                                                                                                              |
| `deploy-port`    | The SSH port of the remote server (usually 22).                                              | Yes      | N/A                                                                                                              |
| `deploy-user`    | The username for SSH access to the remote server.                                         | Yes      | N/A                                                                                                              |
| `deploy-key`     | The private SSH key for accessing the remote server. [Generate an SSH key](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) | Yes      | N/A                                                                                                              |
| `path`           | The path to the build directory on the GitHub runner.                                      | Yes      | `build/trunk/`                                                                                                   |
| `tag-name`           | The tag name from release build.                                      | Yes      | N/A                                                                                                  |
| `switches`       | Rsync switches for deployment. This controls the behavior of the file synchronization process. [Rsync documentation](https://linux.die.net/man/1/rsync) | No       | `-avzr --delete --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"` |
| `slack-webhook`  | Slack webhook URL for notifications. Obtain this from your Slack settings. [Creating Slack webhooks](https://slack.com/help/articles/115005265063-Incoming-webhooks-for-Slack)                  | No       | N/A                                                                                                              |
| `slack-channel`  | Slack channel for notifications.                                                            | No       | `general`                                                                                                        |
| `slack-title`    | Title for the Slack notification.                                                           | No       | `Web Application Deployed`                                                                                       |
| `slack-message`  | Message body for the Slack notification.                                                    | No       | `Deployment process completed. Check logs for details.`                                                          |
| `slack-username` | Username that will appear as the sender of the Slack notification.                         | No       | `WebApp Deploy Bot`                                                                                              |
| `slack-footer`   | Footer text for the Slack notification.                                                     | No       | `Web Application Update Status`                                                                                  |

## Usage

To use this action in your workflow, include the following steps in your GitHub Actions workflow file. If you're new to GitHub Actions, create a `.github/workflows` directory in the root of your repository, and add a YAML file (e.g., `release-deployer.yml`) with the following content:

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
          tag-name: ${{ steps.release.outputs.tag_name }}
          switches: '-avzr --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"'
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}
          slack-channel: general
          slack-title: "Web Application Deployed"
          slack-message: "Deployment process completed."
          slack-username: "WebApp Deploy Bot"
          slack-footer: "Web Application Update Status"
```

In this example, the workflow triggers on closed pull requests and can also be manually triggered via the GitHub Actions tab. It uses the `google-github-actions/release-please-action` to manage releases and then runs the custom deployer action if releases are created.

## Important Note

1. **Build Directory**: The `path` input (default `build/trunk/`) specifies the directory from which files will be copied to the remote server using `rsync`. Ensure that your build process outputs the necessary files to this directory, or adjust the `path` input accordingly to match your build output location.

2. **Rsync `--delete` Option**: The `--delete` option in the `switches` input is used to keep the remote directory in sync with the local build directory by deleting files on the remote server that no longer exist locally. This can be dangerous as it may result in the deletion of important files or content on the server, such as user-uploaded images or other assets. Use this option with caution to avoid unintended data loss. Consider excluding directories that should not be deleted by adding them to the `--exclude` list in the `switches` input.

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
| `GITHUB_TOKEN`     | GitHub token for authentication (provided by default in GitHub Actions) |
| `DEPLOY_PATH`      | Remote deploy path           |
| `DEPLOY_HOST`      | Remote deploy host           |
| `DEPLOY_PORT`      | Remote deploy port           |
| `DEPLOY_USER`      | Remote deploy user           |
| `DEPLOY_KEY`       | Remote deploy key            |
| `SLACK_WEBHOOK`    | Slack webhook URL for notifications |

To add secrets to your repository:
1. Navigate to the repository on GitHub.
2. Click on "Settings".
3. Click on "Secrets" in the sidebar.
4. Click on "New repository secret".
5. Add each secret one by one.

## Why This Is Useful

### Continuous Integration and Continuous Deployment (CI/CD)

CI/CD is a method to frequently deliver apps to customers by introducing automation into the stages of app development. The main concepts attributed to CI/CD are continuous integration, continuous delivery, and continuous deployment. This action helps implement CI/CD by automating the deployment process ([#12](https://github.com/devuri/rd-web-app-release-deployer-action/issues/12)), ensuring that your application is always in a deployable state and that updates are delivered to users quickly and efficiently.

### Security

- **Automated Environment Setup**: By automating the setup of the environment, this action reduces the risk of human error, which can introduce security vulnerabilities.
- **Controlled Deployments**: The use of SSH keys and tokens ensures that only authorized personnel can deploy updates, maintaining the integrity and security of the application.
- **Consistent Builds**: Automated builds and deployments ensure that the code running in production is consistent with the code in the repository, reducing the risk of inconsistencies that can lead to security issues.

By incorporating this action into your workflow, you can achieve a more reliable and secure deployment process, improve your development workflow, and ensure that your applications are deployed consistently and quickly.

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).
