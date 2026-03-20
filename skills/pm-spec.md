# /pm-spec (internal sub-skill)

> ⚠️ This is an internal sub-skill. Do not invoke it directly — it is called by `/pm-features`, `/pm-review`, and `/pm-perf` to create GitHub issues.

## Purpose

Take a feature suggestion, UX finding, or performance opportunity and create a well-formed GitHub issue on the current milestone.

## Steps

### 1. Resolve current milestone
```
gh api repos/<owner>/<repo>/milestones --jq 'sort_by(.due_on) | .[] | select(.state=="open") | {number, title, due_on}'
```
Pick the nearest open milestone. If none exists, create issues without a milestone and warn the user.

### 2. For each item passed from the calling skill, create a GitHub issue

Structure the issue body as:

```markdown
## Summary
<1–2 sentence description of the problem or opportunity>

## User Story
As a <persona>, I want <goal> so that <benefit>.

## Acceptance Criteria
- [ ] <specific, testable criterion>
- [ ] <specific, testable criterion>

## Notes
<edge cases, open questions, or implementation hints — keep brief>
```

Create the issue:
```
gh issue create \
  --title "<clear, action-oriented title>" \
  --body "<formatted body above>" \
  --milestone "<milestone title>" \
  --label "<appropriate labels>"
```

Use labels that already exist in the repo. Common mappings:
- Feature suggestion → `enhancement`
- UX finding → `ux` or `enhancement`
- Bug or missing state → `bug`
- Performance → `performance` or `enhancement`

If a label doesn't exist, skip it rather than creating new ones.

### 3. Report back
After all issues are created, return a list of the created issue numbers and titles to the calling skill so it can display them to the user.
