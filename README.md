# Introduction

This repository is a home for snippets, tips and tricks and examples of scripting for the [Jenkins Pipeline plugin](https://github.com/jenkinsci/workflow-plugin/blob/master/README.md) **and GitHub Actions workflows**.

## 🚀 NEW: GitHub Actions Migration

This repository now includes **executable GitHub Actions workflows** that demonstrate the same CI/CD patterns as the Jenkins examples! All workflows are tested and can be triggered via `workflow_dispatch` for hands-on learning.

### Quick Start with GitHub Actions

1. **Browse workflows**: Check out [`.github/workflows/`](.github/workflows/) for 14+ working examples
2. **Run a workflow**: Use `gh workflow run <workflow-name>` or trigger manually via GitHub UI
3. **Learn migration**: See [MIGRATION_GUIDE.md](./MIGRATION_GUIDE.md) for side-by-side Jenkins/Actions comparisons
4. **Set up secrets**: Follow [SECRETS.md](./SECRETS.md) to configure required credentials

### Featured Workflows

- 🎨 **Console Output Formatting** - ANSI colors (no plugin needed!)
- 📦 **Archive Artifacts** - Upload/download patterns
- 🔐 **Credentials Management** - Secrets with automatic masking
- 🐳 **Docker Integration** - Build and run containers
- ⚡ **Parallel Execution** - Matrix strategies
- 🔨 **Build Tools** - Maven, Node.js, MSBuild examples
- 📊 **Notifications** - Slack integration patterns

# Layout

The repository is organized into multiple directories:

## Jenkins Pipeline Examples

* **pipeline-examples** - General Pipeline examples demonstrating various plugins and patterns
* **global-library-examples** - Examples of writing and using global libraries on Jenkins
* **jenkinsfile-examples** - Complete `Jenkinsfile` examples for real-world scenarios
* **declarative-examples** - Declarative Pipeline syntax examples
* **docs** - Documentation, guides, and best practices

## GitHub Actions Workflows

* **[.github/workflows/](.github/workflows/)** - Executable GitHub Actions workflows demonstrating equivalent CI/CD patterns
  - Foundation patterns (conditionals, variables, artifacts)
  - Build tool integrations (Maven, Node.js, MSBuild)
  - Advanced patterns (parallelization, notifications, parameters)
* **[MIGRATION_GUIDE.md](./MIGRATION_GUIDE.md)** - Side-by-side Jenkins Pipeline → GitHub Actions comparisons
* **[SECRETS.md](./SECRETS.md)** - Secret configuration guide for GitHub Actions

Please put your script into its own directory under the appropriate directory above, with a README.md file included explaining what your script does or shows. Make sure your script is commented so that others can understand how it works, why it works, etc.

# License

All contributions are under the MIT license, like Jenkins itself.

# Pull requests

We want them!

# External resources

## Jenkins Pipeline
* [Pipeline scripts collection of the Docker team](https://github.com/docker/jenkins-pipeline-scripts)
* [Pipeline scripts collection of the Fabric8 team](https://github.com/fabric8io/jenkins-pipeline-library)
* [Pipeline scripts collection of the Funkwerk](https://github.com/funkwerk/jenkins-workflow)

## GitHub Actions
* [GitHub Actions Documentation](https://docs.github.com/en/actions)
* [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
* [Migrating from Jenkins to GitHub Actions](https://docs.github.com/en/actions/migrating-to-github-actions/migrating-from-jenkins-to-github-actions)
