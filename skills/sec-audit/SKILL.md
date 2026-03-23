---
name: sec-audit
description: Audit code for security vulnerabilities, insecure patterns, and sensitive data exposure — overview then one-by-one session.
---

# /sec-audit

Audit code for security vulnerabilities, insecure patterns, and sensitive data exposure.

Usage: `/sec-audit`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin`.

### 2. Determine scope

Check the current branch and working tree state:

```
git branch --show-current
git status --short
git diff
git diff --cached
```

**If on main branch:** scan the full source tree. Ignore `node_modules`, lock files, build output, and vendored directories.

**If on a feature branch with uncommitted changes:** audit those diffs (staged and unstaged).

**If on a feature branch with nothing to commit:** audit the full branch diff vs main:
```
git diff main...HEAD
```

If neither yields anything to audit, abort: _"Nothing to audit — no changes found and not on main."_

### 3. Understand context
Before auditing, read the files in scope to understand the stack, auth model, and data flow. Security issues are context-dependent — know what you're looking at first.

### 4. Audit for the following

**Secrets & credentials**
- Hardcoded API keys, tokens, passwords, or private keys
- Secrets committed to version control or logged
- `.env` values referenced directly in source

**Injection**
- SQL injection (string concatenation in queries)
- Command injection (unsanitised input passed to shell/exec)
- Template injection
- XSS (unescaped user input rendered in HTML)

**Authentication & authorisation**
- Missing or bypassable auth checks
- Insecure session handling (weak tokens, no expiry, no invalidation)
- Privilege escalation paths
- JWT issues (alg:none, missing verification)

**Data exposure**
- Sensitive data in logs, error messages, or API responses
- PII or credentials in URLs or query params
- Stack traces or internal details exposed to end users

**Insecure defaults & configuration**
- CORS misconfiguration (wildcard origins with credentials)
- Missing security headers (CSP, HSTS, X-Frame-Options)
- Debug mode or verbose errors enabled in production paths
- Insecure cookie flags (missing HttpOnly, Secure, SameSite)

**Dependency risks**
- Use of known-vulnerable package versions (flag if spotted in code, but do not run a full dep scan — that's `/deps-audit`)

**Cryptography**
- Weak or broken algorithms (MD5, SHA1 for passwords, ECB mode)
- Hardcoded IV or salt
- Insecure random number generation for security-sensitive operations

### 5. Dump all findings as an overview

Present all findings at once in a summary table:

```
## Security Audit — N findings

#  Severity  Category          File:Line                        Summary
1  Critical  Secrets           src/config.ts:14                 Hardcoded API key
2  High      Injection         src/api/search.ts:88             SQL built with string concat
3  High      Auth              src/middleware/auth.ts:32        JWT signature not verified
4  Medium    Data Exposure     src/api/users.ts:61              Password hash in API response
5  Low       Config            src/server.ts:9                  Missing HSTS header
```

Severity guide:
- **Critical** — exploitable now, likely leads to data breach or full compromise
- **High** — serious risk, should be fixed before merging or deploying
- **Medium** — notable vulnerability, fix soon
- **Low** — hardening improvement, defence-in-depth

After the table, ask: _"Ready to go through these one by one? (y/n)"_

### 6. One-by-one session

If the user says `y`, walk through each finding individually:

```
── Finding #N ─────────────────────────────
Severity: <Critical | High | Medium | Low>
File: <path>:<line>
Category: <category>

<Detailed description of the vulnerability and how it could be exploited>

Fix:
<Specific, actionable remediation>
───────────────────────────────────────────
Create a GitHub issue for this? (y/n)
```

Wait for the user's response before showing the next finding.

If the user responds `y`, invoke the `/pm-spec` sub-skill to create a GitHub issue on the current milestone. Confirm the issue number before continuing.

### 7. Summary
After all findings:

```
## Audit complete
N findings  (N critical, N high, N medium, N low)
Y issues created: #<numbers>
```

If no findings: _"No security issues found."_
