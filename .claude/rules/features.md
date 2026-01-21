# Feature Organization Rules

This document describes the feature-centric organization system used in this project.

## Directory Structure

```
features/
├── index.md                        # Domain registry
└── {domain}/                       # Domain folder
    └── {feature-id}/               # Feature folder
        ├── FEATURE.md              # Living documentation (intended state)
        └── specs/                  # Specifications
            └── {NN}-{spec-name}/   # Numbered spec
                ├── spec-research.md # Research for this spec (context, contracts, test plan)
                ├── spec.md          # Requirements
                └── checklist.md     # Progress tracking
```

## File Purposes

### features/index.md
Domain registry listing all feature domains with descriptions and folder names.

### FEATURE.md
Living documentation showing current intended state of a feature. Updated as the feature evolves. Contains:
- Metadata and ownership
- Files touched
- Problem/goals/non-goals
- Intended state
- Architecture
- Changelog of specs

### spec-research.md
Research output for each spec (lives in spec folder). Contains:
- Problem context for this spec
- Exploration findings (patterns, key files, integration points)
- Key decisions with rationale
- Interface contracts (types, function signatures)
- Test plan (derived from contracts: happy path, edge cases, error cases, mocks)
- Files to create/modify
- Edge cases to handle

### spec.md
Concise requirements document (~50-100 lines). Contains:
- Overview
- Out of scope
- Functional requirements with success criteria
- Technical design

### checklist.md
Progress tracking for implementation. Contains:
- Tasks organized by phase (X.0 tests, X.1 impl)
- Checkbox status
- Session notes

## Naming Conventions

- **Domains**: lowercase, hyphenated (e.g., `notifications`)
- **Features**: lowercase, hyphenated (e.g., `user-notifications`)
- **Specs**: numbered prefix + name (e.g., `01-foundation`, `02-advanced`)

## Workflows

### Phase 1: Research
1. Use `research` skill
2. Explore codebase, dialog with user
3. Design approach, define interface contracts
4. Derive test plan from contracts
5. Decompose into spec units
6. Create FEATURE.md + spec-research.md for ALL decomposed specs
7. Create research branch: `{domain}/{feature}/research-{NN}`
8. Commit and push → user creates research PR (stack base)

### Phase 2: Spec
1. Use `spec` skill for each decomposed unit
2. Create spec branch stacking on research (or previous spec)
3. Gather clarifications upfront
4. Parallel drafting → single review at end
5. Generate checklist
6. Commit spec.md + checklist.md
7. Push → user creates spec PR

### Phase 3: Implement
1. Use `implement` skill
2. Checkout existing spec branch
3. Phase X.0: Tests first (all FRs)
4. Phase X.1: Implementation (all FRs)
5. Phase X.2: Review
6. Push → user handles PR via Graphite

### Bug Fixes / Enhancements
1. Run `research` to understand scope
2. Create spec if significant work
3. Implement and update FEATURE.md

## Progressive Disclosure

Load only what you need:
- Quick overview? Read FEATURE.md
- Context/decisions/tests? Read spec-research.md (in spec folder)
- Requirements? Read spec.md
- Progress? Read checklist.md

---

## Stacked Diff Workflow

This project uses stacked PRs with Graphite CLI. Stack structure:

```
research PR (stack base)
    └── spec-01 PR (all FRs for spec 01)
        └── spec-02 PR (all FRs for spec 02)
            └── spec-03 PR ...
```

### Branch Naming

| Type | Pattern | Example |
|------|---------|---------|
| Research | `{domain}/{feature}/research-{NN}` | `notifications/user-notifications/research-01` |
| Spec | `{domain}/{feature}/{NN}-{spec-name}` | `notifications/user-notifications/01-foundation` |

**Research numbering:** Increment for each research session (`research-01`, `research-02`, ...)
**Spec numbering:** Globally sequential per feature (`01-xxx`, `02-xxx`, ...)

### Stack Structure

```
notifications/user-notifications/research-01      ← PR 0 (stack base)
    └── notifications/user-notifications/01-foundation  ← PR 1
        └── notifications/user-notifications/02-handlers  ← PR 2
            └── notifications/user-notifications/03-ui     ← PR 3

[After merge, new research session]
notifications/user-notifications/research-02      ← New stack base
    └── notifications/user-notifications/04-filters  ← PR 1
```

### Spec Scoping

| Metric | Target | Maximum |
|--------|--------|---------|
| FRs per spec | 3-5 | 8 |
| Lines per spec PR | 200-400 | 600 |
| Specs per research | 2-4 | 6 |

### Commit Discipline

**Research branch:**
```
research({feature}): add FEATURE.md and spec-research for {N} specs
```

**Spec branch (by spec skill):**
```
spec({NN}-{spec}): add spec and checklist
```

**Spec branch (by implement skill):**
```
test(FR1): add validation tests
test(FR2): add handler tests
feat(FR1): implement validation
feat(FR2): implement handlers
fix({spec}): address review feedback
```

| Order | Prefix | Created By | Purpose |
|-------|--------|------------|---------|
| 1 | `research` | research skill | FEATURE.md + all spec-research.md |
| 2 | `spec` | spec skill | spec.md + checklist.md |
| 3 | `test` | implement skill | Test commits (red phase) |
| 4 | `feat` | implement skill | Implementation commits (green phase) |
| 5 | `fix` | implement skill | Review fix commits |

### PR Handling

**Do NOT create PRs directly.** User manages PRs via Graphite CLI:
- Run `/stack-new` to create PR in Graphite stack

### Anti-patterns

- Creating PRs via `gh pr create` (use Graphite instead)
- Single commit bundling tests and implementation
- Implementation commits before test commits
- Squashing all commits (loses TDD sequence visibility)
- Spec PRs exceeding 600 lines without justification
- Skipping Phase X.2 review
- Creating branch without stacking on research/previous spec
