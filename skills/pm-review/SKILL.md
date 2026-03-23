---
name: pm-review
description: Audit the product for UX friction, missing states, copy issues, onboarding gaps, and product debt — then create GitHub issues for findings.
argument-hint: [focus-area]
---

# /pm-review

Holistic product review: audit the codebase for UX friction, missing states, copy issues, onboarding gaps, and product debt — then create GitHub issues for findings on the current milestone.

Usage: `/pm-review` or `/pm-review <optional focus area>`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin`.

### 2. Understand the product
Read the README and any docs. Briefly scan the codebase structure to understand what the product does and how it is organised.

If `$ARGUMENTS` were passed, treat them as a focus area (e.g. "onboarding", "settings page", "checkout flow") and restrict the review to that area.

### 3. Review for the following dimensions

For each dimension, scan the relevant code (UI components, route handlers, copy strings, error paths) and note specific findings.

**UX & Flow**
- Confusing or non-obvious user flows
- Missing affordances (unlabelled icons, unclear CTAs)
- Inconsistent navigation or back-tracking behaviour

**Missing States**
- Empty states (lists with no items, no results)
- Loading states (async operations with no feedback)
- Error states (failed requests, validation errors)
- Edge cases (zero, one, many items; long strings; no permissions)

**Copy & Tone**
- Unclear or technical error messages shown to users
- Inconsistent terminology across the UI
- CTAs that don't clearly describe the action
- Placeholder text left in production

**Onboarding & First-Run**
- Steps with no guidance for new users
- Missing tooltips, empty states with no call to action
- Friction in the critical path to first value

**Product Debt**
- Deprecated or dead UI still visible to users
- Features that appear half-finished
- Inconsistencies between similar screens or flows

### 4. Present findings
Group findings by dimension. For each finding:

```
### <Short title>
**Where:** <file path or UI location>
**Issue:** <what's wrong or missing>
**Suggestion:** <what to do about it>
```

Ask: _"Which of these would you like to create as GitHub issues? (e.g. 'all', '1 3 5', or 'none')"_

Wait for the user's response.

### 5. Create issues
For each approved finding, invoke the `/pm-spec` sub-skill to create a well-formed GitHub issue on the current milestone.

### 6. Summary
List the created issue numbers and titles.
