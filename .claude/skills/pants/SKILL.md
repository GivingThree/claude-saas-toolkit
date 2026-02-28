---
name: pants
description: >
  Start a coding session — read roadmap, parking lot, confirm branch, review
  context, and present a briefing. One leg at a time.
argument-hint: [task-description]
disable-model-invocation: true
---

# Pants — Start a Coding Session

Perform these steps in order to establish full context before coding begins.

---

## Step 1: Read Project Context

Read these files (skip any that don't exist):

1. `CLAUDE.md` — Project conventions, tech stack, architecture patterns
2. `docs/ROADMAP.md` — Current state of planned work, phases, priorities
3. `docs/PARKING_LOT.md` — Unresolved items from previous sessions

---

## Step 2: Confirm the Branch

1. Run `git status` to check the current branch and working tree state.
2. Run `git log --oneline -5` to see recent commits for context.
3. If there are uncommitted changes from a previous session, **alert the user**
   before proceeding. Do not discard them.
4. Confirm the branch name matches what's expected for the task. If the task
   description specifies a branch, switch to it (or create it).

---

## Step 3: Review Task Context

If `$ARGUMENTS` was provided, use it as the task description:

1. Search the roadmap for related items.
2. Search the parking lot for related items.
3. Identify which files are most likely to be touched.

If no arguments were provided, present the roadmap and parking lot summaries
and ask the user what they'd like to work on.

---

## Step 4: Present Briefing

Deliver a concise briefing:

```
## Session Briefing

### Branch
`<branch-name>` — [clean | N uncommitted changes]

### Task
<task description or "Awaiting direction">

### Roadmap Context
- [Relevant roadmap items, current phase, related work]

### Parked Items
- [Any parking lot items related to this task, or "None related"]

### Key Files
- [Files likely to be involved, based on task context]

### Ready to go?
[Any blockers, concerns, or suggestions before starting]
```
