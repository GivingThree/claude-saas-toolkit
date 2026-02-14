---
# ============================================================================
# SKILL: /park
# ============================================================================
# PURPOSE:
#   Quickly add an item to docs/PARKING_LOT.md without breaking flow.
#   The parking lot is a lightweight holding area for bugs, ideas, and
#   improvements discovered during coding sessions that don't belong on
#   the current branch.
#
# WHEN TO USE:
#   - You discover a bug unrelated to the current task
#   - You spot a potential improvement or refactor opportunity
#   - A feature idea comes up mid-session
#   - You notice technical debt worth tracking
#
# SCOPE:
#   Cross-project. Works with any project that has docs/PARKING_LOT.md.
#
# INVOCATION EXAMPLES:
#   /park bug auth "Login fails silently when JWT expires mid-session"
#   /park feature calendar "Add drag-to-reschedule on month view"
#   /park debt api "Appeals route handler is 400+ lines, needs splitting"
#   /park ux settings "Unsaved changes warning missing on tab switch"
#
# ARGUMENTS:
#   $1 = category (bug|performance|feature|ux|devex|security|debt)
#   $2 = related area (e.g., auth, calendar, api, kanban, settings)
#   $3 = description (the item itself — one sentence)
#
# If the user provides a free-form description without structured args,
# parse it intelligently and assign the best-fit category and area.
# ============================================================================
name: park
description: >
  Add an item to docs/PARKING_LOT.md. Use when discovering bugs, improvements,
  or ideas unrelated to the current task.
argument-hint: <category> <area> <description>
---

# Park — Add Item to Parking Lot

Add an entry to `docs/PARKING_LOT.md` without disrupting the current workflow.

---

## Input Parsing

Arguments: `$ARGUMENTS`

**If structured** (3 parts): `$1` = category, `$2` = related area, `$3` = description.

**If free-form** (user typed a plain sentence): Infer the best-fit category and
related area from context. Use your understanding of the codebase to classify.

**Valid categories:** `bug`, `performance`, `feature`, `ux`, `devex`, `security`, `debt`

---

## Steps

1. **Read** `docs/PARKING_LOT.md` to get the current contents.

2. **Check for duplicates** — If a very similar item already exists, tell the
   user instead of adding a duplicate.

3. **Append** a new row to the markdown table:

   ```
   | category | Related Area | One-line description of the item |
   ```

4. **Save** the file. Do NOT commit — the item will be included in the next
   commit or picked up by `/shipit`.

5. **Confirm** to the user what was parked:

   ```
   Parked: [category] [area] — "description"
   ```

---

## Edge Cases

- If `docs/PARKING_LOT.md` does not exist, create it with the standard header:

  ```markdown
  # Parking Lot

  Lightweight holding area for ideas, bugs, and improvements discovered during
  sessions. Items live here until they merit promotion to the roadmap or are resolved.

  | Category | Related Area | Item |
  |----------|--------------|------|
  ```

- If the table header exists but has no rows, add the first row after the
  header separator line (`|----------|...`).

- Keep entries to a **single line**. If the user provides a long description,
  condense it.
