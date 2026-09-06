---
title: Layered Architecture Specification Guide (Controller-Service-Repository)
architecture_type: layered
complexity: medium
best_for:
  - Standard REST APIs & traditional CRUD applications
  - Applications organized by technical layer
  - Small to medium codebases with established MVC patterns
tags:
  - express
  - layered
  - mvc
  - controller-service-repository
---

# Layered Architecture Specification Guide

Layered Architecture (n-tier / Controller-Service-Repository) organizes code horizontally into **technical layers**. Each layer has a specific responsibility (HTTP handling, business logic, data persistence) and communicates strictly with the layer directly beneath it.

---

## 1. Directory Structure Specifications

```text
src/
├── config/                 # Environment variables & configuration
├── controllers/            # Request handling & response formatting
├── services/               # Business logic & domain workflows
├── repositories/           # Data access & database queries
├── models/                 # Database entities / ORM model definitions
├── routes/                 # Express Router endpoint definitions
├── middlewares/            # Application & validation middlewares
├── types/                  # Shared TypeScript interfaces & DTOs
├── app.ts                  # Express application setup
└── server.ts               # HTTP server bootstrap & graceful shutdown
```

---

## 2. Layer Responsibilities & Data Flow

```text
HTTP Request → Routes → Middleware → Controller → Service → Repository → DB / Models
                                                                              │
HTTP Response ← Controller (JSON Payload) ← Service DTO ← Repository Entity ──┘
```

### Layer Responsibilities

1. **Presentation / Routes Layer (`src/routes/`)**:
   - Binds HTTP methods (`GET`, `POST`, `PUT`, `DELETE`) and paths (`/api/v1/users`).
   - Attaches validation middleware (`validate(schema)`) and auth middleware.
   - Delegates requests to controllers.
2. **Controller Layer (`src/controllers/`)**:
   - Reads request input (`req.body`, `req.params`, `req.query`).
   - Invokes appropriate service method.
   - Returns HTTP status codes (`200 OK`, `201 Created`, `400 Bad Request`) and JSON response envelopes (`{ success: true, data }`).
   - **Forbidden**: Never execute raw database queries or complex business logic inside controllers.
3. **Service Layer (`src/services/`)**:
   - Implements business logic, domain rules, calculation logic, and workflow orchestration.
   - Calls repository methods for data access.
   - **Forbidden**: Never accept Express `req` or `res` objects inside services. Accepts plain TypeScript objects/DTOs.
4. **Repository / Data Access Layer (`src/repositories/`)**:
   - Executes database operations using ORM/ODM drivers (Prisma, TypeORM, Mongoose).
   - Abstracts database queries away from services.
5. **Model Layer (`src/models/`)**:
   - Defines database schemas, ORM model mappings, or entity structures.

---

## 3. Strict Horizontal Layering Rules

1. **Downstream Dependency Direction**: Dependencies flow strictly top-down (`Controller → Service → Repository`).
2. **No Layer Bypassing**: Controllers must NEVER call Repositories directly. All data access must pass through Services.
3. **No Upstream Calls**: Repositories must never import or call Services. Services must never import or call Controllers.
4. **DTO Mapping**: Repositories return database entities/models. Services map entities into DTOs before returning data to Controllers.

---

## 4. When to Use & When Not to Use

### Use Layered Architecture When:
- Maintaining an existing traditional Express codebase organized by technical layers (`controllers/`, `services/`).
- Building standard CRUD applications with straightforward technical boundaries.

### Do NOT Use Layered Architecture When:
- Building a feature-heavy application with multiple domain teams (Use [Modular Architecture](./modular.md)).
- The codebase is growing large and finding all code for a single feature requires jumping across multiple global directories (Migrate to [Modular Architecture](./modular.md)).

---

## 5. Agent Layered Architecture Verification Checklist

When implementing or reviewing Layered Architecture:
- [ ] Are files organized by technical layer (`controllers/`, `services/`, `repositories/`)?
- [ ] Do controllers delegate all business logic to services without running raw DB queries?
- [ ] Are services free of Express `req` and `res` objects?
- [ ] Do controllers avoid calling repositories directly (no layer bypassing)?
- [ ] Do dependencies flow strictly downstream (`Controller → Service → Repository`)?
- [ ] Is global error handling and validation middleware active?
