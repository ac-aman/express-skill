---
title: Code Organization Guideline (Architecture-Agnostic)
category: guidelines
---

# Code Organization Guidelines

This guideline defines universal, architecture-agnostic rules for organizing Express.js codebases. These principles apply whether the project follows Simple, Layered, Modular, Clean, DDD, or Hexagonal architecture.

---

## 1. Entry Point & Bootstrap Separation

Every Express application must separate server bootstrapping from application configuration:

### Rule 1.1: Server Entry Point (`server.ts` / `server.js`)
- **Single Responsibility**: Manages HTTP server lifecycle, port binding, and OS signal handlers (`SIGTERM`, `SIGINT`).
- **Forbidden**: Do not define routes, middleware, or business logic in `server.ts`.
- **Example**:
  ```typescript
  import app from './app';
  import { config } from './config';

  const server = app.listen(config.PORT, () => {
    console.log(`Server running on port ${config.PORT}`);
  });

  process.on('SIGTERM', () => {
    server.close(() => process.exit(0));
  });
  ```

### Rule 1.2: Application Setup (`app.ts` / `app.js`)
- **Single Responsibility**: Instantiates Express app, attaches global middleware, mounts root routers, and registers the global error handler.
- **Forbidden**: Never call `app.listen()` inside `app.ts`.

---

## 2. Configuration Isolation (`config/`)

- All environment variable loading (`process.env`), parsing, and infrastructure configs must live in `src/config/`.
- **Forbidden**: Do not read raw `process.env` inside controllers, services, repositories, or route handlers. Always import from `config`.

---

## 3. Structural Cohesion & Boundary Rules

1. **Follow Selected Architecture**: Organize files strictly according to the active architecture specification ([Modular](../architectures/modular.md), [Layered](../architectures/layered.md), [Clean](../architectures/clean.md), [DDD](../architectures/ddd.md), [Hexagonal](../architectures/hexagonal.md), or [Simple](../architectures/simple.md)).
2. **Explicit Public Boundaries**: Use index export files (`index.ts` / `index.js`) at module or layer boundaries to define public exports.
3. **No Direct Internal Reaching**: Import from boundary export files instead of reaching into internal implementation files of another module/layer.
4. **No Circular Dependencies**: Ensure dependency direction flows strictly downstream (e.g. Controllers → Services → Repositories). Never create circular imports between components.

---

## 4. Shared Infrastructure (`src/shared/` or `src/common/`)

- Reserve global shared directories strictly for cross-cutting infrastructure used across multiple architectural units:
  - Database connection instances (e.g. Prisma client, Mongoose connection)
  - Global middlewares (error handler, auth middleware, logger)
  - Base error classes (`AppError`)
  - Generic utility functions (date formatters, password hashing)
- **Forbidden**: Do not place feature-specific or domain-specific business rules inside shared folders.

---

## 5. Agent Verification Checklist

When organizing or refactoring code, verify:
- [ ] Is server bootstrapping (`server.ts`) separated from app setup (`app.ts`)?
- [ ] Are all environment variable reads isolated in `config/`?
- [ ] Does every boundary export a clean public interface via `index.ts`?
- [ ] Are there zero circular imports across files?
- [ ] Is feature/domain logic kept out of global `shared/` directories?
