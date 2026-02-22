# GitHub Copilot Instructions

These instructions define how GitHub Copilot and Copilot agents should operate when making changes to this repository and any repositories derived from `template-base`.

## Core Principles

### Clarity Over Cleverness
- Write code that is easy to read, understand, and maintain
- Prefer explicit and straightforward solutions over clever or terse implementations
- Use clear variable and function names that convey intent
- Avoid unnecessary abstractions or overly complex patterns

### Small, Focused Changes
- Keep pull requests small and focused on a single concern
- Each commit should represent a logical, atomic unit of work
- Avoid bundling unrelated changes together
- If a task requires multiple changes, break it into separate PRs when possible

### Scope Discipline
- **No drive-by refactors**: Do not refactor code unrelated to the task at hand
- **No gratuitous formatting**: Only format files you're meaningfully modifying
- **Stay on task**: Resist the urge to "fix" unrelated issues or tech debt
- If you notice issues outside the scope, create separate issues instead of fixing them in the current PR

### Observability and Error Handling
- Make all critical-path flows observable with logs, metrics, or traces that provide actionable context.
- Ensure errors are accurately handled and logged with sufficient detail for debugging without leaking sensitive data.
- Apply the “throw early, catch late” principle: validate and throw at the source of failure, and catch at higher boundaries where contextual logging and recovery occur.

## Quality Gates

### Testing Requirements
- **Always** add or update tests when modifying functionality
- Run existing tests before and after making changes
- If tests exist for the area you're modifying, ensure they pass
- If no test infrastructure exists, explicitly state this in the PR description
- **Exception**: Documentation-only changes do not require tests

### Build and Validation
- Always run the project's build commands before finalizing changes
- Fix any build errors or warnings introduced by your changes
- Do not introduce new linter warnings or errors
- If build/test commands are unclear, check:
  - Repository README
  - CI/CD workflows (`.github/workflows/`)
  - Package manager configuration files (`package.json`, `pom.xml`, etc.)

### When Tests Cannot Run
If you cannot run tests or builds (e.g., missing dependencies, environment constraints):
- Clearly state this limitation in the PR description
- Explain what validation steps you performed instead
- Document any assumptions made
- Suggest how reviewers can validate the changes

## Code Style and Conventions

### Follow Existing Patterns
- Match the style and conventions of the surrounding code
- Respect language-specific idioms and best practices
- Follow the repository's `.editorconfig` settings
- Be consistent with existing naming conventions, file organization, and project structure

### Comments and Documentation
- Add comments only when necessary to explain complex logic or non-obvious decisions
- Avoid redundant comments that simply restate what the code does
- Update relevant documentation when changing behavior or interfaces
- Keep README files, API docs, and inline documentation in sync with code changes

## Configuration and Workflow Changes

### CI/CD Workflows
- Exercise extreme caution when modifying `.github/workflows/` files
- Understand that `template-base` provides reusable workflows used by downstream repositories
- Test workflow changes thoroughly before merging
- Document any breaking changes to workflow inputs or behavior
- Consider backward compatibility for repositories calling these workflows

### Dependency Management
- Only add new dependencies when absolutely necessary
- Prefer well-maintained, widely-used libraries
- Check for security vulnerabilities before adding dependencies
- Update `dependabot.yml` if adding dependencies in new ecosystems
- Document why a new dependency is required in the PR description

### Configuration Files
- Maintain consistency across configuration files (`.editorconfig`, `.gitignore`, etc.)
- Keep configurations stack-agnostic when possible to support derived templates
- Document non-obvious configuration choices

## Pull Request Hygiene

### PR Title Requirements

PR titles **must** follow the Conventional Commits format:

```
<type>(<optional scope>): <description>
```

**Valid types:** `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `perf`, `ci`, `build`

**Examples:**
- ✅ `feat: add user authentication endpoint`
- ✅ `fix(ui): correct button alignment issue`
- ✅ `docs: update PR title guidelines`
- ✅ `chore!: update all dependencies` (breaking change)
- ❌ `Update README` (missing type)
- ❌ `Add feature` (missing type)

See [docs/pr-title-guidelines.md](../docs/pr-title-guidelines.md) for complete guidance.

**Automated Enforcement:**

This repository provides a reusable PR title lint workflow at `.github/workflows/pr-title-lint.yml` that validates PR titles against the Conventional Commits format using the regex pattern:

```
^(feat|fix|chore|docs|refactor|test|perf|ci|build)(\(.+\))?(!)?: .+
```

Derived repositories should call this workflow to enforce PR title standards. See the [PR title guidelines](../docs/pr-title-guidelines.md) for usage instructions.

### PR Descriptions Must Include
1. **Clear summary** of what changed and why
2. **Type of change** (bug fix, feature, refactor, docs, config, etc.)
3. **Testing performed** - what you tested and how
4. **Build status** - confirmation that build/tests pass, or explanation if they don't
5. **Breaking changes** - if any, clearly document what breaks and migration path
6. **Related issues** - reference related issues or PRs

### Before Submitting
- Review your own changes critically
- Ensure commit messages are clear and descriptive
- Remove debugging code, commented-out code, and temporary files
- Verify that only intended files are included in the PR
- Check that sensitive information (credentials, keys, personal data) is not included

## Agent Delegation

This section defines the contract that **AI agents and bots** must follow when opening PRs in this repository or any repository derived from `template-base`. Human contributors are encouraged to follow the same structure for clarity.

### Task Description Format

Every agent-authored PR must include a structured task description in the PR body using this format:

```
### Goal
One sentence: what this PR accomplishes.

