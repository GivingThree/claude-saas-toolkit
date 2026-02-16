# Claude SaaS Toolkit

Reusable Claude Code skills for multi-tenant SaaS development across
GivingThree projects.

## Setup

Clone this repo and copy the `.claude/skills/` directory into any project:

```bash
# From your target project root
cp -r /path/to/claude-saas-toolkit/.claude/skills/ .claude/skills/
```

Or add individual skills:

```bash
mkdir -p .claude/skills/pants
cp /path/to/claude-saas-toolkit/.claude/skills/pants/SKILL.md .claude/skills/pants/
```

Once `.claude/skills/<name>/SKILL.md` exists in a repo, Claude Code
automatically discovers it as a `/name` slash command.

For skills available across all your projects (not just one repo), copy them
to `~/.claude/skills/` instead.

## Directory

| Skill | Purpose |
|-------|---------|
| `/pants` | Start a coding session — load context, confirm branch, present a briefing. |
| `/shipit` | Close a session — commit, push, update roadmap, parking lot, help docs, and summarize. |
| `/park` | Quick-add an item to `docs/PARKING_LOT.md` without breaking flow. |
| `/help` | Update the in-app help system when user-facing features change. |
| `/security-review` | Multi-tenant security audit checklist. |
| `/pr-ready` | Pre-PR checklist — lint, build, docs, draft description. |
| `/api-scaffold` | Generate an API route with auth, tenancy, and logging. |
| `/migration` | Generate an idempotent database migration. |
| `/session-start-hook` | Create a SessionStart hook script for remote web sessions. |

## Skill Groups

### Session workflows
- `/pants` — Start a session with full context briefing
- `/shipit` — Close a session cleanly and summarize what shipped

### Documentation
- `/park` — Quick-add items to `docs/PARKING_LOT.md`
- `/help` — Update in-app help when features change

### Code quality
- `/security-review` — Multi-tenant security audit checklist
- `/pr-ready` — Pre-PR checklist — lint, build, docs, draft description

### Scaffolding
- `/api-scaffold` — Generate API route with auth, tenancy, logging
- `/migration` — Generate idempotent database migration

### Hooks
- `/session-start-hook` — Set up SessionStart hooks for web sessions

> **How is `/session-start-hook` different from `/pants`?**
> `/pants` is a conversational briefing — it reads your roadmap, confirms your
> branch, and presents context so you can start coding. `/session-start-hook`
> creates an actual shell script that runs automatically when a remote session
> boots — installing dependencies, configuring the environment, and ensuring
> linters and tests work before you even type a prompt.

## Project Structure

```
claude-saas-toolkit/
├── .claude/
│   └── skills/
│       ├── pants/SKILL.md
│       ├── shipit/SKILL.md
│       ├── park/SKILL.md
│       ├── help/SKILL.md
│       ├── security-review/SKILL.md
│       ├── pr-ready/SKILL.md
│       ├── api-scaffold/SKILL.md
│       ├── migration/SKILL.md
│       └── session-start-hook/SKILL.md
└── README.md
```

## Adding a New Skill

1. Create a directory under `.claude/skills/`:
   ```bash
   mkdir -p .claude/skills/my-skill
   ```
2. Write `.claude/skills/my-skill/SKILL.md` with YAML frontmatter and
   markdown instructions (see existing skills for the format).
3. Commit and push — the skill is now available as `/my-skill` in any
   checkout of this repo.
