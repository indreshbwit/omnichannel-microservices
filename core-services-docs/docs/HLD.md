# High-Level Design (HLD) — Core Service Architecture

## 1. Introduction

The Core Service manages foundational business entities such as tenants, users, roles, permissions, OAuth credentials, and authentication mechanisms. It serves as a critical microservice in the overall platform, supporting multi-tenancy, security, and extensibility.

## 2. Architecture Overview

![Component Diagram](../../diagrams/previews/component-diagram.png)

The Core Service architecture includes:

- **Tenant Management**: Handles onboarding and lifecycle of tenants.
- **User Management**: Internal and external user lifecycle, profile, and credentials.
- **Role & Permission Management**: RBAC system for access control.
- **OAuth Credential Management**: Integrates with external platforms for delegated access.
- **Authentication & Authorization**: JWT-based authentication, OAuth token validation.
- **Communication**: 
  - gRPC for synchronous API interactions.
  - Kafka for event streaming and async notifications.
- **Feature Flags**: Configurable flags at tenant and service level to enable/disable features dynamically.

## 3. Deployment Context

- Runs in Kubernetes environment with service registration/discovery.
- Uses PostgreSQL for relational data storage.
- Secrets and tokens encrypted and managed securely.
- Kafka cluster for async event streaming.

## 4. Major Components

- Tenant
- User
- Role
- Permission
- PlatformCredential
- OAuthBinding
- AccessControl
- Authentication

## 5. Data Flow

- Tenant onboarding triggers database schema provisioning.
- Users register or are created internally, assigned roles.
- Users authenticate via password or OAuth.
- Role-based permissions enforced on API calls.
- Changes propagated asynchronously through Kafka topics.

## 6. Technology Stack

| Layer             | Technology                  |
|-------------------|-----------------------------|
| API Layer         | gRPC + OpenAPI Gateway       |
| Data Persistence  | PostgreSQL                   |
| Messaging         | Kafka                       |
| Authentication    | JWT + OAuth 2.0              |
| Configuration     | Feature Flags (DB + cache)   |
| Containerization  | Docker + Kubernetes          |

---

*Refer to `/docs/Diagrams/ComponentDiagram.drawio` for editable source.*

*End of HLD*

