---
name: claude-md
description: >
  Build or update a repo's CLAUDE.md — the project context file that makes
  Claude Code sessions effective and consistent.
argument-hint: "[audit | section <name>]"
disable-model-invocation: true
---

# CLAUDE.md — Build & Maintain Project Context

Generate or update the `CLAUDE.md` file for this repository. A good CLAUDE.md
is the single highest-leverage thing you can do for Claude Code — it turns
every session from cold-start guessing into informed, consistent work.

---

## Mode Selection

Check `$ARGUMENTS`:

- **No arguments** — Full create/update (all steps below)
- **`audit`** — Read the existing `CLAUDE.md` and report gaps, stale info,
  and missing sections. Do not modify the file. Present findings as a
  checklist the user can act on.
- **`section <name>`** — Regenerate only the named section (e.g.,
  `section tech-stack`, `section commands`, `section architecture`).
  Read the existing file, replace that section, preserve everything else.

---

## Step 1: Analyze the Codebase

Read and scan these sources to build a mental model of the project. Skip any
that don't exist — not every project has all of these.

### 1a. Project metadata
- `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Gemfile`,
  `composer.json`, or equivalent — identify framework, language, dependencies
- `tsconfig.json` / `jsconfig.json` — strict mode, path aliases
- `.nvmrc`, `.node-version`, `.python-version`, `.tool-versions` — runtime versions

### 1b. Configuration
- `next.config.*`, `vite.config.*`, `webpack.config.*` — framework config
- `tailwind.config.*` — custom theme, plugins, safelisted classes
- `.eslintrc*`, `.prettierrc*`, `biome.json` — linting/formatting setup
- `jest.config.*`, `vitest.config.*`, `playwright.config.*` — test config
- `docker-compose.yml`, `Dockerfile` — containerization

### 1c. Structure
- Run `ls` on the project root and key directories (`app/`, `src/`, `lib/`,
  `components/`, `api/`, `database/`, `scripts/`, `docs/`)
- Identify the routing pattern (App Router, Pages Router, file-based, etc.)

### 1d. Auth & data
- Scan for auth files (`middleware.ts`, `lib/auth/`, `auth/`, etc.)
- Identify database (Supabase, Prisma, Drizzle, raw SQL, etc.)
- Check for multi-tenancy patterns (`organization_id`, tenant isolation, RLS)

### 1e. Existing docs
- Read `CLAUDE.md` if it exists (update mode vs. create mode)
- Read `README.md` for project description and setup instructions
- Check for `docs/ROADMAP.md`, `docs/PARKING_LOT.md`
- Check for `CONTRIBUTING.md`, `ARCHITECTURE.md`, or similar

### 1f. Commands
- Extract scripts from `package.json` (or equivalent)
- Identify which test runner is configured (if any)
- Check for a Makefile, Justfile, or task runner

---

## Step 2: Draft the CLAUDE.md

Use the structure below as the template. Every section is important, but
adapt to what the project actually has — don't invent sections for things
that don't exist. A lean, accurate CLAUDE.md beats a padded one.

### Guiding Principles

1. **Specificity over generality.** Don't say "follow best practices" — say
   which linter runs, what the import convention is, which auth function
   every API route must call.

2. **Actionable over descriptive.** A session guideline should tell Claude
   *what to do*, not *what the philosophy is*.

3. **Current over aspirational.** Document what the project *is*, not what
   you wish it were. If there are no tests, say "No test framework
   configured" — don't add a testing section with hypothetical commands.

4. **Concrete examples.** When documenting patterns (API routes, auth flow,
   state management), reference actual files in the repo so Claude can read
   them for the full picture.

5. **Structured for scanning.** Use tables for tech stacks, roles, and env
   vars. Use trees for project structure. Use numbered steps for workflows.
   Claude parses structured content faster and more reliably than prose.

---

## CLAUDE.md Template

