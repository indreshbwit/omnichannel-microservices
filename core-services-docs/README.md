# Core Services Architecture Documentation

Welcome to the Core Services Architecture documentation repository.

This repository contains the comprehensive technical documentation for the Core Service microservice that handles tenant, user, role, permission, OAuth, authentication, and access control management in our multi-tenant SaaS platform.

---

## Structure

- `/docs` — General architectural docs, APIs, domain models, communication patterns.
- `/docs/components` — Component-specific detailed docs (Users, OAuth Bindings, Roles, Access Control, etc.)
- `/docs/models` — Domain model definitions in Markdown format.
- `/docs/api` — Protocol definitions for gRPC and OpenAPI specs.
- `/docs/communication` — Communication layers documentation (gRPC, Kafka, Feature Flags, Service Discovery).
- `/diagrams` — Draw.io XML diagrams and PNG previews illustrating architecture, data models, and component interactions.

---

## Key Features

- Multi-tenant tenant onboarding and management
- Fine-grained Role-Based Access Control (RBAC)
- OAuth 2.0 Integration for third-party platforms (Google, Microsoft)
- User and internal user management
- Secure credential management
- gRPC synchronous APIs + Kafka async event streaming
- Feature flags for configurable behavior per tenant/service

---

## Getting Started

- Review the High-Level Design [HLD.md](./docs/HLD.md)
- Explore domain models in [DomainModels.md](./docs/DomainModels.md)
- Understand core APIs in [APIs.md](./docs/APIs.md)
- Dive into components docs under `/docs/components/`

---

## Diagrams Preview

| Diagram Type     | Description                     | Preview                              |
|------------------|--------------------------------|------------------------------------|
| ER Diagram       | Entity-Relationship Model       | ![ER Diagram](./diagrams/previews/er-diagram.png)          |
| Class Diagram    | Object-Oriented Class Model     | ![Class Diagram](./diagrams/previews/class-diagram.png)    |
| Component Diagram| Microservices & Components View | ![Component Diagram](./diagrams/previews/component-diagram.png) |

---

For any questions, please contact the architecture team.

*End of README*

