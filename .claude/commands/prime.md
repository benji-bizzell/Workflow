---
description: Load essential project context and show current development status
---

# Prime

Load essential project context to begin development. Reads core documentation and shows current project status.

## Usage

```
/prime
```

No parameters needed.

## Process

### Step 1: Load Core Documentation

Read the following files:
1. `CLAUDE.md` - Main project guidance (if exists)
2. `features/index.md` - Domain registry and feature overview

### Step 2: Scan Features Directory

For each feature in `features/`:
1. Check for `FEATURE.md` - note feature status
2. Check for `research.md` - note if research exists
3. Check for `specs/` - count specs and their status
4. Identify active work (in-progress specs via checklist.md)

### Step 3: Present Current Status

Parse findings and present:

```markdown
## Current Project Status

**Active Features:**
- [List features with in-progress work]

**Ready for Work:**
- [List features with pending specs or research]

**Recently Completed:**
- [List recently completed specs]

**Feature Summary:**
| Feature | Domain | Research | Specs | Status |
|---------|--------|----------|-------|--------|
| [name] | [domain] | [yes/no] | [count] | [status] |
```

### Step 4: Suggest Next Steps

Based on current status:

| Situation | Suggestion |
|-----------|------------|
| No features | "Start with `research` skill to explore and plan" |
| Feature without research | "Run `research` skill to explore and decompose" |
| Research done, no specs | "Create specs using `spec` skill" |
| Specs exist, not implemented | "Implement using `implement` skill" |
| Implementation in progress | "Continue with `implement` - checklist shows [X] items remaining" |

## Output Format

```markdown
# Project Primed

**Loaded**: CLAUDE.md, features/index.md

## Current Status
[Parsed status from features/]

## Active Work
[In-progress items from checklists]

## Next Steps
[Suggested actions based on status]

## Workflow Reference
- `research` - Explore, design, decompose
- `spec` - Create implementable specs
- `implement` - TDD execution with review

Ready for development.
```
