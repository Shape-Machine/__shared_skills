# __shared_skills

Reusable Claude / agent skills shared across projects.

## TL;DR Installation

### Claude Code

**Global (available in all projects):**
```bash
# Clone this repo
git clone git@github.com:Shape-Machine/__shared_skills.git ~/.claude/__shared_skills

# Symlink skills into your global Claude skills directory
ln -s ~/.claude/__shared_skills/skills/* ~/.claude/skills/
```

**Per-project:**
```bash
# From inside your project root
ln -s path/to/__shared_skills/skills/* .claude/skills/
```

Claude Code picks up skills automatically from `~/.claude/skills/` (global) or `.claude/skills/` (project). Each skill is a directory containing a `SKILL.md` file.

---

### Codex CLI

**Global:**
```bash
# Symlink or copy skills into Codex's instructions directory
ln -s path/to/__shared_skills/skills/* ~/.codex/instructions/
```

**Per-project:**
```bash
ln -s path/to/__shared_skills/skills/* .codex/instructions/
```

Codex picks up `.md` instruction files from `~/.codex/instructions/` (global) or `.codex/instructions/` (project).

---

## Structure

Skills live in `skills/<name>/SKILL.md` and are prefixed by category:

| Skill | Description |
|---|---|
| `/dev-milestone` | Show open/closed issues for the current milestone, grouped by title similarity |
| `/dev-impl` | Read issues, review code, propose an impl plan, then implement on a feature branch |
| `/dev-pr` | Draft and create a PR for the current feature branch |
| `/dev-pr-merged` | Switch to main, pull, verify PR is merged, delete local feature branch |
| `/dev-review` | Review uncommitted changes or branch diff vs main — severity-scored findings, optional issue creation |
| `/sec-audit` | Security audit of diff, branch, or full source (on main) — overview then one-by-one session |
| `/pm-features` | Suggest new features for the ICP, create approved ones as milestone issues |
| `/pm-review` | Audit UX, copy, missing states, onboarding, and product debt — create issues |
| `/pm-perf` | Identify performance opportunities — create approved ones as milestone issues |
| `/pm-spec` | _(internal)_ Turn a feature/finding into a well-formed GitHub issue |
| `/doc-features` | Generate or update `docs/features.md` — CEO-readable feature overview from the codebase (main only) |

Add new skills as `skills/<prefix>-<name>/SKILL.md` with YAML frontmatter. Follow existing conventions.
