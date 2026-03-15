# moostjs

<p align="center">
<img src="https://github.com/moostjs/moostjs/blob/main/moost-logo.png" width="450px"><br>
<a href="https://github.com/moostjs/moostjs/blob/main/LICENSE">
    <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" />
</a>
</p>

A full-stack TypeScript ecosystem for building enterprise-grade applications — from data models to APIs to CLI tools — with a single, coherent architecture.

Moostjs is not just another framework. It is an integrated ecosystem of three pillars: **Wooks** (composable event processing), **Moost** (metadata-driven application framework), and **Atscript** (universal type and schema language). Together, they eliminate the fragmentation that plagues full-stack TypeScript development.

## The Ecosystem

### [Wooks](https://wooks.moost.org) — Composable Event Processing Engine

Wooks reimagines how backend frameworks handle event context. Instead of mutating request objects and chaining middleware in fragile order-dependent pipelines, Wooks introduces **composable functions** — inspired by Vue's Composition API — for the server side.

- **One API across all event types** — HTTP, CLI, WebSocket, and Workflow handlers use the same composables (`useRouteParams()`, `useLogger()`, `useEventId()`)
- **Lazy evaluation** — cookies, body, headers are parsed only when accessed; early auth failures skip expensive parsing entirely
- **Automatic per-event caching** — call a composable anywhere in the call stack, it computes once
- **No middleware ordering bugs** — composables are independent and type-safe
- **Faster than Fastify** — 70K req/s in realistic SaaS benchmarks (auth + cookies + body parsing), outperforming Fastify, h3, Hono, and Express

```
@wooksjs/event-core    Event context primitives (AsyncLocalStorage, typed slots, caching)
@wooksjs/event-http    HTTP server with request/response composables
@wooksjs/event-cli     CLI adapter with auto-generated help
@wooksjs/event-ws      WebSocket adapter with rooms and broadcasting
@wooksjs/event-wf      Workflow engine with pausable/resumable pipelines
@wooksjs/http-proxy    Reverse proxy composable
@wooksjs/http-static   Static file serving composable
@wooksjs/http-body     Body parsing with compression & security
```

### [Moost](https://moost.org) — Metadata-Driven Application Framework

Moost brings NestJS-style developer ergonomics — decorators, dependency injection, interceptors, pipes — without the boilerplate. No modules. No `forRoot()`. No Express or Fastify dependency. Just clean, composable architecture powered by Wooks underneath.

- **No module abstraction** — controllers register directly, promoting a simpler mental model
- **Cross-adapter controllers** — one controller can serve HTTP, CLI, and WebSocket simultaneously
- **Structured lifecycle** — interceptors (7 priority levels), pipes (9-stage pipeline), and DI scopes (singleton + per-event)
- **10% faster than NestJS+Fastify**, 56% faster than NestJS+Express in weighted benchmarks
- **Enterprise-ready integrations** — OpenTelemetry tracing, Swagger/OpenAPI generation, RBAC/ABAC access control, Vite HMR

```
moost                  Core: decorators, DI, pipes, interceptors
@moostjs/event-http    REST API adapter
@moostjs/event-cli     CLI application adapter
@moostjs/event-ws      WebSocket adapter
@moostjs/event-wf      Workflow adapter
@moostjs/swagger       OpenAPI 3.0/3.1 documentation
@moostjs/otel          OpenTelemetry tracing
@moostjs/arbac         Role-based & attribute-based access control
@moostjs/vite          Vite dev server with HMR
create-moost           Project scaffolding CLI
```

### [Atscript](https://atscript.moost.org) — Universal Type & Schema Language

Atscript solves a fundamental problem in full-stack development: maintaining the same data model across TypeScript types, validation schemas, UI metadata, database schemas, and API documentation. Define it once in `.as` files, and Atscript generates everything else.

- **Single source of truth** — types, validation, DB schema, JSON Schema, and metadata from one definition
- **Database-agnostic ORM** — adapters for SQLite, MongoDB, MySQL, and PostgreSQL with schema sync, relations, aggregations, and vector search
- **Runtime validation** — type-safe validators with partial mode, custom plugins, and path tracking
- **Build-tool agnostic** — unplugin integration for Vite, Webpack, Rollup, esbuild, Rspack
- **IDE support** — VSCode extension with syntax highlighting, LSP, diagnostics, and go-to-definition

