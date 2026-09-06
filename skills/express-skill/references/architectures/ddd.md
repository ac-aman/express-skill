---
title: Domain-Driven Design (DDD) Specification Guide
architecture_type: ddd
complexity: very-high
best_for:
  - Complex enterprise business domains
  - Systems organized into distinct Bounded Contexts
  - Event-driven architectures with rich domain invariants
  - Cross-functional domain expert & developer teams
tags:
  - express
  - ddd
  - domain-driven-design
  - bounded-contexts
  - aggregates
  - domain-events
---

# Domain-Driven Design (DDD) Specification Guide

Domain-Driven Design (DDD) models complex business domains by aligning software design directly with business domain concepts. It separates system capabilities into explicit **Bounded Contexts** and uses tactical patterns (Aggregates, Value Objects, Domain Events, Domain Services) to enforce domain invariants.

---

## 1. Directory Structure Specifications

```text
src/
├── contexts/                       # Strategic Bounded Contexts
│   ├── ordering/                   # Ordering Bounded Context
│   │   ├── domain/                 # Tactical Domain Model (Pure Domain Logic)
│   │   │   ├── aggregates/         # Aggregates & Aggregate Roots (OrderAggregate)
│   │   │   ├── value-objects/      # Immutable Value Objects (Money, OrderId)
│   │   │   ├── events/             # Domain Events (OrderCreatedEvent)
│   │   │   └── services/           # Domain Services
│   │   ├── application/            # Application Layer (Use cases / Command handlers)
│   │   │   ├── commands/           # CreateOrderCommand & CommandHandler
│   │   │   └── queries/            # OrderQueryHandler
│   │   └── infrastructure/         # Persistence & External Adapters
│   │       ├── persistence/        # OrderRepository implementation
│   │       └── http/               # Express routes & controllers
│   └── inventory/                  # Inventory Bounded Context
├── shared/
│   └── kernel/                     # Shared Kernel (DomainEventBus, BaseEntity, ValueObject)
├── config/
├── app.ts
└── server.ts
```

---

## 2. Tactical DDD Building Blocks

1. **Bounded Context**: An explicit linguistic and model boundary within which a domain model applies uniquely (e.g. `ordering` vs `inventory`).
2. **Aggregate & Aggregate Root**: A cluster of domain objects treated as a single unit for data changes. All external access passes through the Aggregate Root (`OrderAggregate`).
3. **Value Object**: Immutable domain concept defined by its attributes rather than identity (e.g. `Money({ amount: 100, currency: 'USD' })`, `EmailAddress`).
4. **Domain Event**: Records something significant that occurred in the domain (`OrderPlacedEvent`). Used to communicate asynchronously between Bounded Contexts.
5. **Domain Service**: Stateless domain operation that does not naturally belong to a single Aggregate.

---

## 3. Data Flow & Implementation Example

```text
HTTP Request → Bounded Context HTTP Controller → Application CommandHandler → Aggregate Root → Domain Event Bus → Event Handler
```

### Aggregate Root Example

```typescript
// src/contexts/ordering/domain/aggregates/order.aggregate.ts
import { AggregateRoot } from '../../../../shared/kernel/aggregate-root';
import { Money } from '../value-objects/money.vo';
import { OrderPlacedEvent } from '../events/order-placed.event';

export class OrderAggregate extends AggregateRoot {
  private constructor(
    public readonly id: string,
    private customerId: string,
    private totalAmount: Money,
    private status: 'DRAFT' | 'PLACED' | 'SHIPPED'
  ) {
    super();
  }

  public placeOrder(): void {
    if (this.status !== 'DRAFT') {
      throw new Error('Only draft orders can be placed');
    }
    this.status = 'PLACED';
    // Record domain event for event bus dispatch
    this.addDomainEvent(new OrderPlacedEvent(this.id, this.customerId, this.totalAmount));
  }
}
```

---

## 4. Ubiquitous Language & Context Boundaries

- **Ubiquitous Language**: Class names, methods, and variables must reflect exact business terms used by domain experts (`placeOrder()`, `reserveStock()`, NOT `updateOrderStatusTo2()`).
- **Context Boundaries**: Never share domain entities directly between Bounded Contexts. Use DTOs or Domain Events over a Shared Kernel Event Bus for inter-context communication.

---

## 5. When to Use & When Not to Use

### Use DDD Architecture When:
- Building complex enterprise domains with rich rules, invariants, and workflows.
- Developing event-driven microservices or modular monoliths with distinct Bounded Contexts.
- Working closely with domain experts who define complex business operations.

### Do NOT Use DDD Architecture When:
- Building standard CRUD applications with little to no domain logic (Use [Modular](./modular.md) or [Layered](./layered.md)).
- Applying DDD to simple domains causes massive overhead (Anemic Domain Model anti-pattern).

---

## 6. Agent DDD Verification Checklist

When implementing Domain-Driven Design:
- [ ] Is the codebase organized into explicit Bounded Contexts under `src/contexts/`?
- [ ] Are Aggregate Roots (`domain/aggregates/`) enforcing transactional consistency boundaries?
- [ ] Are Value Objects immutable with value-based equality?
- [ ] Are state changes inside Aggregates emitting Domain Events (`domain/events/`)?
- [ ] Are class and method names strictly adhering to the domain's Ubiquitous Language?
- [ ] Is inter-context communication handled via DTOs or Domain Events rather than entity sharing?
