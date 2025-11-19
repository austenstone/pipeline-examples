# Jenkins Pipeline to GitHub Actions Migration Guide

This guide provides side-by-side comparisons and migration patterns for converting Jenkins Pipeline syntax to GitHub Actions workflows.

## Table of Contents

- [Core Concepts](#core-concepts)
- [Syntax Comparison](#syntax-comparison)
- [Common Patterns](#common-patterns)
- [Build Tools](#build-tools)
- [Advanced Patterns](#advanced-patterns)
- [Best Practices](#best-practices)

## Core Concepts

### Pipeline Structure

| Jenkins Pipeline | GitHub Actions | Notes |
|-----------------|----------------|-------|
| `Jenkinsfile` | `.github/workflows/*.yml` | Multiple workflow files supported |
| `pipeline { }` | `jobs:` | YAML vs Groovy syntax |
| `agent any` | `runs-on: ubuntu-latest` | Specify runner OS explicitly |
| `stages { }` | `jobs:` | Different mental model |
| `stage('name')` | `job-id:` | Jobs run in parallel by default |
| `steps { }` | `steps:` | Similar concept |

**Jenkins Example:**
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Building..."'
            }
        }
    }
}
```

**GitHub Actions Example:**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Build
        run: echo "Building..."
```

### Checkout Code

| Jenkins | GitHub Actions |
|---------|----------------|
| `checkout scm` | `uses: actions/checkout@v4` |

**Jenkins:**
```groovy
stage('Checkout') {
    steps {
        checkout scm
    }
}
```

**GitHub Actions:**
```yaml
- name: Checkout code
  uses: actions/checkout@v4
```

## Syntax Comparison

### Environment Variables

**Jenkins:**
```groovy
environment {
    FOO = "PIPELINE"
    BAR = credentials("my-creds")
}
```

**GitHub Actions:**
```yaml
env:
  FOO: PIPELINE
  BAR: ${{ secrets.MY_CREDS }}
```

**Workflow:** [environment-variables.yml](.github/workflows/environment-variables.yml)

### Conditional Execution

**Jenkins:**
```groovy
when {
    branch "master"
}
```

**GitHub Actions:**
```yaml
if: github.ref == 'refs/heads/master'
```

**Workflow:** [conditional-execution.yml](.github/workflows/conditional-execution.yml)

### Credentials

**Jenkins:**
```groovy
environment {
    CREDS = credentials("my-credentials")
}
steps {
    sh 'echo $CREDS_USR'
    sh 'echo $CREDS_PSW'
}
```

**GitHub Actions:**
```yaml
env:
  CREDS_USR: ${{ secrets.MY_CREDENTIALS_USR }}
  CREDS_PSW: ${{ secrets.MY_CREDENTIALS_PSW }}
steps:
  - run: echo $CREDS_USR
  - run: echo $CREDS_PSW
```

**Workflow:** [credentials-username-password.yml](.github/workflows/credentials-username-password.yml)

## Common Patterns

### Console Output Formatting

**Jenkins (with AnsiColor plugin):**
```groovy
ansiColor('xterm') {
    sh 'echo "\u001B[31mRed Text\u001B[0m"'
}
```

**GitHub Actions (native support):**
```yaml
- run: echo -e "\033[31mRed Text\033[0m"
```

**Workflow:** [console-output-formatting.yml](.github/workflows/console-output-formatting.yml)

### Archiving Artifacts

**Jenkins:**
```groovy
archiveArtifacts artifacts: 'output/*.txt', excludes: 'output/*.md'
```

**GitHub Actions:**
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: |
      output/*.txt
      !output/*.md
```

**Workflow:** [archive-artifacts.yml](.github/workflows/archive-artifacts.yml)

### Docker Builds

**Jenkins:**
```groovy
agent {
    dockerfile {
        args "-v /tmp:/tmp"
    }
}
```

**GitHub Actions:**
```yaml
container:
  image: Dockerfile
  options: -v /tmp:/tmp

# Or build explicitly:
- run: docker build -t myapp .
- run: docker run myapp
```

**Workflow:** [docker-build.yml](.github/workflows/docker-build.yml)

### Post Conditions

**Jenkins:**
```groovy
post {
    always {
        echo "Always runs"
    }
    success {
        echo "On success"
    }
    failure {
        echo "On failure"
    }
}
```

**GitHub Actions:**
```yaml
- name: Always runs
  if: always()
  run: echo "Always runs"

- name: On success
  if: success()
  run: echo "On success"

- name: On failure
  if: failure()
  run: echo "On failure"
```

**Workflow:** [post-conditions.yml](.github/workflows/post-conditions.yml)

### Stash/Unstash

**Jenkins:**
```groovy
stash name: "build-artifacts", includes: "output/*"

// Later, on different node:
unstash "build-artifacts"
```

**GitHub Actions:**
```yaml
# Upload
- uses: actions/upload-artifact@v4
  with:
    name: build-artifacts
    path: output/*

# Download (in different job)
- uses: actions/download-artifact@v4
  with:
    name: build-artifacts
    path: output/
```

**Workflow:** [stash-unstash.yml](.github/workflows/stash-unstash.yml)

## Build Tools

### Maven

**Jenkins:**
```groovy
agent {
    docker {
        image 'maven:3.9-eclipse-temurin-17'
    }
}
steps {
    sh 'mvn clean package'
}
```

**GitHub Actions:**
```yaml
- uses: actions/setup-java@v4
  with:
    java-version: '17'
    distribution: 'temurin'
    cache: 'maven'
- run: mvn clean package
```

**Workflow:** [maven-docker.yml](.github/workflows/maven-docker.yml)

### Node.js

**Jenkins:**
```groovy
node('node') {
    sh 'npm install'
    sh 'npm test'
}
```

**GitHub Actions:**
```yaml
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'
- run: npm install
- run: npm test
```

**Workflow:** [nodejs-build-test-deploy.yml](.github/workflows/nodejs-build-test-deploy.yml)

### MSBuild

**Jenkins:**
```groovy
bat 'nuget restore SolutionName.sln'
bat "\"${tool 'MSBuild'}\" SolutionName.sln /p:Configuration=Release"
```

**GitHub Actions:**
```yaml
- uses: microsoft/setup-msbuild@v2
- uses: nuget/setup-nuget@v2
- run: nuget restore SolutionName.sln
- run: msbuild SolutionName.sln /p:Configuration=Release
```

**Workflow:** [msbuild-windows.yml](.github/workflows/msbuild-windows.yml)

## Advanced Patterns

### Parallel Execution

**Jenkins:**
```groovy
parallel {
    stage('Test 1') {
        steps { sh 'test1' }
    }
    stage('Test 2') {
        steps { sh 'test2' }
    }
}
```

**GitHub Actions:**
```yaml
# Using matrix strategy
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        test: [test1, test2]
    steps:
      - run: ./${{ matrix.test }}

# Or separate jobs (run in parallel by default)
jobs:
  test1:
    runs-on: ubuntu-latest
    steps:
      - run: test1
  test2:
    runs-on: ubuntu-latest
    steps:
      - run: test2
```

**Workflow:** [parallel-execution.yml](.github/workflows/parallel-execution.yml)

### Parameters

**Jenkins:**
```groovy
parameters {
    booleanParam(name: 'DEPLOY', defaultValue: false)
    choice(name: 'ENV', choices: ['dev', 'prod'])
}
```

**GitHub Actions:**
```yaml
on:
  workflow_dispatch:
    inputs:
      deploy:
        type: boolean
        default: false
      environment:
        type: choice
        options:
          - dev
          - prod
```

**Workflow:** [workflow-parameters.yml](.github/workflows/workflow-parameters.yml)

### Notifications

**Jenkins (Slack plugin):**
```groovy
post {
    failure {
        slackSend(
            channel: '#builds',
            message: "Build failed: ${env.BUILD_URL}"
        )
    }
}
```

**GitHub Actions:**
```yaml
- name: Slack notification
  if: failure()
  uses: slackapi/slack-github-action@v2
  with:
    webhook: ${{ secrets.SLACK_WEBHOOK_URL }}
    payload: |
      {
        "text": "Build failed: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"
      }
```

**Workflow:** [slack-notifications.yml](.github/workflows/slack-notifications.yml)

## Best Practices

### GitHub Actions Advantages

1. **Native YAML syntax** - More readable and maintainable than Groovy
2. **Built-in caching** - Actions like `setup-node` include cache support
3. **Matrix builds** - Easy parallel execution across multiple configurations
4. **Marketplace** - Thousands of pre-built actions
5. **No plugin dependencies** - Core functionality is built-in

### Migration Tips

1. **Start with simple workflows** - Migrate basic pipelines first
2. **Use workflow_dispatch** - Test manually before automating triggers
3. **Leverage actions** - Don't reinvent the wheel, use marketplace actions
4. **Secret management** - Set up secrets before testing workflows
5. **Test incrementally** - Commit and test each workflow individually

### Common Gotchas

| Issue | Jenkins | GitHub Actions | Solution |
|-------|---------|----------------|----------|
| **Sequential vs Parallel** | Stages run sequentially by default | Jobs run in parallel by default | Use `needs:` to create dependencies |
| **Workspace persistence** | Workspace persists across stages | Each job has fresh workspace | Use `actions/upload-artifact` and `actions/download-artifact` |
| **Environment variables** | Available across entire pipeline | Job-scoped by default | Set at workflow level with `env:` |
| **Modulo operator** | `%` works in expressions | Not supported in `if:` | Use workarounds like `contains()` with arrays |
| **Secret checking** | `credentials()` checks existence | Secrets always exist in context | Check secret values in steps, not conditions |

### Performance Optimization

1. **Use caching** - Cache dependencies to speed up builds
   ```yaml
   - uses: actions/cache@v4
     with:
       path: ~/.m2/repository
       key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
   ```

2. **Limit artifact retention** - Save storage with shorter retention periods
   ```yaml
   retention-days: 7
   ```

3. **Use matrix builds efficiently** - Set `fail-fast: false` to run all combinations
   ```yaml
   strategy:
     matrix:
       os: [ubuntu-latest, windows-latest]
     fail-fast: false
   ```

4. **Optimize Docker builds** - Use Docker layer caching
   ```yaml
   - uses: docker/build-push-action@v5
     with:
       cache-from: type=gha
       cache-to: type=gha,mode=max
   ```

## Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
- [Migrating from Jenkins to GitHub Actions](https://docs.github.com/en/actions/migrating-to-github-actions/migrating-from-jenkins-to-github-actions)
- [SECRETS.md](./SECRETS.md) - Secret configuration guide for this repository

## Workflow Index

All migrated workflows are in [`.github/workflows/`](.github/workflows/):

### Foundation (Phase 1)
- [console-output-formatting.yml](.github/workflows/console-output-formatting.yml) - ANSI colors
- [archive-artifacts.yml](.github/workflows/archive-artifacts.yml) - Artifact handling
- [environment-variables.yml](.github/workflows/environment-variables.yml) - Environment scoping
- [conditional-execution.yml](.github/workflows/conditional-execution.yml) - Branch conditions
- [credentials-username-password.yml](.github/workflows/credentials-username-password.yml) - Secrets
- [docker-build.yml](.github/workflows/docker-build.yml) - Docker integration

### Build Tools (Phase 2)
- [maven-docker.yml](.github/workflows/maven-docker.yml) - Maven with quality analysis
- [nodejs-build-test-deploy.yml](.github/workflows/nodejs-build-test-deploy.yml) - Complete Node.js workflow
- [msbuild-windows.yml](.github/workflows/msbuild-windows.yml) - Windows .NET builds
- [parallel-execution.yml](.github/workflows/parallel-execution.yml) - Matrix strategies

### Advanced Patterns (Phase 3)
- [post-conditions.yml](.github/workflows/post-conditions.yml) - Status handling
- [stash-unstash.yml](.github/workflows/stash-unstash.yml) - Artifact sharing
- [workflow-parameters.yml](.github/workflows/workflow-parameters.yml) - Input parameters
- [slack-notifications.yml](.github/workflows/slack-notifications.yml) - Notifications
