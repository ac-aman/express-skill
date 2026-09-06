---
title: Safe Refactoring Workflow Guideline (Generic)
category: guidelines
---

# Generic Safe Refactoring Workflow Guidelines

This guideline defines architecture-agnostic standards for safely refactoring, restructuring, and reorganizing existing Express.js codebases while preserving 100% of working behavior.

---

## 1. Core Refactoring Principles

1. **Empirical Inspection First**: Analyze `package.json`, current folder layout, existing ORM models, route handlers, and unit/integration tests before moving or editing code.
2. **Strict Behavior & Contract Preservation**: Structural refactoring must NEVER break existing HTTP endpoints, route URIs, HTTP methods, status codes, or request/response payload schemas unless explicitly requested by the user.
3. **Move Before Rewriting**: Move existing working code into new target files/directories FIRST. Verify imports and exports before attempting to rewrite or clean up internal logic.
4. **Workspace Import Path Updating**: Search the entire workspace for all consumers of moved or renamed files/symbols and update their import paths immediately.
5. **No Unnecessary Rewrites or Stack Changes**: Do not replace working code, swap ORM libraries (e.g. replacing Mongoose with Prisma), or replace validation libraries (e.g. replacing Joi with Zod) during a refactor unless requested.

---

## 2. Step-by-Step Refactoring Sequence

```text
1. Empirical Inspection & Workspace Analysis
    ↓
2. Identify Component Boundaries & Target Architecture
    ↓
3. Create New Target File & Folder Layout
    ↓
4. Relocate Existing Implementation (Move Before Rewriting)
    ↓
5. Define Boundary Export Interfaces (index.ts)
    ↓
6. Update All Workspace Import Statements & References
    ↓
7. Verify Syntax, Imports, and Behavioral Consistency
```

---

## 3. Common Refactoring Scenarios

- **Scenario A (Monolithic Controller)**: Create focused controller files (`create-user.controller.ts`), move handlers without changing business logic, export from `index.ts`, update `user.routes.ts`.
- **Scenario B (Oversized Service > 500 lines)**: Create dedicated use-case services (`authenticate-user.service.ts`, `reset-password.service.ts`), move capability functions, update controllers.
- **Scenario C (Technical Layers to Modular)**: Create `src/modules/<feature>/`, move related controllers/services/routes, export via `index.ts`, update `app.ts`.

---

## 4. Comments & TODO Protocol

For rules on adding mini-comments explaining code relocation or marking legacy logic for future review (e.g., `// TODO: Preserve legacy timestamp format...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 5. Dangerous Anti-Patterns to Avoid

- **Do NOT delete legacy code** simply because its purpose is not immediately clear.
- **Do NOT alter raw database queries** or ORM models during a structural refactor.
- **Do NOT swallow errors** during refactoring with empty `catch (err) {}` blocks.
- **Do NOT introduce empty placeholder abstractions** (factories, unused interfaces, ports) that the project does not need.

---

## 6. Agent Verification Checklist

- [ ] Has the project structure and `package.json` been inspected before moving files?
- [ ] Are all HTTP route paths, request parameters, and response JSON schemas 100% preserved?
- [ ] Have code files been moved before modifying their internal implementation?
- [ ] Have all import statements across the workspace been updated to match new file locations?
- [ ] Are underspecified or complex legacy code areas marked with explicit `// TODO:` comments per [code-comments-and-todos.md](./code-comments-and-todos.md)?
- [ ] Do automated tests or build checks (`npm run build`, `npm test`) execute without errors?
