---
title: Hexagonal Architecture Specification Guide (Ports & Adapters)
architecture_type: hexagonal
complexity: high
best_for:
  - Multi-interface applications (REST API, gRPC, CLI, Message Queue consumers)
  - Applications with swappable infrastructure (e.g. switching DBs or third-party APIs)
  - Core domain isolation from driving actors and driven infrastructure
tags:
  - express
  - hexagonal
  - ports-and-adapters
  - primary-secondary-ports
---

# Hexagonal Architecture Specification Guide (Ports & Adapters)

Hexagonal Architecture (Ports and Adapters) decouples the core application logic from external driving actors (HTTP, gRPC, CLI) and driven infrastructure (Databases, Redis, Mail APIs). The core application sits at the center of the hexagon, communicating strictly through explicit **Ports** (TypeScript interfaces) implemented by **Adapters**.

---

## 1. Directory Structure Specifications

```text
src/
├── core/                           # Inside the Hexagon: Pure Application Logic
│   ├── domain/                     # Domain entities & business rules
│   └── services/                   # Application use-case implementations
├── ports/                          # Hexagonal Boundaries (Interfaces)
│   ├── driving/                    # Primary / Inbound Ports (Use Case interfaces)
│   └── driven/                     # Secondary / Outbound Ports (Repository & Gateway interfaces)
├── adapters/                       # Outside the Hexagon: Tech Implementations
│   ├── driving/                    # Primary Adapters (Triggers Core)
│   │   ├── http/                   # Express Controllers & Routes
│   │   ├── grpc/                   # gRPC handlers
│   │   └── cli/                    # CLI commands
│   └── driven/                     # Secondary Adapters (Invoked by Core)
│       ├── persistence/            # Prisma / Mongoose DB Adapters
│       ├── messaging/              # RabbitMQ / Kafka Adapters
│       └── email/                  # SendGrid / SES Adapters
├── config/                         # Environment config
├── app.ts                          # Express driving adapter setup
└── server.ts                       # Application entry point & IoC port wiring
```

---

## 2. Hexagonal Taxonomy & Data Flow

```text
                                +-----------------------------+
                                |     CORE APPLICATION        |
[Driving Adapter]   --------->  | [Driving Port] (Inbound)    |
Express Controller / CLI        |           │                 |
                                |     Core Services & Domain  |
                                |           │                 |
[Driven Adapter]    <---------  | [Driven Port] (Outbound)    |
Prisma DB / Redis               +-----------------------------+

Data Flow: Driving Adapter → Driving Port → Core Service → Driven Port → Driven Adapter
```

### Port & Adapter Taxonomy

1. **Driving Ports (Inbound / Primary)**: Interfaces defined by the core specifying what actions external actors can invoke (e.g., `CreateUserUseCasePort`).
2. **Driving Adapters (Primary)**: Entry points that translate external triggers into Driving Port calls (e.g., Express HTTP Controller, gRPC handler, CLI command).
3. **Driven Ports (Outbound / Secondary)**: Interfaces defined by the core specifying what external services the core requires (e.g., `UserRepositoryPort`, `NotificationGatewayPort`).
4. **Driven Adapters (Secondary)**: Tech implementations of Driven Ports (e.g., `PrismaUserRepository`, `SendGridNotificationAdapter`).

---

## 3. Code Implementation Example

### A. Driven Port & Adapter Example

```typescript
// src/ports/driven/user-repository.port.ts (Outbound Port)
export interface UserRepositoryPort {
  save(user: UserDomain): Promise<UserDomain>;
  findByEmail(email: string): Promise<UserDomain | null>;
}

// src/adapters/driven/persistence/prisma-user.adapter.ts (Secondary Adapter)
import { UserRepositoryPort } from '../../../ports/driven/user-repository.port';

export class PrismaUserAdapter implements UserRepositoryPort {
  async save(user: UserDomain): Promise<UserDomain> {
    const record = await prisma.user.create({ data: user.toRow() });
    return UserDomain.fromRow(record);
  }

  async findByEmail(email: string): Promise<UserDomain | null> {
    const record = await prisma.user.findUnique({ where: { email } });
    return record ? UserDomain.fromRow(record) : null;
  }
}
```

### B. Driving Adapter Example

```typescript
// src/adapters/driving/http/user.controller.ts (Primary Adapter)
import { Request, Response } from 'express';
import { CreateUserUseCasePort } from '../../../ports/driving/create-user-use-case.port';

export class UserController {
  constructor(private readonly createUserUseCase: CreateUserUseCasePort) {}

  async createUser(req: Request, res: Response): Promise<void> {
    const result = await this.createUserUseCase.execute(req.body);
    res.status(201).json({ success: true, data: result });
  }
}
```

---

## 4. When to Use & When Not to Use

### Use Hexagonal Architecture When:
- Building an application with multiple entry mechanisms (e.g., Express REST API + gRPC + CLI + Webhook consumer).
- Requiring completely swappable infrastructure (e.g., ability to switch from Postgres to MongoDB or mock external HTTP APIs without touching core logic).
- High emphasis on core domain unit testability.

### Do NOT Use Hexagonal Architecture When:
- Building a standard single-entry REST API (Use [Modular](./modular.md) or [Layered](./layered.md)).
- Creating ports and adapters for simple CRUD operations adds unnecessary interface indirection.

---

## 5. Agent Hexagonal Architecture Verification Checklist

When implementing Hexagonal Architecture:
- [ ] Is `src/core/` completely isolated with zero imports from Express or database drivers?
- [ ] Are Driving Ports defined in `src/ports/driving/` and Driven Ports defined in `src/ports/driven/`?
- [ ] Are Express controllers located strictly in `src/adapters/driving/http/`?
- [ ] Are DB repositories located strictly in `src/adapters/driven/persistence/` implementing Driven Ports?
- [ ] Can core services be tested in isolation by passing mock Driven Ports?
