---
name: dev-pr
description: Draft and create a pull request for the current feature branch.
---

# /dev-pr

Create a pull request for the current feature branch.

Usage: `/dev-pr`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin` — parse the owner/repo from the URL (supports both SSH and HTTPS formats).

### 2. Verify on a feature branch
Check the current branch:
```
git branch --show-current
```

If the current branch is `main` or `master`, abort: _"You are on the main branch — switch to a feature branch before creating a PR."_

### 3. Verify branch has been pushed
Check if the current branch has an upstream:
```
git status -sb
```

If no upstream is set, push the branch first:
```
git push -u origin <branch-name>
```

### 4. Check for uncommitted changes
Run `git status --short`. If there are uncommitted changes, warn the user:
_"There are uncommitted changes. These will not be included in the PR. Proceed anyway? (y/n)"_

Wait for confirmation before continuing.

### 5. Gather branch context
Collect the diff and commits between this branch and main:
```
git log main..HEAD --oneline
git diff main...HEAD
```

Also check if any GitHub issues are referenced in commit messages (look for `#<number>` patterns) — these will be linked in the PR body.

### 6. Draft PR title and body
Derive a concise PR title (under 70 characters) from the branch name and commit messages.

Build the PR body:
```
## Summary
<1–3 bullet points describing what was changed and why>

## Issues
<list of linked issues as "Closes #N" or "Relates to #N" — only if found in commits>

## Test plan
<bulleted checklist of how to verify this change works>
```

Show the drafted title and body to the user and ask: _"Create this PR? (y/n)"_

Wait for confirmation before proceeding. The user may also suggest edits — incorporate any feedback before creating.

### 7. Create the PR
```
gh pr create --title "<title>" --body "<body>"
```

### 8. Report
Print the PR URL returned by `gh pr create`. Done.
