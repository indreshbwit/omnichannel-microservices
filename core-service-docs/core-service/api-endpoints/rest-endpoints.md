# 🌐 REST API Endpoints for Core Service

This document provides an overview of the REST APIs available in the Core Service, designed for external client and admin dashboard consumption.

---

## 📌 Base URL

/api/v1/


---

## 📖 Endpoint Groups

### 🔹 Tenant Management

- `POST /tenants/` — Create tenant
- `GET /tenants/{id}` — Retrieve tenant details
- `GET /tenants/` — List all tenants

### 🔹 User Management

- `POST /users/` — Create new user
- `GET /users/{id}` — Get user profile
- `PATCH /users/{id}` — Update user info
- `GET /users/?tenant_id=` — Filter by tenant

### 🔹 Role & Permission Management

- `POST /roles/` — Create role
- `POST /roles/{role_id}/permissions/` — Assign permissions
- `GET /permissions/` — List permissions

### 🔹 Credential Management

- `POST /credentials/outlook/` — Add Outlook credentials
- `GET /credentials/{id}` — Retrieve credential metadata

---

## 🔐 Authentication

- Uses OAuth2 Bearer Token
- Validates JWT tokens with tenant scoping
- Enforced via API Gateway or FastAPI dependencies

---

## 🔄 Versioning

- All APIs are under `/api/v1/`
- Future versions will follow semantic versioning: `/api/v2/`, etc.

---

## 🛠️ Tooling

- Swagger UI available at: `/docs`
- OpenAPI schema available at: `/openapi.json`

---

## 📚 Related Docs

- [Components](../components/)
- [gRPC Services](./grpc-services.md)
- [Communication Patterns](../communication-patterns/)


