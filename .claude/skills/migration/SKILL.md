---
name: migration
description: >
  Create a new database migration file following project conventions
  (idempotent, multi-org safe, with verification).
argument-hint: <action_description>
---

# Migration — Create a Database Migration File

Generate a new SQL migration at `database/migrations/$ARGUMENTS.sql`.

---

## Before Creating

1. **Find the migrations directory** — Check `database/migrations/`, `supabase/migrations/`,
   or whatever path the project uses. Read CLAUDE.md if unsure.

2. **Read 1-2 existing migrations** to match the project's exact style,
   comment conventions, and patterns.

3. **Understand the schema** — Read `database/schema.sql` or relevant
   existing migrations to know current table structures.

---

## Migration Template

Generate the file with this structure. Adapt to the specific change needed.

```sql
-- Migration: [Human-readable title of the change]
-- Description: [What this migration does and why]
-- Date: [Today's date]
--
-- SAFETY:
--   - Idempotent: safe to run multiple times
--   - Multi-org: applies to all organizations where applicable
--   - Non-destructive: does not drop data

DO $$
DECLARE
  -- Declare variables for tracking progress
  v_count INTEGER := 0;
BEGIN
  RAISE NOTICE 'Starting migration: [title]...';

  -- =========================================================================
  -- Step 1: [Description of first change]
  -- =========================================================================

  -- Check if the change has already been applied (idempotent guard)
  IF NOT EXISTS (
    SELECT 1 FROM information_schema.columns
    WHERE table_name = 'target_table'
    AND column_name = 'new_column'
  ) THEN
    ALTER TABLE target_table
    ADD COLUMN new_column TYPE DEFAULT value;

    RAISE NOTICE 'Added column: target_table.new_column';
    v_count := v_count + 1;
  ELSE
    RAISE NOTICE 'Column already exists: target_table.new_column (skipping)';
  END IF;

  -- =========================================================================
  -- Step 2: [Description of second change, if any]
  -- =========================================================================

  -- For multi-org changes, loop through organizations:
  -- FOR v_org_id IN SELECT id FROM organizations LOOP
  --   IF NOT EXISTS (...) THEN
  --     INSERT INTO ...
  --     v_count := v_count + 1;
  --   END IF;
  -- END LOOP;

  RAISE NOTICE 'Migration complete. Changes applied: %', v_count;

END $$;

-- =========================================================================
-- Verification: Confirm the migration was applied correctly
-- =========================================================================
SELECT column_name, data_type, column_default
FROM information_schema.columns
WHERE table_name = 'target_table'
ORDER BY ordinal_position;
```

---

## Key Conventions

- **Always idempotent** — Use `IF NOT EXISTS` guards so the migration can
  be safely re-run without errors.
- **Multi-org safe** — If adding config/seed data, loop through all
  organizations. Include `organization_id` in all data operations.
- **RAISE NOTICE** — Log progress for visibility when running manually.
- **Count changes** — Track how many modifications were made.
- **Verification query** — End with a SELECT that confirms the change.
- **Comment blocks** — Separate logical steps with `===` divider comments.
- **No destructive operations without confirmation** — If the migration
  drops columns or tables, add a prominent warning comment and ask the
  user to confirm before creating the file.

---

## After Creating

1. Tell the user the file was created and its path.
2. Remind them to review the SQL before running.
3. Mention the command to apply it (e.g., `npm run db:push` or
   running it directly via Supabase SQL editor).
