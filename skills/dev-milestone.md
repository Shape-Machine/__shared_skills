# /dev-milestone

Show a structured view of the current GitHub milestone's issues, grouped by theme.

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin` — parse the owner/repo from the URL (supports both SSH and HTTPS formats).

### 2. Resolve the milestone
- If an argument was passed (`$ARGUMENTS`), treat it as the milestone title or number to look up.
- Otherwise, detect the active milestone by finding the milestone with the nearest due date that still has open issues.
- If no milestone can be resolved, abort with a helpful message.

### 3. Fetch issues
Use `gh issue list --milestone "<milestone>" --state all --limit 200 --json number,title,state,labels,body` to fetch all issues in the milestone.

### 4. Display closed issues (compact)
Show a compact checklist of closed issues:

```
## ✅ Closed (N)
- #12 Fix login redirect loop
- #15 Add user avatar upload
```

### 5. Group open issues by title similarity
Analyze the open issue titles and group them by semantic/thematic similarity — look for shared keywords, features, or subsystems in the titles. Do not use labels. Use your judgment to form coherent groups that would make sense to work on together in the same feature branch and PR.

Rules:
- Each issue belongs to exactly one group.
- If an issue is clearly distinct from all others, it forms its own 1-issue group.
- Name each group with a short descriptive label (2–5 words) derived from the titles.

### 6. Display open issues grouped
For each group, show:

```
## 🔲 Open (N)

### Auth & Session Management  →  branch: feature/auth-session
- #8 Session token not refreshing
- #11 Logout doesn't clear cookie
- #14 Add remember-me option

### Onboarding Flow  →  branch: feature/onboarding
- #17 Skip button missing on step 2
```

Suggest a branch name for each group using the pattern `feature/<short-slug>`.

### 7. Summary line
End with a one-line summary: total open, total closed, total in milestone.
