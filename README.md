# Claude SaaS Toolkit

Private plugin marketplace for Claude Code. Contains reusable skills for
multi-tenant SaaS development across all GivingThree projects.

## Table of Contents

- [Setup](#setup)
- [Directory](#directory)
- [Plugins](#plugins)
- [Project Structure](#project-structure)
- [Adding a New Skill](#adding-a-new-skill)
- [Adding a New Plugin](#adding-a-new-plugin)
- [Versioning](#versioning)

## Setup

```bash
# Add this marketplace (one-time per machine)
/plugin marketplace add GivingThree/claude-saas-toolkit

# Install plugins
/plugin install sesh
/plugin install docs
/plugin install code-quality
/plugin install scaffolding
/plugin install hooks
```

## Directory

| Short | Qualified | Purpose |
|-------|-----------|---------|
| `/pants` | `/sesh:pants` | Start a coding session — load context, confirm branch, present a briefing. One leg at a time. |
| `/shipit` | `/sesh:shipit` | Close a session — commit, push, update roadmap, parking lot, help docs, and summarize. |
| `/park` | `/docs:park` | Quick-add an item to `docs/PARKING_LOT.md` without breaking flow. |
| `/help` | `/docs:help` | Update the in-app help system when user-facing features change. |
| `/security-review` | `/code-quality:security-review` | Multi-tenant security audit checklist. |
| `/pr-ready` | `/code-quality:pr-ready` | Pre-PR checklist — lint, build, docs, draft description. |
| `/api-scaffold` | `/scaffolding:api-scaffold` | Generate an API route with auth, tenancy, and logging. |
| `/migration` | `/scaffolding:migration` | Generate an idempotent database migration. |
| `/session-start-hook` | `/hooks:session-start-hook` | Create a SessionStart hook script for remote web sessions (dependency installs, env config). |

## Plugins

### sesh
Session start and close workflows.

- `/pants` — Start a session with full context briefing
- `/shipit` — Close a session cleanly and summarize what shipped

### docs
Documentation maintenance helpers.

- `/park` — Quick-add items to `docs/PARKING_LOT.md`
- `/help` — Update in-app help when features change

### code-quality
Code review and security auditing.

- `/security-review` — Multi-tenant security audit checklist
- `/pr-ready` — Pre-PR checklist — lint, build, docs, draft description

### scaffolding
Code generation for Next.js + Supabase projects.

- `/api-scaffold` — Generate API route with auth, tenancy, logging
- `/migration` — Generate idempotent database migration

### hooks
Claude Code environment hooks.

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
├── .claude-plugin/
│   └── marketplace.json        # Plugin catalog
├── plugins/
│   ├── sesh/                   # /pants, /shipit
│   ├── docs/                   # /park, /help
│   ├── code-quality/           # /security-review, /pr-ready
│   ├── scaffolding/            # /api-scaffold, /migration
│   └── hooks/                  # /session-start-hook
└── README.md
```

Each plugin directory contains:
```
plugin-name/
├── .claude-plugin/
│   └── plugin.json             # Name, version, description
└── skills/
    └── skill-name/
        └── SKILL.md            # Skill instructions
```

## Adding a New Skill

1. Choose which plugin it belongs to (or create a new plugin directory)
2. Create the skill directory: `plugins/<plugin>/skills/<skill-name>/`
3. Write `SKILL.md` with YAML frontmatter + markdown instructions
4. If creating a new plugin, add `plugin.json` and update `marketplace.json`
5. Commit, push, and run `/plugin marketplace update` in your projects

## Adding a New Plugin

```bash
# 1. Create the directory structure
mkdir -p plugins/my-plugin/.claude-plugin
mkdir -p plugins/my-plugin/skills/my-skill

# 2. Create plugin.json
cat > plugins/my-plugin/.claude-plugin/plugin.json << 'EOF'
{
  "name": "my-plugin",
  "description": "What this plugin does",
  "version": "1.0.0",
  "author": { "name": "GivingThree" },
  "license": "UNLICENSED"
}
EOF

# 3. Create your SKILL.md (see existing skills for format)

# 4. Add to marketplace.json in .claude-plugin/
# 5. Commit and push
```

## Versioning

Bump the `version` field in the relevant `plugin.json` when making changes.
Follow semver: patch for fixes, minor for new skills, major for breaking changes.
