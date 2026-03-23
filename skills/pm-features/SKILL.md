---
name: pm-features
description: Suggest new features tailored to the product's ICP/persona and create GitHub issues for approved suggestions on the current milestone.
argument-hint: [persona-hint]
---

# /pm-features

Suggest new features tailored to the product's ICP/persona, then create GitHub issues for the approved suggestions on the current milestone.

Usage: `/pm-features` or `/pm-features <optional instructions or persona hint>`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin`.

### 2. Understand the product
Read available context to build a picture of what the product does:
- `README.md` and any docs
- Existing open/closed GitHub issues and their themes
- The codebase structure (top-level dirs, key files) — do not do a deep scan, just orient yourself

### 3. Resolve the ICP / persona
- If `$ARGUMENTS` were passed, use them as the persona/ICP description or additional instructions.
- Otherwise, try to infer the ICP from the product context gathered above.
- If the ICP is genuinely unclear, ask the user: _"Who is the primary user of this product? (e.g. role, industry, goals)"_ — wait for their answer before continuing.

### 4. Generate feature suggestions
Produce 5–8 feature suggestions grounded in the ICP's goals and pain points. For each:

```
### <Feature Title>
**Why:** <the user problem or opportunity this addresses>
**Value:** <what the user gains>
**Scope hint:** <rough sense of effort — small / medium / large>
```

Do not suggest features that are already covered by open issues in the repo.

### 5. Get approval
Present the suggestions and ask: _"Which of these would you like to create as GitHub issues? (e.g. 'all', '1 3 5', or 'none')"_

Wait for the user's response.

### 6. Create issues
For each approved suggestion, invoke the `/pm-spec` sub-skill to create a well-formed GitHub issue on the current milestone.

### 7. Summary
List the created issue numbers and titles.
