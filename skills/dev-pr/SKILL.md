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
Print the PR URL returned by `gh pr create`.

### 9. Wait for Qodo review and assess suggestions

After reporting the PR URL, ask the user:
_"Wait for Qodo to review this PR? (~7 minutes) (y/n)"_

If the user says no, skip this step entirely and end.

If the user says yes:

**9a. Wait for Qodo**

Wait 7 minutes. Display a countdown message every minute so the user knows what is happening:
```
⏳ Waiting for Qodo review... 7 minutes remaining
⏳ Waiting for Qodo review... 6 minutes remaining
...
✅ Checking for Qodo review now
```

Use `sleep 60` in a loop or equivalent to implement the countdown.

**9b. Fetch Qodo review comments**

Detect the git provider from `git remote get-url origin`, then fetch PR comments.

For GitHub:
```bash
gh pr view --json number --jq '.number'     # get PR number
gh api repos/{owner}/{repo}/issues/{pr_number}/comments   # PR-level comments
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments    # inline review comments
```

Look for comments where the author login is one of: `qodo-merge[bot]`, `pr-agent-pro`, `pr-agent-pro-staging`, `qodo-ai[bot]`.

**9c. Handle review-not-ready cases**

- If no Qodo comments found at all: inform _"Qodo hasn't reviewed yet. Run `/qodo-pr-resolver` in a few minutes."_ and end.
- If any comment contains "Come back again in a few minutes" or "An AI review agent is analysing this pull request": inform _"Qodo review is still in progress. Run `/qodo-pr-resolver` in a few minutes."_ and end.

**9d. Parse and assess each suggestion**

Extract the individual suggestions/issues from the Qodo comments (deduplicate inline vs. summary comments by issue title, as described in the qodo-pr-resolver skill).

For each suggestion, **read the referenced file and lines** to assess its validity:

```
Read <file>:<lines> to understand the current code at that location
```

Then assess each suggestion using this rubric:
- **Valid** — the issue exists in the current code as Qodo describes, and the fix is applicable and correct
- **Stale** — Qodo's description references code that has already been changed or no longer exists at the cited location
- **Invalid** — the issue does not actually apply (false positive, misunderstood context, or the fix would break things)

**9e. Display assessment table**

Present findings in this format:

```
Qodo Review for PR #<N>: <PR Title>
<total> suggestions found — <valid count> need attention

| # | Severity | Issue Title | Location | Assessment | Reason |
|---|----------|-------------|----------|------------|--------|
| 1 | 🔴 CRITICAL | Missing input validation | src/api.py:42 | ✅ Valid | Input is passed directly to the query without sanitization |
| 2 | 🟡 MEDIUM | Rename variable for clarity | src/utils.py:18 | ⚠️ Stale | The variable `foo` was already renamed to `bar` in this branch |
| 3 | ⚪ LOW | Add docstring | src/models.py:7 | ❌ Invalid | This is an internal private method not part of the public API |
```

Severity follows the same mapping as qodo-pr-resolver (position within "Action required" / "Review recommended" / "Other" groups).

After the table, print a short summary:
_"Run `/qodo-pr-resolver` to interactively fix the valid suggestions."_
