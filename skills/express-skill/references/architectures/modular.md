---
title: Modular Architecture Specification Guide (Primary Default)
architecture_type: modular
complexity: medium-high
is_primary_default: true
best_for:
  - Feature-rich Express applications
  - Multi-team backend development
  - Monoliths scaling cleanly without giant files
  - Domain & feature-grouped backend services
tags:
  - express
  - modular
  - feature-based
  - progressive-decomposition
---

# Modular Architecture Specification Guide

Modular Architecture (Modular Monolith) is the **primary default architecture** for Express.js applications built using this skill. 

Instead of organizing code by technical layers across the entire project (e.g. all controllers in one global folder), Modular Architecture groups code by **business feature or domain capability**. Each feature lives in its own module directory under `src/modules/<feature-name>/` and owns its HTTP handling, business logic, data access, schemas, and types.

---

## 1. Directory Structure Specifications

### Root Application Layout

```text
src/
├── config/                 # Environment variable parsing & application config
├── modules/                # Feature modules (Domain boundaries)
│   ├── auth/               # Authentication module
│   ├── users/              # User management module
│   ├── orders/             # Order management module
│   └── payments/           # Payment processing module
├── shared/                 # Cross-cutting infrastructure & shared kernel
│   ├── database/           # DB client setup (Prisma, Mongoose, TypeORM)
│   ├── middleware/         # Global middlewares (error handler, rate limit, auth)
│   ├── errors/             # Base AppError classes
│   └── utils/              # Generic utility functions (date formatters, logger)
├── app.ts                  # Express application setup & router binding
└── server.ts               # HTTP server bootstrap & graceful shutdown
```

---

## 2. Progressive Decomposition Lifecycle

Do **not** create empty subdirectories upfront. Expand internal module structures progressively as feature complexity demands.

### Stage 1: Flat Feature Module (Small / Emerging Feature)
For new or compact feature modules with few operations, keep all files flat inside the module root:

```text
src/modules/users/
├── user.controller.ts     # Request handling & HTTP status mapping
├── user.service.ts        # Business logic & domain rules
├── user.repository.ts     # Database queries / ORM operations
├── user.routes.ts         # Express Router endpoints binding
├── user.schema.ts         # Input validation schemas (Zod/Joi)
├── user.types.ts          # TypeScript interfaces & DTOs
└── index.ts               # Public interface export file
```

### Stage 2: Component Subdirectories (Growing Feature)
As the number of endpoints and operations grows, separate files into component subdirectories:

```text
src/modules/users/
├── controllers/
│   ├── create-user.controller.ts
│   ├── get-user.controller.ts
│   └── update-user.controller.ts
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
└── index.ts               # Public interface export file
```

> **Rule**: Do not create a component folder by default. Create a folder (e.g., `repositories/`) only when there are multiple files or a clear need for that component role.

### Stage 3: Sub-Feature Capability Grouping (Large Enterprise Feature)
When a single module covers distinct major capabilities (e.g. Authentication vs Profile vs Search), group internally by capability:

```text
src/modules/users/
├── authentication/
│   ├── login.controller.ts
│   ├── login.service.ts
│   └── auth.routes.ts
├── profile/
│   ├── update-profile.controller.ts
│   ├── profile.service.ts
│   └── user-profile.repository.ts
├── search/
│   ├── search-users.controller.ts
│   └── user-search.repository.ts
└── index.ts               # Module-level public export file
```

---

## 3. Component Responsibilities & Data Flow

Every request follows a strict unidirectional data flow through module components:

```text
HTTP Request → Routes → Middleware (Validate & Auth) → Controller → Service → Repository → DB
                                                                                             │
HTTP Response ← Controller (JSON Payload) ← Service DTO ← Repository Entity ←──────────────┘
```

### Component Responsibilities

1. **`routes/` (Express Router)**:
   - Defines HTTP methods and paths (`router.post('/', ...)`).
   - Attaches validation middleware (`validate(schema)`) and auth middleware (`authenticate`, `authorize`).
   - Delegates request to controller handlers.
2. **`controllers/` (HTTP Interface)**:
   - Reads request input (`req.body`, `req.params`, `req.query`).
   - Delegates business operations to service layer.
   - Formats HTTP status codes (`200 OK`, `201 Created`) and returns JSON responses using standard response envelopes (`{ success: true, data }`).
   - **Forbidden**: Never execute raw database queries or complex domain calculations inside controllers.
