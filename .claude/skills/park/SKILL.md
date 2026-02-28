---
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
