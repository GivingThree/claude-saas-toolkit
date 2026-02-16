---
# ============================================================================
# SKILL: /api-scaffold
# ============================================================================
# PURPOSE:
#   Scaffold a new Next.js App Router API route following project conventions.
#   Generates GET and POST handlers with authentication, authorization,
#   multi-tenant filtering, activity logging, error handling, and TypeScript
#   types — all wired up and ready for business logic.
#
# WHEN TO USE:
#   - Creating a new REST API endpoint
#   - Adding CRUD operations for a new entity
#   - Expanding an existing resource with new sub-routes
#
# SCOPE:
#   Cross-project. Designed for Next.js 14+ App Router with Supabase.
#   Reads CLAUDE.md to adapt to each project's specific import paths,
#   auth function names, and database patterns.
#
# INVOCATION EXAMPLES:
#   /api-scaffold donations          — creates app/api/donations/route.ts
#   /api-scaffold donations/[id]     — creates app/api/donations/[id]/route.ts
#   /api-scaffold campaigns/[id]/goals — nested resource route
#
# ARGUMENTS:
#   $ARGUMENTS = the resource path relative to app/api/
# ============================================================================
name: api-scaffold
description: >
  Scaffold a new Next.js API route following project conventions (auth,
  permissions, multi-tenancy, activity logging).
argument-hint: <resource-path>
---

# API Scaffold — Generate a New API Route

Generate a fully-wired API route file at `app/api/$ARGUMENTS/route.ts`.

---

## Before Scaffolding

1. **Read `CLAUDE.md`** to identify:
   - Auth import path (e.g., `@/lib/auth/server`)
   - Auth function name (e.g., `getCurrentUser`)
   - Database client import (e.g., `createAdminClient` from `@/lib/auth/supabase.server`)
   - Activity logger import (e.g., `logActivity` from `@/lib/activity-log`)
   - Type import path (e.g., `Database` from `@/lib/auth/supabase.browser`)

2. **Read an existing API route** in the project (e.g., `app/api/appeals/route.ts`)
   to match the exact patterns, import style, and error handling conventions.

3. **Confirm the resource name** with the user if ambiguous. Infer the
   database table name from the resource path (e.g., `donations` -> `donations`
   table, `mission-items` -> `mission_items` table).

---

## Route Template

Generate the file with this structure. Adapt imports and patterns to match
what you found in Steps 1 and 2.

```typescript
import { NextRequest, NextResponse } from 'next/server';
import { createAdminClient } from '@/lib/auth/supabase.server';
import { getCurrentUser } from '@/lib/auth/server';
import { logActivity } from '@/lib/activity-log';

// =============================================================================
// GET /api/<resource>
// Fetch <resource> for the authenticated user's organization.
// =============================================================================
export async function GET(request: NextRequest) {
  // 1. Authenticate
  const user = await getCurrentUser();
  if (!user) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  const supabase = createAdminClient();

  try {
    // 2. Query with organization_id isolation
    const { data, error } = await supabase
      .from('<table_name>')
      .select('*')
      .eq('organization_id', user.organizationId)
      .order('created_at', { ascending: false });

    if (error) throw error;

    return NextResponse.json({ items: data });
  } catch (error: any) {
    console.error('[API /<resource> GET] Error:', error);
    return NextResponse.json(
      { error: 'Failed to fetch <resource>', details: error?.message },
      { status: 500 }
    );
  }
}

// =============================================================================
// POST /api/<resource>
// Create a new <resource> in the authenticated user's organization.
// =============================================================================
export async function POST(request: NextRequest) {
  // 1. Authenticate
  const user = await getCurrentUser();
  if (!user) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  // 2. Authorize — adjust roles as needed
  if (!['super_admin', 'consultant', 'org_admin', 'editor'].includes(user.role)) {
    return NextResponse.json({ error: 'Forbidden' }, { status: 403 });
  }

  const body = await request.json();
  const supabase = createAdminClient();

  try {
    // 3. Validate required fields
    if (!body.name) {
      return NextResponse.json(
        { error: 'Missing required fields: name' },
        { status: 400 }
      );
    }

    // 4. Insert with organization_id
    const { data: created, error } = await (supabase as any)
      .from('<table_name>')
      .insert({
        organization_id: user.organizationId,
        name: body.name,
        created_by: user.id,
      })
      .select()
      .single();

    if (error) {
      if (error.code === '23505') {
        return NextResponse.json(
          { error: 'A <resource> with this name already exists' },
          { status: 409 }
        );
      }
      throw error;
    }

    // 5. Log activity
    await logActivity(user, {
      action: 'create',
      entityType: '<entity_type>',
      entityId: created.id,
      entityName: created.name,
    });

    return NextResponse.json({ item: created }, { status: 201 });
  } catch (error: any) {
    console.error('[API /<resource> POST] Error:', error);
    return NextResponse.json(
      { error: 'Failed to create <resource>', details: error?.message },
      { status: 500 }
    );
  }
}
```

---

## After Scaffolding

1. Replace all `<resource>`, `<table_name>`, and `<entity_type>` placeholders
   with actual values.
2. Adjust the `select()` fields, required body fields, and insert payload
   to match the database schema.
3. Tell the user what was created and what they need to customize.

---

## If Creating a Detail Route (`[id]`)

Also scaffold PUT/PATCH and DELETE handlers following the same patterns:
- Verify the entity belongs to the user's organization before modifying
- Use `detectChanges()` from the activity logger for update operations
- Return 404 if the entity is not found (within the user's org)
