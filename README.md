# Template Base

A base template repository with CI/CD workflows and standardized configurations.

## CI/CD

### Continuous Integration (Reusable Workflow)

This repository provides a reusable CI workflow intended to be **called by derived templates and project repos**.

- Base repo (`template-base`): contains **CI implementation**
- Derived repos (backend/frontend templates or real projects): define **when CI runs** (push/PR) and call the base workflow

### Use in a derived repository

Create `.github/workflows/ci.yml` in the derived repo:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: /template-base/.github/workflows/ci.yml@v1
    with:
      backend-enabled: true
      backend-tech-stack: "java"
      backend-build-command: "mvn -B clean verify"
      backend-test-command: "mvn -B test"
      java-version: "21"

      frontend-enabled: false

```

**Node.js Frontend Example:**

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  ci:
    uses: EdwardRosenberg/template-base/.github/workflows/ci.yml@main
    with:
      # Disable backend
      backend-enabled: false
      
      # Frontend configuration
      frontend-enabled: true
      frontend-tech-stack: 'node'
      frontend-node-version: '20'
      frontend-package-manager: 'npm'
      frontend-setup-command: 'npm ci'
      frontend-lint-command: 'npm run lint'
      frontend-build-command: 'npm run build'
      frontend-test-command: 'npm test'
```

**Python Backend Example:**

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  ci:
    uses: EdwardRosenberg/template-base/.github/workflows/ci.yml@main
    with:
      # Backend configuration
      backend-enabled: true
      backend-tech-stack: 'python'
      python-version: '3.11'
      backend-setup-command: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
      backend-test-command: 'pytest'
      
      # Disable frontend
      frontend-enabled: false
```

**Full-Stack Example (Node.js Frontend + Java Backend):**

```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  ci:
    uses: EdwardRosenberg/template-base/.github/workflows/ci.yml@main
    with:
      # Backend configuration
      backend-enabled: true
      backend-tech-stack: 'java'
      java-version: '21'
      backend-build-command: 'mvn clean install'
      backend-test-command: 'mvn test'
      
      # Frontend configuration
      frontend-enabled: true
      frontend-tech-stack: 'node'
      frontend-node-version: '20'
      frontend-setup-command: 'npm ci'
      frontend-build-command: 'npm run build'
      frontend-test-command: 'npm test'
