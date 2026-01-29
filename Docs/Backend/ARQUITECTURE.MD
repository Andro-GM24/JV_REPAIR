# ARCHITECTURE

## Scope
This document defines the **high-level architecture** of the project.
It intentionally avoids:
- API endpoint definitions
- Database schemas
- Internal data models
- Business rules at field/state level

Those concerns belong to separate documents:
- API_CONTRACTS.md
- DB_DESIGN.md

---

## Architectural style

The system is built using a **microservices architecture** with the following characteristics:

- Domain-oriented services
- Synchronous communication between services
- No shared databases
- No event broker in the MVP
- Real payment processing
- Designed to run on a **single-node deployment (Raspberry Pi)**

The goal is correctness, clarity, and learning microservices fundamentals.

---

## Deployment context

### Target environment
- Single Raspberry Pi (ARM architecture)
- Linux-based OS
- Docker as container runtime
- Docker Compose for orchestration
- Local persistent storage (SD or SSD)

### Implications
- No Kubernetes
- No heavy infrastructure components
- Limited CPU and memory
- Services must be lightweight and efficient

---

## System components (services)

The system is composed of **six independent services**.

Each service:
- Runs as its own container
- Owns its own persistence
- Is independently deployable
- Communicates with other services only through network calls

### Services overview

1. **Auth Service**
   - Responsible for identity and authentication
   - Issues and validates authentication tokens
   - Cross-cutting concern for all other services

2. **Catalog Service**
   - Owns the product and service catalog
   - Source of truth for what can be sold or booked

3. **Orders Service**
   - Owns the purchasing workflow
   - Contains cart and checkout logic
   - Coordinates interactions between services during a purchase

4. **Inventory Service**
   - Owns stock and availability
   - Responsible for reserving and confirming stock
   - Prevents overselling

5. **Payments Service**
   - Owns payment processing
   - Integrates with an external payment gateway (Wompi)
   - Is the authority on payment state

6. **Appointments Service**
   - Owns service bookings (appointments)
   - Independent domain from product orders
   - Manages scheduling logic

---

## Service relationships (conceptual)

- Orders depends on:
  - Catalog (what is being bought)
  - Inventory (whether it can be sold)
  - Payments (whether it was paid)

- Inventory and Payments do **not** depend on Orders internally.
  They expose capabilities, Orders orchestrates.

- Appointments is isolated from Orders and Inventory.

- Auth is used by all services but does not depend on them.

This creates a **directed dependency graph** with no cycles.

---

## Communication model

- All inter-service communication is **synchronous**
- Services interact through network boundaries
- No service accesses another service’s database
- No asynchronous messaging infrastructure is used in the MVP

External systems (payment gateway) communicate only with the Payments service.

---

## Repository strategy

The project uses a **monorepo** strategy:

- One repository contains all services
- Each service remains logically and operationally independent
- Shared tooling is allowed (linting, CI, scripts)
- Shared business logic across services is not allowed

Monorepo is a development convenience, not an architectural dependency.

---

## Explicit non-goals (for architecture)

The following are intentionally excluded from this document:

- API endpoint definitions
- Database schemas
- Internal service state machines
- Payment payload formats
- Retry and timeout policies
- Monitoring and alerting specifics

These will be defined in later design stages.

---

## Architectural stability

Decisions in this document are considered **stable** for the MVP.

Future iterations may introduce:
- Event-driven communication
- Service scaling beyond a single node
- More granular services

However, those changes must preserve:
- Clear service ownership
- No shared databases
- Explicit service boundaries
