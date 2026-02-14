# Claude SaaS Toolkit

Private plugin marketplace for Claude Code. Contains reusable skills for
multi-tenant SaaS development across all GivingThree projects.

## Setup

```bash
# Add this marketplace (one-time per machine)
/plugin marketplace add GivingThree/claude-saas-toolkit

# Install plugins
/plugin install session-lifecycle
/plugin install documentation
/plugin install code-quality
/plugin install scaffolding
/plugin install hooks
```

## Plugins

### session-lifecycle
Session start and close workflows.

| Skill | Purpose |
|-------|---------|
| `/session-lifecycle:shipit` | Close a session — commit, push, update docs, summarize |
| `/session-lifecycle:session-kickoff` | Start a session — load context, confirm branch, brief |

### documentation
Documentation maintenance helpers.

| Skill | Purpose |
|-------|---------|
| `/documentation:park` | Quick-add items to `docs/PARKING_LOT.md` |
| `/documentation:help-update` | Update in-app help when features change |

### code-quality
Code review and security auditing.

| Skill | Purpose |
|-------|---------|
| `/code-quality:security-review` | Multi-tenant security audit checklist |
| `/code-quality:pr-ready` | Pre-PR checklist — lint, build, docs, draft description |

### scaffolding
Code generation for Next.js + Supabase projects.

| Skill | Purpose |
|-------|---------|
| `/scaffolding:api-scaffold` | Generate API route with auth, tenancy, logging |
| `/scaffolding:migration` | Generate idempotent database migration |

### hooks
Claude Code environment hooks.

| Skill | Purpose |
|-------|---------|
| `/hooks:session-start-hook` | Set up SessionStart hooks for web sessions |

## Project Structure

```
claude-saas-toolkit/
├── .claude-plugin/
│   └── marketplace.json        # Plugin catalog
├── plugins/
│   ├── session-lifecycle/      # /shipit, /session-kickoff
│   ├── documentation/          # /park, /help-update
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