3. **`services/` (Business & Domain Logic)**:
   - Contains core business logic, domain rules, and workflow orchestration.
   - Calls repository layer for data persistence.
   - **Forbidden**: Never accept Express `req` or `res` objects inside services. Services must accept plain TypeScript objects/DTOs.
4. **`repositories/` (Data Persistence)**:
   - Executes database operations using ORM/ODM drivers (Prisma, TypeORM, Mongoose).
   - Handles queries, mutations, and database transactions.
   - **Splitting Rule**: When repositories grow large, split by responsibility (`user-read.repository.ts`, `user-write.repository.ts`, `user-search.repository.ts`).
5. **`schemas/` (Input Validation)**:
   - Defines Zod or Joi schemas for request validation.
6. **`types/` (Type Definitions)**:
   - Contains DTO interfaces and type definitions specific to the module.
7. **`index.ts` (Public Interface Boundary)**:
   - Exports the public interface of the module (routes, public services, public types).

---

## 4. Module Encapsulation & Inter-Module Communication Rules

### Rule 4.1: Public Boundary Encapsulation (`index.ts`)
Every module must define a clear public interface in `index.ts`:

```typescript
// src/modules/users/index.ts
export { default as userRouter } from './routes/user.routes';
export { UserService } from './services/user-management.service';
export type { UserDTO } from './types/user.types';
```

### Rule 4.2: Zero Internal File Reaching
External modules must ONLY import from the target module's root index. Direct imports into internal implementation files of another module are **FORBIDDEN**:

```typescript
// Good (Imports via module root):
import { UserService } from '../users';

// Bad (FORBIDDEN - Reaching into internal implementation files):
import { UserService } from '../users/services/user-management.service';
```

### Rule 4.3: Inter-Module Communication Patterns
When Module A requires data or actions from Module B:
1. **Direct Service Call**: Call Module B's public `UserService` exported via Module B's `index.ts`.
2. **Asynchronous Events**: Emit domain events (e.g. `order.created`) via an event emitter/queue for asynchronous side effects (e.g. notifications, analytics) to prevent tight coupling.

---

## 5. Shared Kernel Boundary (`src/shared/`)

The `src/shared/` directory is reserved strictly for cross-cutting infrastructure used across 3 or more modules:

- **Database Client**: `src/shared/database/prisma.client.ts`
- **Global Middleware**: `src/shared/middleware/error.middleware.ts`, `auth.middleware.ts`
- **Base Error Hierarchy**: `src/shared/errors/app-error.ts`
- **Generic Utilities**: `src/shared/utils/password.util.ts`

> **Forbidden**: Never place module-specific domain logic, schemas, or feature models inside `src/shared/`.

---

## 6. Readable Mini-Comments & TODO Protocol

### A. Readable Mini-Comments
Add concise 1-line comments explaining multi-step business orchestrations inside services:
```typescript
// Verify account status before initiating order transaction
await userService.assertUserActive(userId);

// Reserve inventory items atomically inside database transaction
await inventoryService.reserveItems(items, tx);
```

### B. TODO Protocol for Underspecified Module Boundaries
If a feature module's scope, database schema, or inter-module event contract is underspecified, insert an explicit `// TODO:` comment:

```typescript
// TODO: Clarify if Order module should publish 'order.cancelled' event to Payment module.
```

---

## 7. Refactoring Legacy Code to Modular Architecture

When refactoring a legacy global-layered codebase (`src/controllers/`, `src/services/`, `src/models/`) into Modular Architecture:

1. **Identify Domain Features**: Group existing routes and models into domain boundaries (`users`, `orders`, `products`).
2. **Create Module Directory**: Create `src/modules/<feature-name>/`.
3. **Move Implementation Files**: Move related controllers, services, repositories, and routes into the feature module.
4. **Create `index.ts`**: Define explicit public exports for the module.
5. **Update Imports**: Search workspace and update all import paths to consume the module via `index.ts`.

---

## 8. Agent Modular Architecture Verification Checklist

When implementing or reviewing Modular Architecture:
- [ ] Is each feature self-contained inside `src/modules/<feature-name>/`?
- [ ] Does every module export its public routes, services, and types via `index.ts`?
- [ ] Are external imports passing strictly through module root `index.ts` files without reaching into internal subfolders?
- [ ] Is progressive decomposition followed (flat files → component subdirectories → capability subfolders)?
- [ ] Are controllers kept free of raw database queries and domain business calculations?
- [ ] Are services kept free of Express `req` and `res` objects?
- [ ] Is `src/shared/` restricted to cross-cutting infrastructure (DB client, global error middleware)?
- [ ] Are underspecified module boundaries or event contracts marked with explicit `// TODO:` comments?
