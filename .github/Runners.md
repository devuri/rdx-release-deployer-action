## Setting Up and Using Self-Hosted Runners

Self-hosted runners provide flexibility and cost savings for running GitHub Actions workflows on your own infrastructure. This tutorial covers the setup, usage, and security considerations for self-hosted runners.

### 1. Introduction to Self-Hosted Runners

Self-hosted runners allow you to run GitHub Actions workflows on your own machines, giving you full control over the hardware and software configurations. This can be beneficial for:
- Reducing costs associated with GitHub-hosted runners.
- Utilizing specific hardware or software requirements.
- Running workflows in a secure, private environment.

### 2. Setting Up a Self-Hosted Runner

#### Prerequisites
- A machine running a supported operating system (Linux, macOS, or Windows).
- Admin access to the GitHub repository, organization, or enterprise where you want to add the runner.

#### Steps to Set Up a Self-Hosted Runner

1. **Navigate to Settings**:
   - Go to your GitHub repository or organization.
   - Click on "Settings".

2. **Add a New Runner**:
   - In the left sidebar, click on "Actions" and then "Runners".
   - Click on "New self-hosted runner".
   - Select the operating system of your machine.

3. **Download and Configure the Runner**:
   - Follow the instructions provided to download the runner application. The commands will look something like this for a Linux machine:
     ```bash
     mkdir actions-runner && cd actions-runner
     curl -o actions-runner-linux-x64-2.285.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.285.0/actions-runner-linux-x64-2.285.0.tar.gz
     tar xzf ./actions-runner-linux-x64-2.285.0.tar.gz
     ```
   - Configure the runner with the provided token and URL:
     ```bash
     ./config.sh --url https://github.com/owner/repo --token YOUR_TOKEN
     ```

4. **Install and Start the Runner**:
   - Install and start the service:
     ```bash
     sudo ./svc.sh install
     sudo ./svc.sh start
     ```

### 3. Using Self-Hosted Runners in Workflows

Once your self-hosted runner is set up, you can specify it in your workflow files.

```yaml
name: 🚀 Self-Hosted Runner Workflow
on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: self-hosted
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Run a script
        run: echo "Hello from self-hosted runner"
```

### 4. Security Considerations

#### Access Control

- **Limit Permissions**: Ensure that only necessary users have admin access to add or manage self-hosted runners.
- **Restrict Runner Scope**: You can add runners at the repository, organization, or enterprise level. Restrict runners to specific repositories if possible to limit exposure.

#### Network Security

- **Firewall Configuration**: Restrict network access to the runner machine. Only allow connections from necessary IP addresses and GitHub's IP ranges.
- **VPN**: Consider using a VPN to provide secure access to the runner machine.

#### Machine Security

- **Isolation**: Use virtual machines or containers to isolate runners from other services on the same host.
- **Regular Updates**: Keep the operating system and software on the runner machine up to date with security patches.

#### Runner Configuration

- **Environment Variables**: Avoid storing sensitive information directly in the runner environment. Use GitHub secrets to manage sensitive data.
- **Audit Logs**: Enable logging and monitoring on the runner machine to keep track of activity and detect any anomalies.

### 5. Managing Self-Hosted Runners

#### Monitoring and Maintenance

- **Runner Status**: Regularly check the status of your self-hosted runners in the GitHub Actions settings.
- **Logs**: Review runner logs for any errors or issues.
- **Updates**: Periodically update the runner software to the latest version to benefit from improvements and security fixes.

#### Scaling

- **Auto-Scaling**: Consider using auto-scaling groups or Kubernetes to manage multiple runner instances based on workload demand.



Self-hosted runners provide a flexible and cost-effective solution for running GitHub Actions workflows. By following best practices for setup, usage, and security, you can effectively integrate self-hosted runners into your CI/CD pipeline while maintaining control over your infrastructure.

For more details, refer to the [official GitHub documentation on self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners).
