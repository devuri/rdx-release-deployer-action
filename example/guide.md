# Complete Guide to Using the Release Deployer Action

## Introduction

The Release Deployer Action is a GitHub Actions workflow designed to automate your entire release and deployment pipeline. It combines release management with automated deployment to remote servers, making it ideal for web applications that need consistent, reliable deployments.

Unlike traditional CI/CD setups that build everything on GitHub's servers, this action is optimized for efficiency by default—it deploys your source code to your production server and handles dependency installation and building there. This approach saves GitHub Actions minutes while ensuring your application builds in its actual target environment.

## What You'll Learn

By the end of this tutorial, you'll know how to:
- Set up automated releases with deployment
- Configure the action for different deployment scenarios
- Implement staging and production workflows
- Troubleshoot common issues
- Optimize your deployment process

## Prerequisites

Before starting, ensure you have:

1. **A GitHub repository** with your web application
2. **A remote server** with SSH access where you want to deploy
3. **Basic knowledge** of GitHub Actions workflows
4. **Server requirements** (if using default server-side builds):
   - SSH access
   - Git installed
   - Composer (for PHP projects)
   - Node.js and npm (for JavaScript projects)

## Part 1: Initial Setup

### Step 1: SSH Key Setup

You have two options for SSH keys:

#### Option A: Generate New SSH Keys (Recommended)

Create dedicated SSH keys for deployment:

```bash
# Generate a new SSH key pair specifically for deployments
ssh-keygen -t rsa -b 4096 -C "github-actions@yourdomain.com" -f ~/.ssh/github_deploy_key

# This creates two files:
# - github_deploy_key (private key - for GitHub secrets)
# - github_deploy_key.pub (public key - for your server)
```

#### Option B: Use Existing SSH Keys

If you already have SSH keys you want to use:

```bash
# List your existing keys
ls -la ~/.ssh/

# Typical key files:
# - id_rsa / id_rsa.pub (RSA keys)
# - id_ed25519 / id_ed25519.pub (Ed25519 keys)
# - Custom named keys

# Use your existing private key content for GitHub secrets
# Use your existing public key for server authorization
```

**Security Note**: Using dedicated deployment keys (Option A) is more secure as it allows you to:
- Rotate keys independently
- Limit key usage to specific servers
- Revoke deployment access without affecting other services

### Step 2: Configure Server Access

Add the public key to your server:

```bash
# On your server, add the public key to authorized_keys
cat id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Step 3: Set Up GitHub Secrets

In your GitHub repository, go to Settings > Secrets and variables > Actions, then add these secrets:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `SITE_URL` | Your website's URL | `example.com` |
| `DEPLOY_PATH` | Server path for deployment | `/var/www/html` |
| `DEPLOY_HOST` | Server hostname or IP | `192.168.1.100` |
| `DEPLOY_PORT` | SSH port | `22` |
| `DEPLOY_USER` | SSH username | `www-data` |
| `DEPLOY_KEY` | Private SSH key content | Contents of `id_rsa` file |
| `SLACK_WEBHOOK` | Slack webhook URL (optional) | `https://hooks.slack.com/...` |

## Part 2: Basic Implementation

### Step 4: Create Your First Workflow

Create `.github/workflows/deploy.yml` in your repository:

```yaml
name: Release and Deploy
on:
  pull_request:
    types:
      - closed
  workflow_dispatch:

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Generate Release
        uses: googleapis/release-please-action@v4
        id: release
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          command: manifest
          default-branch: main

      - name: Deploy to Production
        if: ${{ steps.release.outputs.releases_created }}
        uses: devuri/rdx-release-deployer-action@v1
        with:
          site-url: ${{ secrets.SITE_URL }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          deploy-path: ${{ secrets.DEPLOY_PATH }}
          deploy-host: ${{ secrets.DEPLOY_HOST }}
          deploy-port: ${{ secrets.DEPLOY_PORT }}
          deploy-user: ${{ secrets.DEPLOY_USER }}
          deploy-key: ${{ secrets.DEPLOY_KEY }}
          tag-name: ${{ steps.release.outputs.tag_name }}
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}
```

### Step 5: Configure Release Please

Create `release-please-config.json` in your repository root:

```json
{
  "packages": {
    ".": {
      "release-type": "simple",
      "include-component-in-tag": false
    }
  }
}
```

