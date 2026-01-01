# Feature Request: Default Ruleset for Branch Management

**Issue Type:** Enhancement  
**Labels:** enhancement, configuration, branch-protection  
**Priority:** Medium  

---

## Summary

Implement a default GitHub ruleset configuration that enforces best practices for branch management across the template-base repository and provides a reference implementation for derived repositories.

---

## Problem Statement

Currently, the template-base repository lacks a standardized branch protection and management strategy through GitHub rulesets. While the repository provides comprehensive CI/CD workflows and configuration files (`.editorconfig`, `.gitignore`, `dependabot.yml`, reusable CI workflow), there is no default ruleset configuration to:

- Enforce code quality gates before merging
- Prevent direct pushes to protected branches  
- Require pull request reviews
- Ensure CI/CD checks pass before merge
- Maintain a clean commit history
- Protect critical branches from force pushes and deletion

### Current Gaps

This gap means that derived repositories may not follow consistent branch management best practices, potentially leading to:

- **Security Risks**: Unreviewed code being merged to main branches
- **Quality Issues**: CI/CD checks being bypassed
- **Data Loss**: Accidental deletion of important branches
- **Inconsistency**: Varying branch protection across projects
- **Compliance Concerns**: Lack of enforced code review and approval process

---

## Proposed Solution

Create a default GitHub ruleset configuration that can be:

1. **Applied to this template repository** as a reference implementation
2. **Documented for adoption** by derived repositories (backend, frontend, and project templates)
3. **Versioned and maintained** alongside other template configurations

### Recommended Ruleset Configuration

#### Target Branches

The ruleset should apply to:
- `main` - Production-ready code
- `develop` - Integration branch for ongoing development
- `release/*` - Release preparation branches (pattern match)
- `hotfix/*` - Emergency fix branches (pattern match)

#### Core Rules

##### 1. Pull Request Requirements

- **Require pull requests for all changes**
  - No direct commits to protected branches
  - Enforce proper code review workflow
  
- **Review requirements**:
  - Require at least **1 approving review** (minimum)
  - Recommended: 2+ reviews for critical repositories
  - Dismiss stale reviews when new commits are pushed
  - Require review from code owners (if CODEOWNERS file exists)
  - Require approval of the most recent reviewable push
  
- **Additional pull request rules**:
  - Require conversation resolution before merging
  - Block merge on requested changes

##### 2. Status Check Requirements

- **Require status checks to pass before merging**:
  - Require the `ci-success` job to pass (job name: `ci-success`, display name: `CI Success`)
  - This integrates with the existing job from the reusable workflow (`.github/workflows/ci.yml`)
  - This ensures both backend and frontend builds/tests pass
  
- **Branch freshness**:
  - Require branches to be up to date before merging
  - Prevents merge conflicts and integration issues

##### 3. Commit and History Requirements

- **Prevent destructive actions**:
  - Block force pushes to protected branches
  - Prevent branch deletion
  
- **Optional but recommended**:
  - Require linear history (no merge commits, use squash or rebase)
  - Require signed commits (GPG/SSH signatures for enhanced security)
  - Require commit messages to follow conventional commits format

##### 4. Bypass Permissions

- **Repository administrators**: Can bypass protections in emergency situations
- **Service accounts**: Optionally allow specific bots for automated releases (e.g., release automation, version bumping)
- **Document bypass usage**: Require documentation when bypass is used

#### Rule Enforcement Levels

The configuration should support different enforcement levels:

**Level 1: Basic Protection (Recommended Minimum)**
- Require pull requests
- Require 1 approval
- Require CI to pass
- Block force push and deletion

**Level 2: Standard Protection (Recommended for Most Projects)**
- All Level 1 rules
- Require branch to be up to date
- Dismiss stale reviews
- Require conversation resolution

**Level 3: Strict Protection (Recommended for Critical Projects)**
- All Level 2 rules
- Require 2+ approvals
- Require code owner review
- Require signed commits
- Require linear history

---

## Implementation Approach

### Phase 1: Documentation

Create comprehensive documentation for rulesets:

1. **New file**: `docs/BRANCH_PROTECTION.md` (at repository root, consistent with this feature request location)
   - Explain ruleset concept and benefits
   - Provide step-by-step configuration instructions
   - Include screenshots of GitHub UI configuration
   - Document rationale for each rule
   - Provide troubleshooting guide