```

#### Available Input Parameters

**Backend Parameters:**
- `backend-enabled` (boolean, default: `true`) - Enable/disable backend job
- `backend-tech-stack` (string, default: `'custom'`) - Technology stack: `java`, `python`, `node`, or `custom`
- `backend-os-matrix` (string, default: `'["ubuntu-latest"]'`) - OS matrix as JSON array
- `backend-setup-command` (string) - Custom setup command to run before build
- `backend-build-command` (string) - Build command to execute
- `backend-test-command` (string) - Test command to execute
- `java-version` (string, default: `'21'`) - Java version (used when `backend-tech-stack: 'java'`)
- `java-distribution` (string, default: `'temurin'`) - Java distribution
- `python-version` (string, default: `'3.11'`) - Python version (used when `backend-tech-stack: 'python'`)
- `backend-node-version` (string, default: `'20'`) - Node.js version (used when `backend-tech-stack: 'node'`)

**Frontend Parameters:**
- `frontend-enabled` (boolean, default: `true`) - Enable/disable frontend job
- `frontend-tech-stack` (string, default: `'custom'`) - Technology stack: `node` or `custom`
- `frontend-os-matrix` (string, default: `'["ubuntu-latest"]'`) - OS matrix as JSON array
- `frontend-node-version` (string, default: `'20'`) - Node.js version
- `frontend-package-manager` (string, default: `'npm'`) - Package manager: `npm`, `yarn`, or `pnpm`
- `frontend-setup-command` (string) - Custom setup command to run before build
- `frontend-lint-command` (string) - Linting command to execute
- `frontend-build-command` (string) - Build command to execute
- `frontend-test-command` (string) - Test command to execute

**General Parameters:**
- `ci-success-enabled` (boolean, default: `true`) - Enable CI success summary job (useful for branch protection)

#### Workflow Structure

The CI workflow is designed to be generic and easily adaptable to different project types. It includes:

**Triggers:**
- `push` events to `main` and `develop` branches (when used standalone)
- `pull_request` events targeting `main` and `develop` branches (when used standalone)
- `workflow_call` for use as a reusable workflow (when called from derived templates)

**Jobs:**

1. **Backend Build & Test**
   - Runs on a configurable matrix of operating systems
   - Uses fail-fast strategy to stop on first failure
   - Supports Java/Maven, Python, Node.js, or custom tech stacks
   - Configurable setup, build, and test commands

2. **Frontend Build & Test**
   - Runs on a configurable matrix of operating systems
   - Uses fail-fast strategy to stop on first failure
   - Supports Node.js with npm/yarn/pnpm or custom tech stacks
   - Configurable setup, lint, build, and test commands

3. **CI Success** (Summary Job)
   - Depends on all previous jobs
   - Validates that all CI jobs passed successfully
   - Useful for setting up branch protection rules

#### Key Features

- **Reusable**: Can be called from derived templates to eliminate template drift
- **Composable**: Mix and match backend/frontend configurations as needed
- **No Commenting/Uncommenting**: Configure via input parameters instead
- **Fail-Fast Strategy**: Jobs stop immediately on first failure to save CI/CD resources
- **Matrix Builds**: Easily extendable to test across multiple OS versions
- **Caching Support**: Built-in caching for Maven, pip, and npm to speed up builds
- **Modular Structure**: Backend and frontend jobs are separated for clarity and parallel execution

#### Benefits of the Reusable Workflow Approach

1. **Eliminates Template Drift**: Derived templates call the base workflow instead of copying it
2. **Centralized Updates**: Updates to CI logic happen in one place
3. **No Manual Sync**: No need to copy/paste workflow changes across repositories
4. **Cleaner Configurations**: Use input parameters instead of commenting/uncommenting code sections
5. **Composable**: Enable only the jobs you need (backend-only, frontend-only, or full-stack)

#### Versioning

When calling the reusable workflow, you can specify the version:

- `@main` - Always use the latest version (recommended for staying up-to-date)
- `@v1.0.0` - Pin to a specific release tag (recommended for stability)
- `@abc123` - Pin to a specific commit SHA (for maximum stability)

**Example:**
```yaml
uses: EdwardRosenberg/template-base/.github/workflows/ci.yml@v1.0.0
```

#### More Examples

Additional workflow examples for different project types are available in [`.github/workflows/examples/`](.github/workflows/examples/):

- **Java/Maven Backend**: [`java-backend.yml`](.github/workflows/examples/java-backend.yml)
- **Python Backend**: [`python-backend.yml`](.github/workflows/examples/python-backend.yml)
- **Node.js Backend**: [`nodejs-backend.yml`](.github/workflows/examples/nodejs-backend.yml)
- **Node.js Frontend**: [`nodejs-frontend.yml`](.github/workflows/examples/nodejs-frontend.yml)
- **Full-Stack**: [`fullstack.yml`](.github/workflows/examples/fullstack.yml)
- **Multi-OS Testing**: [`multi-os.yml`](.github/workflows/examples/multi-os.yml)

See the [examples README](.github/workflows/examples/README.md) for detailed usage instructions.

## Configuration Files

This repository includes several configuration files:

- `.editorconfig`: Defines coding styles for various file types
- `.gitignore`: Comprehensive gitignore for multiple languages and frameworks
- `.github/dependabot.yml`: Automated dependency updates for GitHub Actions, Maven, npm, and Docker
- `.github/workflows/ci.yml`: Continuous Integration workflow

## Contributing

Please see the pull request template for contribution guidelines.

## License

<!-- Add your license information here -->
