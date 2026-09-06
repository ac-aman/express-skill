---
title: Hexagonal Architecture Guide (Ports & Adapters)
architecture_type: hexagonal
complexity: high
best_for:
  - Multi-interface applications (REST, gRPC, CLI, Message Queue)
  - Systems with swappable databases or external APIs
  - Independent core logic isolated from driver/driven delivery mechanisms
tags:
  - express
  - hexagonal
  - ports-and-adapters
---

# Hexagonal Architecture Guidelines

## Overview
<!-- Write high-level description of Hexagonal (Ports & Adapters) Architecture for Express here -->

## When to Use & When Not to Use
<!-- List criteria for choosing Hexagonal Architecture -->

## Recommended Folder Structure
```
src/
├── core/
│   ├── domain/
│   └── services/
├── ports/
│   ├── driving/          # Inbound ports (e.g. Primary interfaces, UseCases)
│   └── driven/           # Outbound ports (e.g. Secondary interfaces, Repositories)
├── adapters/
│   ├── driving/          # Express HTTP routes, gRPC controllers, CLI
│   └── driven/           # Postgres DB client, Redis adapter, HTTP clients
└── app.js
```

## Primary (Driving) vs Secondary (Driven) Ports & Adapters
<!-- Guidelines for driving adapters vs driven adapters and port contract definitions -->

## Testing Strategy
<!-- Instructions for mocking driven ports and unit testing domain core independently -->

## Related Template
<!-- Reference relative path to template: ../../templates/hexagonal/ -->
