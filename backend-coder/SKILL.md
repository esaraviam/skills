---
name: backend-coder
description: Create distinctive, production-grade backend systems with high architectural quality. Use this skill when the user asks to build APIs, services, system architectures, data models, integrations, or backend applications. Generates robust, scalable, and well-designed code that avoids generic boilerplate patterns.
license: Complete terms in LICENSE.txt
---

This skill guides the creation of distinctive, production-grade backend systems with a strong architectural point of view. Avoid generic CRUD boilerplate — instead, design systems with intentional structure, scalability, and clarity.

The user provides backend requirements: an API, service, system, or architecture. They may include context about business rules, scale, or constraints.

## Backend Design Thinking

Before coding, deeply understand the problem and commit to a **clear architectural direction**:

- **Domain**: What problem does the system solve? What are the core entities and invariants?
- **Style**: Choose a strong paradigm:
  - Domain-Driven Design (DDD)
  - Functional / declarative
  - Hexagonal (Ports & Adapters)
  - Event-driven / CQRS
  - Modular monolith vs microservices
- **Constraints**: Performance, scalability, team size, deployment model
- **Trade-offs**: Be explicit — consistency vs availability, simplicity vs flexibility, latency vs throughput
- **Differentiation**: What makes this backend *well-designed*, not just working?

**CRITICAL**: Avoid accidental architecture. Every decision must be intentional and justified.

---

## Implementation Principles (TypeScript-first)

All implementations must:

- Use **TypeScript as a first-class language**, leveraging:
  - Strong typing
  - Generics
  - Type inference
  - Utility types
- Prefer **composition over inheritance**
- Favor **immutability and pure functions** where possible
- Design for **testability and observability**
- Keep **side effects isolated**

---

## Architecture Guidelines

### 1. Structure

Use a clear and scalable structure:

- Feature-based or domain-based modules
- Separation of concerns:
  - Domain (business logic)
  - Application (use cases)
  - Infrastructure (DB, APIs, external services)
- Avoid anemic models — business logic should live in the domain

---

### 2. API Design

- Design APIs with **intent, not just endpoints**
- Use consistent patterns:
  - REST (resource-oriented) OR
  - RPC (use-case driven) OR
  - GraphQL (when justified)
- Validate inputs rigorously (e.g. Zod, class-validator)
- Return meaningful error structures (not just status codes)

---

### 3. Data Modeling

- Model the domain, not just tables
- Use:
  - Value Objects
  - Entities
  - Aggregates (when needed)
- Avoid leaking persistence concerns into domain logic

---

### 4. Persistence & Infrastructure

- Abstract external dependencies behind interfaces
- Support swapping implementations (DB, cache, queues)
- Use repositories intentionally — not blindly

---

### 5. Error Handling

- Avoid try/catch chaos
- Use structured error types
- Separate:
  - Domain errors
  - Application errors
  - Infrastructure errors

---

### 6. Performance & Scalability

- Think in terms of:
  - Latency
  - Throughput
  - Bottlenecks
- Use:
  - Caching (when justified)
  - Async processing / queues
  - Batching and pagination

---

### 7. Observability

- Include:
  - Structured logging
  - Metrics hooks
  - Traceability (correlation IDs)
- Systems should be debuggable in production

---

## Code Quality Standards

- No generic boilerplate or “tutorial-style” code
- Use expressive naming aligned with the domain
- Keep functions small but meaningful
- Prefer clarity over cleverness
- Comments explain **why**, not **what**

---

## Technology Preferences

Default stack (unless specified otherwise):

- **Runtime**: Node.js
- **Language**: TypeScript (strict mode)
- **Frameworks** (choose intentionally):
  - Fastify (performance-oriented)
  - NestJS (structured / enterprise)
  - Express (only if simplicity is required)
- **Validation**: Zod preferred
- **ORM/DB**:
  - Prisma (default)
  - Drizzle (for more control)
- **Testing**:
  - Vitest / Jest

---

## Output Expectations

When generating a backend solution:

1. Explain the **architectural approach**
2. Justify key decisions and trade-offs
3. Provide a **clean folder structure**
4. Deliver **production-grade TypeScript code**
5. Include **examples of usage (requests, flows, etc.)**

---

## Anti-Patterns to Avoid

- Generic CRUD generators without domain thinking
- Fat controllers / thin logic layers
- Leaky abstractions
- Overengineering without need
- Copy-paste architecture (no intentionality)

---

## Guiding Principle

This is not about making something that *works*.

This is about building a backend that:
- scales,
- communicates intent clearly,
- and can be evolved safely over time.

Every system should feel like it was **designed**, not assembled.
