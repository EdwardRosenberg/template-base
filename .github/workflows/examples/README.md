# CI Workflow Examples

This directory contains example workflow files demonstrating how to use the reusable CI workflow from derived repositories.

## How to Use

1. Choose the example that best matches your project type
2. Copy the example file to `.github/workflows/ci.yml` in your repository
3. Customize the parameters as needed for your project
4. Commit and push - the workflow will run automatically

## Available Examples

### [java-backend.yml](./java-backend.yml)
- **Use Case**: Java/Maven backend project
- **Features**: JDK setup, Maven caching, build and test
- **Configuration**: Java 21 with Temurin distribution

### [python-backend.yml](./python-backend.yml)
- **Use Case**: Python backend project
- **Features**: Python setup, pip caching, custom setup with requirements.txt, pytest
- **Configuration**: Python 3.11

### [nodejs-backend.yml](./nodejs-backend.yml)
- **Use Case**: Node.js backend project (e.g., Express API)
- **Features**: Node.js setup, npm caching, build and test
- **Configuration**: Node.js 20 with npm

### [nodejs-frontend.yml](./nodejs-frontend.yml)
- **Use Case**: Node.js/React frontend project
- **Features**: Node.js setup, npm caching, lint, build, and test
- **Configuration**: Node.js 20 with npm

### [fullstack.yml](./fullstack.yml)
- **Use Case**: Full-stack application with separate backend and frontend
- **Features**: Combined Java backend + Node.js frontend
- **Configuration**: Runs both jobs in parallel with directory-specific commands

### [multi-os.yml](./multi-os.yml)
- **Use Case**: Testing across multiple operating systems
- **Features**: Tests on Ubuntu, macOS, and Windows
- **Configuration**: Matrix build across three platforms

## Customization Tips

### Versioning
Pin the workflow to a specific version for stability:

```yaml
uses: EdwardRosenberg/template-base/.github/workflows/ci.yml@v1.0.0
```

Or use `@main` to always get the latest version:

```yaml
uses: EdwardRosenberg/template-base/.github/workflows/ci.yml@main
```

### Enabling/Disabling Jobs

Set `backend-enabled: false` to skip the backend job:

```yaml
with:
  backend-enabled: false
  frontend-enabled: true
```

### Custom Commands

Use multi-line commands for complex setups:

```yaml
with:
  backend-setup-command: |
    echo "Running custom setup"
    ./scripts/setup.sh
    make dependencies
```

### Multiple OS Testing

Specify OS matrix as a JSON array:

```yaml
with:
  backend-os-matrix: '["ubuntu-latest", "macos-latest"]'
  frontend-os-matrix: '["ubuntu-latest", "windows-latest"]'
```

### Package Managers

For Node.js projects, specify your package manager:

```yaml
with:
  frontend-package-manager: 'yarn'  # or 'npm' or 'pnpm'
  frontend-setup-command: 'yarn install --frozen-lockfile'
  frontend-build-command: 'yarn build'
```

## Benefits

✅ **No Template Drift**: Changes to CI logic are centralized in the base repository  
✅ **No Manual Sync**: Updates propagate automatically when you use `@main`  
✅ **Clean Configuration**: No commented-out code sections  
✅ **Composable**: Enable only the jobs you need  
✅ **Flexible**: Supports multiple tech stacks and configurations  

## Support

For issues or questions about using the reusable workflow, please open an issue in the template-base repository.
