---
title: Safe Refactoring Workflow Guideline
category: guidelines
---

# Safe Refactoring Workflow Guidelines

Refactoring an existing Express.js codebase must improve maintainability and structure while preserving 100% of working behavior.

---

## Core Refactoring Rules

1. **Empirical Inspection First**: Analyze the existing codebase, routes, ORM models, and `package.json` dependencies before moving or editing code.
2. **Preserve Public API Contracts**: Do not modify endpoint URLs, HTTP methods, request body schemas, or HTTP status codes unless explicitly instructed to change business behavior.
3. **Move Before Rewriting**: Extract code into new module files or subdirectories before attempting logic refactoring.
4. **Update Imports & References**: Search the entire workspace for usages of renamed or moved files/symbols and update their import statements.
5. **No Unnecessary Rewrites**: Do not rewrite working functions or swap libraries (e.g. replacing Axios with Fetch or Prisma with TypeORM) unless requested.

---

## Step-by-Step Refactoring Sequence

```text
1. Inspect Codebase & Tests
    ↓
2. Identify Feature Boundaries & File Ownership
    ↓
3. Create Module / Capability Folders
    ↓
4. Extract Logic & Move Code to Cohesive Files
    ↓
5. Define Module Public Export (index.ts)
    ↓
6. Update All Project Imports
    ↓
7. Run Build & Tests to Verify Zero Regressions
```

---

## Anti-Pattern Warnings

- **Do NOT delete functional code** simply because its purpose is not immediately clear.
- **Do NOT rewrite database queries** during a structural refactor unless fixing a verified bug.
- **Do NOT introduce theoretical abstractions** (e.g., factories, generic wrappers) during a refactor unless required by the target architecture.
