Markdown

# Permission Models Overview

Permissions define **granular access rights** that can be assigned to roles to enforce fine-grained access control across services and resources.

---

## Data Model: `permissions`

| Field          | Type                     | Description                                  |
| :------------- | :----------------------- | :------------------------------------------- |
| `id`           | UUID (PK)                | Primary identifier                           |
| `tenant_id`    | UUID (FK)                | Tenant the permission belongs to             |
| `name`         | String                   | Permission name, e.g., `read:emails`, `send:sms` |
| `description`  | String (nullable)        | Optional human-readable description          |
| `created_at`   | Timestamp                | Record creation timestamp                    |
| `updated_at`   | Timestamp                | Auto-updated timestamp                       |

---

## Data Model: `role_permissions`

| Field           | Type                     | Description                                |
| :-------------- | :----------------------- | :----------------------------------------- |
| `id`            | UUID (PK)                | Unique identifier                          |
| `role_id`       | UUID (FK)                | Role assigned this permission              |
| `permission_id` | UUID (FK)                | Permission granted                         |
| `granted_at`    | Timestamp                | Timestamp when permission was granted      |

---

## Relationships

Illustrates the many-to-many relationship between roles and permissions.

erDiagram
    ROLE ||--o{ ROLE_PERMISSIONS : has
    PERMISSION ||--o{ ROLE_PERMISSIONS : granted_to
    
Many-to-many relationship: Each role can have multiple permissions; each permission can belong to multiple roles.
Permissions are scoped by tenant.

Example: Permission Record
JSON

{
  "id": "fe2a3e67-5d3a-4d4e-b7db-98a6bb6c7b19",
  "tenant_id": "df924d02-743f-4c9d-a9ab-2d0e6230b9cc",
  "name": "read:emails",
  "description": "Allows read access to email resources"
}

Example: Role Permission Assignment
JSON

{
  "id": "7fbcfa10-b5d2-4f2a-8497-f2133cd59e22",
  "role_id": "bb78e206-d9d2-47b3-8884-b238624d1241",
  "permission_id": "fe2a3e67-5d3a-4d4e-b7db-98a6bb6c7b19",
  "granted_at": "2025-05-29T14:50:00Z"
}

Best Practices
Permissions should be named consistently using colon-separated scopes (e.g., service:action).
Use tenant-scoped permissions to ensure isolation.
Avoid assigning permissions directly to users; use roles as the abstraction layer.
Indexing & Optimization
Unique constraint on (tenant_id, name).
Indexes on role_id and permission_id for fast joins in authorization checks.
