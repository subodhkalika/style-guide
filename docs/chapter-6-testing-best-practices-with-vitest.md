# Chapter 6: Testing Best Practices with Vitest

## 🎯 Purpose

This chapter establishes guidelines for writing, organizing, and executing tests in Node.js microservices using **Vitest**. Testing ensures code quality, reliability, and maintainability across our projects.

---

## 🧪 Testing Framework

We use **Vitest** for unit and integration testing in our Node.js microservices.

- Fast, modern testing framework that supports ES modules and TypeScript.
- Built-in mocking, snapshot testing, and coverage support.

---

## 🗂️ Recommended Testing Structure

Follow a structure that mirrors your `/src` directory:

```
/tests
├── users/
│ ├── users.controller.test.ts
│ └── users.service.test.ts
└── orders/
├── orders.controller.test.ts
└── orders.service.test.ts
```

- ✅ Organize tests by feature or module.
- ✅ Name test files consistently with `.test.ts` suffix.

---

## 🛠️ Testing Guidelines

- Write **unit tests** for individual functions, services, and utilities.
- Write **integration tests** for routes and controller logic using Fastify’s injection testing.
- Use **Vitest’s built-in mocking** for dependencies (e.g., database clients, external APIs).
- Write tests in **TypeScript** to align with the main codebase.
- Ensure **100% test coverage** for critical business logic.
- Keep tests **deterministic** and **isolated** (avoid shared state).

---

## 🔧 Example

**Example unit test** for a UserService:

```typescript
import { describe, it, expect, vi } from 'vitest';
import { UserService } from '../../src/modules/users/users.service';
import { DatabaseClient } from '../../src/plugins/db';

describe('UserService', () => {
  it('should fetch user by ID', async () => {
    const mockDbClient = {
      query: vi.fn().mockResolvedValue([{ id: '1', name: 'Alice' }])
    } as unknown as DatabaseClient;

    const userService = new UserService(mockDbClient);
    const user = await userService.getUserById('1');

    expect(user).toEqual([{ id: '1', name: 'Alice' }]);
    expect(mockDbClient.query).toHaveBeenCalledWith(
      'SELECT * FROM users WHERE id = $1',
      ['1']
    );
  });
});
```

## 📊 Code Coverage
Use vitest run --coverage to generate coverage reports.

Maintain at least 80% code coverage.

Treat missing coverage as a warning and discuss improvements during code review.
