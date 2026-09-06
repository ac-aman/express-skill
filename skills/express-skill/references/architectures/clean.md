---
title: Clean Architecture Specification Guide (Onion / Concentric)
architecture_type: clean
complexity: high
best_for:
  - Enterprise applications with long lifecycles
  - Core business logic isolated from frameworks & databases
  - Systems requiring maximum unit testability without DB dependencies
  - Explicit requirements for Dependency Inversion
tags:
  - express
  - clean-architecture
  - onion-architecture
  - dependency-inversion
---

# Clean Architecture Specification Guide

Clean Architecture (Onion / Concentric Architecture) isolates core business entities and use-cases from external frameworks, databases, UI, and third-party libraries. The architecture is governed by the strict **Dependency Rule**: source code dependencies must point strictly **inward** toward the core domain.

---

## 1. Directory Structure Specifications

```text
src/
├── domain/                 # Ring 1: Core Domain Entities & Invariants (Pure JS/TS)
│   ├── entities/           # Business entities & value objects
│   └── errors/             # Domain-specific errors
├── use-cases/              # Ring 2: Application Use Cases & Business Rules
│   ├── ports/              # Repository & gateway interfaces (Outbound Ports)
│   ├── dto/                # Input/Output Data Transfer Objects
│   └── <use-case>.ts       # Use case interactors (e.g. create-user.use-case.ts)
├── interface-adapters/     # Ring 3: Adapters Converting Data Between Use Cases & Infra
│   ├── controllers/        # Express HTTP request controllers
│   ├── presenters/         # Output response formatters
│   └── repositories/       # Concrete DB repository implementations
├── infrastructure/         # Ring 4: External Frameworks, Web Server & Drivers
│   ├── database/           # Prisma / Mongoose connection & ORM client
│   ├── express/            # Express app, routes & middleware
│   └── webserver/          # HTTP server bootstrap
├── config/                 # Environment variables config
└── server.ts               # Application entry point & IoC dependency wiring
```

---

## 2. Concentric Rings & Dependency Rule

```text
+--------------------------------------------------------------+
| Infrastructure & Frameworks (Express, Database, ORM)        |  Ring 4 (Outer)
|   +------------------------------------------------------+   |
|   | Interface Adapters (Controllers, Repositories)      |   |  Ring 3
|   |   +----------------------------------------------+   |   |
|   |   | Application Use Cases (Interactors, Ports)  |   |   |  Ring 2
|   |   |   +--------------------------------------+   |   |   |
|   |   |   | Core Domain Entities & Business Rules|   |   |   |  Ring 1 (Inner Core)
|   |   |   +--------------------------------------+   |   |   |
|   |   +----------------------------------------------+   |   |
|   +------------------------------------------------------+   |
+--------------------------------------------------------------+

Dependency Rule: Source code dependencies point strictly INWARD (Outer Ring → Inner Ring).
Inner Rings (Domain/Use-Cases) must NEVER import from Outer Rings (Express/DB/ORM).
```

---

## 3. Layer Responsibilities & Implementation Patterns

### Ring 1: Core Domain Entities (`src/domain/`)
- Contains enterprise business rules, entity models, and domain invariants.
- **Pure Code**: Zero imports from Express, Prisma, Mongoose, or external libraries.

### Ring 2: Application Use Cases (`src/use-cases/`)
- Contains application-specific business workflows (interactors).
- Defines **Outbound Ports** (TypeScript interfaces for repositories and external services).
- **Example Use Case & Port**:
  ```typescript
  // src/use-cases/ports/user-repository.port.ts
  export interface UserRepositoryPort {
    findByEmail(email: string): Promise<UserEntity | null>;
    save(user: UserEntity): Promise<UserEntity>;
  }

  // src/use-cases/create-user.use-case.ts
  export class CreateUserUseCase {
    constructor(private readonly userRepo: UserRepositoryPort) {}

    async execute(input: CreateUserDTO): Promise<UserEntity> {
      const existing = await this.userRepo.findByEmail(input.email);
      if (existing) throw new DomainError('Email already registered');
      const user = UserEntity.create(input);
      return this.userRepo.save(user);
    }
  }
  ```

### Ring 3: Interface Adapters (`src/interface-adapters/`)
- Converts data between Use Cases and Infrastructure format.
- Implements Outbound Ports (e.g. `PrismaUserRepository` implements `UserRepositoryPort`).

### Ring 4: Infrastructure (`src/infrastructure/`)
- Express app setup, HTTP route binding, database client connection.

---

## 4. When to Use & When Not to Use

### Use Clean Architecture When:
- Explicit requirement for strong dependency inversion and framework independence.
- Core business logic must be tested 100% in isolation without a database.
- The project has a long enterprise lifecycle expected to outlive specific ORMs or web frameworks.

### Do NOT Use Clean Architecture When:
- Building standard CRUD web applications or prototyping (Use [Modular](./modular.md) or [Layered](./layered.md)).
- Team is unfamiliar with Dependency Inversion Principle; over-abstracting simple APIs creates excessive boilerplate.

---

## 5. Agent Clean Architecture Verification Checklist

When implementing Clean Architecture:
- [ ] Do `src/domain/` and `src/use-cases/` have ZERO imports from Express, ORM drivers, or external frameworks?
- [ ] Are repository interfaces (Outbound Ports) defined in `src/use-cases/ports/` and implemented in `src/interface-adapters/repositories/`?
- [ ] Do source dependencies point strictly inward toward the core domain?
- [ ] Are use-case instances wired via Dependency Injection in `server.ts` or IoC factories?
- [ ] Can core use-cases be unit tested by passing mock repository ports without a database?
