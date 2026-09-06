---
title: Input Validation Guideline (Generic & Production-Grade)
category: guidelines
---

# Generic Input Validation Guidelines

This guideline defines architecture-agnostic standards for request payload validation, input sanitization, schema co-location, error formatting, and library usage in Express.js backends.

---

## 1. Core Validation Principles

1. **Validate All External Inputs**: Every incoming HTTP request parameter (`req.body`, `req.params`, `req.query`, `req.headers`) must be strictly validated before reaching controllers or service layers.
2. **Schema-Based Validation**: Use schema validation libraries (`zod` or `joi`) rather than writing manual `if/else` checks inside controllers.
3. **Fail-Fast Behavior**: Immediately reject invalid payloads with an HTTP `400 Bad Request` status and a structured error payload detailing the exact invalid fields.
4. **Sanitize & Strip Unknown Fields**: Configure validation schemas to strip unknown query parameters or body properties to prevent Mass Assignment vulnerability attacks.
5. **Type Coercion & Parsing**: Use schemas to parse strings into numbers (e.g. `req.query.page`), convert strings to ISO Dates, and trim whitespace.

---

## 2. Reusable Validation Middleware Pattern (Zod Example)

```typescript
// src/shared/middleware/validate.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { AnyZodObject, ZodError } from 'zod';
import { StatusCodes } from 'http-status-codes';

export const validate = (schema: AnyZodObject) => {
  return async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const parsed = await schema.parseAsync({
        body: req.body,
        query: req.query,
        params: req.params,
      });

      req.body = parsed.body;
      req.query = parsed.query as any;
      req.params = parsed.params as any;
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        res.status(StatusCodes.BAD_REQUEST).json({
          success: false,
          error: {
            code: 'VALIDATION_ERROR',
            message: 'Invalid request input parameters',
            details: error.errors.map((e) => ({ field: e.path.join('.'), message: e.message })),
          },
        });
        return;
      }
      next(error);
    }
  };
};
```

---

## 3. Schema Co-location & Example

Co-locate schemas with feature modules: `src/modules/users/schemas/create-user.schema.ts`.

```typescript
// src/modules/users/schemas/create-user.schema.ts
import { z } from 'zod';

export const createUserSchema = z.object({
  body: z.object({
    email: z.string().email('Invalid email address format'),
    password: z.string().min(8, 'Password must be at least 8 characters long'),
    fullName: z.string().min(2).max(100).trim(),
    role: z.enum(['USER', 'ADMIN']).default('USER'),
  }),
});

export type CreateUserInput = z.infer<typeof createUserSchema>;
```

---

## 4. Comments & TODO Protocol

For rules on adding mini-comments or marking underspecified validation rules (e.g. `// TODO: Confirm password complexity requirements...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 5. Agent Validation Checklist

- [ ] Are all HTTP request bodies, path params, and query strings validated before reaching controllers?
- [ ] Is input validation handled using schema middleware (`validate(schema)`) instead of manual `if/else` checks in controllers?
- [ ] Do validation failures return HTTP `400 Bad Request` with structured `{ field, message }` details?
- [ ] Are unknown request body fields stripped to prevent mass assignment vulnerabilities?
- [ ] Are underspecified validation rules explicitly marked with `// TODO:` comments per [code-comments-and-todos.md](./code-comments-and-todos.md)?
