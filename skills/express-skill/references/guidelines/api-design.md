---
title: Generic API Design & Request Lifecycle Guideline
category: guidelines
---

# Generic API Design & Request Lifecycle Guidelines

This guideline defines architecture-agnostic standards for RESTful API design, request validation, error handling, standardized response formatting, and library usage in Express.js backends.

---

## 1. RESTful URL & Endpoint Design Rules

1. **Plural Resource Naming**: Use plural nouns for resource paths (`/api/v1/users`, `/api/v1/orders/:orderId/items`).
2. **Nouns over Verbs**: Rely on standard HTTP methods (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`) rather than verb URLs (`POST /api/v1/create-user` ❌ -> `POST /api/v1/users` ✅).
3. **API Versioning**: Prefix all public routes with version identifiers (`/api/v1/`).
4. **Query Parameters for Filtering**: Use query params for pagination, filtering, sorting, and field selection:
   `GET /api/v1/products?page=1&limit=20&category=electronics&sort=-price`

---

## 2. Request Processing Lifecycle

```text
Incoming Request → Validation (Zod) → Auth/Permissions → Execution (Service) → Uniform Response Envelope
```

---

## 3. Universal Response Envelope Standards

### A. Success Response Envelope (`HTTP 200/201/202`)
```json
{
  "success": true,
  "data": { "id": "usr_98765", "email": "user@example.com", "role": "admin" },
  "meta": { "page": 1, "limit": 20, "total": 100, "totalPages": 5 }
}
```
*Note: Include `meta` only for paginated queries or list operations.*

### B. Error Response Envelope (`HTTP 4xx/5xx`)
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request input parameters",
    "details": [{ "field": "body.email", "message": "Must be a valid email address" }]
  }
}
```

---

## 4. Comprehensive Error Handling & Status Code Mapping

| HTTP Status | Error Code Enum | Scenario |
| :--- | :--- | :--- |
| **`400 Bad Request`** | `VALIDATION_ERROR` / `BAD_REQUEST` | Malformed JSON or input validation failure |
| **`401 Unauthorized`** | `UNAUTHORIZED` / `INVALID_TOKEN` | Missing, expired, or invalid auth token |
| **`403 Forbidden`** | `FORBIDDEN` / `INSUFFICIENT_PERMISSIONS` | Authenticated user lacks permission for action |
| **`404 Not Found`** | `RESOURCE_NOT_FOUND` | Requested entity/resource ID does not exist |
| **`409 Conflict`** | `RESOURCE_CONFLICT` | Duplicate unique field (e.g., email already registered) |
| **`422 Unprocessable`** | `UNPROCESSABLE_ENTITY` | Syntactically correct input failing domain invariant |
| **`429 Too Many Requests`** | `RATE_LIMIT_EXCEEDED` | Client exceeded endpoint rate limit |
| **`500 Internal Error`** | `INTERNAL_SERVER_ERROR` | Uncaught exception or infrastructure failure |

---

## 5. Comments & TODO Protocol

For rules on adding readable mini-comments or marking underspecified API requirements (e.g., `// TODO: Clarify with product team...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 6. Established Libraries & Endpoint Example

Utilize standard libraries: `zod` for schemas, `http-status-codes` for HTTP constants, `express-async-errors` for async error handling, and `helmet` for security headers.

```typescript
import { Request, Response, NextFunction } from 'express';
import { StatusCodes } from 'http-status-codes';
import { userService } from '../services/user.service';

export const createUserController = async (req: Request, res: Response, _next: NextFunction): Promise<void> => {
  const { email, password, fullName } = req.body;
  // Execute business logic with service layer
  const newUser = await userService.createUser({ email, password, fullName });

  // TODO: Determine if welcome email should be sent synchronously or queued via Redis.

  res.status(StatusCodes.CREATED).json({ success: true, data: newUser });
};
```

---

## 7. Agent Verification Checklist

- [ ] Does the URL use plural nouns (`/api/v1/users`) and standard HTTP verbs (`GET`, `POST`, `PATCH`, `DELETE`)?
- [ ] Are responses formatted using standard `{ success, data, meta }` or `{ success, error }` envelopes?
- [ ] Are status codes handled using standard HTTP constants (`http-status-codes`)?
- [ ] Are underspecified features explicitly marked with `// TODO:` comments per [code-comments-and-todos.md](./code-comments-and-todos.md)?