### Constraints
- What must NOT change (e.g., "do not modify pom.xml parent version")
- Assumptions made

### Acceptance Criteria
- [ ] Build passes (`<build-command>`)
- [ ] All tests pass (`<test-command>`)
- [ ] No new lint warnings (`<lint-command>`)
- [ ] Only files in declared scope were modified
- [ ] PR title follows Conventional Commits format

### Files in Scope
Explicit list of every file the agent was permitted to touch:
- `src/main/java/...`
- `.github/workflows/ci.yml`
```

### Agent Self-Validation Checklist

Before submitting a PR, an agent **must** verify all of the following. If any check cannot be run (e.g., environment constraints), this must be stated explicitly in the PR body.

- [ ] **Build passes** — run the project's build command end-to-end
- [ ] **Tests pass** — run the full test suite; no regressions introduced
- [ ] **Lint clean** — no new warnings or errors from the linter/static analyser
- [ ] **Scope respected** — no files outside the declared scope were modified
- [ ] **No sensitive data** — no credentials, keys, or personal data in the diff
- [ ] **No debugging artifacts** — no temporary files, `console.log`, `System.out.println`, or commented-out code left in
- [ ] **PR title valid** — follows `<type>(<scope>): <description>` Conventional Commits format
- [ ] **PR description complete** — includes Goal, Constraints, Acceptance Criteria, and Files in Scope

### Automated Enforcement

Agent PRs are subject to an additional automated validation workflow (`.github/workflows/agent-validation.yml`) that enforces:

1. **PR title lint** — Conventional Commits format required
2. **Scope check** — files changed must be within the declared `allowed-paths`
3. **Build / test / lint gates** — all three must pass
4. **Human review required** — `CODEOWNERS` ensures a human from `@EdwardRosenberg/platform-team` must approve every PR, regardless of author

If the agent validation workflow fails, the PR cannot be merged until the agent addresses all failures.

### Identity and Attribution

- Agents should commit as `github-actions[bot]` or a dedicated bot account
- Add a `Co-authored-by: <delegating-human> <email>` trailer to preserve attribution
- Example commit message:
  ```
  feat: add health check endpoint

  Co-authored-by: Carolina <carolina@example.com>
  ```

### What Agents Must NOT Do

- ❌ Push directly to `main` — always open a PR
- ❌ Self-approve PRs — a human reviewer is always required
- ❌ Modify `CODEOWNERS`, `.github/workflows/`, or `sync-config.yml` unless explicitly tasked with doing so
- ❌ Introduce new dependencies without documenting the reason in the PR body
- ❌ Expand scope beyond the declared `Files in Scope` list

## Template-Specific Guidance

### This is a Base Template
- Changes here will be inherited by backend and frontend template repositories
- Changes here may affect multiple downstream projects
- Keep all configurations stack-agnostic unless there's a compelling reason not to
- Consider the impact on derived templates before making breaking changes

### File Organization
- Configuration files belong in the repository root (`.editorconfig`, `.gitignore`)
- GitHub-specific files belong in `.github/` directory
- Workflow files belong in `.github/workflows/`
- Issue and PR templates belong in `.github/ISSUE_TEMPLATE/` and `.github/`

## What to Avoid

### Anti-Patterns
- ❌ Reformatting entire files when making a small change
- ❌ Mixing refactoring with feature work or bug fixes
- ❌ Adding dependencies for functionality easily implemented with standard libraries
- ❌ Over-engineering solutions to simple problems
- ❌ Copying code instead of extracting common functionality
- ❌ Ignoring existing error handling patterns
- ❌ Skipping validation steps to save time

### Scope Creep
- ❌ "While I'm here, let me also fix..."
- ❌ "I noticed this other issue, so I'll include a fix..."
- ❌ "Let me update all these other files to be consistent..."

If you encounter legitimate issues outside your current scope, create a separate issue or PR instead.

## Summary

When in doubt, remember:
1. **Small changes** are easier to review and less risky
2. **Tests and builds** must pass before merging
3. **Stay focused** on the task at hand
4. **Follow existing patterns** in the codebase
5. **Document your changes** clearly in the PR

These guidelines help maintain code quality, reduce review friction, and ensure that `template-base` and all derived repositories remain maintainable and consistent over time.
