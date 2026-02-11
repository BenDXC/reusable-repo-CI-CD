# Contributing

Thank you for considering contributing to this project! Here's how you can help.

## How to Contribute

### Reporting Issues

- Use the [bug report template](.github/ISSUE_TEMPLATE/bug_report.yml) for bugs
- Use the [feature request template](.github/ISSUE_TEMPLATE/feature_request.yml) for new ideas
- Check existing issues before creating a new one

### Making Changes

1. **Fork** the repository
2. **Create** a feature branch from `main`:
   ```bash
   git checkout -b feat/your-feature-name
   ```
3. **Make** your changes following the guidelines below
4. **Test** your workflows using [act](https://github.com/nektos/act) or a test repository
5. **Commit** using [conventional commits](https://www.conventionalcommits.org/):
   ```
   feat: add Ruby CI workflow
   fix(python): handle missing pyproject.toml gracefully
   docs: update Node.js workflow inputs table
   ```
6. **Push** and open a Pull Request

## Workflow Authoring Guidelines

When creating or modifying reusable workflows, follow these conventions:

### File Naming

- CI workflows: `reusable-ci-<language>.yml`
- CD workflows: `reusable-cd-<target>.yml`
- Shared workflows: `reusable-<purpose>.yml`

### Structure

Every reusable workflow should include:

1. **Header comment** with description, usage example
2. **`workflow_call` trigger** with typed inputs and secrets
3. **Sensible defaults** for all inputs
4. **Permissions** block (principle of least privilege)
5. **Matrix support** for version testing
6. **Caching** for the relevant package manager
7. **Conditional steps** gated by boolean inputs

### Action Versions

- Always use the latest major version (e.g., `actions/checkout@v4`)
- Never use `@master` or `@main` for third-party actions
- Pin third-party actions to specific versions or SHA

### Testing

- Test workflows in a separate repository before submitting
- Verify all inputs work as documented
- Test with both default and custom values
- Ensure workflows fail gracefully when optional tools are missing

## Code of Conduct

Be respectful, constructive, and inclusive. We're all here to build better CI/CD pipelines.
