## Using `upload-artifact` Action with the Release Deployer

The `upload-artifact` action in GitHub Actions allows you to upload files and directories from your workflow to be used later in the same workflow or in other workflows. This is particularly useful for sharing build outputs, logs, or other data between jobs.

### 1. Introduction to `upload-artifact`

The `upload-artifact` action is a built-in GitHub Action provided by the `actions` organization. It enables you to save artifacts, such as build results or test reports, which can be downloaded later from the GitHub Actions UI or used by subsequent steps in your workflow.

### 2. Setting Up `upload-artifact` with Release Deployer

To use the `upload-artifact` action in conjunction with the Release Deployer action, you need to specify the files or directories you want to upload. This ensures that artifacts like build outputs and logs can be preserved and utilized effectively.

### 3. Example Workflow with `upload-artifact`

Here is an example workflow that demonstrates how to use the `upload-artifact` action to upload build outputs and test reports along with the Release Deployer action:

```yaml
name: 🚀 Release Deployer with Artifacts
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
          default-branch: master

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
          switches: '-avzr --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"' # Rsync switches for deployment (default)
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}       # Slack webhook URL for notifications

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-artifacts
          path: build/
          retention-days: 7

      - name: Upload deployment logs
        uses: actions/upload-artifact@v3
        with:
          name: deployment-logs
          path: logs/
          retention-days: 14
```

### Explanation

- **Run release-please**: This step uses the `googleapis/release-please-action` to manage releases.
- **Run Custom Deployer Action**: This step uses the `devuri/rdx-release-deployer-action` to deploy the application with the required and optional parameters.
- **Upload Build Artifacts**: This step uses the `actions/upload-artifact` action to upload the contents of the `build/` directory as an artifact named `build-artifacts` with a retention period of 7 days.
- **Upload Deployment Logs**: This step uses the `actions/upload-artifact` action to upload the contents of the `logs/` directory as an artifact named `deployment-logs` with a retention period of 14 days.

### 4. Practical Use Cases for `upload-artifact`

#### Sharing Build Outputs

You can use `upload-artifact` to share build outputs between different jobs or workflows. For example, you might build your project in one job and then run integration tests on the built output in another job.

#### Storing Logs and Reports

Uploading logs and reports as artifacts makes it easy to review them later, especially when debugging failing workflows. You can upload test reports, coverage reports, and any other logs generated during your workflow.

### 5. Advanced Usage

You can customize the `upload-artifact` action to handle various scenarios:

- **Conditional Uploads**: Use `if` conditions to upload artifacts only when certain conditions are met, such as when tests fail.
- **Retention**: Specify how long the artifacts should be retained.

### Example: Conditional Uploads and Retention

```yaml
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: npm run build

      - name: Run tests
        run: npm test

      - name: Upload build artifacts
        if: always()
        uses: actions/upload-artifact@v3
        with:
          name: build-artifacts
          path: |
            build/
            dist/
          retention-days: 7

      - name: Upload test reports
        if: failure()
        uses: actions/upload-artifact@v3
        with:
          name: test-reports
          path: |
            test-reports/
            logs/test-*.log
          retention-days: 30

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
          switches: '-avzr --exclude="*.env" --exclude="env" --exclude=".github" --exclude=".git" --exclude=".gitignore" --exclude=".user.ini"' # Rsync switches for deployment (default)
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}       # Slack webhook URL for notifications
```

### Explanation

- **Conditional Uploads**:
  - **Build Artifacts**: Upload build artifacts regardless of the workflow result using `if: always()`.
  - **Test Reports**: Upload test reports only if the tests fail using `if: failure()`.
- **Retention**:
  - **Build Artifacts**: Retain for 7 days.
  - **Test Reports**: Retain for 30 days.


The `upload-artifact` action is a powerful tool for managing and sharing artifacts in your GitHub Actions workflows. By understanding its usage and capabilities, you can enhance your CI/CD processes, improve debugging, and streamline collaboration.

For more details, refer to the [official GitHub documentation on the `upload-artifact` action](https://github.com/actions/upload-artifact).
