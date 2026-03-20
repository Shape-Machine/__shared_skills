# __shared_skills

Reusable Claude / agent skills shared across projects.

## TL;DR Installation

### Claude Code

**Global (available in all projects):**
```bash
# Clone this repo
git clone git@github.com:Shape-Machine/__shared_skills.git ~/.claude/__shared_skills

# Symlink skills into your global Claude commands directory
ln -s ~/.claude/__shared_skills/skills/* ~/.claude/commands/
```

**Per-project:**
```bash
# From inside your project root
ln -s path/to/__shared_skills/skills/* .claude/commands/
```

Slash commands are `.md` files — Claude Code picks them up automatically from `~/.claude/commands/` (global) or `.claude/commands/` (project).

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

Skills live in `skills/` as `.md` files and are prefixed by category:

| Skill | Description |
|---|---|
| `/dev-milestone` | Show open/closed issues for the current milestone, grouped by title similarity |
| `/dev-impl` | Read issues, review code, propose an impl plan, then implement on a feature branch |

Add new skills as `skills/<prefix>-<name>.md`. Follow existing conventions.
