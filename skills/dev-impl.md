# /dev-impl

Read one or more GitHub issues, review the relevant code, propose an implementation plan, and implement it on a feature branch after approval.

Usage: `/dev-impl 11 22 33`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin` — parse the owner/repo from the URL (supports both SSH and HTTPS formats).

Also verify the working tree is clean (`git status`). If there are uncommitted changes, warn the user and abort.

### 2. Parse issue numbers
Read the issue numbers from `$ARGUMENTS` (space-separated integers). If none are provided, abort with usage instructions.

### 3. Fetch all issue descriptions
For each issue number, fetch the full issue details:
```
gh issue view <number> --json number,title,body,labels,milestone,comments
```

Read all issue titles, bodies, and any clarifying comments. Build a complete understanding of what is being asked across all issues before touching any code.

### 4. Review relevant code
Based on the issue descriptions, identify the areas of the codebase likely affected:
- Look for filenames, function names, component names, or feature keywords mentioned in the issues.
- Search the codebase for these symbols and read the relevant files.
- Understand the current implementation state before proposing changes.

Do not do a full repo scan — be targeted and efficient.

### 5. Propose implementation plan
Present a clear implementation plan that covers all issues. Structure it as:

```
## Implementation Plan
Issues: #11, #22, #33
Branch: feature/issue-11-22-33-<short-slug>

### Summary
<1–2 sentence overview of what will be done>

### Changes
- **file/path.ts** — <what will change and why>
- **another/file.ts** — <what will change and why>

### Issue Breakdown
- #11 — addressed by <which changes>
- #22 — addressed by <which changes>

### Out of scope
<anything explicitly not being done, if relevant>
```

Then stop and wait for the user to approve the plan. Do not proceed until explicitly approved.

### 6. On approval — update issue descriptions
For each issue, prepend the approved implementation plan to its description using:
```
gh issue edit <number> --body "<new body>"
```

The new body format:
```
## ✅ Implementation Plan
_Approved: <YYYY-MM-DD HH:MM>_

<the approved plan>

---

## ~~Original Description~~
> ⚠️ Superseded by the implementation plan above.

<original body verbatim>
```

### 7. Create feature branch
```
git checkout -b feature/issue-11-22-33-<short-slug>
```

Derive the short slug from the shared theme of the issue titles (2–4 words, kebab-case).

### 8. Implement
Implement all changes per the approved plan. Work through the issues systematically. Write clean, focused code — do not refactor or change things outside the plan's scope.

### 9. Commit and push
Stage and commit all changes:
```
git add <relevant files>
git commit -m "<concise summary covering all issues>

Implements: #11, #22, #33"
git push -u origin <branch-name>
```

Do not create a pull request. Stop here and inform the user that the branch has been pushed and is ready for a PR.
