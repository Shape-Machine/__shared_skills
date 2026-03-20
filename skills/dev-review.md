# /dev-review

Review code for quality, logic, edge cases, and security issues — working from either uncommitted changes or the full branch diff vs main.

Usage: `/dev-review`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin`.

### 2. Determine what to review

Check for uncommitted changes:
```
git status --short
git diff
git diff --cached
```

- **If there are uncommitted changes** (staged or unstaged): review those diffs.
- **If the working tree is clean**: find all commits on the current branch that are not on main:
  ```
  git log main..HEAD --oneline
  git diff main...HEAD
  ```
  Review the full diff of the branch vs main.

If neither yields any diff (e.g. on main with nothing to commit), abort: _"Nothing to review — no uncommitted changes and no branch diff found."_

### 3. Understand context
Before reviewing, briefly read the files touched by the diff to understand the surrounding code and intent. Do not review in isolation.

### 4. Review the diff

Analyse the changes across the following dimensions:

- **Logic & correctness** — wrong assumptions, off-by-one errors, incorrect conditions, missed return values
- **Edge cases** — null/undefined, empty collections, zero values, concurrent access, large inputs
- **Security** — injection risks, unvalidated input, exposed sensitive data, insecure defaults
- **Code quality** — duplication, overly complex logic, poor naming, unnecessary side effects
- **Error handling** — unhandled exceptions, swallowed errors, missing user feedback
- **Performance** — obvious inefficiencies introduced by the change

### 5. Dump all findings as an overview

Present all findings at once in a summary table:

```
## Code Review — N findings

#  Severity  Category        File:Line                        Summary
1  High      Logic           src/api/orders.ts:42             Incorrect total calculation
2  High      Security        src/auth/login.ts:18             Password logged on failure
3  Medium    Edge Case       src/utils/parse.ts:7             No null check before .trim()
4  Low       Quality         src/components/Modal.tsx:55      Magic number, should be a constant
```

Severity guide:
- **Critical** — likely to cause data loss, security breach, or production crash
- **High** — significant bug or risk that should be fixed before merging
- **Medium** — notable issue worth addressing soon but not a blocker
- **Low** — improvement or nitpick, nice to have

After the table, ask: _"Ready to go through these one by one? (y/n)"_

### 6. One-by-one session

If the user says `y`, walk through each finding individually:

```
── Finding #N ─────────────────────────────
Severity: <Critical | High | Medium | Low>
File: <path>:<line>
Category: <Logic | Edge Case | Security | Quality | Error Handling | Performance>

<Clear description of the issue>

Suggestion:
<Specific, actionable fix>
───────────────────────────────────────────
Create a GitHub issue for this? (y/n)
```

Wait for the user's response before showing the next finding.

If the user responds `y`, invoke the `/pm-spec` sub-skill to create a GitHub issue on the current milestone. Confirm the issue number before continuing.

### 7. Summary
After all findings, show a summary:

```
## Review complete
X findings  (N critical, N high, N medium, N low)
Y issues created: #<numbers>
```

If there were no findings: _"No issues found — looks good to merge."_
