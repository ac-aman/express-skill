---
name: express-skill
description: Execution guide for building and refactoring Express.js backends using modular architecture, progressive code decomposition, small cohesive files, and evidence-based implementation.
license: MIT
metadata:
  author: "ac-aman"
  version: "1.0.4"
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

Guide agents to reliably produce Express.js codebases that are:
- Well-organized around business modules or features.
- Built with small, cohesive files rather than oversized dumping grounds.
- Scaled via progressive decomposition without unnecessary upfront abstractions.
- Refactored safely while preserving existing working behavior.
- Grounded in empirical inspection of project context (zero hallucinated dependencies or APIs).

---

## Agent Execution Workflow

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

## 1. Project Inspection Rules (Anti-Hallucination)

Before creating or editing code in an existing codebase, gather empirical evidence:

1. **Inspect Directory Structure**: Examine existing source directories to understand established layouts.
2. **Inspect `package.json`**: Check installed `dependencies` and `devDependencies`. Never import an uninstalled package.
3. **Inspect Infrastructure & DB**: Identify ORM schemas, database clients, and environment variables before referencing them.
4. **Identify Established Conventions**: Align new code with existing naming and file patterns unless instructed to refactor.
5. **Never Invent Missing Context**: If an API contract or schema is missing, ask for clarification or use explicit `// TODO:` comments instead of assuming.

---

## 2. Architecture Selection & Reference Loading

Determine the target architecture:
- **New Projects**: Use **Modular Architecture** by default.
- **Existing Projects**: Preserve the project's established architecture unless migration is requested.
- **Explicit User Request**: Load requested architecture file (`references/architectures/<name>.md`).

### Architecture Reference Mapping

Load and read the target architecture specification before writing code:

| Architecture | Reference File Path | Primary Use Case |
| :--- | :--- | :--- |
| **Modular** (Default) | `[references/architectures/modular.md](./references/architectures/modular.md)` | Feature-rich, scalable, maintainable Express applications |
| **Layered** | `[references/architectures/layered.md](./references/architectures/layered.md)` | Traditional Controller-Service-Repository applications |
| **Simple** | `[references/architectures/simple.md](./references/architectures/simple.md)` | Microservices, prototypes, or single-purpose scripts (<5 endpoints) |
| **Clean** | `[references/architectures/clean.md](./references/architectures/clean.md)` | Strict dependency inversion & framework decoupling |
| **DDD** | `[references/architectures/ddd.md](./references/architectures/ddd.md)` | Complex domain logic with Bounded Contexts & Aggregates |
| **Hexagonal** | `[references/architectures/hexagonal.md](./references/architectures/hexagonal.md)` | Multi-adapter systems (Ports & Adapters pattern) |

---

## 3. Operational Guidelines Index

Load relevant guideline files based on task requirements:

- **TypeScript Standards & Type Safety**: `[references/guidelines/typescript.md](./references/guidelines/typescript.md)`
- **Code Organization**: `[references/guidelines/code-organization.md](./references/guidelines/code-organization.md)`
- **File Size & Decomposition**: `[references/guidelines/file-size-and-decomposition.md](./references/guidelines/file-size-and-decomposition.md)`
- **Naming Conventions**: `[references/guidelines/naming.md](./references/guidelines/naming.md)`
- **Dependencies & Inspection**: `[references/guidelines/dependencies.md](./references/guidelines/dependencies.md)`
- **Centralized Error Handling**: `[references/guidelines/error-handling.md](./references/guidelines/error-handling.md)`
- **Input Validation**: `[references/guidelines/validation.md](./references/guidelines/validation.md)`
- **Configuration & Environment**: `[references/guidelines/configuration.md](./references/guidelines/configuration.md)`
- **Security Standards**: `[references/guidelines/security.md](./references/guidelines/security.md)`
- **Testing Strategy**: `[references/guidelines/testing.md](./references/guidelines/testing.md)`
- **RESTful API Design**: `[references/guidelines/api-design.md](./references/guidelines/api-design.md)`
- **Safe Refactoring Workflow**: `[references/guidelines/refactoring.md](./references/guidelines/refactoring.md)`
- **Comments & TODO Protocol**: `[references/guidelines/code-comments-and-todos.md](./references/guidelines/code-comments-and-todos.md)`

---

## 4. General Execution Rules

1. **Avoid Oversized Files**: Decompose files by responsibility and capability when multiple use cases accumulate.
2. **Progressive Decomposition**: Start with simple file layouts. Add subdirectories or capability subfolders only when complexity demands it.
3. **Zero Unnecessary Abstractions**: Do not create empty interfaces, ports, adapters, or factories unless required by the target architecture.
4. **Preserve API Contracts**: Never alter route URLs, HTTP methods, status codes, or payload schemas during structural refactoring.

---

## 5. Verification Checklist

- [ ] Has `package.json` been inspected before introducing imports?
- [ ] Has the target architecture guide been loaded and followed?
- [ ] Are TypeScript standards followed (`strict: true`, zero `any`, explicit return types)?
- [ ] Are module boundaries (`index.ts`) respected without deep internal file reaching?
- [ ] Are missing rules or uninstalled packages annotated with explicit `// TODO:` comments?
- [ ] Do existing build and test scripts pass cleanly?
