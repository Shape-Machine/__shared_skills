---
name: pm-perf
description: Identify performance optimisation opportunities in the codebase and create GitHub issues for approved findings on the current milestone.
argument-hint: [focus-area]
---

# /pm-perf

Identify performance optimisation opportunities in the codebase, then create GitHub issues for approved findings on the current milestone.

Usage: `/pm-perf` or `/pm-perf <optional focus area>`

## Steps

### 1. Verify git repo
Check that the current working directory is a git repository with a remote. If not, abort with a clear message.

Detect the GitHub repo from `git remote get-url origin`.

### 2. Understand the product
Read the README and briefly scan the codebase structure to understand the stack and architecture.

If `$ARGUMENTS` were passed, treat them as a focus area (e.g. "API response times", "frontend bundle", "database queries") and restrict the review to that area.

### 3. Scan for performance issues

Review the relevant code across the following dimensions:

**Frontend**
- Large or unoptimised assets (images, fonts, icons)
- Missing lazy loading for routes, components, or images
- Unnecessary re-renders or missing memoisation
- Blocking scripts or render-blocking resources
- Bundle size concerns (large dependencies, no code splitting)

**Data fetching**
- N+1 queries or waterfall fetches
- Missing pagination on large lists
- No caching on expensive or repeated requests
- Fetching more data than needed (over-fetching)

**Backend / API**
- Unindexed queries or missing database indexes
- Synchronous operations that should be async or queued
- Heavy computation in request handlers
- No rate limiting or debouncing on expensive endpoints

**General**
- Missing memoisation of expensive computations
- Repeated work that could be cached or deduplicated
- Operations that block the UI thread unnecessarily

### 4. Present findings
Group findings by dimension. For each finding:

```
### <Short title>
**Where:** <file path or code location>
**Issue:** <what the performance problem is>
**Impact:** <who is affected and how — e.g. "slow on mobile", "blocks page load">
**Suggestion:** <what to do about it>
```

Ask: _"Which of these would you like to create as GitHub issues? (e.g. 'all', '1 3 5', or 'none')"_

Wait for the user's response.

### 5. Create issues
For each approved finding, invoke the `/pm-spec` sub-skill to create a well-formed GitHub issue on the current milestone.

### 6. Summary
List the created issue numbers and titles.
