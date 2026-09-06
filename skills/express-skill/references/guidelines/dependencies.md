---
title: Implementation Safety & Anti-Hallucination Guideline
category: guidelines
---

# Implementation Safety Rules (Anti-Hallucination)

When building or refactoring Express.js applications, agents must follow strict operational rules to avoid inventing dependencies, introducing unneeded abstractions, or breaking existing codebase functionality.

---

## 1. Do Not Invent Project Details

Before modifying an existing Express project:

1. **Inspect Existing Code**: Read `package.json` and directory structure before generating code.
2. **Identify Core Tech Stack**: Determine the existing database, ORM/ODM (Prisma, TypeORM, Mongoose), validation library (Zod, Joi), and auth mechanism.
3. **Reuse Existing Patterns**: Align with established project conventions when they are consistent and maintainable.
4. **Do Not Invent Files or Dependencies**: Never assume a library is installed without checking `package.json`.
5. **Do Not Invent Environment Variables or Schema Fields**: Ensure environment variables and database models exist before referencing them.
6. **Do Not Replace Established Architecture**: Do not rewrite a project's architecture without an explicit instruction to do so.

---

## 2. Preserve Existing Behavior

When refactoring code:

1. **Understand Before Modifying**: Fully read and analyze existing logic before making edits.
2. **Preserve Public Contracts**: Maintain API request/response format, status codes, and query params unless instructed to change behavior.
3. **Move Before Rewriting**: When splitting large files, move existing working code to new files first before attempting refactoring.
4. **Update All Import Statements**: Check and update all import/export references across the codebase after moving files.
5. **Do Not Delete Functional Code**: Never remove logic simply because its purpose is not immediately obvious.

---

## 3. Do Not Generate Placeholder Architecture

Avoid creating empty abstractions merely because an architectural pattern supports them.

Do NOT generate empty or unused:
- `factories/`
- `adapters/`
- `ports/`
- `interfaces/`
- `events/`
- `mappers/`

Architecture must follow actual project requirements, not theoretical completeness.
