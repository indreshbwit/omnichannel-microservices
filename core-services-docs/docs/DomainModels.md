# Domain Models

This document describes the core domain models used in the Core Service, their attributes, relationships, and constraints.

---

## Tenant

Represents an isolated tenant or customer of the system.

| Field       | Type     | Description                           |
| ----------- | -------- | ----------------------------------- |
| `Id`        | `GUID`   | Unique identifier for the tenant    |
| `Name`      | `string` | Human-readable tenant name           |
| `DbSchema`  | `string` | Database schema assigned to tenant   |
| `Status`    | `enum`   | Tenant status (Active, Suspended)    |
| `CreatedAt` | `DateTime` | Timestamp of tenant creation       |
| `UpdatedAt` | `DateTime` | Timestamp of last update            |

**Relationships:**  
- Has many `Users`  
- Has many `PlatformCredentials`

---

## User

Represents a user belonging to a tenant.

| Field       | Type     | Description                       |
| ----------- | -------- | --------------------------------- |
| `Id`        | `GUID`   | Unique user ID                    |
| `TenantId`  | `GUID`   | Foreign key to Tenant             |
| `Email`     | `string` | User’s email address (unique)    |
| `Name`      | `string` | Full name                       |
| `RoleId`    | `GUID`   | Foreign key to Role               |
| `Status`    | `enum`   | User status (Active, Disabled)   |
| `CreatedAt` | `DateTime` | Creation timestamp              |
| `UpdatedAt` | `DateTime` | Last update timestamp           |

**Relationships:**  
- Belongs to a `Tenant`  
- Has one `Role`  
- Has many `OAuthBindings`

---

## Role

Defines a role which bundles multiple permissions.

| Field       | Type     | Description                        |
| ----------- | -------- | --------------------------------- |
| `Id`        | `GUID`   | Unique Role ID                    |
| `Name`      | `string` | Role name (e.g., Admin, User)     |
| `Description` | `string` | Human-friendly description       |
| `CreatedAt` | `DateTime` | Creation timestamp              |
| `UpdatedAt` | `DateTime` | Last update timestamp           |

**Relationships:**  
- Has many `Permissions`

---

## Permission

Defines an action allowed on a resource.

| Field       | Type     | Description                    |
| ----------- | -------- | ------------------------------ |
| `Id`        | `GUID`   | Unique Permission ID          |
| `Action`    | `string` | Action name (read, write)      |
| `Resource`  | `string` | Resource name (users, tenants) |
| `Description` | `string` | Explanation of permission    |
| `CreatedAt` | `DateTime` | Creation timestamp           |
| `UpdatedAt` | `DateTime` | Last update timestamp        |

---

## PlatformCredential

Stores OAuth client credentials for external service platforms.

| Field          | Type     | Description                       |
| -------------- | -------- | --------------------------------- |
| `Id`           | `GUID`   | Unique credential ID             |
| `TenantId`     | `GUID`   | Tenant owning this credential    |
| `PlatformName` | `string` | e.g., Outlook, Google            |
| `ClientId`     | `string` | OAuth client ID                  |
| `ClientSecret` | `string` | OAuth client secret (encrypted) |
| `RedirectUris` | `string[]` | Allowed OAuth redirect URIs    |
| `CreatedAt`    | `DateTime` | Created timestamp              |
| `UpdatedAt`    | `DateTime` | Updated timestamp              |

---

## OAuthBinding

Links internal users to external OAuth accounts.

| Field        | Type     | Description                    |
| ------------ | -------- | ------------------------------ |
| `Id`         | `GUID`   | Unique binding ID              |
| `UserId`     | `GUID`   | Internal user ID               |
| `Platform`   | `string` | External platform name         |
| `ExternalId` | `string` | External user account ID       |
| `AccessToken`| `string` | OAuth access token (encrypted) |
| `RefreshToken` | `string` | OAuth refresh token (encrypted) |
| `ExpiresAt`  | `DateTime` | Token expiration timestamp   |
| `CreatedAt`  | `DateTime` | Binding creation timestamp    |
| `UpdatedAt`  | `DateTime` | Last update timestamp         |

---

## ER Diagram

Refer to `/docs/diagrams/er-diagram.drawio.xml` for the full ER diagram, showing relationships and cardinality.

---

# Summary

These models form the foundation of Core Service’s multi-tenant, OAuth-enabled identity management. All data is tenant-scoped to ensure strict data isolation.


