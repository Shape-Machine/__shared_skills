---
name: doc-features
description: Generate or update docs/features.md with a clean, CEO-readable overview of all product features derived from the codebase. Runs on main only.
---

# /doc-features

Generate or update `docs/features.md` — a clean, CEO-readable overview of all product features, derived from the codebase.

Usage: `/doc-features`

## Steps

### 1. Verify git repo and branch
Check that the current working directory is a git repository. If not, abort.

Check the current branch:
```
git branch --show-current
```

If not on `main`, abort: _"This skill must be run on the main branch."_

### 2. Scan the codebase for features
Read the codebase to build a comprehensive picture of what the product does. Focus on:
- Route definitions and API endpoints
- UI screens, pages, and components
- Core domain models and entities
- Key user-facing actions and workflows
- Configuration options and integrations

Do not inventory every file — focus on what a user or stakeholder would recognise as a feature. Ignore internal infrastructure, build tooling, and test code.

### 3. Derive and categorise features
Group features into 4–8 themes based on the product's natural domains (e.g. Authentication, Dashboard, Billing, Integrations). Use your judgment — themes should reflect how a user thinks about the product, not how the code is organised.

For each feature, write one concise bullet: what it does, from the user's perspective. No implementation details. No jargon.

Writing style:
- Minimal, precise, complete
- Active voice, present tense
- Audience is the CEO — assume intelligence, not technical depth
- Each bullet should stand alone in one line where possible

### 4. Generate the new content

Structure:
```markdown
# Features

_Last updated: <YYYY-MM-DD>_

## <Theme>
- <Feature bullet>
- <Feature bullet>

## <Theme>
- <Feature bullet>
```

No preamble, no introduction paragraph, no closing remarks. Just the content.

### 5. Compare with existing file
Check if `docs/features.md` exists.

- **If it does not exist:** show the generated content and ask: _"docs/features.md does not exist. Create it? (y/n)"_
- **If it exists:** show a diff between the current file and the newly generated content:
  ```
  git diff --no-index docs/features.md <new content>
  ```
  Then ask: _"Replace docs/features.md with the above? (y/n)"_

Wait for the user's response before writing anything.

### 6. Write the file
If the user confirms, write the generated content to `docs/features.md`.

Then stage and commit:
```
git add docs/features.md
git commit -m "Update docs/features.md"
```

Inform the user the file has been written and committed.
