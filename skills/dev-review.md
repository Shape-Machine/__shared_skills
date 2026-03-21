# /dev-review

Review code for quality, logic, edge cases, and security issues — working from either uncommitted changes or the full branch diff vs main.

Usage: `/dev-review`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin`.

### 2. Determine what to review

Check the current branch and uncommitted changes:
```
git branch --show-current
git status --short
git diff
git diff --cached
```

Record whether the session is on a **feature branch** (anything other than `main` or `master`) — this affects the one-by-one session in step 6.

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
```

The action prompt after each finding depends on context:

**On a feature branch:**
- If the fix is a concrete code change: ask _"Fix this now? (y/n)"_
  - If `y`: apply the fix directly, confirm the change, then continue to the next finding.
  - If `n`: ask _"Create a GitHub issue instead? (y/n)"_ — if `y`, invoke `/pm-spec`.
- If the fix is not a concrete code change (architectural concern, missing tests, design issue): skip the fix prompt and ask _"Create a GitHub issue for this? (y/n)"_ — if `y`, invoke `/pm-spec`.

**On main (or reviewing a clean branch with no active work):**
- Ask _"Create a GitHub issue for this? (y/n)"_
- If `y`, invoke the `/pm-spec` sub-skill to create a GitHub issue on the current milestone. Confirm the issue number before continuing.

Wait for the user's response before showing the next finding.

### 7. Summary
After all findings, show a summary:

```
## Review complete
X findings  (N critical, N high, N medium, N low)
Y fixed in place
Z issues created: #<numbers>
```

If there were no findings: _"No issues found — looks good to merge."_