2. **Configuration reference**: `docs/rulesets/` directory
   - Multiple configuration examples (basic, standard, strict) as JSON or YAML
   - Documented structure for manual recreation via GitHub UI
   - Note: GitHub API supports ruleset creation, but manual UI configuration is more common
   - Include both API examples and UI-based instructions

3. **Update README.md**:
   - Add "Branch Protection Strategy" section
   - Link to detailed ruleset documentation
   - Mention integration with CI/CD workflow

### Phase 2: Apply to Template Repository

1. Configure the recommended ruleset on template-base repository
2. Test with sample pull requests to validate rules work as expected
3. Document any issues or adjustments needed

### Phase 3: Guidance for Derived Repositories

1. Create a guide for applying rulesets to derived repositories
2. Provide examples for different project types:
   - Small team projects (1 reviewer)
   - Enterprise projects (multiple reviewers, strict rules)
   - Open source projects (external contributors)
3. Document how to customize for specific needs

### Phase 4: Integration

1. Ensure ruleset works seamlessly with:
   - Reusable CI workflow (`.github/workflows/ci.yml`)
   - The `ci-success` summary job
   - Dependabot automated PRs
   - PR template requirements
   
2. Update workflow examples to reference branch protection

---

## Alternatives Considered

### 1. Branch Protection Rules (Legacy)

**Pros**:
- More familiar to some users
- Widely documented

**Cons**:
- Legacy GitHub feature
- Less flexible than rulesets
- Cannot target multiple branches with patterns as easily
- Being phased out in favor of rulesets

**Decision**: Use modern rulesets as they are GitHub's recommended approach going forward.

### 2. No Default Configuration

**Pros**:
- Maximum flexibility for repository maintainers
- No imposed workflow

**Cons**:
- Inconsistency across repositories
- Security and quality risks
- New users may not know best practices
- Template value proposition reduced

**Decision**: Template repositories should provide opinionated, secure defaults that can be customized if needed.

### 3. Infrastructure as Code (Terraform/Pulumi)

**Pros**:
- Version controlled configuration
- Automated application
- Consistency through code

**Cons**:
- Adds external dependencies
- Requires additional tools and expertise
- Overkill for single repository configuration
- Reduces accessibility for users

**Decision**: Provide well-documented manual configuration that can optionally be automated by users who need it.

### 4. GitHub CLI Scripts

**Pros**:
- Scriptable and repeatable
- Can be version controlled

**Cons**:
- Requires `gh` CLI installation
- May break with API changes
- Not as user-friendly as UI configuration

**Decision**: Provide CLI examples as supplementary option, but focus on UI-based configuration for accessibility.

---

## Benefits

### Security & Quality
- ✅ **Prevents unauthorized changes**: All code must go through review
- ✅ **Ensures CI/CD validation**: Tests and builds must pass
- ✅ **Reduces bugs**: Multiple eyes on every change
- ✅ **Audit trail**: Clear history of who approved what

### Team & Process
- ✅ **Encourages collaboration**: Forces pull request workflow
- ✅ **Knowledge sharing**: Reviews spread knowledge across team
- ✅ **Onboarding**: Clear process for new contributors
- ✅ **Consistency**: Same workflow across all repositories

### Risk Mitigation
- ✅ **Accident prevention**: Cannot accidentally force push or delete
- ✅ **Rollback safety**: Protected history makes rollback easier
- ✅ **Compliance**: Meets requirements for code review and approval
- ✅ **Quality gates**: Prevents breaking changes from reaching main

### Template Value
- ✅ **Best practices built-in**: Users get secure defaults
- ✅ **Reference implementation**: See how it should be done
- ✅ **Time savings**: Don't need to research branch protection
- ✅ **Reduced errors**: Consistent configuration across projects

---

## Additional Context

### GitHub Documentation References

- [About rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)
- [Creating rulesets for a repository](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/creating-rulesets-for-a-repository)
- [Available rules for rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets)
- [Managing rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/managing-rulesets-for-a-repository)

### Integration Points

The ruleset integrates with existing template-base features:

