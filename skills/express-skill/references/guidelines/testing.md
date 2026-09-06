---
title: Testing Strategy Guideline (Generic & Production-Grade)
category: guidelines
---

# Generic Testing Strategy & Guidelines

This guideline defines architecture-agnostic standards for unit testing, endpoint integration testing, mock strategies, test environment isolation, and library usage in Express.js backends.

---

## 1. Core Testing Strategy & Pyramid

1. **Unit Tests (Fast & Isolated)**:
   - Test domain services, utility functions, validation schemas, and business logic in isolation.
   - **Mock External Dependencies**: Mock database repositories, third-party HTTP clients, and external services.
2. **Integration Tests (End-to-End HTTP)**:
   - Test Express routes end-to-end using `supertest`.
   - Verify request parsing, validation middleware, controller execution, error handling middleware, and HTTP response codes against a test database.
3. **Zero Test Pollution**: Tests must be independent. Clean up database state in `beforeEach` / `afterEach` hooks.

---

## 2. File Co-location & Naming Conventions

- **Unit Test Files**: Co-locate unit tests alongside implementation files (`create-user.service.ts` → `create-user.service.spec.ts`).
- **Integration Test Files**: Store endpoint integration tests in `src/modules/<feature>/__tests__/` or `tests/integration/` (`user.routes.test.ts`).

---

## 3. Implementation Examples (`supertest` & Mocks)

### A. Endpoint Integration Testing (`supertest`)
```typescript
import request from 'supertest';
import { StatusCodes } from 'http-status-codes';
import app from '../../../app';

describe('POST /api/v1/users', () => {
  it('should return 201 Created and user data for valid input', async () => {
    const payload = { email: 'testuser@example.com', password: 'StrongPassword123!', fullName: 'Test User' };
    const response = await request(app).post('/api/v1/users').send(payload).expect(StatusCodes.CREATED);
    expect(response.body).toHaveProperty('success', true);
    expect(response.body.data.email).toBe(payload.email);
  });
});
```

### B. Unit Testing Service with Mock Repository
```typescript
import { CreateUserService } from './create-user.service';
import { ConflictError } from '../../../shared/errors/app-error';

describe('CreateUserService', () => {
  let service: CreateUserService;
  let mockUserRepo: { findByEmail: jest.Mock; create: jest.Mock };

  beforeEach(() => {
    mockUserRepo = { findByEmail: jest.fn(), create: jest.fn() };
    service = new CreateUserService(mockUserRepo as any);
  });

  it('should throw ConflictError if user email already exists', async () => {
    mockUserRepo.findByEmail.mockResolvedValue({ id: 'existing_123', email: 'taken@example.com' });
    await expect(service.execute({ email: 'taken@example.com', password: 'password123', fullName: 'John' })).rejects.toThrow(ConflictError);
  });
});
```

---

## 4. Test Environment Isolation (`.env.test`)

1. Load dedicated environment variables from `.env.test` during test execution (`NODE_ENV=test`).
2. Use an isolated test database URI (`postgresql://.../mydb_test`). Never run integration tests against development or production databases.

---

## 5. Comments & TODO Protocol

For rules on Arrange-Act-Assert (AAA) comments or marking missing test suites (e.g. `// TODO: Mock external Stripe webhook...`), see [code-comments-and-todos.md](./code-comments-and-todos.md).

---

## 6. Maintained Testing Libraries

- **Test Runners**: `jest` or `vitest`.
- **HTTP Integration**: `supertest`.

---

## 7. Agent Testing Verification Checklist

- [ ] Are unit tests (`.spec.ts`) co-located with implementation files?
- [ ] Are external database and infrastructure calls mocked in unit tests?
- [ ] Do integration tests use `supertest` to verify HTTP status codes and JSON response envelopes?
- [ ] Is test execution configured to use `.env.test` against an isolated test database?
- [ ] Are missing test suites explicitly marked with `// TODO:` comments per [code-comments-and-todos.md](./code-comments-and-todos.md)?
