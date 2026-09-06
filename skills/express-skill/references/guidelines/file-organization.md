---
title: File Organization Principles Guideline
category: guidelines
---

# File Organization Principles

This guideline outlines how files and folders should be organized across Express projects.

---

## Technical Layering vs Feature Grouping

### Technical Layering (Avoid for Large Codebases)
Organizing files globally by technology type (`src/controllers/`, `src/services/`, `src/routes/`) causes developer friction as projects grow. Finding all files related to `User` requires jumping between multiple global directories.

### Feature Grouping (Recommended)
Organizing files by feature (`src/modules/users/`, `src/modules/orders/`) keeps related code co-located, reducing context switching and enabling independent module evolution.

---

## Directory Root Layout

```text
src/
├── config/             # Application environment & configuration loading
├── modules/            # Domain feature modules
│   ├── auth/
│   ├── users/
│   └── billing/
├── shared/             # Cross-cutting utilities & infrastructure
│   ├── database/
│   ├── middleware/
│   ├── errors/
│   └── utils/
├── app.ts              # Express application configuration & router registration
└── server.ts           # HTTP server initialization & graceful shutdown
```

---

## Co-location Rules

1. **Keep Test Files Near Implementation**:
   - Prefer co-locating unit tests near the file being tested: `create-user.service.ts` and `create-user.service.spec.ts`.
   - Integration tests can be stored in `src/modules/<feature>/__tests__/` or `tests/integration/`.
2. **Schemas & Types**:
   - Place validation schemas (`zod`/`joi`) and types directly alongside the module that owns them.