```markdown
# CLAUDE.md — <Project Name>

This file provides guidance for AI assistants working with this codebase.

## Project Overview

<1-3 sentences: what the app does, who it's for, domain-specific terminology.>

## Tech Stack

| Layer | Technology |
|-------|------------|
| Framework | <e.g., Next.js 16 (App Router)> |
| Language | <e.g., TypeScript 5.x (strict mode)> |
| UI | <e.g., React 19, Tailwind CSS 4> |
| State | <e.g., Zustand> |
| Database | <e.g., PostgreSQL via Supabase (Row Level Security)> |
| Auth | <e.g., Supabase Auth + Portal JWT> |
| Deployment | <e.g., Vercel> |
| Package Manager | <e.g., npm> |
<Add or remove rows based on what the project actually uses.>

## Project Structure

<ASCII tree of the key directories. Focus on what's useful for navigation —
don't list every file, but do annotate directories with their purpose.
Call out any unusually large files by name.>

## Commands

```bash
<command>    # <what it does>
<command>    # <what it does>
```

<Note if there's no test framework, no formatter, etc. — absence of tooling
is just as important to document as presence.>

## Architecture Patterns

<This is the most important section for multi-file consistency. Document the
patterns that every new piece of code must follow. Common subsections:>

### Authentication
<How auth works. Which function to call. Where it lives. Dual systems if any.>

### Roles & Permissions
<Table of roles, their scopes, and what they can do. Reference the permission
helpers by name.>

### Multi-Tenancy
<How tenant isolation works. Which column. RLS policies. What every query
must include.>

### API Route Pattern
<Numbered steps every API route follows: auth, permissions, query, log, respond.
Reference a real route file as the canonical example.>

### State Management
<What the store manages. Where it lives. Key patterns.>

### Database Schema
<Core entities and their relationships. Key views. Naming conventions.>

<Add domain-specific patterns: workflow stages, recurrence systems,
billing tiers, etc.>

## Environment Variables

### Required
```
VARIABLE_NAME    # description
```

### Optional
```
VARIABLE_NAME    # description
```

## Conventions

<Bullet list of project conventions — file naming, import style, component
patterns, testing status, large files to be aware of, migration conventions,
activity logging expectations, etc.>

---

## Session Guidelines

### Branch Discipline

- **One branch per issue.** Each session should work on a single, well-scoped
  problem. Do not mix unrelated fixes on the same branch.
- **Distinguish root cause from symptoms.** If debugging reveals a second,
  unrelated problem, note it in the parking lot rather than fixing it in-place.
- **Ask before expanding scope.** If a fix naturally leads to adjacent
  improvements, confirm with the user before adding work to the current branch.

### Before Starting Work

1. **Read `docs/ROADMAP.md`** to check whether the issue or feature is already
   tracked, which stage it belongs to, and whether related items exist.
2. **Read `docs/PARKING_LOT.md`** for any previously parked items that relate
   to the current task.
3. **Confirm the branch name** matches the pattern in the task description.
   Create it locally if it does not exist yet.

### During a Session

- Keep commits small and focused. Each commit should represent a single
  logical change.
- Mark completed work in the todo list immediately — do not batch completions.
- If you discover a bug, improvement idea, or technical debt unrelated to the
  current task, add it to `docs/PARKING_LOT.md` instead of fixing it on the spot.

### Parking Lot

The file `docs/PARKING_LOT.md` is a lightweight holding area for ideas, bugs,
and improvements discovered during sessions.

Format:

| Category | Related Area | Item |
|----------|--------------|------|

Categories: `bug`, `performance`, `feature`, `ux`, `devex`, `security`, `debt`

Keep entries to a single line. If an item graduates to the roadmap, remove it
from the parking lot.
```

---

## Step 3: Adapt to the Project

Before writing the file, review your draft against the actual codebase:

1. **Remove sections that don't apply.** No database? Drop the schema section.
   Single-user app? Drop multi-tenancy. No auth? Drop roles & permissions.

2. **Add domain-specific sections.** A fundraising app needs appeal types and
   production stages. An e-commerce app needs order lifecycle. A CLI tool
   needs argument parsing conventions. Let the project's domain drive the
   architecture section.

3. **Reference real files.** Instead of generic descriptions, point to actual
   files: "See `app/api/appeals/route.ts` for the canonical API route pattern."
   This lets Claude read the source of truth instead of relying on your summary.

4. **Match existing tone.** If the project already has docs with a particular
   voice (technical, casual, terse), match it. Consistency matters.

5. **Check for project-specific session workflows.** If the project uses
   `/shipit`, `/pants`, or custom closing rituals, include them in the session
   guidelines. If the project has an in-app help system that needs updating
   when features change, document that responsibility.

---

## Step 4: Write or Update

### Create mode (no existing CLAUDE.md)
- Write the complete file to `CLAUDE.md` at the project root.
- Tell the user what was generated and suggest they review each section.

### Update mode (CLAUDE.md exists)
- Read the existing file carefully.
- Preserve any custom content the user has added (custom sections, specific
  phrasing, project-specific rituals).
- Update factual content: tech stack versions, project structure changes,
  new architecture patterns, new commands, new env vars.
- Add missing sections from the template if the project has grown to need them.
- Call out what changed so the user can review the diff.

### Audit mode (`$ARGUMENTS` = "audit")
- Read the existing file and the codebase.
- Report a checklist of findings:
  - Sections present vs. missing
  - Stale information (outdated versions, removed files still referenced,
    deprecated patterns)
  - Gaps (auth pattern undocumented, env vars missing, new directories
    not in the structure tree)
  - Opportunities (patterns that would benefit from documentation)
- Do not modify the file.

### Section mode (`$ARGUMENTS` = "section <name>")
- Read the existing file.
- Regenerate only the named section using fresh codebase analysis.
- Preserve all other sections exactly as-is.
- Valid section names: `overview`, `tech-stack`, `structure`, `commands`,
  `architecture`, `env-vars`, `conventions`, `session-guidelines`

---

## Step 5: Verify

After writing or updating, do a quick sanity check:

1. **File references exist.** Every file path mentioned in CLAUDE.md should
   actually exist in the repo. Glob for any referenced paths.
2. **Commands work.** If you listed `npm run dev`, confirm the script exists
   in package.json.
3. **No secrets.** Env vars section should list variable *names* and
   descriptions, never actual values.
4. **Structure matches reality.** The project tree should reflect the current
   state of the repo, not a stale snapshot.

Report any discrepancies to the user.
