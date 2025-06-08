# Chapter 2: Project Structure and recommendations

## 📂 Project Structure

We recommend using a **feature-based structure** optimized for Fastify:

```
/src
- ├── app.ts # Fastify app configuration and plugin registration
- ├── server.ts # Server bootstrap file
- ├── plugins/ # Fastify plugins (logger, DB, etc.)
- │ ├── logger.ts
- │ ├── db.ts
- │ └── auth.ts
├── modules/ # Feature-based modules
│ ├── users/
- │ │ ├── users.routes.ts
- │ │ ├── users.controller.ts
- │ │ ├── users.service.ts
- │ │ └── users.schema.ts
│ └── orders/
- │ ├── orders.routes.ts
- │ ├── orders.controller.ts
- │ ├── orders.service.ts
- │ └── orders.schema.ts
├── utils/ # Common utilities and helpers
- │ └── errorHandler.ts
├── config/ # Application configuration
- │ └── index.ts
└── types/ # Global TypeScript definitions
- └── index.d.ts
/tests
├── users/
- │ ├── users.controller.test.ts
- │ └── users.service.test.ts
└── orders/
- ├── orders.controller.test.ts
- └── orders.service.test.ts
```

### Why this structure?
- ✅ **Scalable & modular**: Groups related code together by feature.
- ✅ **Fastify-friendly**: Supports Fastify plugins and modular architecture.
- ✅ **Clear separation**: Divides route, controller, service, and schema logic.
- ✅ **Testing-ready**: Tests mirror the source code structure for easy discovery.

---

## 📌 Next Chapter

- Chapter 3: Pull request and commit conventions