---
name: express-skill
description: Execution guide for building and refactoring Express.js backends using modular architecture, progressive code decomposition, small cohesive files, and evidence-based implementation.
license: MIT
metadata:
  author: "ac-aman"
  version: "1.0.0"
  tags:
    - express
    - node
    - typescript
    - rest-api
    - backend
    - modular-architecture
    - workflow
---

# Express.js Development & Orchestration Guide

Use this skill when creating, extending, or refactoring Express.js backend applications. 

This document serves as the **execution, orchestration, and reference loading guide** for AI coding agents. Detailed architecture specifications, folder trees, and code rules are maintained inside dedicated reference files under `references/`.

---

## Purpose & Objective

The objective of this skill is to guide agents to reliably produce Express.js codebases that are:
- Well-organized around business modules or features.
- Built with small, cohesive files rather than oversized dumping grounds.
- Scaled via progressive decomposition without unnecessary upfront abstractions.
- Refactored safely while preserving existing working behavior.
- Grounded in empirical inspection of project context (zero hallucinated dependencies or APIs).

---

## Agent Execution Workflow

When tasked with creating or modifying an Express project, follow this sequence:

```text
User Request
    ↓
1. Inspect Project & Context (Anti-Hallucination)
    ↓
2. Determine Architecture (Modular by Default / Existing / Explicit)
    ↓
3. Load & Read Relevant Architecture Reference (references/architectures/<name>.md)
    ↓
4. Load & Read Relevant Guidelines (references/guidelines/<guideline>.md)
    ↓
5. Plan Implementation (Cohesive, small files & minimal abstractions)
    ↓
6. Execute Implementation / Refactoring
    ↓
7. Check Imports, Module Boundaries & Dependencies
    ↓
8. Verify Behavior & Run Code Checks
```

---

## 1. Project Inspection Instructions (Anti-Hallucination & Evidence Rules)

Before creating or editing code in an existing codebase, you **must** gather empirical evidence:

1. **Inspect Directory Structure**: Examine existing source directories to understand the established layout.
2. **Inspect `package.json`**:
   - Check installed dependencies (e.g. Express, TypeScript, ORM/ODM libraries like Prisma/TypeORM/Mongoose, validation libraries like Zod/Joi).
   - Do **not** import or use a library that is not present in `package.json`.
3. **Inspect Database & Infrastructure Usage**: Identify existing ORM schemas, database connection files, and environment variable conventions before referencing models or configuration.
4. **Identify Established Conventions**: Align new code with existing naming and file patterns unless instructed to refactor.
5. **Never Invent Missing Context**: If an API, schema, or configuration detail is missing, inspect the workspace or ask for clarification rather than assuming.

---

## 2. Architecture Selection & Reference Loading Rules

Determine the target architecture using the following rules:

1. **New Projects (Default)**: Use **Modular Architecture** by default.
2. **Existing Projects**: Preserve the project's existing established architecture unless the user explicitly requests an architectural migration.
3. **Explicit User Request**: If the user explicitly requests a specific architecture (e.g. Clean, DDD, Layered, Hexagonal, Simple), select that requested architecture.

### Architecture Reference Mapping

Once selected, you **must** load and read the corresponding reference file before generating or modifying code:

| Architecture Name | Reference File Path | Primary Use Case |
| :--- | :--- | :--- |
| **Modular** (Default) | `[references/architectures/modular.md](./references/architectures/modular.md)` | Feature-rich, scalable, and maintainable Express applications |
| **Layered** | `[references/architectures/layered.md](./references/architectures/layered.md)` | Traditional Controller-Service-Repository applications |
| **Simple** | `[references/architectures/simple.md](./references/architectures/simple.md)` | Very small APIs, prototypes, or single-purpose scripts |
| **Clean** | `[references/architectures/clean.md](./references/architectures/clean.md)` | Explicit requirement for strong dependency inversion & framework decoupling |
| **DDD** | `[references/architectures/ddd.md](./references/architectures/ddd.md)` | Complex domain logic with aggregates, value objects & domain events |
| **Hexagonal** | `[references/architectures/hexagonal.md](./references/architectures/hexagonal.md)` | Multi-adapter systems (Ports & Adapters pattern) |

---

## 3. Mandatory Operational Guidelines

In addition to reading the architecture reference, read the relevant guideline files for code organization rules:

- **Responsibility-Based Code Splitting**: `[references/guidelines/large-files.md](./references/guidelines/large-files.md)`
- **Module Boundaries & Public Interfaces**: `[references/guidelines/module-boundaries.md](./references/guidelines/module-boundaries.md)`
- **File & Directory Organization**: `[references/guidelines/file-organization.md](./references/guidelines/file-organization.md)`
- **Safe Refactoring Workflow**: `[references/guidelines/refactoring.md](./references/guidelines/refactoring.md)`

---

## 4. General Execution Rules

1. **Avoid Oversized Dumping Grounds**: Do not place unrelated responsibilities into single controllers, services, repositories, or routes.
2. **Split by Cohesion, Not Line Count**: Decompose files when they contain multiple independent responsibilities or capabilities—never split artificially just to meet a line count.
3. **Progressive Decomposition**: Start with simple file layouts. Create subdirectories or capability subfolders only when a module's complexity justifies them.
4. **Zero Unnecessary Abstractions**: Do not create empty interfaces, ports, adapters, or factories unless required by the target architecture or current implementation needs.

---

## 5. Refactoring Workflow

When refactoring or restructuring existing code:

1. **Analyze Existing Behavior**: Read and understand the full logic of the code before attempting edits.
2. **Preserve Public API Contracts**: Ensure HTTP routes, request parameters, status codes, and response schemas remain intact unless changes are explicitly requested.
3. **Move Before Rewriting**: Extract logic into focused files first, then verify behavior before refactoring internal implementation.
4. **Update Imports & References**: Search for all consumers of moved or renamed symbols and update their import paths.

---

## 6. Verification Workflow

Before completing any task, execute the following verification steps:

1. **Syntax & Import Check**: Verify that all newly created or moved files have correct relative import paths and exports.
2. **Module Boundary Check**: Ensure internal files of a module are not directly imported by other modules (imports should pass through `index.ts`).
3. **Dependency Alignment**: Verify no unlisted npm packages or unconfigured environment variables were introduced.
4. **Empirical Test & Build**: Run existing build or test commands (e.g. `npm run build`, `npm test`) if available in the workspace to confirm zero regressions.
