---
title: Naming Conventions Guideline
category: guidelines
---

# Naming Conventions

Consistent naming rules ensure clear intent and easy file navigation across Express codebases.

---

## File Naming Conventions

Use **kebab-case** with explicit role suffixes for all implementation files.

| Component | Naming Format | Example |
| :--- | :--- | :--- |
| **Controller** | `<feature>.<role>.controller.ts` or `<use-case>.controller.ts` | `user.controller.ts`, `create-user.controller.ts` |
| **Service** | `<feature>.<role>.service.ts` or `<capability>.service.ts` | `user.service.ts`, `authenticate-user.service.ts` |
| **Repository** | `<feature>.<role>.repository.ts` | `user-read.repository.ts`, `user-write.repository.ts` |
| **Route** | `<feature>.routes.ts` | `user.routes.ts`, `auth.routes.ts` |
| **Schema** | `<feature>.schema.ts` or `<action>.schema.ts` | `user.schema.ts`, `create-user.schema.ts` |
| **Types** | `<feature>.types.ts` | `user.types.ts` |

---

## Code Symbol Naming Conventions

- **Classes**: `PascalCase` with role suffix (`CreateUserService`, `UserRepository`, `AuthController`).
- **Functions & Methods**: `camelCase` starting with strong action verbs (`createUser`, `findUserById`, `validateAuthToken`).
- **Interfaces & Types**: `PascalCase` (`UserDTO`, `CreateUserInput`, `AuthPayload`). Do NOT prefix interfaces with `I` (e.g. use `UserService` instead of `IUserService` unless explicitly required by existing codebase).
- **Constants**: `UPPER_SNAKE_CASE` (`MAX_LOGIN_ATTEMPTS`, `DEFAULT_PAGE_SIZE`).
- **Routes & Endpoints**: Kebab-case plural nouns for HTTP paths (`/api/v1/users`, `/api/v1/payment-intents`).
