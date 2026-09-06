---
title: Domain-Driven Design (DDD) Guide
architecture_type: ddd
complexity: very-high
best_for:
  - Complex domain business logic
  - Event-driven systems
  - Large enterprise products
  - Cross-functional domain teams
tags:
  - express
  - ddd
  - domain-driven-design
  - bounded-contexts
  - aggregates
---

# Domain-Driven Design (DDD) Guidelines

## Overview
<!-- Write high-level description of Domain-Driven Design for Express backends here -->

## When to Use & When Not to Use
<!-- List criteria for choosing DDD Architecture -->

## Recommended Folder Structure
```
src/
├── contexts/
│   ├── ordering/
│   │   ├── domain/
│   │   │   ├── aggregates/
│   │   │   ├── value-objects/
│   │   │   ├── events/
│   │   │   └── services/
│   │   ├── application/
│   │   │   └── commands/
│   │   └── infrastructure/
│   │       └── persistence/
│   └── inventory/
├── shared/
│   └── kernel/
└── app.js
```

## Bounded Contexts & Aggregate Boundaries
<!-- Guidelines for defining bounded contexts, aggregates, value objects, and domain events -->

## Ubiquitous Language & Strategic Design
<!-- Rules for maintaining ubiquitous language across code and domain models -->

## Related Template
<!-- Reference relative path to template: ../../templates/ddd/ -->
