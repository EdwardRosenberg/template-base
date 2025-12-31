# Template Base

A base template repository with CI/CD workflows and standardized configurations.

## CI/CD

### Continuous Integration Workflow

This repository includes a comprehensive CI workflow (`.github/workflows/ci.yml`) that automatically runs on every push and pull request to the `main` and `develop` branches.

#### Workflow Structure

The CI workflow is designed to be generic and easily adaptable to different project types. It includes:

**Triggers:**
- `push` events to `main` and `develop` branches
- `pull_request` events targeting `main` and `develop` branches

**Jobs:**

1. **Backend Build & Test**
   - Runs on a matrix of operating systems (currently `ubuntu-latest`)
   - Uses fail-fast strategy to stop on first failure
   - Includes placeholders for backend-specific build steps
   - Example configurations provided for:
     - Java/Maven projects
     - Python projects

2. **Frontend Build & Test**
   - Runs on a matrix of operating systems (currently `ubuntu-latest`)
   - Uses fail-fast strategy to stop on first failure
   - Includes placeholders for frontend-specific build steps
   - Example configurations provided for:
     - Node.js/npm projects

3. **CI Success** (Summary Job)
   - Depends on all previous jobs
   - Validates that all CI jobs passed successfully
   - Useful for setting up branch protection rules

#### Key Features

- **Fail-Fast Strategy**: Jobs will stop immediately on first failure to save CI/CD resources
- **Matrix Builds**: Easily extendable to test across multiple OS versions or language versions
- **Caching Support**: Examples include caching for Maven, pip, and npm to speed up builds
- **Modular Structure**: Backend and frontend jobs are separated for clarity and parallel execution

#### Customization

To customize the CI workflow for your specific project:

1. Uncomment the relevant setup steps in the workflow file
2. Modify the matrix configuration to test on additional operating systems or language versions
3. Add or remove jobs as needed for your project structure
4. Update the `ci-success` job dependencies if you add new jobs

#### Example Usage

**For a Java/Maven project:**
- Uncomment the JDK setup and Maven build steps in the `backend` job
- Adjust the Java version as needed

**For a Node.js project:**
- Uncomment the Node.js setup and npm steps in the `frontend` job
- Adjust the Node.js version as needed

**For testing multiple versions:**
- Uncomment and modify the matrix configuration to include additional versions

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
