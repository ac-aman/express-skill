---
title: Layered Architecture Guide
architecture_type: layered
complexity: medium
best_for:
  - Standard REST APIs
  - Traditional web applications
  - CRUD-focused backends
  - Small to medium teams
tags:
  - express
  - layered
  - mvc
  - controller-service-repository
---

# Layered Architecture Guidelines

## Overview
<!-- Write high-level description of Layered (Controller-Service-Repository / MVC) Architecture here -->

## When to Use & When Not to Use
<!-- List criteria for choosing Layered Architecture -->

## Recommended Folder Structure
```
src/
├── controllers/
├── services/
├── repositories/
├── models/
├── middlewares/
├── routes/
├── config/
└── app.js
```

## Layer Responsibilities & Rules
<!-- Define strict boundaries between Controllers, Services, and Repositories -->

## Data Flow & DTOs
<!-- Instructions on request data flow, validation, and DTO mapping -->

## Related Template
<!-- Reference relative path to template: ../../templates/layered/ -->
