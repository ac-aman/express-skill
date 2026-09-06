---
title: File Size & Progressive Decomposition Guideline (Generic)
category: guidelines
---

# Generic File Size & Progressive Decomposition Guidelines

This guideline defines architecture-agnostic rules for file decomposition, responsibility-based code splitting, progressive modularization, and eliminating oversized dumping-ground files in Express.js backends.

---

## 1. Core Principles

1. **Split by Single Responsibility**: Each file must have a single, clearly definable responsibility or capability. If a file handles multiple independent business use cases, decompose it into focused files.
2. **Decompose by Cohesion, Not Arbitrary Line Limits**: Line count (e.g. > 300 lines) is a warning signal, not an architectural trigger. Split a file when its internal functions change for different reasons or serve different capabilities—never split a cohesive implementation artificially just to meet a line count.
3. **Progressive Decomposition Lifecycle**: Start with simple, flat file structures for new features. Introduce subdirectories or capability subfolders only when complexity and file count actually demand them.
4. **No Generic Dumping Grounds**: Never create generic dumping grounds like `utils.ts`, `helpers.ts`, `common.ts`, or `misc.ts`. Utilities must have narrow, explicit responsibilities (e.g. `date-formatter.ts`, `password-hasher.ts`).

---

## 2. Responsibility-Based Splitting Rules

### A. Controllers
When a controller contains handlers for more than 3-4 distinct HTTP endpoints or capabilities, split by use case:
```text
controllers/
├── register-user.controller.ts
├── authenticate-user.controller.ts
├── update-profile.controller.ts
└── manage-user-status.controller.ts
```

### B. Services
Decompose oversized service files by business capability:
```text
services/
├── create-order.service.ts
├── calculate-order-tax.service.ts
├── process-refund.service.ts
└── generate-shipping-label.service.ts
```

### C. Repositories
Split repositories when database queries serve distinct access patterns:
```text
repositories/
├── user-read.repository.ts      # Querying user profiles & details
├── user-write.repository.ts     # User mutations & updates
├── user-search.repository.ts    # Search filtering & pagination
└── user-auth.repository.ts      # Credentials & auth token queries
```

---

## 3. Progressive Decomposition Stages

- **Stage 1 (Flat Layout)**: Small modules keep flat files (`user.controller.ts`, `user.service.ts`, `user.repository.ts`, `user.routes.ts`, `index.ts`).
- **Stage 2 (Component Subdirectories)**: Component folders (`controllers/`, `services/`, `repositories/`) introduced ONLY when file count or complexity demands it.
- **Stage 3 (Capability Subfolders)**: Sub-feature grouping (`authentication/`, `profile/`, `search/`) for large enterprise modules.

---

## 4. Comments & TODO Protocol

For guidelines on adding mini-comments or marking legacy oversized files for refactoring (e.g. `// TODO: Decompose legacy UserService...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 5. Agent Verification Checklist

- [ ] Can the single responsibility of every file be described in one short sentence?
- [ ] Are controllers and services split by use case or business capability?
- [ ] Are generic dumping-ground files (`utils.ts`, `helpers.ts`) avoided?
- [ ] Is progressive decomposition followed without creating empty folders upfront?
- [ ] Are oversized legacy files flagged with explicit `// TODO: Decompose...` comments per [code-comments-and-todos.md](./code-comments-and-todos.md)?
