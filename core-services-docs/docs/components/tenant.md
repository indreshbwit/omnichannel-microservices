# Tenant Management Component

## Overview

Tenant Management is responsible for onboarding and managing tenants (customers/organizations) in the multi-tenant Core Service. Each tenant has isolated data storage via PostgreSQL schemas.

---

## Responsibilities

- Create and manage tenant profiles  
- Maintain tenant lifecycle status (active, suspended, deleted)  
- Assign isolated DB schema per tenant for data separation  
- Manage tenant-wide configuration such as feature flags  

---

## Data Model

| Field       | Type    | Description                          |
|-------------|---------|------------------------------------|
| `id`        | UUID    | Unique tenant identifier            |
| `name`      | String  | Tenant name                        |
| `db_schema` | String  | PostgreSQL schema assigned to tenant |
| `status`    | Enum    | Tenant status (Active, Suspended)  |
| `created_at`| DateTime| Timestamp of tenant creation        |
| `updated_at`| DateTime| Timestamp of last update            |

---

## Relationships

- One tenant to many users  
- One tenant to many roles  
- One tenant to many platform credentials  

---

## Class Diagram Reference

![Tenant Class Diagram](../../diagrams/previews/class-diagram.png)

---

## API Endpoints

### gRPC - TenantService

| Method           | Request Type       | Response Type      | Description                       |
|------------------|--------------------|--------------------|---------------------------------|
| `CreateTenant`   | `CreateTenantRequest`| `TenantResponse`  | Create a new tenant              |
| `GetTenant`      | `GetTenantRequest`   | `TenantResponse`  | Retrieve tenant by ID            |
| `UpdateTenant`   | `UpdateTenantRequest`| `TenantResponse`  | Update tenant details            |
| `DeleteTenant`   | `DeleteTenantRequest`| `Empty`           | Remove a tenant                  |

---

### REST API (via gRPC Gateway)

| Endpoint                | Method | Description           |
|-------------------------|--------|-----------------------|
| `/v1/tenants`           | POST   | Create tenant         |
| `/v1/tenants/{tenantId}`| GET    | Get tenant details    |
| `/v1/tenants/{tenantId}`| PUT    | Update tenant         |
| `/v1/tenants/{tenantId}`| DELETE | Delete tenant         |

---

## Security

- Only admin users with specific roles may create/update/delete tenants  
- Tenant isolation enforced via DB schema and RBAC

---

## Summary

Tenant Management is foundational to Core Service multi-tenancy, ensuring data isolation and configurable tenant-specific behavior.


