---
name: shipit
description: >
  Close out a coding session — commit, push, update roadmap, parking lot,
  help docs, and summarize. Use when the user says "shipit" or "ship it".
argument-hint: [quick]
disable-model-invocation: true
---

# Ship It — Session Closing Checklist

> *"I just shipped my pants!" — Kmart, 2013*

When this skill is invoked, execute **every step below in order**.
Do not skip steps unless `$ARGUMENTS` is "quick" (see Quick Mode below).

---

## Quick Mode

If `$ARGUMENTS` contains "quick", perform **only Steps 1 and 6** (commit/push
and summarize). Skip roadmap, parking lot, and help guide updates.

---

## Step 1: Commit and Push

Leave no uncommitted diffs behind.

1. Run `git status` to see all staged, unstaged, and untracked changes.
2. Stage all relevant files. Be deliberate — add files by name, not `git add .`.
   - **Never commit** `.env`, credentials, or secrets.
3. Write a clear, concise commit message summarizing the session's work.
4. Push to the current working branch (use `git push -u origin <branch>`).
5. If push fails due to network errors, retry up to 4 times with exponential
   backoff (2s, 4s, 8s, 16s).

---

## Step 2: Update the Roadmap

Read `docs/ROADMAP.md` and update it to reflect the session's outcomes:

- **Completed items** — Mark with `[x]` or add `✅ COMPLETED` label.
- **Partially completed items** — Note progress inline, e.g.,
  `(partially complete — segments UI done, metrics pending)`.
- **Invalidated items** — Strike or note why the item is no longer needed.
  Do NOT silently delete items.
- **New planned work** — If the session surfaced work not yet on the roadmap,
  add it to the appropriate phase using existing formatting (headings,
  checkboxes, bold labels).
- **Update the `Last Updated` date** at the top of the file.

If `docs/ROADMAP.md` does not exist, skip this step and note it in the summary.

---

## Step 3: Update the Parking Lot

Read `docs/PARKING_LOT.md` and append any ideas, bugs, or improvements
discovered during the session that were **not addressed**.

**Format:** A markdown table with three columns:

```
| Category | Related Area | Item |
```

**Valid categories:** `bug`, `performance`, `feature`, `ux`, `devex`,
`security`, `debt`

Keep each entry to a single line. If an item was already in the parking lot
and was resolved during this session, **remove it**.

If `docs/PARKING_LOT.md` does not exist, skip this step and note it in
the summary.

---

## Step 4: Update the Help Guide (if applicable)

This step applies only if the session changed user-facing behavior:
- New views, buttons, workflows, or features
- Changed UI terminology, labels, or navigation
- Modified feature behavior that affects user instructions

If any of the above apply:

1. **Article content** — Edit or create files in `lib/help/articles/`.
   Each article is a TypeScript object with `slug`, `title`, `description`,
   `category`, `tags`, and `sections[]`.
2. **Navigation** — Update `lib/help/navigation.ts` if new articles or
   sections were added.
3. **Article index** — If a new article was created, export it from
   `lib/help/articles/index.ts` and add to the `allArticles` array.
4. **"New Features in This Version"** — Prepend a new entry to the
   collapsible section in `app/help/page.tsx`. Include the date and a
   linked description. Follow the existing list-item format.

If no user-facing changes were made, or no help system exists, skip this step.

---

## Step 5: Final Commit and Push

If Steps 2–4 produced changes:

1. Stage the updated documentation files.
2. Commit with a message like: `docs: update roadmap, parking lot, and help guide`
3. Push again to the working branch.

---

## Step 6: Summarize

Deliver a summary to the user. Keep it tight — think PR description, not novel.

**Format:**

```
## Session Summary

### What shipped
- [Bullet list of completed work]

### Roadmap changes
- [What was marked complete, added, or modified]

### Parked for later
- [Items added to the parking lot, or "Nothing parked"]

### Help guide
- [What was updated, or "No user-facing changes"]

### Branch
- `<branch-name>` pushed to origin
```
