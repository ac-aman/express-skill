---
title: Clean Architecture Guide
architecture_type: clean
complexity: high
best_for:
  - Enterprise applications
  - High testability requirements
  - Decoupled infrastructure and frameworks
  - Long-lived core business logic
tags:
  - express
  - clean-architecture
  - onion-architecture
  - dependency-inversion
---

# Clean Architecture Guidelines

## Overview
<!-- Write high-level description of Clean / Concentric Architecture for Express here -->

## When to Use & When Not to Use
<!-- List criteria for choosing Clean Architecture -->

## Recommended Folder Structure
```
src/
├── domain/
│   └── entities/
├── use-cases/
├── interface-adapters/
│   ├── controllers/
│   ├── presenters/
│   └── repositories/
├── infrastructure/
│   ├── database/
│   ├── express/
│   └── webserver/
└── app.js
```

## Dependency Rule & Layer Boundaries
<!-- Define the strict inner-pointing Dependency Rule and interface abstractions -->

## Dependency Injection & Inversion of Control
<!-- Instructions on wiring dependencies and IoC containers/factories -->

## Related Template
<!-- Reference relative path to template: ../../templates/clean/ -->
