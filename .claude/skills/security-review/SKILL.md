---
name: security-review
description: >
  Run a multi-tenant SaaS security review checklist. Use when reviewing API
  routes, database queries, or auth flows.
argument-hint: [path-or-scope]
allowed-tools: Read, Grep, Glob, Bash
context: fork
agent: Explore
---

# Security Review — Multi-Tenant SaaS Audit

Run a structured security audit. Check every item below and report findings.

---

## Scope

- If `$ARGUMENTS` is a file path or directory, audit only that scope.
- If `$ARGUMENTS` is `--full`, audit the entire `app/api/` directory tree.
- If no arguments, audit files changed on the current branch vs. main:
  `git diff --name-only main...HEAD`

---

## Checklist

### 1. Authentication

For every API route in scope:

- [ ] Calls `getCurrentUser()` (or equivalent auth function) at the top
- [ ] Returns 401 if user is not authenticated
- [ ] Does NOT proceed with any database operations before auth check

**Search pattern:** Files in `app/api/` that do NOT contain `getCurrentUser`

---

### 2. Authorization (Role-Based Access)

- [ ] Write operations (POST, PUT, PATCH, DELETE) check user role
- [ ] Permission helpers used correctly (`canCreate()`, `canEdit()`, etc.)
- [ ] Role checks happen BEFORE database operations, not after
- [ ] Super admin / consultant roles scoped appropriately

---

### 3. Multi-Tenant Isolation

This is the **most critical** check for SaaS applications:

- [ ] Every SELECT query includes `.eq('organization_id', user.organizationId)`
- [ ] Every INSERT includes `organization_id: user.organizationId`
- [ ] No query fetches data without an organization_id filter
- [ ] URL parameters (IDs in path) are validated against the user's org
- [ ] No cross-tenant data leakage in JOIN queries

**Search pattern:** `.from(` calls that do NOT chain `.eq('organization_id'`

---

### 4. SQL Injection / Query Safety

- [ ] No raw SQL with string interpolation
- [ ] Supabase query builder used for all queries (parameterized by default)
- [ ] Any `.rpc()` calls use parameterized arguments
- [ ] User input is not directly concatenated into queries

---

### 5. Input Validation

- [ ] Request body is validated before use
- [ ] URL parameters are validated (UUID format, allowed values)
- [ ] No `eval()`, `Function()`, or dynamic code execution with user input

---

### 6. Secrets and Environment

- [ ] No hardcoded secrets, API keys, or passwords in source code
- [ ] `SUPABASE_SERVICE_KEY` only used server-side (not in client components)
- [ ] JWT_SECRET not exposed to client
- [ ] `.env` files are in `.gitignore`

**Search pattern:** Grep for patterns like `sk_`, `key=`, `secret=`, `password=`

---

### 7. XSS Prevention

- [ ] User-generated content is not rendered with `dangerouslySetInnerHTML`
- [ ] If used, content is sanitized first
- [ ] URL parameters are not reflected into HTML without escaping

---

### 8. Activity Logging

- [ ] All create/update/delete operations call `logActivity()`
- [ ] Log entries include meaningful entity names and change details
- [ ] No sensitive data (passwords, tokens) in log entries

---

## Report Format

```
## Security Review Report

**Scope:** [what was reviewed]
**Date:** [today's date]
**Risk Level:** [LOW | MEDIUM | HIGH | CRITICAL]

### Findings

#### [CRITICAL/HIGH/MEDIUM/LOW] Finding Title
- **File:** path/to/file.ts:line
- **Issue:** Description of the vulnerability
- **Fix:** Recommended remediation

### Summary
- Files reviewed: N
- Findings: N (X critical, Y high, Z medium, W low)
- [PASS | NEEDS ATTENTION | BLOCKED — do not merge]
```
