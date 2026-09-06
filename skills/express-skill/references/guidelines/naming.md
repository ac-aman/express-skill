---
title: Naming Conventions Guideline (Generic)
category: guidelines
---

# Generic Naming Conventions Guidelines

This guideline defines architecture-agnostic naming rules for files, directories, symbols, classes, interfaces, database entities, and HTTP routes in Express.js backends.

---

## 1. File & Directory Naming Conventions

### A. File Names
Use **kebab-case** with explicit role suffixes for all implementation files:

| Component Role | File Naming Pattern | Example |
| :--- | :--- | :--- |
| **Controller** | `<feature>.<role>.controller.ts` or `<use-case>.controller.ts` | `user.controller.ts`, `create-user.controller.ts` |
| **Service** | `<feature>.<role>.service.ts` or `<capability>.service.ts` | `user.service.ts`, `authenticate-user.service.ts` |
| **Repository** | `<feature>.<role>.repository.ts` | `user-read.repository.ts`, `user-write.repository.ts` |
| **Route** | `<feature>.routes.ts` | `user.routes.ts`, `auth.routes.ts` |
| **Schema** | `<feature>.schema.ts` or `<action>.schema.ts` | `user.schema.ts`, `create-user.schema.ts` |
| **Types / DTOs** | `<feature>.types.ts` or `<feature>.dto.ts` | `user.types.ts`, `create-user.dto.ts` |
| **Middleware** | `<purpose>.middleware.ts` | `auth.middleware.ts`, `error.middleware.ts` |

### B. Directory Names
Use **kebab-case** plural or feature names for directories (`src/modules/users/`, `src/controllers/`).

---

## 2. Symbol & Code Naming Conventions

| Code Element | Casing | Rule & Prefix/Suffix Convention | Example |
| :--- | :--- | :--- | :--- |
| **Classes** | `PascalCase` | End with role suffix | `CreateUserService`, `UserRepository`, `AuthController` |
| **Interfaces / Types** | `PascalCase` | Descriptive intent. Do NOT prefix with `I` | `UserDTO`, `CreateUserInput`, `AuthPayload` (Not `IUser`) |
| **Functions / Methods** | `camelCase` | Start with active verb | `createUser`, `findUserById`, `verifyAuthToken` |
| **Variables & Params** | `camelCase` | Descriptive nouns/nouns phrases | `userRecord`, `authToken`, `isEmailVerified` |
| **Booleans** | `camelCase` | Prefix with `is`, `has`, `should`, or `can` | `isActive`, `hasPermission`, `shouldSendNotification` |
| **Constants** | `UPPER_SNAKE_CASE` | Global/environment constants | `MAX_LOGIN_ATTEMPTS`, `DEFAULT_PAGE_LIMIT` |

---

## 3. HTTP Route & Parameter Naming Conventions

1. **REST Resource URIs**: Kebab-case plural nouns (`/api/v1/users`, `/api/v1/payment-intents`).
2. **Route Parameters**: `camelCase` (`/api/v1/users/:userId/orders/:orderId`).
3. **Query Parameters**: `camelCase` (`/api/v1/users?sortBy=createdAt&pageIndex=1`).

---

## 4. Comments & TODO Protocol

For rules on mini-comments or marking legacy naming refactors (e.g. `// TODO: Rename legacy file user_service.ts...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 5. Agent Verification Checklist

- [ ] Are all implementation files named using `kebab-case` with explicit role suffixes (`.controller.ts`, `.service.ts`)?
- [ ] Are class names `PascalCase` ending with their architectural role?
- [ ] Are TypeScript interfaces named without an `I` prefix (`UserDTO` instead of `IUserDTO`)?
- [ ] Are HTTP routes kebab-case plural nouns (`/api/v1/payment-intents`)?
- [ ] Are boolean variables prefixed with `is`, `has`, `should`, or `can`?
