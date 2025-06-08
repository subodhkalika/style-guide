# Chapter 5: Anti-Patterns to Avoid

## 🚫 Anti-Patterns
Anti-patterns are common but undesirable coding practices that may seem convenient in the short term but can lead to technical debt, reduced maintainability, and increased complexity in the long run. Recognizing and avoiding these patterns is crucial to building clean, robust, and scalable software.

In this section, we highlight key anti-patterns often encountered in Node.js microservices, along with recommended best practices to avoid them. By steering clear of these pitfalls, developers can write code that’s easier to test, maintain, and extend.

### ❌ Direct instantiation of dependencies within classes.

**Problems with this approach:**
- Tight coupling between UserService and DatabaseClient
- Difficult to test and mock dependencies
- Violates the Dependency Inversion Principle

*Example:*
```typescript
// ❌ Avoid: Direct instantiation of dependencies
import { DatabaseClient } from '../plugins/db';

export class UserService {
  private dbClient: DatabaseClient;

  constructor() {
    this.dbClient = new DatabaseClient(); // direct instantiation ❌
  }

  async getUserById(id: string) {
    return this.dbClient.get(id);
  }
}
```

**✅ Instead, inject dependencies via the constructor.**
**Why this works? :**
- Promotes loose coupling (easy to swap or mock dependencies)
- Adheres to the Dependency Inversion Principle (DIP)
- Enhances testability and maintainability
- Keeps dependencies explicit and declarative

*Example:*

```typescript
// ✅ Good: Inject dependencies via the constructor
import { DatabaseClient } from '../plugins/db';

export class UserService {
  constructor(private readonly dbClient: DatabaseClient) {} // ✅ dependency injection

  async getUserById(id: string) {
    return this.dbClient.get(id);
  }
}
```
---

### ❌ Controllers handling database logic.
**Problems with this approach:**
- Breaks separation of concerns (controllers mix presentation and business logic)
- Harder to test controllers independently
- Makes controllers bloated and harder to maintain
- Violates the Single Responsibility Principle (SRP)

*Example:*
```typescript
// ❌ Avoid: Controller performing business logic
import { FastifyRequest, FastifyReply } from 'fastify';
import { DatabaseClient } from '../plugins/db';

export class UserController {
  constructor(private readonly dbClient: DatabaseClient) {}

  async getUser(req: FastifyRequest, reply: FastifyReply) {
    const userId = req.params.id;
    const user = await this.dbClient.query(
      'SELECT * FROM users WHERE id = $1',
      [userId]
    ); // ❌ Business logic embedded in controller
    reply.send(user);
  }
}
```
**✅ Instead, controllers should delegate business logic to services.**
**Why this works?:**
- Keeps controllers thin and focused on HTTP layer
- Promotes reusable business logic in services
- Easier to test and maintain
- Adheres to SRP and better separation of concerns
*Example:*
```typescript
// ✅ Good: Controller delegates business logic to service
import { FastifyRequest, FastifyReply } from 'fastify';
import { UserService } from '../services/user.service';

export class UserController {
  constructor(private readonly userService: UserService) {}

  async getUser(req: FastifyRequest, reply: FastifyReply) {
    const userId = req.params.id;
    const user = await this.userService.getUserById(userId); // ✅ delegation
    reply.send(user);
  }
}

// services/user.service.ts
import { DatabaseClient } from '../plugins/db';

export class UserService {
  constructor(private readonly dbClient: DatabaseClient) {}

  async getUserById(id: string) {
    return this.dbClient.query('SELECT * FROM users WHERE id = $1', [id]);
  }
}

```
> **Note:** Even with dependency injection, avoid mixing business logic into controllers. Use services to handle domain logic and keep controllers focused on HTTP-level concerns like request validation and response formatting.

---

### ❌ Violating Single Responsibility Principle (SRP) by combining multiple concerns in a single class.
**Problems with this approach:**
- Class becomes hard to maintain and extend
- Difficult to test because of mixed responsibilities
- Changes in one concern can unintentionally affect others
*Example:*
```typescript
// ❌ Avoid: Combining database access and business logic in the same class
import { DatabaseClient } from '../plugins/db';

export class UserManager {
  private dbClient = new DatabaseClient();

  // Fetch user data (data access concern)
  async getUserById(id: string) {
    return this.dbClient.query('SELECT * FROM users WHERE id = $1', [id]);
  }

  // Business logic concern
  async isUserEligible(id: string) {
    const user = await this.getUserById(id);
    return user.age >= 18 && user.active;
  }
}
```
**✅ Instead, keep classes focused on one responsibility. Why this works?**
- Improves maintainability and readability
- Easier to test each class independently
- Changes to business logic or data access are isolated
- Adheres strictly to SRP, enhancing code quality
*Example:*
```typescript
// ✅ Good: Separate data access and business logic concerns

// services/user.repository.ts
import { DatabaseClient } from '../plugins/db';

export class UserRepository {
  constructor(private readonly dbClient: DatabaseClient) {}

  async getUserById(id: string) {
    return this.dbClient.query('SELECT * FROM users WHERE id = $1', [id]);
  }
}

// services/user.service.ts
import { UserRepository } from './user.repository';

export class UserService {
  constructor(private readonly userRepo: UserRepository) {}

  async isUserEligible(id: string) {
    const user = await this.userRepo.getUserById(id);
    return user.age >= 18 && user.active;
  }
}
```
---

### ❌ Fat Interface
**Problems with this approach:**
- Hard to implement correctly, especially in tests.
- Forces implementers to handle unrelated responsibilities.
- Violates SRP and makes maintenance harder.
*Example:*
```typescript
// ❌ Bad: Fat interface with unrelated responsibilities
export interface IDataProcessor {
  validateData(data: any): boolean;
  transformData(data: any): any;
  saveDataToDatabase(data: any): Promise<void>;
  sendDataToExternalAPI(data: any): Promise<void>;
}
```
**✅ Best Practice: Split into Focused Interfaces. Why this works?**
- Allows implementers to pick only what they need.
- Keeps each interface small and single-responsibility.
- Easier to mock or swap in tests.
*Example:*
```typescript
// ✅ Good: Split into focused interfaces
export interface IDataValidator {
  validateData(data: any): boolean;
}

export interface IDataTransformer {
  transformData(data: any): any;
}

export interface IDataSaver {
  saveDataToDatabase(data: any): Promise<void>;
}

export interface IDataSender {
  sendDataToExternalAPI(data: any): Promise<void>;
}

// Then compose the interfaces as needed
export interface IDataProcessingService extends IDataValidator, IDataTransformer, IDataSaver, IDataSender {}
```
---
