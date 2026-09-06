---
title: Security Standards Guideline (Generic & Production-Grade)
category: guidelines
---

# Generic Security Standards Guidelines

This guideline defines architecture-agnostic security rules for Express.js applications, enforcing zero route/data leakage, strict CORS policies, mandatory authentication and authorization on all protected endpoints, HTTP header hardening, injection prevention, and security error integration.

---

## 1. Zero Information & Route Leakage Rules

1. **Disable Framework Signature Headers**:
   Disable the `X-Powered-By: Express` header at application startup to obscure framework identity:
   ```typescript
   app.disable('x-powered-by');
   ```
2. **Sanitize Production Errors**:
   - In production (`NODE_ENV === 'production'`), error responses MUST NEVER include stack traces, raw SQL/MongoDB queries, driver error codes, or internal server file paths.
   - All uncaught errors return a uniform, sanitized payload: `{ "success": false, "error": { "code": "INTERNAL_SERVER_ERROR", "message": "An unexpected server error occurred" } }`.
3. **Prevent Route & Resource Enumeration Leaks**:
   - Do not leak private endpoint existence to unauthenticated users.
   - Apply global authentication middleware BEFORE route handling to enforce consistent `401 Unauthorized` responses before route matching can reveal resource existence through `403 Forbidden` vs `404 Not Found` discrepancies.
4. **Mask Sensitive Response Fields**:
   - Always strip sensitive properties (`password`, `passwordHash`, `salt`, `refreshToken`, `ssn`, `creditCard`) from user and entity models before returning JSON responses.

---

## 2. Mandatory Authentication & Authorization (Default-Deny Posture)

All Express.js routes must operate under a **Default-Deny Security Posture**:

### Rule 2.1: Protected by Default
- Every route and feature router requires authentication (`authenticate`) and role-based authorization (`authorize(...)`) middleware by default.
- **Explicit Public Whitelist**: Only explicitly declared public endpoints (`POST /api/v1/auth/login`, `POST /api/v1/auth/register`, `GET /health`) may bypass authentication.

### Rule 2.2: JWT & Cookie Security Hygiene
- **Access Tokens**: Short-lived (15 minutes). Passed via `Authorization: Bearer <token>` header.
- **Refresh Tokens**: Stored strictly in `httpOnly: true`, `secure: true` (HTTPS-only), `sameSite: 'strict'` cookies to prevent XSS token theft.
- **JWT Secret**: Minimum 32 characters, loaded exclusively from `config.JWT_SECRET`.

### Rule 2.3: Authentication & Authorization Middleware Implementation

```typescript
// src/shared/middleware/auth.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { UnauthorizedError, ForbiddenError } from '../errors/app-error';
import { tokenService } from '../services/token.service';

export const authenticate = async (req: Request, _res: Response, next: NextFunction): Promise<void> => {
  const authHeader = req.headers.authorization;
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    throw new UnauthorizedError('Authentication token required');
  }

  const token = authHeader.split(' ')[1];
  // Verify token signature and expiration
  const decoded = await tokenService.verifyAccessToken(token);
  req.user = decoded; // Attach user payload to request
  next();
};

export const authorize = (...allowedRoles: string[]) => {
  return (req: Request, _res: Response, next: NextFunction): void => {
    if (!req.user) {
      throw new UnauthorizedError('Authentication required');
    }
    if (!allowedRoles.includes(req.user.role)) {
      throw new ForbiddenError('You do not have permission to perform this action');
    }
    next();
  };
};
```

---

## 3. Strict CORS (Cross-Origin Resource Sharing) Configuration

CORS must be strictly configured using `cors` middleware to prevent unauthorized cross-domain requests:

### Core CORS Rules
1. **Never Use Wildcards with Credentials**: Setting `cors({ origin: '*', credentials: true })` is **FORBIDDEN**. Browsers will reject this configuration, and it creates severe security vulnerabilities.
2. **Explicit Allowed Origins**: Load allowed origins from `config.CORS_ALLOWED_ORIGINS` (comma-separated string array).
3. **Preflight Caching**: Configure `maxAge: 86400` (24 hours) to reduce preflight `OPTIONS` request overhead.