```
@atscript/core           Parser, AST, plugin system, diagnostics
@atscript/typescript     TypeScript codegen + runtime (validators, JSON Schema, serialization)
@atscript/db             Generic database abstraction (relations, aggregations, schema sync)
@atscript/db-sqlite      SQLite adapter with FTS5 full-text search
@atscript/db-mongo       MongoDB adapter with vector search
@atscript/db-mysql       MySQL adapter with aggregations
@atscript/db-postgres    PostgreSQL adapter with pgvector and CITEXT
@atscript/moost-db       Moost integration for auto-generated CRUD controllers
@atscript/moost-validator  Moost validation pipe from .as definitions
unplugin-atscript        Build tool integration (Vite, Webpack, Rollup, esbuild, Rspack)
@atscript/vscode         VSCode extension (syntax, LSP, diagnostics)
```

## How They Work Together

The three pillars form a complete pipeline from data definition to running application:

```
  Atscript (.as)          Moost                    Wooks
  ┌──────────────┐     ┌──────────────────┐     ┌─────────────────────┐
  │ Define models │────>│ Controllers      │────>│ HTTP / CLI / WS     │
  │ + validation  │     │ + DI + Pipes     │     │ Event Processing    │
  │ + DB schema   │     │ + Interceptors   │     │ + Composables       │
  │ + metadata    │     │ + Access Control  │     │ + Routing           │
  └──────────────┘     └──────────────────┘     └─────────────────────┘
        │                      │                         │
        v                      v                         v
   Types, Validators,     OpenAPI Docs,            Lazy Parsing,
   DB Adapters,           Telemetry,               Caching,
   JSON Schema            CRUD Generation          Performance
```

**Atscript** defines your data models with validation rules, database annotations, and metadata. **Moost** wires them into controllers with dependency injection, interceptors, and auto-generated CRUD endpoints. **Wooks** handles the actual event processing with composable, lazy, type-safe context management.

## Why This Exists

Full-stack TypeScript development today is fragmented. You pick a web framework, a separate validation library, a separate ORM, wire them together with glue code, and maintain parallel definitions of the same data models across layers. Each piece has its own conventions, its own mental model, its own breaking changes.

Moostjs aims to be a **coherent, integrated ecosystem** where:

- Data models are defined once and flow through every layer
- The same architectural patterns work across HTTP, CLI, WebSocket, and workflow contexts
- Enterprise concerns (auth, telemetry, documentation, access control) are first-class, not afterthoughts
- Performance is a design constraint, not an optimization target

## What Makes It Different

| | Moostjs Ecosystem | NestJS | Fastify / Express |
|---|---|---|---|
| Architecture | Composable event processing | Middleware + modules | Middleware pipeline |
| Event types | HTTP, CLI, WS, Workflows — unified | HTTP-first, others via adapters | HTTP only |
| DI system | Lightweight, no modules | Heavy, module-based | None built-in |
| Data modeling | Atscript — single source of truth | Manual DTOs + class-validator | Manual |
| Database | Multi-adapter ORM from schema | TypeORM/Prisma (separate) | Bring your own |
| Performance | Faster than Fastify in real scenarios | Slower due to abstraction overhead | Varies |
| Validation | Generated from type definitions | Separate class-validator decorators | Bring your own |
| API docs | Generated from decorators + Atscript | Swagger plugin (manual decorators) | Manual |

## Getting Started

```bash
# Scaffold a new Moost project
npm create moost

# Or install individually
npm install moost @moostjs/event-http
```

**Documentation:**
- [Moost Framework](https://moost.org/)
- [Wooks Event Processing](https://wooks.moost.org)
- [Atscript Language](https://atscript.moost.org)

## Supporting the Project

Moostjs is an independent, open-source effort building a complete full-stack TypeScript ecosystem. The scope is ambitious — a composable event engine, an application framework, a type language with database integration, IDE tooling — and it is actively maintained and growing.

If you or your organization benefit from this work, consider sponsoring its continued development. Sponsorship directly funds:

- Continued development of new adapters, database drivers, and integrations
- Performance optimization and production hardening
- Documentation, examples, and developer experience improvements
- Long-term maintenance and security updates

<p align="center">
<a href="https://github.com/sponsors/moostjs">
    <img src="https://img.shields.io/badge/Sponsor-moostjs-ea4aaa?style=for-the-badge&logo=github-sponsors" />
</a>
</p>

## Contributing

We welcome contributors at all levels. Whether it's fixing a bug, improving documentation, adding a database adapter, or building something new on top of the ecosystem — your contributions matter.

1. **Fork** the relevant repo ([moostjs](https://github.com/moostjs/moostjs), [wooksjs](https://github.com/wooksjs/wooksjs), [atscript](https://github.com/moostjs/atscript))
2. **Clone** and install dependencies (`pnpm install`)
3. **Create a branch** for your change
4. **Submit a pull request**

All projects use pnpm workspaces, TypeScript, Vitest for testing, and conventional commits.

## License

MIT
