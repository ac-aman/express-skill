---
name: express-skill
description: Comprehensive workflow, standards, and architecture guide for building production-ready Express.js web applications and REST APIs.
license: MIT
metadata:
  author: "ac-aman"
  version: "1.0.0"
  tags: ["express", "node", "typescript", "rest-api", "backend","MVC", "Features"]
---

# Express.js Skill Guide

This skill provides AI agents with standard patterns, architecture rules, security standards, and workflow procedures for designing, building, debugging, and maintaining Express.js backend services.

---

## Core Architectural Principles

When building or refactoring Express.js applications, always enforce the following architectural rules:

1. **Layered Architecture (Separation of Concerns)**
   - **Routes**: Define HTTP endpoints, map request methods, attach middleware, and pass control to controllers.
   - **Controllers**: Parse input, call service methods, handle response status codes and format JSON output. Keep controllers thin; do not embed business logic or raw database queries here.
   - **Services**: Encapsulate core business logic, domain rules, third-party integrations, and transactions.
   - **Repositories / Models**: Manage database access, schema definitions, and ORM/Query Builder calls.

2. **Strict Request Validation**
   - Use `zod` schema validation on incoming request bodies, query params, and URL path parameters before touching service layers.

3. **Centralized Error Handling**
   - Never handle errors with ad-hoc `try/catch` blocks inside controllers without passing exceptions to Express's global error handler.
   - Express 4: Wrap async route handlers with an `asyncHandler` wrapper or return promises cleanly.
   - Express 5: Async errors automatically pass to `next(err)`.

4. **Security Defaults**
   - Apply `helmet` middleware early in the app lifecycle.
   - Enable CORS with explicit origin constraints (`cors()`).
   - Standardize request rate limiting using `express-rate-limit`.
   - Never expose raw database errors, stack traces, or internal server details in production responses.

---

## Directory Structure Standard

Follow this canonical directory structure for Express projects:

```text
src/
├── config/             # Environment variables (dotenv/zod) & server settings
├── controllers/        # Request handlers (thin controller layer)
├── middleware/         # Custom & third-party middleware (auth, error, logger, validation)
├── models/             # Database schemas / ORM models (Prisma, Mongoose, Kysely, TypeORM)
├── routes/             # Express router definitions & mounting
├── services/           # Business logic & domain services
├── utils/              # Helper functions, logger instance, custom error classes
├── app.ts              # Express application configuration & middleware setup
└── server.ts           # HTTP server initialization & graceful shutdown setup
```

---

## Recommended Packages

| Concern | Package | Usage |
| :--- | :--- | :--- |
| **Framework** | `express` | Main Web Framework |
| **Validation** | `zod` | Input Schema Validation |
| **Security** | `helmet`, `cors`, `express-rate-limit` | HTTP Security & Rate Limiting |
| **Logging** | `pino` or `winston`, `morgan` | Structured Logging |
| **Auth** | `jsonwebtoken`, `argon2` or `bcrypt` | Auth & Password Hashing |
| **Testing** | `supertest`, `vitest` / `jest` | API Integration Testing |
| **Dev Tools** | `tsx` or `nodemon`, `typescript` | Dev Server Execution |
