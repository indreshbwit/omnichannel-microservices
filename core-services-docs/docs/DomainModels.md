# Domain Models

## Overview

This document describes the main domain models/entities used by the Core Service and their relationships.

---

## Entity Relationship Diagram

![ER Diagram](../../diagrams/previews/er-diagram.png)

*Source XML file: `/diagrams/er-diagram.drawio.xml`*

---

## Tenant

| Field        | Type   | Description                      |
|--------------|---------|--------------------------------|
| id           | UUID    | Unique Tenant identifier        |
| name         | String  | Tenant display name             |
| db_schema    | String  | Database schema name (for multi-tenant isolation) |
| status       | Enum    | Status (active, suspended, deleted) |

---

## User

| Field        | Type    | Description                              |
|--------------|---------|----------------------------------------|
| id           | UUID    | Unique User identifier                  |
| tenant_id    | UUID    | Associated Tenant ID                    |
| email        | String  | User's email address                    |
| name         | String  | User's full name                        |
| role_id      | UUID    | Role assigned to the user               |

---

## Role

| Field        | Type    | Description                              |
|--------------|---------|----------------------------------------|
| id           | UUID    | Unique Role identifier                  |
| name         | String  | Role name (Admin, Viewer, Editor, etc.)|
| permissions  | List    | List of Permissions assigned            |

---

## Permission

| Field        | Type    | Description                              |
|--------------|---------|----------------------------------------|
| id           | UUID    | Unique Permission identifier            |
| action       | String  | Action (read, write, delete, update)   |
| resource     | String  | Resource name (tenant, user, document) |

---

## PlatformCredential

| Field        | Type    | Description                              |
|--------------|---------|----------------------------------------|
| id           | UUID    | Unique Credential identifier            |
| platform_name| String  | OAuth platform (Google, Microsoft)      |
| client_id    | String  | OAuth client ID                         |
| secret       | String  | OAuth client secret (encrypted)         |
| tenant_id    | UUID    | Tenant owning this credential            |

---

## OAuthBinding

| Field         | Type     | Description                              |
|---------------|----------|----------------------------------------|
| id            | UUID     | Unique OAuth Binding identifier         |
| user_id       | UUID     | User linked to this OAuth binding       |
| platform_id   | UUID     | Platform linked to the binding           |
| access_token  | String   | OAuth access token                       |
| refresh_token | String   | OAuth refresh token                      |
| expires_at    | DateTime | Token expiry timestamp                   |
| scopes        | List     | OAuth scopes                            |

---

*End of Domain Models*

