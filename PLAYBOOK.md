# Development Playbook

The step-by-step guide for how a feature goes from idea to merged code. This ties together the rules, workflows, and templates — telling you **what to run, when, and why**.

---

## The Big Picture

```
IDEA → SPEC → VALIDATE → IMPLEMENT (TDD) → REVIEW → MERGE
```

Every feature follows this flow. The workflows automate each stage. Skip stages at your own risk — every one exists because something broke without it.

---

## Stage 1: Planning (Spec)

**Goal:** Define what you're building before writing any code.

### What to do

1. **Run the spec workflow** (`workflows/spec.md`)
   - The agent interviews you (minimum 2 rounds)
   - Researches the codebase for entry points and existing patterns
   - Writes a spec using the sprint spec template (`templates/sprint-spec.md`)
   - Saves to `specs/<name>.md`

2. **Run the staff review workflow** (`workflows/staff-review.md`)
   - Reviews your spec as a skeptical senior engineer
   - Checks: design quality, engineering filters, risk, scope, gaps
   - Verdict: PROCEED / SIMPLIFY / RE-PLAN

### When to skip
- Trivial changes (typo, rename, constant) — go straight to Stage 3
- Bug fixes from error logs — use the fix workflow (`workflows/fix.md`) instead

### Output
- `specs/<name>.md` — your implementation contract
- Staff review verdict: PROCEED

---

## Stage 2: Branch Setup

**Goal:** Create an isolated workspace.

### What to do

1. Create a feature branch from main:
   ```bash
   git checkout -b feature/short-description
   ```

2. **Stay on this branch for the entire session.** Never switch back to main after committing.

### Rules
- Branch naming: `feature/`, `fix/`, `refactor/`, `sprint-N/`
- One spec per branch
- Never commit directly to main

---

## Stage 3: Implementation (TDD)

**Goal:** Build the feature test-first.

### What to do

1. **Run the TDD workflow** (`workflows/tdd-workflow.md`)
   - Writes failing tests FIRST (RED phase)
   - Outputs a test plan with categories from `rules/testing.md`
   - Produces RED PROOF (full test output showing failures)
   - Hands off to GREEN phase

2. **Write production code** (GREEN phase)
   - Make the failing tests pass
   - Write minimal code — don't over-build
   - Follow patterns from `rules/code-patterns.md`

3. **Commit** (`workflows/commit.md`)
   - 60-140 LOC production code per commit
   - Conventional format: `feat:`, `fix:`, `refactor:`, `test:`
   - Commit frequently — uncommitted work is lost work

4. **Repeat** steps 1-3 for each piece of the spec

### Mid-implementation checks

| Trigger | Workflow | Why |
|---------|----------|-----|
| Edited domain files | `workflows/check-tenancy.md` | Catch missing tenant filters (P0 security) |
| Edited domain files | `workflows/check-consistency.md` | Catch string constant drift |
| Changed auth/middleware | `workflows/security.md` | Catch auth bypass, PII leaks |

### When things go wrong
- **Existing test breaks:** STOP. Fix your code, not the test. Tests are sacred.
- **Need a file outside spec scope:** STOP. Report as blocker.
- **Bug from error output:** Use `workflows/fix.md` (parse → locate → fix → verify)

---

## Stage 4: Pre-PR Quality Gates

**Goal:** Catch issues before the formal review.

### What to do (pick based on confidence)

| Confidence | Run | Why |
|------------|-----|-----|
| High | `workflows/audit.md` | Quick multi-dimensional scan |
| Medium | `workflows/audit.md` + `workflows/grill.md` | Audit + adversarial pressure |
| Low | `workflows/audit.md` + `workflows/grill.md` + `workflows/review.md` | Full battery |

### The audit (`workflows/audit.md`)
- Multi-tenancy scan
- Security checklist
- Rules compliance
- Cost/resource limits
- Code hygiene (magic numbers, `as any`, edge cases)

### The grill (`workflows/grill.md`)
- Hostile code review
- Challenges every assumption
- Demands proof (show the test)
- Suggests rewrites for ugly code
- Verdict: PASS / NEEDS WORK / SCRAP IT

---

## Stage 5: PR Review-Fix Loop

**Goal:** Automated review, fix, and re-review until clean.

### What to do

1. **Run the PR workflow** (`workflows/pr.md`)

   This orchestrates everything:
   ```
   Commit + Push + Create PR
       ↓
   Run reviews (workflows/review.md — can run multiple perspectives)
       ↓
   Parse findings (workflows/ingest-review.md)
       ↓
   Classify: AUTO-FIX / ASK_USER / SUGGEST
       ↓
   Auto-fix safe findings (max 5/cycle, allowlist only)
       ↓
   Verify (lint + typecheck + full test suite)
       ↓
   Re-review → convergence check
       ↓
   Max 3 cycles, then human decides
   ```

