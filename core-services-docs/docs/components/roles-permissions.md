# Roles & Permissions Component

## Overview

This component defines and manages the access control model using Roles and Permissions. It enables Role-Based Access Control (RBAC) where roles aggregate permissions, and users are assigned roles.

---

## Responsibilities

- Define roles with descriptive names (e.g., Admin, Editor, Viewer).
- Define permissions that represent allowed actions on resources.
- Associate multiple permissions to a role.
- Assign roles to users.
- Enforce RBAC policies in conjunction with the Access Control component.
- Provide APIs to create, update, delete, and retrieve roles and permissions.
- Emit Kafka events for changes in roles or permissions (e.g., `role.created`).

---

## Data Models

### Role

| Field      | Type    | Description                          |
|------------|---------|------------------------------------|
| id         | UUID    | Unique role identifier              |
| name       | String  | Role name (unique per tenant)       |
| description| String  | Optional role description           |
| created_at | DateTime| Creation timestamp                  |
| updated_at | DateTime| Last update timestamp               |

### Permission

| Field      | Type    | Description                          |
|------------|---------|------------------------------------|
| id         | UUID    | Unique permission identifier        |
| action     | String  | Action name (e.g., "read", "write")|
| resource   | String  | Resource name (e.g., "user", "mail")|
| created_at | DateTime| Creation timestamp                  |
| updated_at | DateTime| Last update timestamp               |

### Role-Permission Mapping

- A many-to-many relationship between roles and permissions.

---

## API Surface

### gRPC Methods

| RPC Method          | Request Message           | Response Message          | Description                        |
|---------------------|---------------------------|--------------------------|----------------------------------|
| `CreateRole`         | `CreateRoleRequest`       | `RoleResponse`           | Create a new role                |
| `GetRole`            | `GetRoleRequest`          | `RoleResponse`           | Retrieve role details            |
| `UpdateRole`         | `UpdateRoleRequest`       | `RoleResponse`           | Update role information          |
| `DeleteRole`         | `DeleteRoleRequest`       | `DeleteResponse`         | Delete a role                   |
| `ListRoles`          | `ListRolesRequest`        | `ListRolesResponse`      | List roles within a tenant      |
| `AssignPermissions`  | `AssignPermissionsRequest`| `AssignPermissionsResponse` | Assign permissions to a role     |
| `RevokePermissions`  | `RevokePermissionsRequest`| `RevokePermissionsResponse` | Remove permissions from a role   |

---

## Role-Based Access Control Flow

- Users are assigned roles.
- Each role contains multiple permissions.
- During authorization, check if user's roles include permissions required for the requested action on a resource.
- Enforce access decisions in middleware or service layers.

---

## Security Considerations

- Roles and permissions are tenant-scoped.
- Changes to roles and permissions should be audited.
- Permissions should be fine-grained to minimize privilege escalation.

---

## Integration Points

- **Users Component:** Roles are assigned to users.
- **Access Control:** Enforces permissions during request handling.
- **Kafka:** Event notifications for role/permission changes.

---

## References

- `/models/role.md`
- `/models/permission.md`
- `/docs/api/grpc/user-service.md` (for user-role assignments)
- `/docs/components/access-control.md`

---

*End of Roles & Permissions Component Doc*

