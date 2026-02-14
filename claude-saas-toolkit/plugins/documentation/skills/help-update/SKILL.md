---
# ============================================================================
# SKILL: /help-update
# ============================================================================
# PURPOSE:
#   Update the in-app help system when user-facing features change.
#   Handles article creation/editing, navigation updates, index registration,
#   and the "New Features in This Version" changelog section.
#
# WHEN TO USE:
#   - A new user-facing feature was added
#   - UI terminology, labels, or navigation changed
#   - Feature behavior changed in a way that affects user instructions
#   - Also invoked as part of /shipit when user-facing changes are detected
#
# SCOPE:
#   Cross-project. Adapts to the project's help system structure.
#   Reads CLAUDE.md to find help system paths and conventions.
#   If no help system exists, reports that and exits.
#
# INVOCATION EXAMPLES:
#   /help-update "Added bulk delete for appeals"
#   /help-update "Calendar view now supports drag-to-reschedule"
#   /help-update "Renamed 'Packages' to 'Donor Segments' throughout UI"
#
# ARGUMENTS:
#   $ARGUMENTS = description of what changed (used to guide the updates)
# ============================================================================
name: help-update
description: >
  Update the in-app help system with new or changed features. Edits articles,
  navigation, and the New Features changelog section.
argument-hint: <feature-description>
---

# Help Update — Maintain In-App Documentation

Update the help system to reflect: `$ARGUMENTS`

---

## Step 1: Find the Help System

Look for the help system in common locations:

1. `lib/help/` — articles, navigation, types, search
2. `app/help/` — help page component
3. Check `CLAUDE.md` for project-specific help system paths

If no help system is found, inform the user and exit.

---

## Step 2: Understand Current State

1. **Read the article index** (e.g., `lib/help/articles/index.ts`) to see
   all existing articles and their categories.
2. **Read the navigation** (e.g., `lib/help/navigation.ts`) to understand
   the sidebar structure.
3. **Read the help page** (e.g., `app/help/page.tsx`) to find the
   "New Features" section format.

---

## Step 3: Determine What Needs Updating

Based on `$ARGUMENTS`:

### If updating an existing article:
1. Find the article that covers the changed feature.
2. Edit the relevant section(s) to reflect the new behavior.
3. Update tags if new search terms are relevant.

### If creating a new article:
1. Create a new file in `lib/help/articles/<slug>.ts` using the project's
   `HelpArticle` interface pattern.
2. Export it from `lib/help/articles/index.ts` and add to `allArticles`.
3. Add it to `lib/help/navigation.ts` in the appropriate group.

### If terminology changed:
1. Search all article files for the old term.
2. Replace with the new term throughout.
3. Update tags and descriptions.

---

## Step 4: Update "New Features in This Version"

Prepend a new entry to the "New Features in This Version" section in the
help page component. Follow the project's existing format — typically:

```tsx
<p className="text-gray-700 leading-relaxed mb-5">[Today's date]</p>
<ul className="space-y-4">
  <li className="flex items-start gap-3">
    <span className="mt-1.5 h-1.5 w-1.5 rounded-full bg-brand-500 shrink-0" />
    <div>
      <Link href="/help/[article-slug]#[section-id]" className="font-semibold ...">
        Feature Title
      </Link>
      <p className="text-gray-700 leading-relaxed mt-0.5">
        Description of what's new.
      </p>
    </div>
  </li>
</ul>
```

Adapt to the actual format found in Step 2.

---

## Step 5: Confirm

Report what was updated:

```
## Help Guide Updated

### Articles modified
- [article-slug]: [what changed]

### Articles created
- [article-slug]: [title and category]

### Navigation
- [Updated | No changes needed]

### New Features entry
- Added: "[feature title]" (linked to [article#section])
```