### How findings are classified

| Class | Criteria | Action |
|-------|----------|--------|
| **AUTO** | P0/P1 + concrete fix + on allowlist + small blast radius | Agent fixes automatically |
| **ASK_USER** | Architectural, ambiguous, recurring, contradictions, large blast radius | You decide |
| **SUGGEST** | P2 severity | Presented at end, no action |

### Auto-fix allowlist (the ONLY things auto-fixed)
- Missing tenant filter on queries
- Unused imports
- Missing typed error throws
- Missing CAS count checks
- Missing timeouts on external calls

**Everything else goes to ASK_USER.** The agent is the fixer, not the triage arbiter.

---

## Stage 6: Sprint Closeout (if applicable)

**Goal:** Verify all slices compose into a coherent whole before merging to main.

### What to do

1. **Run sprint closeout** (`workflows/sprint-closeout.md`)

   Checks:
   - **Spec fidelity:** Did we build what the spec said? Any ghost slices (claimed done, no code)? Dead ends (cut but code exists)?
   - **Cross-slice cohesion:** Naming consistency, interface contracts, data flow completeness, import graph health
   - **Dead-end detection:** TODOs, stubs, unused code, debug artifacts
   - **Main compatibility:** Merge conflicts, shared-file overlaps
   - **Proof gates:** Lint + typecheck + full test suite

   Verdict: MERGE / FIX FIRST / RETHINK

### When to run
- After ALL slices are DONE on the sprint branch
- Before merging to main

---

## Stage 7: Merge

**Goal:** Get the code into main.

1. Sprint closeout verdict is MERGE
2. Human merges (the agent does NOT merge — that's your call)
3. If items were cut, add them to backlog (`workflows/backlog.md`)

---

## Quick Reference: Which Workflow When

### "I'm starting a new feature"
→ `workflows/spec.md` → `workflows/staff-review.md` → `workflows/tdd-workflow.md` → `workflows/pr.md`

### "I have a bug to fix"
→ `workflows/fix.md` → `workflows/commit.md`

### "I want to review my code before PR"
→ `workflows/audit.md` + `workflows/grill.md`

### "I'm ready for PR"
→ `workflows/pr.md` (orchestrates review + fix + re-review)

### "Sprint is done, ready to merge"
→ `workflows/sprint-closeout.md`

### "I need to explain this code/PR to someone"
→ `workflows/explain.md`

### "I found something we should do later"
→ `workflows/backlog.md`

### "Just a quick fix, no ceremony"
→ Fix it → run quality gates (lint, typecheck, tests) → `workflows/commit.md`

---

## Multi-Agent Parallel Work

When multiple agents work on the same sprint:

```
Sprint Spec (slices table)
    ↓
SCHEMA slices: Serial (one agent at a time)
    ↓
DOMAIN/ROUTE/TEST slices: Parallel (disjoint file lists)
```

### Rules
- Each agent owns exclusive files declared in the spec
- Shared files (schema, types, helpers) belong to the infrastructure agent
- If you need a file outside your scope → STOP, report as blocker
- Pull frequently (`git pull --rebase`) to pick up other agents' changes

### Work Orders

Use the work order template (`templates/work-order.md`) to delegate bounded slices to specialist agents. Key sections:
- FILES YOU MAY TOUCH (allowlist)
- DO NOT TOUCH (out of scope)
- MUST-COVER INVARIANTS (missing any = blocker)
- STOP CONDITIONS (when to halt and report)
- PROOF COMMANDS (how to verify)

---

## Anti-Patterns (What NOT to Do)

| Anti-Pattern | Why It Fails | Do This Instead |
|--------------|-------------|-----------------|
| Code first, spec later | Wrong assumptions, rework | Spec first, even a short one |
| Tests after production code | Tests become confirmatory, not preventive | TDD: tests first, always |
| Modify tests to make code pass | Erodes the safety net | Fix the code, not the tests |
| One giant commit | Can't rollback safely | 60-140 LOC per commit |
| Switch to main after committing | Lose branch context | Stay on branch until PR/merge |
| Auto-fix everything | Agent makes wrong judgment calls | Allowlist only, max 5/cycle |
| Skip the spec for "simple" features | Simple features balloon | Spec it or explicitly call it trivial |
| Defer quality to "future work" | Future work never happens | Ship complete or cut scope now |
