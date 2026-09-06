---
title: Modular Architecture Specification Guide
architecture_type: modular
complexity: medium-high
is_primary_default: true
best_for:
  - Feature-rich Express applications
  - Multi-team development
  - Monoliths scaling cleanly over time
  - Feature & domain-grouped backend services
tags:
  - express
  - modular
  - feature-based
  - progressive-decomposition
---

# Modular Architecture Specification Guide

Modular Architecture is the **primary default architecture** for Express.js applications. It organizes code around **business features or domain capabilities** rather than technical layers. Each feature lives in its own module directory under `src/modules/<feature>/` and owns its HTTP handling, business logic, persistence logic, schemas, and types.

---

## 1. Directory Structure Specifications

### A. Modular Directory Overview

```text
src/
├── config/                 # Global environment & configuration
├── modules/                # Domain feature modules
│   ├── users/              # User feature module
│   ├── orders/             # Order feature module
│   ├── payments/           # Payment feature module
│   └── notifications/      # Notification feature module
├── shared/                 # Cross-cutting infrastructure (DB client, global middleware)
├── app.ts                  # Express application setup & router binding
└── server.ts               # HTTP server entry point & graceful shutdown
```

---

## 2. Progressive Decomposition Lifecycle

Do not create empty subdirectories upfront. Expand internal module structures progressively as feature complexity grows.

### Stage 1: Small / Emerging Feature Module
For new or compact feature modules with few operations:

```text
src/modules/users/
├── user.controller.ts     # Request handling & HTTP response mapping
├── user.service.ts        # Business logic & orchestration
├── user.repository.ts     # Database queries / ORM operations
├── user.routes.ts         # Express Router endpoints binding
├── user.schema.ts         # Input validation schemas (Zod/Joi)
├── user.types.ts          # TypeScript interfaces/types
└── index.ts               # Public interface export file
```

### Stage 2: Decomposed Feature Module (Component Subdirectories)
As operations grow, separate files into component directories:

```text
src/modules/users/
├── controllers/
│   ├── get-user.controller.ts
│   ├── update-user.controller.ts
│   └── delete-user.controller.ts
├── services/
│   ├── user-profile.service.ts
│   └── user-management.service.ts
├── repositories/
│   ├── user-read.repository.ts
│   └── user-write.repository.ts
├── routes/
│   └── user.routes.ts
├── schemas/
│   └── user.schema.ts
├── types/
│   └── user.types.ts
└── index.ts
```

> **Rule**: Create subdirectories only when the module actually has multiple files for that specific role.

### Stage 3: Large Capability Module (Sub-feature Capability Grouping)
When a module encompasses multiple distinct capabilities (e.g. Authentication vs Profile vs Search), decompose internally by capability:

```text
src/modules/users/
├── authentication/
│   ├── login.controller.ts
│   ├── login.service.ts
│   ├── auth.routes.ts
│   └── auth.schema.ts
├── profile/
│   ├── get-profile.controller.ts
│   ├── update-profile.service.ts
│   └── profile.repository.ts
├── search/
│   ├── search-users.controller.ts
│   └── user-search.repository.ts
└── index.ts
```

---

## 3. Design & Code Splitting Rules

### A. Responsibility-Based Splitting
Avoid giant dumping-ground files containing dozens of unrelated functions.

#### Repositories
Instead of one massive `user.repository.ts` containing authentication queries, profile updates, audit logs, and search filters, split by query responsibility:
```text
users/repositories/
├── user-read.repository.ts      # Querying user profiles & details
├── user-write.repository.ts     # User mutations & updates
├── user-search.repository.ts    # Search filters & pagination queries
└── user-auth.repository.ts      # Auth credentials & token persistence
```

#### Services
Instead of a single 2,000-line `user.service.ts`, split by capability or use-case:
```text
users/services/
├── create-user.service.ts
├── update-user.service.ts
├── authenticate-user.service.ts
└── search-users.service.ts
```

### B. Module Boundary & Encapsulation Rules
1. **Public API Export (`index.ts`)**: Every module must expose its public routes, services, and types via `index.ts`.
2. **No Internal Reaching**: External modules must import strictly from the module root (`src/modules/users`), NEVER reaching into internal files (`src/modules/users/services/user-profile.service.ts`).
3. **Shared Directory Rules**: Code shared by 3 or more modules belongs in `src/shared/`. Never place feature-specific logic in `src/shared/`.

---

## 4. When to Use & When Not to Use

### Use Modular Architecture When:
- Building feature-rich or growing Express backends.
- Multiple developers or teams work on different features concurrently.
- You want clear domain isolation without the overhead of heavy enterprise patterns (DDD/Clean).

### Do NOT Use Modular Architecture When:
- Building a trivial, single-file utility script or micro-endpoint (Use [Simple Architecture](./simple.md)).
- Maintaining a project with a deeply entrenched traditional Layered structure where refactoring is out of scope (Use [Layered Architecture](./layered.md)).

---

## 5. Refactoring Guidance for Modular Architecture

When refactoring a legacy global-layered codebase (`src/controllers/`, `src/services/`, `src/models/`) into Modular Architecture:

1. **Identify Feature Domains**: Group existing routes and logic into domain candidates (`users`, `orders`, `products`).
2. **Create Feature Directories**: Create `src/modules/<feature-name>/`.
3. **Move Files First**: Relocate controllers, services, and repositories into their feature module folder before editing their logic.
4. **Create `index.ts`**: Define public exports for the feature module.
5. **Update Imports**: Update all relative imports across the project to consume the module through its `index.ts`.
