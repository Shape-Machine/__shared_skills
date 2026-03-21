# /dev-pr-merged

Switch to main, pull latest, verify the current feature branch's PR is merged, and delete the local feature branch.

Usage: `/dev-pr-merged`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin` — parse the owner/repo from the URL (supports both SSH and HTTPS formats).

### 2. Capture the feature branch name
Record the current branch:
```
git branch --show-current
```

If already on `main` or `master`, abort: _"You are already on main — run this from the feature branch you want to clean up."_

Store this as the feature branch name for later steps.

### 3. Check for uncommitted changes
Run `git status --short`. If there are uncommitted changes, abort:
_"There are uncommitted changes on <branch>. Commit or stash them before switching to main."_

### 4. Switch to main and pull
```
git checkout main
git pull
```

If either command fails, report the error and abort.

### 5. Verify the PR is merged
Look up the PR for the feature branch:
```
gh pr list --head <feature-branch> --state merged --json number,title,mergedAt,url
```

- If a merged PR is found: note its number, title, merged timestamp, and URL — proceed to step 6.
- If no merged PR is found (PR is open, closed without merge, or doesn't exist): report the status and abort without deleting the branch:
  _"No merged PR found for branch '<branch>'. Branch was not deleted."_

### 6. Delete the local feature branch
```
git branch -d <feature-branch>
```

Use `-d` (safe delete) so git will refuse if the branch has unmerged commits relative to HEAD. If `-d` fails for this reason, report the conflict and do not force-delete — ask the user to investigate.

### 7. Report
Print a summary:
```
✓ Switched to main and pulled latest
✓ PR #<number> "<title>" was merged on <date>
✓ Deleted local branch: <feature-branch>
```