Create `.release-please-manifest.json`:

```json
{
  ".": "1.0.0"
}
```

## Part 3: Understanding How It Works

### Default Behavior (Server-Side Builds)

When you use the default configuration:

1. **Source Deployment**: Your source code is deployed to the server using rsync
2. **Remote Installation**: The action automatically detects and installs dependencies:
   - If `composer.json` exists: runs `composer install --no-dev --optimize-autoloader`
   - If `package.json` exists: runs `npm ci` and `npm run build`
3. **Asset Upload**: Build artifacts are uploaded to your GitHub release
4. **Notifications**: Slack notifications keep your team informed

### Alternative: GitHub Actions Builds

If you prefer to build on GitHub's servers, modify your workflow:

```yaml
- name: Deploy with GitHub Actions Build
  uses: devuri/rdx-release-deployer-action@v1
  with:
    # ... other parameters ...
    use-php: true          # Enable PHP setup on GitHub Actions
    use-node: true         # Enable Node.js setup on GitHub Actions
    use-remote-install: false  # Disable server-side installation
    path: build/           # Point to your build output directory
```

## Part 4: Advanced Configurations

### Staging and Production Workflow

For a more robust deployment pipeline:

```yaml
name: Staging and Production Deploy
on:
  pull_request:
    types:
      - closed
  workflow_dispatch:

jobs:
  release:
    runs-on: ubuntu-latest
    outputs:
      releases_created: ${{ steps.release.outputs.releases_created }}
      tag_name: ${{ steps.release.outputs.tag_name }}
    steps:
      - name: Generate Release
        uses: googleapis/release-please-action@v4
        id: release
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          command: manifest
          default-branch: main

  deploy-staging:
    needs: release
    runs-on: ubuntu-latest
    if: ${{ needs.release.outputs.releases_created }}
    steps:
      - name: Deploy to Staging
        uses: devuri/rdx-release-deployer-action@v1
        with:
          site-url: ${{ secrets.STAGING_SITE_URL }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          deploy-path: ${{ secrets.STAGING_DEPLOY_PATH }}
          deploy-host: ${{ secrets.STAGING_DEPLOY_HOST }}
          deploy-port: ${{ secrets.STAGING_DEPLOY_PORT }}
          deploy-user: ${{ secrets.STAGING_DEPLOY_USER }}
          deploy-key: ${{ secrets.STAGING_DEPLOY_KEY }}
          tag-name: ${{ needs.release.outputs.tag_name }}
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Production
        uses: devuri/rdx-release-deployer-action@v1
        with:
          site-url: ${{ secrets.PRODUCTION_SITE_URL }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          deploy-path: ${{ secrets.PRODUCTION_DEPLOY_PATH }}
          deploy-host: ${{ secrets.PRODUCTION_DEPLOY_HOST }}
          deploy-port: ${{ secrets.PRODUCTION_DEPLOY_PORT }}
          deploy-user: ${{ secrets.PRODUCTION_DEPLOY_USER }}
          deploy-key: ${{ secrets.PRODUCTION_DEPLOY_KEY }}
          tag-name: ${{ needs.release.outputs.tag_name }}
          slack-webhook: ${{ secrets.SLACK_WEBHOOK }}
```

### Custom Rsync Configuration

Control exactly what gets deployed using the `switches` parameter:

```yaml
- name: Deploy with Custom Rsync
  uses: devuri/rdx-release-deployer-action@v1
  with:
    # ... other parameters ...
    switches: >-
      -avzr 
      --exclude="*.env" 
      --exclude=".git*" 
      --exclude="node_modules" 
      --exclude="tests/" 
      --delete
```

**Warning**: The `--delete` option removes files on the server that don't exist locally. Use with caution.

### PHP and Node.js Specific Configurations

For PHP projects:

```yaml
- name: Deploy PHP Application
  uses: devuri/rdx-release-deployer-action@v1
  with:
    # ... other parameters ...
    use-php: true
    php-version: '8.2'
    php-extensions: 'mysqli,pdo_mysql,gd'
    use-cache: true
```

For Node.js projects:

```yaml
- name: Deploy Node.js Application
  uses: devuri/rdx-release-deployer-action@v1
  with:
    # ... other parameters ...
    use-node: true
    node-version: '20'
    use-cache: true
```

