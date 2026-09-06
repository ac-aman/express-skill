---
title: TypeScript Standards & Declaration Guideline (Generic)
category: guidelines
---

# Generic TypeScript Standards & Declaration Guidelines

This guideline defines architecture-agnostic standards for strict TypeScript type safety, ESLint alignment, zero `any` usage, type declaration (`.d.ts`) files, Express request augmentation, and DTO definitions across Express.js backends.

---

## 1. Strict Type Safety Rules (No `any`)

1. **Zero `any` Types Allowed**: `any` is strictly **FORBIDDEN** in controllers, services, repositories, middlewares, schemas, and tests.
2. **Use `unknown` for Dynamic Data**: When receiving un-validated external data or generic JSON payloads, use `unknown` combined with type narrowing or Zod parsing (`z.infer<typeof schema>`).
3. **Strict Compiler Options**: All Express TypeScript projects must configure `strict: true`, `noImplicitAny: true`, and `strictNullChecks: true` in `tsconfig.json`.
4. **Explicit Return Types**: All exported functions, service methods, and controller handlers must declare explicit return types (`Promise<void>`, `Promise<UserDTO>`).

---

## 2. ES Modules & Import Conventions

1. **Type-Only Imports**: Use explicit `import type` syntax when importing TypeScript interfaces or type aliases to enable clean build stripping and prevent circular runtime imports:
   ```typescript
   // Good (Type-only imports):
   import type { Request, Response, NextFunction } from 'express';
   import type { UserDTO, CreateUserInput } from '../types/user.types';
   ```
2. **ES Module Syntax**: Always use standard ES `import`/`export` statements. CommonJS `require()` and `module.exports` are forbidden unless configuring legacy CommonJS tools.

---

## 3. Global Declarations & `.d.ts` Files

Store global type augmentations and ambient declarations inside `src/types/`:

```text
src/
├── types/
│   ├── express.d.ts        # Express Request augmentation (req.user)
│   ├── global.d.ts         # Global environment & process types
│   └── index.d.ts          # Shared ambient declarations
```

### A. Express Request Augmentation (`src/types/express.d.ts`)
Augment the global `Express.Request` interface to safely type custom request properties attached by middleware (e.g. `req.user`):

```typescript
// src/types/express.d.ts
import type { AuthenticatedUser } from '../modules/auth/types/auth.types';

declare global {
  namespace Express {
    interface Request {
      user?: AuthenticatedUser;
      correlationId?: string;
    }
  }
}

export {}; // Ensure file is treated as a module module
```

### B. Ambient Global Environment Types (`src/types/global.d.ts`)

```typescript
// src/types/global.d.ts
declare namespace NodeJS {
  interface ProcessEnv {
    NODE_ENV: 'development' | 'production' | 'test';
    PORT?: string;
    DATABASE_URL: string;
    JWT_SECRET: string;
  }
}
```

---

## 4. DTO & Interface Conventions

- Use `interface` for entity contracts and public service APIs.
- Use `type` for union types, intersection types, and Zod inferred types (`type CreateUserInput = z.infer<typeof createUserSchema>`).
- Do **NOT** prefix interfaces with `I` (e.g. use `UserDTO` or `UserRepositoryPort`, not `IUserDTO`).

---

## 5. Comments & TODO Protocol

For rules on adding mini-comments or marking complex generic type constraints with `// TODO:` comments, see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 6. Agent TypeScript Verification Checklist

- [ ] Is `tsconfig.json` configured with `strict: true` and `noImplicitAny: true`?
- [ ] Are zero `any` types present in the codebase (using `unknown`, generics, or Zod schemas instead)?
- [ ] Are type-only imports using `import type { ... }` syntax?
- [ ] Is `Express.Request` augmented in `src/types/express.d.ts` for custom properties (`req.user`)?
- [ ] Do all exported functions have explicit return types?