1. **CI/CD Workflow** (`.github/workflows/ci.yml`):
   - Status checks will reference the `ci-success` job (display name: `CI Success`)
   - The existing fail-fast strategy aligns with branch protection goals
   - Reusable workflow pattern supports derived repositories

2. **PR Template** (`.github/PULL_REQUEST_TEMPLATE.md`):
   - Template guides contributors to provide necessary context
   - Works with review requirements

3. **Dependabot** (`.github/dependabot.yml`):
   - Automated PRs will still work
   - May need auto-merge configuration for low-risk updates
   - Should pass CI checks automatically

4. **Issue Templates**:
   - Clear process from issue → PR → review → merge
   - Supports traceability

### Flexibility Considerations

The default ruleset should be **opinionated but adaptable**:

**Team Size**:
- Solo developers: Can use 1 reviewer with self-review allowed
- Small teams (2-5): 1-2 reviewers recommended
- Large teams (6+): 2+ reviewers, code owners

**Security Posture**:
- Standard projects: Basic protection
- Sensitive data: Require signed commits
- Compliance needs: Strict protection, audit logging

**Development Workflow**:
- Trunk-based: Protect `main` only, linear history
- Git Flow: Protect `main`, `develop`, release and hotfix patterns
- GitHub Flow: Protect `main`, direct PRs

**Deployment Strategy**:
- Continuous deployment: CI success required
- Scheduled releases: May allow manual override for emergencies
- Enterprise: Additional approval gates

### Real-World Examples

**Example 1: Small Open Source Project**
```
Branches: main
Reviews: 1 required
CI: Must pass
Force push: Blocked
Bypass: Maintainers only
```

**Example 2: Enterprise Application**
```
Branches: main, develop, release/*, hotfix/*
Reviews: 2 required, code owners
CI: Must pass, branch must be up to date
Signed commits: Required
Force push: Blocked
Deletion: Blocked
Linear history: Required
Bypass: Admins only, logged
```

**Example 3: Template Repository (Recommended for template-base)**
```
Branches: main, develop
Reviews: 1 required, dismiss stale
CI: CI Success job must pass
Branch must be up to date: Yes
Force push: Blocked
Deletion: Blocked
Bypass: Admins for emergency fixes
```

---

## Success Criteria

The feature is complete when:

- [ ] Ruleset documentation created in `docs/BRANCH_PROTECTION.md`
- [ ] Configuration examples provided for basic, standard, and strict levels in `docs/rulesets/` directory
- [ ] Default ruleset applied to template-base repository (`main` and `develop` branches)
- [ ] README.md updated with branch protection strategy section
- [ ] Integration with existing CI/CD workflow documented and tested
- [ ] Tested with sample pull requests to validate all rules work correctly
- [ ] Dependabot compatibility verified
- [ ] Guide created for applying to derived repositories
- [ ] Troubleshooting section included in documentation
- [ ] At least one complete example for different project types documented

---

## Implementation Timeline

**Estimated Effort**: 1-2 days

**Phase 1** (4 hours): Research and documentation
- Research GitHub ruleset best practices
- Draft documentation structure
- Create configuration examples

**Phase 2** (2 hours): Template repository implementation
- Apply ruleset to template-base
- Test with sample PRs
- Refine configuration based on testing

**Phase 3** (2 hours): Integration and documentation
- Update README.md
- Document integration points
- Create derived repository guide

**Phase 4** (1 hour): Review and refinement
- Peer review of documentation
- Address feedback
- Final testing

---

## Willingness to Contribute

✅ **Yes, I am willing to contribute to implementing this feature**, including:

- Creating comprehensive ruleset documentation
- Providing multiple configuration examples
- Applying and testing the ruleset on template-base
- Updating README.md with branch protection guidelines
- Creating guides for derived repositories
- Testing integration with CI/CD workflows
- Documenting troubleshooting steps

---

## Next Steps

1. **Review and approve** this feature request
2. **Create a PR** with the documentation and configuration
3. **Test the ruleset** on template-base repository
4. **Gather feedback** from initial usage
5. **Iterate** based on real-world experience
6. **Communicate** to users of derived templates

---

**Related Issues**: None currently  
**Dependencies**: None - can be implemented immediately  
**Breaking Changes**: None - this is additive configuration  

---

*This feature request follows the template-base principles: providing opinionated, secure defaults while maintaining flexibility for different use cases.*
