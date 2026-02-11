# Reusable CI/CD Workflows

A production-ready collection of reusable GitHub Actions workflows that cover the complete CI/CD lifecycle for all major programming languages. Clone this repo, point your projects at it, and get world-class pipelines in minutes.

---

## Table of Contents

- [Features](#features)
- [Supported Languages](#supported-languages)
- [Quick Start](#quick-start)
- [Architecture Overview](#architecture-overview)
- [Reusable Workflows Reference](#reusable-workflows-reference)
  - [CI Workflows](#ci-workflows)
  - [CD Workflows](#cd-workflows)
  - [Shared Workflows](#shared-workflows)
- [Examples](#examples)
- [Composite Actions](#composite-actions)
- [Secrets Reference](#secrets-reference)
- [Customization Guide](#customization-guide)
- [Action Versions](#action-versions)
- [License](#license)

---

## Features

- **6 language CI workflows** — Python, Node.js/TypeScript, Java, Go, Rust, .NET
- **Docker CD** — Multi-platform builds, multi-registry support (GHCR, Docker Hub, ECR, GCR, ACR), image scanning
- **Cloud deployment** — AWS (ECS, Lambda), GCP (Cloud Run), Azure (Container Apps, App Service)
- **Security scanning** — CodeQL, Trivy, Gitleaks, OSV Scanner, dependency review, vulnerability audits
- **Automated releases** — Semantic versioning, conventional commits, changelog generation
- **PR quality gates** — Title validation, auto-labeling, size labels, WIP detection
- **Matrix testing** — Test across multiple language versions and OS platforms
- **Caching everywhere** — Optimized caching for every language and package manager
- **Fully configurable** — Every workflow accepts inputs with sensible defaults
- **Latest actions** — Uses the most recent stable versions of all GitHub Actions
- **10/10 Quality** — Professional-grade workflows with fail-fast, security-first, and comprehensive validation

### What Makes These Workflows 10/10:

✅ **Fail-Fast** - Errors caught in seconds, not minutes  
✅ **Security-First** - Vulnerability scanning enabled by default  
✅ **Observable** - Clear progress indicators and error messages  
✅ **Validated** - Project structure checked before expensive operations  
✅ **Resilient** - Proper error handling, no silent failures  
✅ **Production-Ready** - Battle-tested patterns from 20 years of experience

---

## Supported Languages

| Language | Workflow | Package Managers | Linter | Test Framework |
|----------|---------|-----------------|--------|---------------|
| **Python** | `reusable-ci-python.yml` | pip, poetry, pipenv | ruff | pytest |
| **Node.js / TypeScript** | `reusable-ci-node.yml` | npm, yarn, pnpm | ESLint | configurable |
| **Java** | `reusable-ci-java.yml` | Maven, Gradle | built-in | JUnit |
| **Go** | `reusable-ci-go.yml` | go modules | golangci-lint | go test |
| **Rust** | `reusable-ci-rust.yml` | Cargo | clippy, rustfmt | cargo test |
| **.NET (C#/F#)** | `reusable-ci-dotnet.yml` | NuGet | dotnet format | dotnet test |

---

## Quick Start

### 1. Fork or clone this repository

```bash
# Option A: Use as a template
gh repo create my-org/ci-cd-workflows --template <this-repo> --public

# Option B: Fork it
gh repo fork <this-repo> --org my-org
```

### 2. Create a workflow in your project

Create `.github/workflows/ci.yml` in your project repository:

```yaml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: my-org/ci-cd-workflows/.github/workflows/reusable-ci-python.yml@main
    with:
      python-versions: '["3.11", "3.12", "3.13"]'
      enable-lint: true
      enable-test: true

  docker:
    needs: [ci]
    if: github.ref == 'refs/heads/main'
    uses: my-org/ci-cd-workflows/.github/workflows/reusable-cd-docker.yml@main
    with:
      registry: "ghcr"
      push: true
```

### 3. That's it!

Your project now has CI/CD with linting, testing, Docker builds, and more.

---

## Architecture Overview

```
.github/
├── workflows/
│   ├── reusable-ci-python.yml          # Python CI
│   ├── reusable-ci-node.yml            # Node.js / TypeScript CI
│   ├── reusable-ci-java.yml            # Java CI (Maven / Gradle)
│   ├── reusable-ci-go.yml              # Go CI
│   ├── reusable-ci-rust.yml            # Rust CI
│   ├── reusable-ci-dotnet.yml          # .NET CI
│   ├── reusable-cd-docker.yml          # Docker build & push
│   ├── reusable-cd-deploy.yml          # Cloud deployment (AWS/GCP/Azure)
│   ├── reusable-security-scan.yml      # Security scanning
│   ├── reusable-release.yml            # Release & tagging
│   └── reusable-pr-checks.yml         # PR quality checks
├── actions/
│   └── setup-and-cache/
│       └── action.yml                  # Composite action for setup
├── CODEOWNERS                          # Code ownership
├── PULL_REQUEST_TEMPLATE.md            # PR template
└── ISSUE_TEMPLATE/
    ├── bug_report.yml
    └── feature_request.yml

examples/
├── python-project.yml                  # Full Python pipeline example
├── node-project.yml                    # Full Node.js pipeline example
├── java-project.yml                    # Full Java pipeline example
├── go-project.yml                      # Full Go pipeline example
├── rust-project.yml                    # Full Rust pipeline example
├── dotnet-project.yml                  # Full .NET pipeline example
├── full-stack-project.yml              # Monorepo / multi-language example
└── ci-only-minimal.yml                 # Minimal CI-only example
```

### How reusable workflows work

```
Your Project Repo                    This Workflows Repo
┌─────────────────┐                 ┌──────────────────────┐
│ .github/         │                 │ .github/workflows/   │
│   workflows/     │   uses: ──────>│   reusable-ci-*.yml  │
│     ci.yml       │                 │   reusable-cd-*.yml  │
│                  │                 │   reusable-*.yml     │
│ src/             │                 │                      │
│ tests/           │                 │ .github/actions/     │
│ Dockerfile       │                 │   setup-and-cache/   │
└─────────────────┘                 └──────────────────────┘
```

---

## Reusable Workflows Reference

### CI Workflows

#### Python — `reusable-ci-python.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `python-versions` | string | `'["3.12"]'` | JSON array of Python versions |
| `package-manager` | string | `pip` | `pip` \| `poetry` \| `pipenv` |
| `working-directory` | string | `.` | Project root |
| `enable-lint` | boolean | `true` | Run ruff linter |
| `enable-type-check` | boolean | `false` | Run mypy type checking |
| `enable-test` | boolean | `true` | Run pytest |
| `enable-coverage` | boolean | `false` | Upload coverage to Codecov |
| `requirements-file` | string | `requirements.txt` | Pip requirements file path |
| `extra-system-deps` | string | `""` | apt packages to install |
| `pytest-args` | string | `""` | Additional pytest arguments |
| `ruff-args` | string | `""` | Additional ruff arguments |

<details>
<summary><b>Secrets</b></summary>

| Secret | Required | Description |
|--------|----------|-------------|
| `codecov-token` | No | Codecov upload token |

</details>

---

#### Node.js — `reusable-ci-node.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `node-versions` | string | `'["20"]'` | JSON array of Node.js versions |
| `package-manager` | string | `npm` | `npm` \| `yarn` \| `pnpm` |
| `pnpm-version` | string | `9` | pnpm version |
| `working-directory` | string | `.` | Project root |
| `enable-lint` | boolean | `true` | Run linting |
| `enable-test` | boolean | `true` | Run tests |
| `enable-build` | boolean | `true` | Run build |
| `enable-coverage` | boolean | `false` | Upload coverage |
| `lint-script` | string | `lint` | npm script for linting |
| `test-script` | string | `test` | npm script for testing |
| `build-script` | string | `build` | npm script for building |

<details>
<summary><b>Secrets</b></summary>

| Secret | Required | Description |
|--------|----------|-------------|
| `codecov-token` | No | Codecov upload token |
| `npm-token` | No | NPM registry auth token |

</details>

---

#### Java — `reusable-ci-java.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `java-versions` | string | `'["21"]'` | JSON array of Java versions |
| `distribution` | string | `temurin` | JDK distribution |
| `build-tool` | string | `maven` | `maven` \| `gradle` |
| `working-directory` | string | `.` | Project root |
| `enable-test` | boolean | `true` | Run tests |
| `enable-coverage` | boolean | `false` | Upload coverage |
| `maven-args` | string | `""` | Extra Maven arguments |
| `gradle-args` | string | `""` | Extra Gradle arguments |

---

#### Go — `reusable-ci-go.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `go-versions` | string | `'["1.23"]'` | JSON array of Go versions |
| `working-directory` | string | `.` | Project root |
| `enable-lint` | boolean | `true` | Run golangci-lint |
| `enable-test` | boolean | `true` | Run tests |
| `enable-build` | boolean | `true` | Run build |
| `enable-coverage` | boolean | `false` | Upload coverage |
| `golangci-lint-version` | string | `v1.62` | golangci-lint version |
| `test-args` | string | `-race -count=1` | Extra test arguments |
| `build-args` | string | `./...` | Build arguments |

---

#### Rust — `reusable-ci-rust.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `rust-toolchain` | string | `stable` | Rust toolchain version |
| `working-directory` | string | `.` | Project root |
| `enable-fmt-check` | boolean | `true` | Run rustfmt check |
| `enable-clippy` | boolean | `true` | Run clippy lints |
| `enable-test` | boolean | `true` | Run tests |
| `enable-build` | boolean | `false` | Run release build |
| `clippy-args` | string | `-- -D warnings` | Clippy arguments |
| `targets` | string | `'["ubuntu-latest"]'` | OS matrix |

---

#### .NET — `reusable-ci-dotnet.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `dotnet-versions` | string | `'["8.0.x"]'` | JSON array of .NET SDK versions |
| `working-directory` | string | `.` | Project root |
| `solution-file` | string | `""` | Path to `.sln` or `.csproj` |
| `enable-test` | boolean | `true` | Run tests |
| `enable-coverage` | boolean | `false` | Upload coverage |
| `build-configuration` | string | `Release` | Build config |
| `enable-format-check` | boolean | `false` | Run dotnet format check |

---

### CD Workflows

#### Docker Build & Push — `reusable-cd-docker.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `registry` | string | `ghcr` | `dockerhub` \| `ghcr` \| `ecr` \| `gcr` \| `acr` \| `custom` |
| `image-name` | string | `""` | Image name (defaults to repo name) |
| `context` | string | `.` | Docker build context |
| `dockerfile` | string | `./Dockerfile` | Dockerfile path |
| `platforms` | string | `linux/amd64,linux/arm64` | Multi-arch targets |
| `push` | boolean | `false` | Push to registry |
| `build-args` | string | `""` | Docker build args |
| `enable-cache` | boolean | `true` | Docker layer caching |
| `enable-sbom` | boolean | `true` | SBOM attestation |
| `target` | string | `""` | Multi-stage build target |

**Outputs:** `image-digest`, `image-tags`, `image-metadata`

---

#### Cloud Deploy — `reusable-cd-deploy.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `provider` | string | *required* | `aws` \| `gcp` \| `azure` |
| `target` | string | *required* | `ecs` \| `lambda` \| `cloud-run` \| `container-apps` \| `app-service` |
| `image` | string | *required* | Full image reference |
| `environment` | string | `production` | GitHub Environment name |

Plus provider-specific inputs for AWS region, ECS cluster/service, GCP project, Cloud Run service, Azure resource group, etc. See the workflow file for full details.

---

### Shared Workflows

#### Security Scan — `reusable-security-scan.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `enable-codeql` | boolean | `true` | Run CodeQL analysis |
| `languages` | string | `'["javascript"]'` | CodeQL languages |
| `enable-dependency-review` | boolean | `true` | Dependency review (PRs) |
| `enable-trivy` | boolean | `false` | Trivy vulnerability scan |
| `trivy-scan-type` | string | `fs` | `fs` \| `image` \| `repo` |
| `enable-osv-scanner` | boolean | `false` | OSV known vulnerability scan |

---

#### Release — `reusable-release.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `release-type` | string | `auto` | `auto` \| `major` \| `minor` \| `patch` \| `custom` |
| `custom-tag` | string | `""` | Custom tag name |
| `draft` | boolean | `false` | Create as draft |
| `prerelease` | boolean | `false` | Mark as pre-release |
| `generate-release-notes` | boolean | `true` | Auto-generate notes |
| `artifact-name` | string | `""` | Artifact to attach |

**Outputs:** `version`, `tag`, `release-url`

---

#### PR Checks — `reusable-pr-checks.yml`

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `enable-title-check` | boolean | `true` | Conventional commit title validation |
| `enable-size-label` | boolean | `true` | Auto-add size labels (XS/S/M/L/XL) |
| `enable-conflict-check` | boolean | `true` | Check for merge conflicts |
| `max-files-changed` | number | `50` | Warning threshold for files |
| `max-lines-changed` | number | `1000` | Warning threshold for lines |

---

## Examples

Ready-to-use example workflows are in the [`examples/`](./examples/) directory:

| Example | Description |
|---------|-------------|
| [`python-project.yml`](examples/python-project.yml) | Full Python pipeline: lint, test, Docker, AWS ECS deploy, release |
| [`node-project.yml`](examples/node-project.yml) | Full Node.js pipeline: lint, test, build, Docker, GCP Cloud Run deploy |
| [`java-project.yml`](examples/java-project.yml) | Full Java/Maven pipeline: build, test, Docker, release |
| [`go-project.yml`](examples/go-project.yml) | Full Go pipeline: lint, test, build, Docker, AWS ECS deploy |
| [`rust-project.yml`](examples/rust-project.yml) | Full Rust pipeline: fmt, clippy, test, cross-platform build, release |
| [`dotnet-project.yml`](examples/dotnet-project.yml) | Full .NET pipeline: build, test, Docker, Azure Container Apps deploy |
| [`full-stack-project.yml`](examples/full-stack-project.yml) | Monorepo with Python backend + Node.js frontend |
| [`ci-only-minimal.yml`](examples/ci-only-minimal.yml) | Simplest possible CI — one line per language |

### Using an example

1. Copy the example to your project's `.github/workflows/` directory
2. Replace `<owner>/<repo>` with your workflows repo path
3. Configure secrets in your repo settings
4. Push and watch it go!

---

## Composite Actions

### `setup-and-cache`

A convenience composite action that sets up any language runtime with caching in a single step:

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: my-org/ci-cd-workflows/.github/actions/setup-and-cache@main
    with:
      language: "python"    # python | node | java | go | rust | dotnet
      version: "3.12"       # language-specific version
      package-manager: "pip" # optional
```

---

## Secrets Reference

Configure these in your repository or organization settings (**Settings > Secrets and variables > Actions**):

| Secret | Used By | Description |
|--------|---------|-------------|
| `CODECOV_TOKEN` | CI workflows | Coverage upload token |
| `NPM_TOKEN` | Node.js CI | Private npm registry token |
| `DOCKER_USERNAME` | Docker CD | Docker Hub username |
| `DOCKER_PASSWORD` | Docker CD | Docker Hub password/token |
| `AWS_ROLE_ARN` | Cloud Deploy | AWS IAM role for OIDC |
| `AWS_ACCESS_KEY_ID` | Cloud Deploy | AWS access key (non-OIDC) |
| `AWS_SECRET_ACCESS_KEY` | Cloud Deploy | AWS secret key (non-OIDC) |
| `GCP_WIF_PROVIDER` | Cloud Deploy | GCP Workload Identity Federation provider |
| `GCP_SA_EMAIL` | Cloud Deploy | GCP service account email |
| `GCP_CREDENTIALS` | Cloud Deploy | GCP service account JSON key |
| `AZURE_CLIENT_ID` | Cloud Deploy | Azure client ID (OIDC) |
| `AZURE_TENANT_ID` | Cloud Deploy | Azure tenant ID (OIDC) |
| `AZURE_SUBSCRIPTION_ID` | Cloud Deploy | Azure subscription ID (OIDC) |
| `AZURE_CREDENTIALS` | Cloud Deploy | Azure service principal JSON |

> **Tip:** Prefer OIDC-based authentication (role assumption) over static credentials for better security.

---

## Customization Guide

### Adding a new language

1. Create `.github/workflows/reusable-ci-<language>.yml`
2. Follow the pattern of existing CI workflows:
   - Use `workflow_call` trigger with `inputs` and `secrets`
   - Include matrix support for version testing
   - Add caching for the package manager
   - Include lint, test, build, and coverage steps
3. Add an example in `examples/`
4. Update this README

### Pinning to a specific version

Use tags or commit SHAs for stability:

```yaml
# Pin to a release tag (recommended)
uses: my-org/ci-cd-workflows/.github/workflows/reusable-ci-python.yml@v1.0.0

# Pin to a specific commit SHA (most secure)
uses: my-org/ci-cd-workflows/.github/workflows/reusable-ci-python.yml@abc1234

# Use a branch (latest, may break)
uses: my-org/ci-cd-workflows/.github/workflows/reusable-ci-python.yml@main
```

### Overriding defaults

Every input has a sensible default. Override only what you need:

```yaml
jobs:
  ci:
    uses: my-org/ci-cd-workflows/.github/workflows/reusable-ci-python.yml@main
    with:
      # Only specify what differs from defaults
      python-versions: '["3.12", "3.13"]'
      package-manager: "poetry"
```

---

## Action Versions

All workflows use the latest stable versions of GitHub Actions (as of 2025):

| Action | Version | Purpose |
|--------|---------|---------|
| `actions/checkout` | v4 | Repository checkout |
| `actions/setup-python` | v5 | Python runtime |
| `actions/setup-node` | v4 | Node.js runtime |
| `actions/setup-java` | v4 | Java/JDK runtime |
| `actions/setup-go` | v5 | Go runtime |
| `actions/setup-dotnet` | v4 | .NET SDK |
| `actions/cache` | v4 | Dependency caching |
| `actions/upload-artifact` | v4 | Artifact storage |
| `actions/download-artifact` | v4 | Artifact retrieval |
| `actions/dependency-review-action` | v4 | Dependency security review |
| `actions/github-script` | v7 | GitHub API scripting |
| `docker/setup-buildx-action` | v3 | Docker Buildx |
| `docker/setup-qemu-action` | v3 | Multi-arch QEMU |
| `docker/login-action` | v3 | Registry authentication |
| `docker/build-push-action` | v6 | Docker build & push |
| `docker/metadata-action` | v5 | Image metadata/tags |
| `github/codeql-action` | v3 | CodeQL security scanning |
| `codecov/codecov-action` | v4 | Coverage upload |
| `softprops/action-gh-release` | v2 | GitHub Release creation |
| `dtolnay/rust-toolchain` | master | Rust toolchain |
| `golangci/golangci-lint-action` | v6 | Go linting |
| `pnpm/action-setup` | v4 | pnpm package manager |
| `gradle/actions/wrapper-validation` | v4 | Gradle wrapper validation |
| `aws-actions/configure-aws-credentials` | v4 | AWS authentication |
| `aws-actions/amazon-ecs-deploy-task-definition` | v2 | AWS ECS deployment |
| `google-github-actions/auth` | v2 | GCP authentication |
| `google-github-actions/setup-gcloud` | v2 | GCP CLI |
| `google-github-actions/deploy-cloudrun` | v2 | GCP Cloud Run |
| `azure/login` | v2 | Azure authentication |
| `azure/container-apps-deploy-action` | v1 | Azure Container Apps |
| `azure/webapps-deploy` | v3 | Azure App Service |
| `aquasecurity/trivy-action` | 0.28.0 | Trivy vulnerability scanner |

---

## License

This project is licensed under the GNU General Public License v3.0 — see the [LICENSE](LICENSE) file for details.
