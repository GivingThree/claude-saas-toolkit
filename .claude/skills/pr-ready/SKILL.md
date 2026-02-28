---
name: pr-ready
description: >
  Prepare the current branch for a pull request — review changes, run checks,
  verify docs, draft PR description.
argument-hint: [--draft]
disable-model-invocation: true
---

# PR Ready — Pre-Pull-Request Checklist

Prepare the current branch for a clean pull request.

---

## Step 1: Understand the Changes

1. Run `git log --oneline main...HEAD` (or the base branch) to see all commits.
2. Run `git diff main...HEAD --stat` for a file-level summary.
3. Run `git diff main...HEAD` for the full diff.
4. Identify the **purpose** of the branch: bug fix, feature, refactor, docs, etc.

---

## Step 2: Run Checks (skip if `$ARGUMENTS` contains `--draft`)

Read `package.json` and/or `CLAUDE.md` to find available commands, then run:

1. **Lint** — e.g., `npm run lint`
2. **Type check** — e.g., `npx tsc --noEmit` (if TypeScript)
3. **Build** — e.g., `npm run build`
4. **Tests** — e.g., `npm test` (if a test framework exists)

Report results for each. If any check fails:
- Show the error output
- Suggest a fix
- Ask the user if they want to fix before continuing

---

## Step 3: Documentation Check

Verify documentation is consistent with the changes:

1. **ROADMAP.md** — Are completed items marked? New items added?
2. **PARKING_LOT.md** — Any items that should be parked from this work?
3. **Help Guide** — If user-facing features changed, is the help updated?
4. **CLAUDE.md** — If architecture patterns changed, is CLAUDE.md updated?

Report any gaps.

---

## Step 4: Security Spot-Check

Quick scan of the diff for common issues:

- [ ] No secrets or credentials in the diff
- [ ] API routes have auth checks
- [ ] Database queries include organization_id filtering
- [ ] No `console.log` with sensitive data

This is NOT a full security review (use `/security-review` for that).
Just a quick sanity check on the diff.

---

## Step 5: Draft PR Description

Generate a PR description in this format:

```markdown
## Summary
<1-3 bullet points describing what this PR does and why>

## Changes
<Grouped list of changes by area/file>

## Testing
- [ ] Lint passes
- [ ] Build succeeds
- [ ] [Manual testing steps relevant to the changes]

## Documentation
- [ ] Roadmap updated (if applicable)
- [ ] Help guide updated (if applicable)

## Screenshots
<If UI changes were made, note where screenshots should go>
```

---

## Step 6: Present the Report

```
## PR Readiness Report

### Branch
`<branch>` -> `<base-branch>` (N commits, M files changed)

### Checks
- Lint: [PASS | FAIL]
- Types: [PASS | FAIL | N/A]
- Build: [PASS | FAIL]
- Tests: [PASS | FAIL | N/A]

### Docs
- [Up to date | Gaps found — see above]

### Security
- [Clean | Issues found — see above]

### PR Description
[The drafted description, ready to copy]

### Verdict
[READY TO MERGE | NEEDS FIXES — see above]
```
