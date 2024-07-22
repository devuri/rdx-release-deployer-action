# Release Deployer

## Description

The Release Deployer workflow action is an Automated Release Deployment for Continuous Integration / Continuous Deployment (CI/CD). It is designed to automate the deployment process of your web application. This action handles setting up the environment, installing dependencies, building the project (the build package), deploying it to a remote server, and sending notifications to Slack.

## Inputs

| Input                 | Description                                                                                                          | Required | Default                                                                                                                            |
|-----------------------|----------------------------------------------------------------------------------------------------------------------|----------|------------------------------------------------------------------------------------------------------------------------------------|
| site-url              | The Deployment site URL                                                                                              | Yes      | N/A                                                                                                                                |
| github-token          | GitHub Token. This is provided automatically by GitHub. Use the default value ${{ secrets.GITHUB_TOKEN }}.  [More info](https://docs.github.com/en/actions/security-guides/automatic-token-authentication)  | Yes      | N/A                                                                                                                                |
| deploy-path           | The path on the remote server where the application will be deployed.                                                | Yes      | N/A                                                                                                                                |
| deploy-host           | The hostname or IP address of the remote server.                                                                     | Yes      | N/A                                                                                                                                |
| deploy-port           | The SSH port of the remote server (usually 22).                                                                      | Yes      | N/A                                                                                                                                |
| deploy-user           | The username for SSH access to the remote server.                                                                    | Yes      | N/A                                                                                                                                |
| deploy-key            | The private SSH key for accessing the remote server. [Generate an SSH key](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)                                              | Yes      | N/A                                                                                                                                |
| tag-name              | The tag name from release build.                                                                                     | Yes      | N/A                                                                                                                                |
| path                  | The path to the build directory on the GitHub runner.                                                                | Yes      | build/trunk/                                                                                                                       |
| switches              | Rsync switches for deployment. This controls the behavior of the file synchronization process. [Rsync documentation](https://linux.die.net/man/1/rsync)   | No       | -avzr --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini" |
| slack-webhook         | Slack webhook URL for notifications. Obtain this from your Slack settings. [Creating Slack webhooks](https://slack.com/help/articles/115005265063-Incoming-webhooks-for-Slack)                   | No       | N/A                                                                                                                                |
| slack-channel         | Slack channel for notifications.                                                                                     | No       | general                                                                                                                            |
| slack-title           | Title for the Slack notification.                                                                                    | No       | Web Application Deployed                                                                                                           |
| slack-message         | Message body for the Slack notification.                                                                             | No       | Deployment process completed. Check logs for details.                                                                              |
| slack-username        | Username that will appear as the sender of the Slack notification.                                                   | No       | WebApp Deploy Bot                                                                                                                  |
| slack-footer          | Footer text for the Slack notification.                                                                              | No       | Web Application Update Status                                                                                                      |
| php-version           | PHP version to setup                                                                                                 | No       | 7.4                                                                                                                                |
| php-extensions        | PHP extensions to install                                                                                            | No       | pcov                                                                                                                               |
| node-version          | Node.js version to setup                                                                                             | No       | 16                                                                                                                                 |
| use-php               | Whether to setup PHP                                                                                                 | No       | true                                                                                                                               |
| use-node              | Whether to setup Node.js                                                                                             | No       | true                                                                                                                               |
| use-remote-install       | Whether to execute remote SSH installs/updates over                                                                               | No       | false                                                                                                                               |
| upload-release-assets | Whether to upload release assets                                                                                     | No       | true                                                                                                                               |
| release-files         | Files to upload to release                                                                                           | No       | build.zip;CHANGELOG.md                        |
| use-cache             | Whether to use caching for dependencies                                                                              | No       | false                                         |                             
| upload-artifact       | Whether to upload artifacts                                                                                          | No       | false                                         |
| artifact-name         | Name of the artifact to upload                                                                                       | No       | build-artifact                                |
| artifact-path         | Path of the artifact to upload                                                                                       | No       | build/trunk/                                  |

## Usage

To use this action in your workflow, include the following steps in your GitHub Actions workflow file. If you're new to GitHub Actions, create a `.github/workflows` directory in the root of your repository, and add a YAML file (e.g., `release-deployer.yml`) with the following content:

### Example Workflow

Basic example:

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
        uses: googleapis/release-please-action@v4
        id: release
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          command: manifest
          default-branch: main

      - name: Run Custom Deployer Action
        if: ${{ steps.release.outputs.releases_created }}
        uses: devuri/rdx-release-deployer-action@v1
        with:
          site-url: ${{ secrets.SITE_URL }}                 # Deployment site URL
          github-token: ${{ secrets.GITHUB_TOKEN }}         # GitHub Token
          deploy-path: ${{ secrets.DEPLOY_PATH }}           # Remote deploy path
          deploy-host: ${{ secrets.DEPLOY_HOST }}           # Remote deploy host
          deploy-port: ${{ secrets.DEPLOY_PORT }}           # Remote deploy port
          deploy-user: ${{ secrets.DEPLOY_USER }}           # Remote deploy user
          deploy-key: ${{ secrets.DEPLOY_KEY }}             # Remote deploy key
          tag-name: ${{ steps.release.outputs.tag_name }}   # Release tag name

          path: build/trunk/                                # Path to the build directory on the GitHub runner (default: build/trunk/)
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}       # Slack webhook URL for notifications
```

Example with comments for each parameter:

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
        uses: googleapis/release-please-action@v4
        id: release
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          command: manifest
          default-branch: main

      - name: Run Custom Deployer Action
        if: ${{ steps.release.outputs.releases_created }}
        uses: devuri/rdx-release-deployer-action@v1
        with:
          # Required parameters
          site-url: ${{ secrets.SITE_URL }}                 # Deployment site URL
          github-token: ${{ secrets.GITHUB_TOKEN }}         # GitHub Token
          deploy-path: ${{ secrets.DEPLOY_PATH }}           # Remote deploy path
          deploy-host: ${{ secrets.DEPLOY_HOST }}           # Remote deploy host
          deploy-port: ${{ secrets.DEPLOY_PORT }}           # Remote deploy port
          deploy-user: ${{ secrets.DEPLOY_USER }}           # Remote deploy user
          deploy-key: ${{ secrets.DEPLOY_KEY }}             # Remote deploy key
          tag-name: ${{ steps.release.outputs.tag_name }}   # Release tag name

          # Optional parameters with defaults
          path: build/trunk/                                # Path to the build directory on the GitHub runner (default: build/trunk/)
          switches: '-avzr --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"' # Rsync switches for deployment (default: '-avzr --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"')
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}       # Slack webhook URL for notifications
          slack-channel: general                            # Slack channel for notifications (default: general)
          slack-title: "Web Application Deployed"           # Title for the Slack notification (default: "Web Application Deployed")
          slack-message: "Deployment process completed."    # Message body for the Slack notification (default: "Deployment process completed. Check logs for details.")
          slack-username: "WebApp Deploy Bot"               # Username that will appear as the sender of the Slack notification (default: "WebApp Deploy Bot")
          slack-footer: "Web Application Update Status"     # Footer text for the Slack notification (default: "Web Application Update Status")

          # Optional setup parameters with defaults
          php-version: '7.4'                                # PHP version to setup (default: '7.4')
          php-extensions: 'pcov'                            # PHP extensions to install (default: 'pcov')
          node-version: '16'                                # Node.js version to setup (default: '16')

          # Boolean flags
          use-php: true                                     # Whether to setup PHP (default: true)
          use-node: true                                    # Whether to setup Node.js (default: true)
          use-remote-install: true                          # Whether to execute remote SSH updates (default: false)
          upload-release-assets: true                       # Whether to upload release assets (default: true)

          # Files to upload to release
          release-files: 'build.zip;CHANGELOG.md'           # Files to upload to release (default: 'build.zip;CHANGELOG.md')

```

### Explanation
- **Required Parameters**: These are essential for the action to run and have no default values.
- **Optional Parameters with Defaults**: These parameters have default values and can be overridden.
- **Optional Parameters without Defaults**: These parameters do not have default values specified in the workflow.
- **Boolean Flags**: These control whether specific steps in the workflow are executed.
- **Files to Upload to Release**: Specifies the files to be uploaded as release assets.

In this example, the workflow triggers on closed pull requests and can also be manually triggered via the GitHub Actions tab. It uses the `googleapis/release-please-action` to [manage releases](https://github.com/googleapis/release-please-action) and then runs the custom deployer action if releases are created.

## Important Note

> [!WARNING]  
> The `--delete` option in the `switches` can be dangerous as it may result in the deletion of important files or content on the server.

1. **Build Directory**: The `path` input (default `build/trunk/`) specifies the directory from which files will be copied to the remote server using `rsync`. Ensure that your build process outputs the necessary files to this directory, or adjust the `path` input accordingly to match your build output location.

2. **Rsync `--delete` Option**: The `--delete` option in the `switches` input is used to keep the remote directory in sync with the local build directory by deleting files on the remote server that no longer exist locally. This can be dangerous as it may result in the deletion of important files or content on the server, such as user-uploaded images or other assets. Use this option with caution to avoid unintended data loss. Consider excluding directories that should not be deleted by adding them to the `--exclude` list in the `switches` input. See: [Rsync Delete Options](https://superuser.com/questions/156664/what-are-the-differences-between-the-rsync-delete-options)

## How It Works

1. **Setup Environment**: The action sets up the necessary environment, including PHP and Node.js if specified.
2. **Install Dependencies**: It installs the required PHP and NPM dependencies if specified.
3. **Build Project**: The action runs the build process for your project if specified.
4. **Deploy Using Rsync**: It deploys the built artifacts to the remote server using `rsync` with the specified switches.
5. **Remote Commands Execution**: Optionally, it runs additional commands on the remote server using SSH to complete the deployment process if specified.
6. **Upload Release Assets**: It uploads the built artifacts to the GitHub release if specified.
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

CI/CD is a method to frequently deliver apps to customers by introducing automation into the stages of app development. The main concepts attributed to CI/CD are continuous integration, continuous delivery, and continuous deployment. This action helps implement CI/CD by automating the deployment process, ensuring that your application is

 always in a deployable state and that updates are delivered to users quickly and efficiently.

### Security

- **Automated Environment Setup**: By automating the setup of the environment, this action reduces the risk of human error, which can introduce security vulnerabilities.
- **Controlled Deployments**: The use of SSH keys and tokens ensures that only authorized personnel can deploy updates, maintaining the integrity and security of the application.
- **Consistent Builds**: Automated builds and deployments ensure that the code running in production is consistent with the code in the repository, reducing the risk of inconsistencies that can lead to security issues.

By incorporating this action into your workflow, you can achieve a more reliable and secure deployment process, improve your development workflow, and ensure that your applications are deployed consistently and quickly.

> [!IMPORTANT]
> GitHub provides a certain amount of free minutes each month, depending on your plan, which can be sufficient for small to medium-sized projects.
> GitHub Actions usage is based on the compute time required to run your workflows, which may vary depending on the runner used and the total minutes consumed.
> To manage usage effectively, optimize your workflows and consider using [self-hosted runners](https://github.com/devuri/setting-up-and-using-self-hosted-runners) for larger projects. [Pricing calculator](https://github.com/pricing/calculator?feature=actions).

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).
