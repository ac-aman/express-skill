---
title: Simple Architecture Specification Guide
architecture_type: simple
complexity: low
best_for:
  - Microservices & single-purpose APIs
  - Rapid prototyping & hackathons
  - Small internal utilities or webhooks
  - Applications with fewer than 5 endpoints
tags:
  - express
  - simple
  - minimal
  - single-file
---

# Simple Architecture Specification Guide

Simple Architecture is designed for low-complexity Express.js backends, single-purpose microservices, internal scripts, or quick prototypes. It minimizes boilerplate and abstractions by placing route handlers and logic into a flat, minimal file layout.

---

## 1. Directory Structure Specifications

### Minimal Single-File / Flat Layout

```text
src/
├── config.ts               # Environment variables loading & parsing
├── handlers.ts             # Route handler functions & simple business logic
├── routes.ts               # Express Router mounting endpoints
├── app.ts                  # Express app setup & middleware mounting
└── server.ts               # HTTP server bootstrap & graceful shutdown
```

*For ultra-small utilities (1-2 endpoints), `handlers.ts`, `routes.ts`, and `app.ts` may be combined into `index.ts`.*

---

## 2. Component Responsibilities & Data Flow

```text
HTTP Request → server.ts → app.ts → routes.ts → handlers.ts → DB / Response
```

1. **`server.ts`**: Starts server listener (`app.listen(PORT)`) and handles OS signals (`SIGTERM`).
2. **`app.ts`**: Instantiates Express, attaches middleware (`express.json()`, `cors`, `helmet`), mounts `routes.ts`, and registers `errorHandler`.
3. **`routes.ts`**: Binds HTTP methods (`GET`, `POST`) to handler functions in `handlers.ts`.
4. **`handlers.ts`**: Reads input, executes database queries (using simple ORM/query builder), and sends HTTP JSON response.

---

## 3. Key Guidelines & Rules

1. **No Over-Engineering**: Do not create controllers, services, repositories, or DTO classes. Functions in `handlers.ts` handle request processing directly.
2. **Centralized Error Handling**: Throw or pass errors to Express's global error handler. Use standard HTTP status numbers or `http-status-codes`.
3. **Transition Trigger**: When `handlers.ts` exceeds ~300 lines or 5 distinct business domains, transition the project to [Modular Architecture](./modular.md) or [Layered Architecture](./layered.md).

---

## 4. When to Use & When Not to Use

### Use Simple Architecture When:
- Building a webhook listener, micro-utility, or proof of concept.
- The entire project has 1 to 5 total API endpoints.
- Maximum speed of development is required without long-term maintainability concerns.

### Do NOT Use Simple Architecture When:
- Building a core product API expected to grow over time.
- Working with a team requiring distinct domain ownership.

---

## 5. Agent Simple Architecture Verification Checklist

When implementing Simple Architecture:
- [ ] Is server listener (`server.ts`) separated from app setup (`app.ts`)?
- [ ] Are route handlers kept in flat files without unnecessary abstraction layers?
- [ ] Are environment variables isolated in `config.ts`?
- [ ] Is global error handling active?
- [ ] Is a transition plan to Modular/Layered architecture flagged if endpoint count exceeds 5?
