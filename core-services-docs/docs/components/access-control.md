# Access Control Component (RBAC + OAuth)

## Overview

The Access Control Component enforces authorization rules to control access to resources and actions within the system. It combines Role-Based Access Control (RBAC) with OAuth scopes to provide fine-grained, tenant-aware permissions. This component ensures users only perform allowed actions based on their assigned roles, permissions, and OAuth scopes.

---

## Responsibilities

- Manage roles and their associated permissions  
- Enforce RBAC policies for all protected resources  
- Integrate OAuth scopes for third-party delegated access  
- Support hierarchical and tenant-scoped roles  
- Provide APIs for role and permission management  
- Evaluate access rights dynamically during service requests  
- Support policy updates with minimal downtime  

---

## Key Concepts

- **Role**: A named collection of permissions (e.g., Admin, Viewer, Editor)  
- **Permission**: Defines a specific allowed action on a resource (e.g., `read:user`, `delete:tenant`)  
- **OAuth Scope**: Permissions delegated via OAuth token scopes  
- **Tenant Scope**: Roles and permissions scoped within tenant boundaries  
- **User-Role Binding**: Assigns users one or more roles per tenant  

---

## Data Models

### Role

| Field        | Type    | Description                      |
|--------------|---------|--------------------------------|
| `id`         | GUID    | Unique identifier               |
| `name`       | string  | Role name (e.g., Admin)         |
| `description`| string  | Role description                |
| `permissions`| List<Permission> | List of assigned permissions |

### Permission

| Field      | Type    | Description                     |
|------------|---------|--------------------------------|
| `id`       | GUID    | Unique identifier               |
| `action`   | string  | Action name (e.g., `read`, `write`) |
| `resource` | string  | Resource name (e.g., `user`, `tenant`) |

### UserRoleBinding

| Field      | Type    | Description                     |
|------------|---------|--------------------------------|
| `id`       | GUID    | Unique identifier               |
| `user_id`  | GUID    | Associated user ID              |
| `role_id`  | GUID    | Assigned role ID                |
| `tenant_id`| GUID    | Tenant context for the role     |

---

## API Endpoints (gRPC)

### RoleService

| Method            | Request Type          | Response Type          | Description                       |
|-------------------|-----------------------|------------------------|---------------------------------|
| `CreateRole`      | `CreateRoleRequest`    | `RoleResponse`         | Create a new role               |
| `GetRole`         | `GetRoleRequest`       | `RoleResponse`         | Retrieve role details           |
| `ListRoles`       | `ListRolesRequest`     | `ListRolesResponse`    | List all roles for a tenant     |
| `UpdateRole`      | `UpdateRoleRequest`    | `RoleResponse`         | Update an existing role         |
| `DeleteRole`      | `DeleteRoleRequest`    | `Empty`                | Delete a role                  |

### PermissionService

| Method             | Request Type           | Response Type          | Description                      |
|--------------------|------------------------|------------------------|---------------------------------|
| `ListPermissions`  | `ListPermissionsRequest` | `ListPermissionsResponse` | List all available permissions |

### UserRoleBindingService

| Method             | Request Type           | Response Type          | Description                      |
|--------------------|------------------------|------------------------|---------------------------------|
| `AssignRole`       | `AssignRoleRequest`     | `Empty`                | Assign role to a user            |
| `RevokeRole`       | `RevokeRoleRequest`     | `Empty`                | Revoke role from a user          |
| `ListUserRoles`    | `ListUserRolesRequest`  | `ListUserRolesResponse`| List roles assigned to a user    |

---

## Access Evaluation Flow

1. **Request received** with JWT containing user claims (roles, permissions, tenant ID, OAuth scopes).  
2. **Extract roles and scopes** from token claims.  
3. **Check permissions** required for the requested action/resource.  
4. **Evaluate RBAC policies**: user must have at least one role granting the permission.  
5. **Evaluate OAuth scopes**: ensure delegated scopes cover the requested permission if OAuth token is used.  
6. **Allow or deny** access based on evaluation.  

---

## Integration Points

- Works tightly with **Authentication Component** (token claims)  
- Enforced in API gateway or service middleware  
- Permissions dynamically loaded per tenant and cached for performance  

---

## Security Considerations

- Role and permission changes propagate in near real-time to caches  
- Least privilege principle applied during role creation  
- Audit logging on role assignments, revocations, and permission changes  

---

## Class Diagram Reference

![Access Control Class Diagram](../../diagrams/previews/access-control-class-diagram.png)

---

## Summary

The Access Control component ensures secure and flexible authorization by combining RBAC with OAuth scopes. It supports multi-tenant environments and dynamic permission evaluation, enabling fine-grained access control across the platform.


