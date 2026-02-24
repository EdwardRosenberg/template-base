# Claude Code Instructions — template-base

This is the **Tier 0 base template** for the organization. Every repository inherits from this template. Changes here propagate to all downstream stack (Tier 1) and archetype (Tier 2) templates via the platform sync workflow.

## Repository Purpose

This repo contains **organization-wide governance only**: reusable CI/CD workflows, PR/issue templates, editor config, copilot/agent instructions, and CODEOWNERS. It has no application code, no language-specific tooling, and no build artifacts.

## What You Can Change

- `.github/workflows/` — Reusable workflows (`ci.yml`, `pr-title-lint.yml`, `agent-validation.yml`)
- `.github/copilot-instructions.md` — Agent guardrails (synced to all children)
- `.github/PULL_REQUEST_TEMPLATE.md`, `.github/ISSUE_TEMPLATE/` — PR and issue templates
- `.editorconfig`, `.gitignore` — Universal formatting and ignore rules
- `CODEOWNERS` — Review requirements
- `docs/` — Platform documentation

## What You Must NOT Change

- Do not add language-specific files (no `pom.xml`, `pyproject.toml`, `package.json`)
- Do not add application code or framework dependencies
- Every file here must be stack-agnostic

## Build & Validation

There is no build step for this repository. Validate changes by:

1. Checking YAML syntax for any modified workflow files
2. Confirming workflow inputs are backward-compatible with existing callers
3. Running the PR title lint regex against sample titles if modifying `pr-title-lint.yml`

## Key Conventions

- **Workflow inputs**: Use kebab-case for input names (e.g., `backend-tech-stack`)
- **File placement**: Config files at repo root, GitHub-specific files in `.github/`
- **Backward compatibility**: Reusable workflow changes must not break existing callers — add new optional inputs with defaults rather than changing existing ones

## Sync Awareness

Files listed in `sync-config.yml` → `sync_paths` are propagated to Tier 1 children. If you add a new governance file that all repos should have, add it to `sync_paths` as well.

Children of this repo:
- `template-backend-java` (Tier 1)
- `template-backend-python` (Tier 1)

## Versioning

This template follows [Semantic Versioning](https://semver.org/) via git tags on the `main` branch.

**Current version is determined by the latest `vX.Y.Z` git tag.**

### When to bump the version

After merging a PR, determine the version increment from the PR title (Conventional Commits format):

| PR title type | Version bump | Example |
|---|---|---|
| `fix:` | **PATCH** (`v1.0.0` → `v1.0.1`) | `fix: correct CI caching for pip` |
| `feat:` | **MINOR** (`v1.0.0` → `v1.1.0`) | `feat: add agent-validation reusable workflow` |
| `feat!:`, `fix!:`, `chore!:` (breaking) | **MAJOR** (`v1.0.0` → `v2.0.0`) | `feat!: rename ci.yml input parameters` |
| `docs:`, `chore:`, `ci:`, `refactor:`, `test:` | **PATCH** (`v1.0.0` → `v1.0.1`) | `docs: update workflow examples` |

### How to bump

1. Check the latest tag: `git describe --tags --abbrev=0`
2. Calculate the new version based on the rules above.
3. Tag the merge commit: `git tag vX.Y.Z && git push origin vX.Y.Z`

### Breaking changes require extra care

A breaking change in `template-base` affects **every downstream repo** calling its reusable workflows. Before bumping major:
- Document the migration path in the PR description
- Consider adding a deprecation period where old inputs still work
- Update `README.md` workflow examples to reflect the new usage

Downstream repos reference this template as `@main` or `@vX.Y.Z`. A major version bump means callers pinned to `@v1` won't automatically pick up `@v2` changes — which is the desired behavior for breaking changes.

## PR Conventions

- Titles must follow Conventional Commits: `<type>(<scope>): <description>`
- Valid types: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `perf`, `ci`, `build`
- Keep PRs small and focused on a single concern
- No drive-by refactors or scope creep

