# GitHub Actions Secrets Configuration

This document lists all secrets required for the GitHub Actions workflows in this repository.

## Setting Secrets

You can set secrets using the GitHub CLI:

```bash
gh secret set SECRET_NAME --body "secret-value"
```

Or via the GitHub web interface:
1. Go to your repository
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Enter the name and value

## Required Secrets by Workflow

### 🔐 credentials-username-password.yml

These secrets demonstrate basic username/password credential handling:

- `FOO_CREDENTIALS_USR` - Example username for testing credential masking
  ```bash
  gh secret set FOO_CREDENTIALS_USR --body "testuser"
  ```

- `FOO_CREDENTIALS_PSW` - Example password for testing credential masking
  ```bash
  gh secret set FOO_CREDENTIALS_PSW --body "testpass123"
  ```

**Note**: These are demonstration values. The workflow will automatically mask them in logs.

### 🐳 Docker Hub (for future workflows)

- `DOCKER_USERNAME` - Your Docker Hub username
  ```bash
  gh secret set DOCKER_USERNAME --body "yourusername"
  ```

- `DOCKER_TOKEN` - Docker Hub access token (create at https://hub.docker.com/settings/security)
  ```bash
  gh secret set DOCKER_TOKEN --body "your-token-here"
  ```

### 📦 Artifactory (for Maven/Gradle workflows)

- `ARTIFACTORY_URL` - Your Artifactory server URL
  ```bash
  gh secret set ARTIFACTORY_URL --body "https://artifactory.example.com"
  ```

- `ARTIFACTORY_USER` - Artifactory username
  ```bash
  gh secret set ARTIFACTORY_USER --body "your-username"
  ```

- `ARTIFACTORY_PASSWORD` - Artifactory password or API key
  ```bash
  gh secret set ARTIFACTORY_PASSWORD --body "your-password-or-api-key"
  ```

### 📊 SonarQube (for code analysis workflows)

- `SONAR_TOKEN` - SonarQube authentication token
  ```bash
  gh secret set SONAR_TOKEN --body "your-sonar-token"
  ```

- `SONAR_HOST_URL` - SonarQube server URL
  ```bash
  gh secret set SONAR_HOST_URL --body "https://sonarqube.example.com"
  ```

### 💬 Slack (for notification workflows)

- `SLACK_WEBHOOK_URL` - Slack incoming webhook URL
  ```bash
  gh secret set SLACK_WEBHOOK_URL --body "https://hooks.slack.com/services/YOUR/WEBHOOK/URL"
  ```

## Testing Workflows Without Real Secrets

If you don't have access to actual services (Artifactory, SonarQube, etc.), you can:

1. **Use mock values**: Set secrets with dummy values - workflows will show the masking behavior
2. **Skip secret-dependent workflows**: Run only the workflows that don't require external services
3. **Use mock servers**: Set up local mock services for testing

## Viewing Masked Secrets in Logs

When you run workflows, GitHub Actions automatically masks any secret values in the logs. They will appear as `***` in the output.

Example:
```bash
echo "Password is $FOO_CREDENTIALS_PSW"
# Appears in logs as: Password is ***
```

## Security Best Practices

- ✅ Never commit secrets to your repository
- ✅ Use short retention times for artifacts containing sensitive data
- ✅ Rotate secrets regularly
- ✅ Use environment-specific secrets (dev, staging, production)
- ✅ Grant minimal necessary permissions to tokens

## Checking Which Secrets Are Set

List all secrets (names only, not values):
```bash
gh secret list
```

## Removing Secrets

```bash
gh secret remove SECRET_NAME
```
