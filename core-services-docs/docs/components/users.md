# Users Component

## Overview

The Users component manages all aspects of user accounts within the system. It handles user identity, profile details, tenancy association, and role assignments. This component ensures that user data is securely stored and accessed.

---

## Responsibilities

- Create, update, retrieve, and delete user accounts.
- Associate users with tenants and roles.
- Enforce user uniqueness (e.g., by email per tenant).
- Manage user status (active, inactive, suspended).
- Support internal and external (OAuth) user accounts.
- Integrate with Authentication and Access Control components.
- Emit Kafka events for user lifecycle changes (created, updated, deleted).

---

## Data Model

| Field       | Type    | Description                        |
|-------------|---------|----------------------------------|
| id          | UUID    | Unique user identifier            |
| tenant_id   | UUID    | Tenant the user belongs to        |
| email       | String  | User email (unique per tenant)    |
| name        | String  | User full name                    |
| role_id     | UUID    | Role assigned to user             |
| status      | Enum    | User status (active, suspended)  |
| created_at  | DateTime| Account creation timestamp        |
| updated_at  | DateTime| Last modification timestamp       |

---

## API Surface

### gRPC Methods

| RPC Method        | Request Message        | Response Message       | Description                  |
|-------------------|-----------------------|-----------------------|------------------------------|
| `CreateUser`      | `CreateUserRequest`    | `UserResponse`        | Create a new user             |
| `GetUser`         | `GetUserRequest`       | `UserResponse`        | Retrieve user by ID           |
| `UpdateUser`      | `UpdateUserRequest`    | `UserResponse`        | Update user details           |
| `DeleteUser`      | `DeleteUserRequest`    | `DeleteResponse`      | Delete or deactivate user     |
| `ListUsers`       | `ListUsersRequest`     | `ListUsersResponse`   | List users within a tenant    |

---

## User Lifecycle

- On user creation, verify email uniqueness within the tenant.
- Assign default role if none specified.
- Status defaults to "active".
- Emit `user.created` Kafka event with user metadata.
- Update and delete operations emit `user.updated` and `user.deleted` events respectively.

---

## Security Considerations

- Passwords (if stored) must be hashed securely (e.g., bcrypt).
- User email and identifiers are sensitive PII; encrypt as needed.
- Limit access to user data based on RBAC policies.
- Audit all user operations.

---

## Integration Points

- **Tenant Service:** To associate users with tenants.
- **Roles & Permissions:** To assign roles and manage access control.
- **Authentication:** Used during login and token generation.
- **Kafka:** For event-driven updates and audit trails.

---

## References

- `/models/user.md` for data model details
- `/docs/api/grpc/user-service.md` for gRPC API definitions
- `/docs/components/roles-permissions.md` for role integration

---

*End of Users Component Doc*

