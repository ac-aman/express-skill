---
title: Module Boundaries & Isolation Guideline
category: guidelines
---

# Module Boundaries and Isolation Guidelines

In a Modular Express architecture, maintaining clean module boundaries is essential to prevent tight coupling and spaghetti code.

---

## Core Rules

1. **Modules Are Self-Contained**: Each feature module under `src/modules/<feature>` should contain all logic necessary to serve that feature.
2. **Explicit Public Interface (`index.ts`)**: Every module must define a clear public interface via its `index.ts` file. 
3. **No Deep Internal Imports**: External modules must NEVER import directly from internal files of another module.
   - **Bad**: `import { UserService } from '../users/services/user-management.service';`
   - **Good**: `import { UserService } from '../users';`
4. **Decouple Module Interactions**: When Module A requires data from Module B, communicate via:
   - Public exported service interfaces
   - Domain events / Event emitter (for asynchronous tasks)
   - Shared DTOs or Types
5. **Shared Kernel Boundary (`src/shared/`)**:
   - Only code required by **3 or more modules** belongs in `src/shared/`.
   - Never put feature-specific domain logic into `src/shared/`.

---

## Module Export Example (`index.ts`)

```typescript
// src/modules/users/index.ts

// Export public router
export { default as userRouter } from './user.routes';

// Export public service for cross-module consumption
export { UserService } from './services/user.service';

// Export public types
export type { UserDTO, UserRole } from './types/user.types';
```

---

## Circular Dependency Prevention

- **Never create circular dependencies between modules.** (e.g., `users` imports `orders` AND `orders` imports `users`).
- If two modules depend heavily on each other, evaluate if:
  1. They should be merged into a single module.
  2. The shared interaction should be extracted into a sub-service or event-driven model.
