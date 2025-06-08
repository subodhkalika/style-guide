# Chapter 4: Design Patterns and Principles

## 🎯 Overview

This chapter establishes coding standards and best practices around **design patterns** and **software engineering principles** to promote maintainable, testable, and robust Node.js microservices.

We focus on:
- [**Dependency Injection**](#🔌-dependency-injection-di)
- [**SOLID Principles**](#🛠%ef%b8%8f-solid-principles)

---

## 🔌 Dependency Injection (DI)

**What is it?**
Dependency Injection is a design pattern that **decouples dependencies** by injecting them at runtime rather than hard-coding them inside classes or functions.

**Why is it important?**
- ✅ Improves testability (easier to mock dependencies)
- ✅ Promotes loose coupling (code easier to maintain and refactor)
- ✅ Makes code easier to understand and extend

### 💡 Guidelines

- Use **constructor injection** as the preferred DI method.
- Avoid **hard-coded dependencies** inside classes or services.
- Keep a **single-responsibility principle** (don’t overload classes with too many dependencies).

### 🔧 Example

**services/users.service.ts**

```typescript
import { DatabaseClient } from '../plugins/db';

export class UserService {
  constructor(private readonly dbClient: DatabaseClient) {}

  async getUserById(id: string) {
    return this.dbClient.query('SELECT * FROM users WHERE id = $1', [id]);
  }
}
```
---

## 🛠️ SOLID Principles

The **SOLID** principles are a set of best practices for designing scalable and maintainable software.

### 🔎 S — Single Responsibility Principle (SRP)

Each class or module should have **one and only one reason to change**.

- ✅ Keep controllers focused on request/response handling.
- ✅ Keep services focused on business logic.

```typescript
// controllers/users.controller.ts
import { FastifyReply, FastifyRequest } from 'fastify';
import { UserService } from '../services/users.service';

export class UsersController {
  constructor(private readonly userService: UserService) {}

  async getUser(req: FastifyRequest, reply: FastifyReply) {
    const user = await this.userService.getUserById(req.params.id);
    reply.send(user);
  }
}

// services/users.service.ts
import { DatabaseClient } from '../plugins/db';

export class UserService {
  constructor(private readonly dbClient: DatabaseClient) {}

  async getUserById(id: string) {
    return this.dbClient.query('SELECT * FROM users WHERE id = $1', [id]);
  }
}
```

### 🔎 O — Open/Closed Principle (OCP)

Classes should be **open for extension but closed for modification**.

- ✅ Use interfaces and abstract classes where applicable.
- ✅ Extend functionality via plugins or decorators instead of modifying core logic.

```typescript
// plugins/logger.ts
export interface Logger {
  log(message: string): void;
}

// plugins/consoleLogger.ts
import { Logger } from './logger';

export class ConsoleLogger implements Logger {
  log(message: string) {
    console.log(message);
  }
}

// services/users.service.ts
import { Logger } from '../plugins/logger';

export class UserService {
  constructor(private readonly logger: Logger) {}

  async getUserById(id: string) {
    this.logger.log(`Fetching user with ID: ${id}`);
    // Fetch logic here
  }
}
```

### 🔎 L — Liskov Substitution Principle (LSP)

Derived classes must be substitutable for their base classes without breaking the application.

- ✅ Use consistent method signatures.
- ✅ Favor composition over inheritance if subclassing becomes too complex.

```typescript
// plugins/logger.ts
export interface Logger {
  log(message: string): void;
}

// plugins/fileLogger.ts
import { Logger } from './logger';

export class FileLogger implements Logger {
  log(message: string) {
    // Write message to a log file
  }
}

// plugins/consoleLogger.ts
import { Logger } from './logger';

export class ConsoleLogger implements Logger {
  log(message: string) {
    console.log(message);
  }
}

// Usage (interchangeable loggers)
const logger: Logger = new FileLogger();
logger.log('Test message');
```

### 🔎 I — Interface Segregation Principle (ISP)

Clients should not be forced to depend on interfaces they do not use.

- ✅ Create small, focused interfaces.
- ✅ Avoid bloated interfaces.

```typescript
// interfaces/userReader.ts
export interface UserReader {
  getUserById(id: string): Promise<any>;
}

// interfaces/userWriter.ts
export interface UserWriter {
  createUser(data: any): Promise<any>;
}

// services/users.service.ts
import { UserReader, UserWriter } from '../interfaces/userReader';

export class UserService implements UserReader, UserWriter {
  async getUserById(id: string) {
    // Fetch user from DB
  }

  async createUser(data: any) {
    // Create user in DB
  }
}
```

### 🔎 D — Dependency Inversion Principle (DIP)

High-level modules should not depend on low-level modules. Both should depend on abstractions.

- ✅ Use interfaces or abstract classes to define dependencies.
- ✅ Inject dependencies via constructors or Fastify plugins.

```typescript
// interfaces/databaseClient.ts
export interface DatabaseClient {
  query(query: string, params: any[]): Promise<any>;
}

// plugins/postgresClient.ts
import { DatabaseClient } from '../interfaces/databaseClient';

export class PostgresClient implements DatabaseClient {
  async query(query: string, params: any[]) {
    // Actual DB logic here
  }
}

// services/users.service.ts
import { DatabaseClient } from '../interfaces/databaseClient';

export class UserService {
  constructor(private readonly dbClient: DatabaseClient) {}

  async getUserById(id: string) {
    return this.dbClient.get('SELECT * FROM users WHERE id = $1', [id]);
  }
}
```
---


## 📝 Summary

- Use **Dependency Injection** to decouple classes and improve testability.
- Follow **SOLID Principles** to design clean, extensible, and maintainable code.
- Always separate concerns:
  - **Controllers**: request/response handling
  - **Services**: business logic
  - **Plugins**: cross-cutting concerns like logging or database access
