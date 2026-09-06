---
title: Code Comments & TODO Protocol Guideline
category: guidelines
---

# Code Comments & TODO Protocol Guidelines

This guideline defines architecture-agnostic standards for writing readable mini-comments and handling underspecified features using the mandatory TODO Protocol across Express.js backends.

---

## 1. Readable Mini-Comments Rules

When writing controller, service, repository, middleware, or configuration logic, follow these commenting rules:

1. **Comment Why, Not What**: Write concise 1-line mini-comments explaining *why* a non-obvious business step or technical decision was made. Do not comment self-explanatory code (e.g. do not write `// Assign email to user`).
2. **Key Business Step Documentation**: Annotate multi-step business workflows (e.g., verifying user state before mutations, atomic transaction boundaries, password hashing).
3. **Keep Comments Short & Readable**: Limit mini-comments to 1 line placed immediately above the target code statement.

### Good Examples
```typescript
// Verify account is active before issuing password reset token
const user = await userService.findActiveUserByEmail(email);

// Deduct inventory stock atomically inside transaction to prevent race conditions
await inventoryService.reserveStock(items, transaction);

// Coerce string PORT into integer for Express listen compatibility
PORT: z.string().transform((val) => parseInt(val, 10)).default('3000'),
```

---

## 2. Mandatory TODO Protocol for Missing / Underspecified Rules

When tasked with implementing or refactoring code where a feature, business rule, infrastructure package, or configuration parameter is underspecified or missing:

1. **Zero Hallucination Rule**: Never invent unverified business logic, uninstalled packages, API endpoints, schema fields, or environment variable names.
2. **Insert Explicit `// TODO:` Comments**: Annotate the code with an explicit `// TODO:` comment describing the exact question, missing package install instruction, or underspecified requirement.
3. **Format Standard**:
   `// TODO: <Descriptive explanation of missing requirement or question>`

### Examples
```typescript
// TODO: Clarify with product team if trial users should receive automated welcome email on registration.
// TODO: Install 'http-status-codes' for type-safe status constants (npm install http-status-codes).
// TODO: Confirm exact OAuth client ID environment variable name from DevOps team.
```

---

## 3. Reference in Other Guidelines

All guideline documents (`security.md`, `error-handling.md`, `api-design.md`, `configuration.md`, `dependencies.md`, etc.) inherit these comment and TODO rules directly from this document. Refer to this guideline whenever adding code comments or marking underspecified tasks.
