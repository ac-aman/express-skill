---
title: Configuration & Environment Management Guideline (Generic)
category: guidelines
---

# Generic Configuration & Environment Management Guidelines

This guideline defines architecture-agnostic standards for environment variable loading, schema validation, type safety, environment file management, and startup error handling in Express.js backends.

---

## 1. Core Principles

1. **Zero Hardcoded Secrets**: Passwords, API tokens, database URIs, JWT secrets, encryption keys, and port numbers MUST be loaded from environment variables (`.env`).
2. **Fail-Fast Startup Validation**: Validate all environment variables at process bootstrap (in `src/config/`). If a required environment variable is missing or invalid, log explicit validation error details and terminate the process immediately (`process.exit(1)`).
3. **Type-Safe Export**: Parse and coerce raw string environment variables into a strongly-typed `config` object (e.g. converting `PORT="3000"` to `number 3000`, `CORS_ALLOWED_ORIGINS` to `string[]`).
4. **Isolated Access**: Services, controllers, and repositories must import `config` from `src/config/`. Never access raw `process.env` directly outside of `src/config/`.
5. **Environment File Hygiene**:
   - `.env.example`: Must be committed to version control with non-sensitive default/placeholder values.
   - `.env`: Must be listed in `.gitignore` and NEVER committed.

---

## 2. Environment Loading & Startup Validation Flow

```text
Process Bootstrap → Load (.env via dotenv) → Validate & Coerce Types (Zod) → Fail-Fast Exit (1) OR Export Immutable Config
```

---

## 3. Central Configuration Implementation (Zod & dotenv)

```typescript
// src/config/index.ts
import dotenv from 'dotenv';
import { z } from 'zod';

dotenv.config();

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  // Coerce string PORT into number for Express listen compatibility
  PORT: z.string().transform((val) => parseInt(val, 10)).default('3000'),
  HOST: z.string().default('0.0.0.0'),
  DATABASE_URL: z.string().url('DATABASE_URL must be a valid connection string'),
  JWT_SECRET: z.string().min(32, 'JWT_SECRET must be at least 32 characters long'),
  // Split comma-separated origin strings into string array for CORS
  CORS_ALLOWED_ORIGINS: z.string().transform((val) => val.split(',')).default('*'),
  // TODO: Verify with infrastructure team if Redis caching is required in staging/prod.
  REDIS_URL: z.string().url().optional(),
});

const validateEnv = () => {
  const parsed = envSchema.safeParse(process.env);
  if (!parsed.success) {
    console.error('❌ FATAL: Invalid environment variables:');
    parsed.error.issues.forEach((issue) => console.error(`  - ${issue.path.join('.')}: ${issue.message}`));
    process.exit(1);
  }
  return Object.freeze(parsed.data);
};

export const config = validateEnv();
export type Config = z.infer<typeof envSchema>;
```

---

## 4. Comments & TODO Protocol

For guidelines on adding readable mini-comments or marking missing configuration parameters (e.g., `// TODO: Confirm exact OAuth client ID...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 5. Agent Verification Checklist

- [ ] Is all `process.env` reading and validation isolated in `src/config/`?
- [ ] Does startup validation fail fast (`process.exit(1)`) with descriptive logs if a required env variable is missing?
- [ ] Are environment variables coerced to correct TypeScript types?
- [ ] Is `.env.example` present with dummy placeholders, and `.env` listed in `.gitignore`?
- [ ] Are missing config details explicitly flagged with `// TODO:` comments per [code-comments-and-todos.md](./code-comments-and-todos.md)?