## Part 5: Troubleshooting

### Common Issues and Solutions

**Issue**: SSH connection fails
```
Solution: 
- Verify SSH key is correctly added to server
- Check server firewall allows connection on specified port
- Ensure deploy user has proper permissions
```

**Issue**: Remote installation fails
```
Solution:
- Check if Composer/npm is installed on server
- Verify server has sufficient disk space
- Review server PHP/Node.js versions compatibility
```

**Issue**: Rsync permissions error
```
Solution:
- Ensure deploy user owns the target directory
- Check directory permissions (usually 755 for directories, 644 for files)
- Verify SELinux/AppArmor policies if applicable
```

**Issue**: Build fails on server
```
Solution:
- Check server logs for specific error messages
- Verify all dependencies are available on server
- Ensure proper environment variables are set
```

### Debugging Tips

1. **Enable verbose logging** by adding `--verbose` to rsync switches
2. **Check server logs** after deployment failures
3. **Test SSH connection manually** before running workflow
4. **Validate secrets** are correctly configured in GitHub

## Part 6: Best Practices

### Security

1. **Use dedicated deploy user** with minimal required permissions
2. **Restrict SSH key usage** to specific commands if possible
3. **Regularly rotate SSH keys** and update secrets
4. **Use environment-specific secrets** for staging vs production

### Performance

1. **Use server-side builds** (default) to save GitHub Actions minutes
2. **Enable caching** for dependencies when using GitHub Actions builds
3. **Optimize rsync excludes** to avoid transferring unnecessary files
4. **Consider using `--compress`** for slow network connections

### Workflow Organization

1. **Separate staging and production** into different jobs
2. **Use descriptive job and step names** for clarity
3. **Implement proper conditional logic** for different environments
4. **Add manual approval steps** for production deployments if needed

### Monitoring

1. **Set up Slack notifications** to track deployment status
2. **Monitor server resources** during and after deployments
3. **Implement health checks** after deployment
4. **Keep deployment logs** for troubleshooting

## Part 7: Advanced Use Cases

### Multiple Environment Deployment

```yaml
strategy:
  matrix:
    environment: [staging, production]
    include:
      - environment: staging
        site_url: ${{ secrets.STAGING_SITE_URL }}
        deploy_host: ${{ secrets.STAGING_DEPLOY_HOST }}
      - environment: production
        site_url: ${{ secrets.PRODUCTION_SITE_URL }}
        deploy_host: ${{ secrets.PRODUCTION_DEPLOY_HOST }}
```

### Conditional Deployments Based on Changes

```yaml
- name: Check for changes
  uses: dorny/paths-filter@v2
  id: changes
  with:
    filters: |
      backend:
        - 'src/**'
        - 'composer.json'
      frontend:
        - 'assets/**'
        - 'package.json'

- name: Deploy Backend
  if: steps.changes.outputs.backend == 'true'
  uses: devuri/rdx-release-deployer-action@v1
  # ... backend deployment config

- name: Deploy Frontend
  if: steps.changes.outputs.frontend == 'true'
  uses: devuri/rdx-release-deployer-action@v1
  # ... frontend deployment config
```

### Database Migration Integration

```yaml
- name: Deploy with Database Migration
  uses: devuri/rdx-release-deployer-action@v1
  with:
    # ... other parameters ...
    use-remote-update: true  # This can run additional update commands

# Add a custom step for migrations
- name: Run Database Migrations
  uses: appleboy/ssh-action@v0.1.10
  with:
    host: ${{ secrets.DEPLOY_HOST }}
    username: ${{ secrets.DEPLOY_USER }}
    key: ${{ secrets.DEPLOY_KEY }}
    port: ${{ secrets.DEPLOY_PORT }}
    script: |
      cd ${{ secrets.DEPLOY_PATH }}
      php artisan migrate --force
```



The Release Deployer Action provides a powerful, flexible solution for automating your deployment pipeline. By leveraging server-side builds by default, it offers cost-effective deployments while maintaining the option to use GitHub Actions runners when needed.

Key takeaways:
- Start with the basic configuration and gradually add complexity
- Use server-side builds to optimize costs and performance
- Implement proper staging workflows for reliable deployments
- Monitor and maintain your deployment pipeline regularly

With this foundation, you can build robust, automated deployment workflows that scale with your application's needs.
