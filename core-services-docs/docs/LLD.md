# Low-Level Design (LLD) – Core Service

## 1. Overview

This document provides the low-level design for the Core Service in the multi-tenant, scalable communication system. It outlines the internal structure, database schema, service interfaces, and architectural components for implementation.

---

## 2. Folder Structure

core-service/
├── app/
│ ├── api/ # REST endpoints
│ ├── db/ # DB sessions, migrations
│ ├── grpc_clients/ # gRPC client stubs
│ ├── grpc_servers/ # gRPC server handlers
│ ├── kafka/ # Kafka producer/consumer logic
│ ├── models/ # SQLAlchemy ORM models
│ ├── schemas/ # Pydantic schemas
│ ├── services/ # Business logic layer
│ ├── utils/ # Helpers: encryption, hashing, etc.
│ └── config.py # Config & settings

yaml


---

## 3. Database Design

### Tables and Relations

| Table                   | Description                                      |
|------------------------|--------------------------------------------------|
| `tenant`               | Tenant metadata                                  |
| `user`                 | Users associated with a tenant                   |
| `role`                 | Roles per tenant                                 |
| `permission`           | Permissions tied to roles                        |
| `user_roles`           | Junction table: user ↔ role                      |
| `role_permissions`     | Junction table: role ↔ permission                |
| `platform_credential`  | OAuth credentials per tenant/platform            |
| `oauth_binding`        | User-specific OAuth tokens                       |

Encrypted fields: access tokens, refresh tokens, client secrets.

---

## 4. Core Service Classes

### TenantService


class TenantService:
    def create_tenant(data: TenantCreate) -> TenantOut:
        # DB insert + Kafka publish
UserService

class UserService:
    def create_user(data: UserCreate, tenant_id: UUID) -> UserOut:
        # Hash password, create user, assign default role
AuthService

class AuthService:
    def login(email, password) -> Token:
        # Verify password, return JWT
RBAC Services
RoleService – Create, assign roles

PermissionService – Define, assign permissions

Enforced at middleware or decorator level

5. Kafka Messaging
Event	Topic	Publisher	Subscribers
tenant.created	core.tenant.events	TenantService	mail-service, others
user.created	core.user.events	UserService	integration-service

Example:

json

{
  "tenant_id": "uuid",
  "name": "Acme Inc",
  "created_at": "2025-05-28T12:00:00Z"
}
6. gRPC APIs

service AuthService {
  rpc ValidateToken(ValidateTokenRequest) returns (UserInfo);
}

service UserService {
  rpc GetUserById(GetUserRequest) returns (UserInfo);
}
Port: 50051

Internal use only (service-to-service)

7. OAuth Binding Flow
Redirect user to external auth

Integration service handles callback

Store encrypted tokens in oauth_binding

Tokens decrypted only when used

Fields:

access_token_encrypted

refresh_token_encrypted

expires_at

8. RBAC Enforcement
Users get roles via user_roles

Roles grant permissions via role_permissions

Middleware checks required permissions


@requires_permission("user:read")
def get_user(...):
9. Security
Encryption: AES-256 (Fernet) for sensitive fields

Authentication: JWT (RS256 public/private keys)

Authorization: Role-based permissions

Secrets: Managed via Kubernetes Secrets and Vault

10. Background Jobs
Task Name	Frequency	Description
refresh_oauth	Every 10 min	Refreshes expired OAuth tokens
cleanup_audit_log	Daily	Cleanup old logs, tokens
retry_kafka	Real-time	Retry failed Kafka messages

11. Error Handling
REST errors: Custom exception handlers

gRPC: Proper status codes

Kafka: Retry with dead-letter queue (DLQ)

Audit: Error audit logs per tenant

12. Monitoring
/health – Liveness, DB check

/metrics – Prometheus

Logging: JSON format, trace IDs, correlation IDs

13. Deployment
Dockerized

Gunicorn/Uvicorn for FastAPI

gRPC & REST coexist in container

Alembic auto-migrations on start

Kubernetes-ready (Helm charts provided)

14. REST API Examples
http

POST /api/v1/tenants/
POST /api/v1/users/
POST /api/v1/auth/login
GET  /api/v1/users/{id}
15. Kafka Consumer Example

@kafka_consumer('core.user.events')
def handle_user_created(msg: UserCreatedEvent):
    log.info("User onboarded", extra={"user_id": msg.user_id})
16. Summary
The Core Service handles multi-tenant user identity, authentication, OAuth binding, and RBAC. It supports gRPC and Kafka for internal communication and provides scalable, secure foundations for the platform
