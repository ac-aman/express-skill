---
title: Error Handling Guideline (Generic)
category: guidelines
---

# Generic Centralized Error Handling Guidelines

This guideline defines architecture-agnostic standards for error taxonomy, centralized error middleware, async error handling, production error sanitization, logger integration, and crash prevention in Express.js backends.

---

## 1. Core Principles

1. **Centralized Error Middleware**: All route handlers and middlewares must bubble errors up to a single centralized Express error middleware (`(err, req, res, next)`). Never send error HTTP responses directly inside service layers or controllers manually without passing through error handling standards.
2. **Operational vs Programmer Error Distinction**:
   - **Operational Errors**: Predictable runtime failures (e.g., 400 Validation Error, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict). Handled gracefully with custom `AppError` instances (`isOperational = true`).
   - **Programmer Errors**: Unhandled bugs or infrastructure crashes (e.g., syntax errors, DB connection failures). Handled with 500 status codes (`isOperational = false`) and sanitized in production.
3. **Async Error Catching**: Always handle async errors. Prefer using `express-async-errors` (or an async wrapper function) to catch rejected promises automatically.
4. **Never Swallow Errors**: Never write empty `catch (err) {}` blocks. Log every caught error or pass it to `next(err)`.
5. **Sanitize Production Responses**: Never leak raw internal error stack traces, SQL queries, or file paths in production.
6. **Unhandled Signal Protection**: Register process-level `uncaughtException` and `unhandledRejection` handlers in `server.ts`.

---

## 2. Implementation & Custom `AppError` Hierarchy

```typescript
// src/shared/errors/app-error.ts
import { StatusCodes } from 'http-status-codes';

export class AppError extends Error {
  constructor(
    message: string,
    public readonly statusCode: number = StatusCodes.INTERNAL_SERVER_ERROR,
    public readonly errorCode: string = 'INTERNAL_ERROR',
    public readonly isOperational: boolean = true,
    public readonly details?: any[]
  ) {
    super(message);
    Object.setPrototypeOf(this, new.target.prototype);
    Error.captureStackTrace(this, this.constructor);
  }
}

export class BadRequestError extends AppError {
  constructor(message = 'Bad Request', details?: any[]) {
    super(message, StatusCodes.BAD_REQUEST, 'BAD_REQUEST', true, details);
  }
}

export class NotFoundError extends AppError {
  constructor(message = 'Resource not found') {
    super(message, StatusCodes.NOT_FOUND, 'RESOURCE_NOT_FOUND', true);
  }
}

export class UnauthorizedError extends AppError {
  constructor(message = 'Unauthorized access') {
    super(message, StatusCodes.UNAUTHORIZED, 'UNAUTHORIZED', true);
  }
}

export class ForbiddenError extends AppError {
  constructor(message = 'Forbidden action') {
    super(message, StatusCodes.FORBIDDEN, 'FORBIDDEN', true);
  }
}

export class ConflictError extends AppError {
  constructor(message = 'Resource conflict') {
    super(message, StatusCodes.CONFLICT, 'RESOURCE_CONFLICT', true);
  }
}
```

---

## 3. Centralized Express Error Middleware

```typescript
// src/shared/middleware/error.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { StatusCodes } from 'http-status-codes';
import { AppError } from '../errors/app-error';

export const errorHandler = (err: Error, _req: Request, res: Response, _next: NextFunction): void => {
  if (err instanceof AppError) {
    res.status(err.statusCode).json({
      success: false,
      error: { code: err.errorCode, message: err.message, ...(err.details && { details: err.details }) },
    });
    return;
  }

  console.error('💥 UNHANDLED_PROGRAMMER_ERROR:', err);
  // TODO: Integrate Sentry/Datadog APM logger for unexpected 500 errors in staging/production.

  const isProduction = process.env.NODE_ENV === 'production';
  res.status(StatusCodes.INTERNAL_SERVER_ERROR).json({
    success: false,
    error: {
      code: 'INTERNAL_SERVER_ERROR',
      message: isProduction ? 'An unexpected server error occurred' : err.message,
      ...(!isProduction && { stack: err.stack }),
    },
  });
};
```

---

## 4. Process Signal Protection (`server.ts`)

```typescript
// src/server.ts
process.on('unhandledRejection', (reason: Error) => {
  console.error('💥 UNHANDLED REJECTION! Shutting down gracefully...', reason.message);
  // TODO: Trigger alert to ops channel on unhandled rejection
  process.exit(1);
});

process.on('uncaughtException', (err: Error) => {
  console.error('💥 UNCAUGHT EXCEPTION! Shutting down immediately...', err.message);
  process.exit(1);
});
```

---

## 5. Comments & TODO Protocol

For rules on adding mini-comments or marking missing APM loggers (e.g. `// TODO: Connect Sentry...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 6. Agent Verification Checklist

- [ ] Is `express-async-errors` imported at the top of `app.ts` (or are async wrappers used)?
- [ ] Is the centralized error middleware registered as the LAST middleware in `app.ts`?
- [ ] Are operational errors thrown as `AppError` instances with explicit HTTP status codes?
- [ ] Are raw error stack traces masked in production responses (`NODE_ENV === 'production'`)?
- [ ] Are process-level `unhandledRejection` and `uncaughtException` listeners registered in `server.ts`?