### Complete CORS Implementation

```typescript
// src/config/cors.config.ts
import cors from 'cors';
import { config } from './index';

const allowedOrigins = config.CORS_ALLOWED_ORIGINS;

export const corsOptions: cors.CorsOptions = {
  origin: (origin, callback) => {
    // Allow requests with no origin (e.g. mobile apps, curl, server-to-server)
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error(`CORS policy violation: Origin ${origin} not allowed`));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With'],
  maxAge: 86400, // Cache preflight OPTIONS for 24 hours
};
```

---

## 4. Injection & Payload Sanitization

1. **SQL / NoSQL Injection Prevention**:
   - Always use ORM/ODM parameterized queries (Prisma, TypeORM, Mongoose).
   - **FORBIDDEN**: Never concatenate raw user strings into SQL queries (`WHERE name = '` + input + `'` ❌).
   - Sanitize MongoDB queries against NoSQL operator injection (e.g. `$gt`, `$where`, `$ne`) by validating inputs using Zod schemas.
2. **HTTP Parameter Pollution (HPP)**:
   - Use `hpp()` middleware to sanitize duplicate query string parameters (e.g., `?role=admin&role=user`) to prevent query pollution attacks.
3. **Payload Size Limits**:
   - Limit HTTP request body size (`express.json({ limit: '10kb' })`) to prevent Memory Exhaustion DoS attacks.

---

## 5. Production Security Middleware Suite (`app.ts`)

Every production Express application must register the full security suite in `app.ts`:

```typescript
import express from 'express';
import helmet from 'helmet';
import cors from 'cors';
import rateLimit from 'express-rate-limit';
import hpp from 'hpp';
import { corsOptions } from './config/cors.config';

const app = express();

// 1. Disable framework signature header
app.disable('x-powered-by');

// 2. HTTP Security Headers (HSTS, CSP, X-Frame-Options: DENY, X-Content-Type-Options: nosniff)
app.use(helmet());

// 3. Strict CORS Middleware
app.use(cors(corsOptions));

// 4. HTTP Parameter Pollution Prevention
app.use(hpp());

// 5. Restrict JSON body size (DoS protection)
app.use(express.json({ limit: '10kb' }));

// 6. Global IP Rate Limiter (Max 100 requests per 15 mins per IP)
const globalRateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
  message: {
    success: false,
    error: {
      code: 'RATE_LIMIT_EXCEEDED',
      message: 'Too many requests from this IP, please try again later.',
    },
  },
});
app.use('/api', globalRateLimiter);
```

---

## 6. Comments & TODO Protocol

For guidelines on adding readable mini-comments or inserting `// TODO:` comments for underspecified security features (e.g. `// TODO: Configure Redis store for distributed rate limiting`), refer to [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 7. Agent Security Verification Checklist

Before completing any task, verify:
- [ ] Is `app.disable('x-powered-by')` and `helmet()` enabled?
- [ ] Is CORS configured with explicit allowed origins (`credentials: true` with wildcard `*` avoided)?
- [ ] Are internal stack traces, DB queries, and server file paths masked in production (`NODE_ENV === 'production'`)?
- [ ] Are sensitive entity properties (`passwordHash`, `refreshToken`) stripped from response JSON payloads?
- [ ] Is authentication (`authenticate`) enforced on ALL non-public routes by default?
- [ ] Are sensitive actions protected by explicit RBAC (`authorize('ADMIN')`)?
- [ ] Are request body size limits (`10kb`) and rate limiters (`express-rate-limit`) configured?
- [ ] Are MongoDB/SQL inputs sanitized against injection using Zod schema validation and ORMs?
- [ ] Are missing security integrations explicitly marked with `// TODO:` comments?
