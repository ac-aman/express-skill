---
title: Dependencies & Anti-Hallucination Guideline (Generic)
category: guidelines
---

# Dependencies & Anti-Hallucination Guidelines

This guideline defines architecture-agnostic rules for package dependency inspection, anti-hallucination, avoiding wheel-reinvention by using well-maintained libraries, and managing npm packages in Express.js backends.

---

## 1. Zero Package Hallucination Rules

1. **Read `package.json` First**: Before generating imports or modifying code, inspect `package.json` (`dependencies` and `devDependencies`).
2. **Never Assume a Package is Installed**: Do NOT import any library (e.g. `zod`, `axios`, `prisma`, `helmet`, `http-status-codes`, `joi`, `bcrypt`) unless explicitly listed in `package.json`.
3. **Reuse Existing Project Stack**: Use the established library installed in the project. Do not introduce competing libraries (e.g., do not add `Joi` if `Zod` is installed; do not add `TypeORM` if `Prisma` is installed).

---

## 2. Prefer Maintained Libraries Over Custom Boilerplate

Do not write massive custom utility functions when a standard, well-maintained ecosystem library handles the responsibility cleanly.

| Domain | Preferred Maintained Library | Reason / Responsibility |
| :--- | :--- | :--- |
| **Input Validation** | `zod` or `joi` | Schema validation, type coercion, type inference |
| **Async Error Handling** | `express-async-errors` | Eliminates manual `try/catch` boilerplate in controllers |
| **HTTP Status Codes** | `http-status-codes` | Eliminates magic status numbers (`200`, `404`, `500`) |
| **Security Headers** | `helmet` | Automated HTTP security headers (HSTS, CSP, X-Frame) |
| **CORS** | `cors` | Cross-Origin Resource Sharing middleware |
| **Rate Limiting** | `express-rate-limit` | Preventing brute force and DoS attacks |
| **Environment Management** | `dotenv` | Loading `.env` into `process.env` |
| **Password Hashing** | `bcrypt` / `bcryptjs` / `argon2` | Secure password hashing & verification |
| **JWT Authentication** | `jsonwebtoken` | Signing & verifying JWT tokens |
| **HTTP Testing** | `supertest` | End-to-end integration testing of Express routes |

---

## 3. Handling Missing Maintained Libraries (TODO Protocol)

If a task requires boilerplate best solved by a maintained library, but the package is **NOT** present in `package.json`:
1. Do **NOT** import the missing library directly (`MODULE_NOT_FOUND` crash).
2. Do **NOT** write 500 lines of custom replacement code.
3. Use the TODO Protocol (see [code-comments-and-todos.md](./code-comments-and-todos.md)): Insert an explicit comment instructing to install the package (e.g., `// TODO: Install 'http-status-codes' (npm install http-status-codes)`).

---

## 4. Agent Verification Checklist

- [ ] Has `package.json` been inspected before introducing imports?
- [ ] Are all imported libraries verified to exist in `package.json`?
- [ ] Are existing installed libraries reused rather than adding duplicate alternatives?
- [ ] Are missing library recommendations clearly documented with `// TODO: npm install <package>` comments per [code-comments-and-todos.md](./code-comments-and-todos.md)?
