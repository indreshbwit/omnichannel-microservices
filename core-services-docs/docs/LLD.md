# Low-Level Design (LLD)

## Modules

- **Tenant Management**: Create, update, delete tenants.
- **User Management**: User CRUD, platform bindings.
- **RBAC**: Roles, permissions, assignments.
- **OAuth**: Token management and refresh.

## Internal Services

- AuthService
- RoleService
- UserService

## Data Layer

- PostgreSQL for tenant/user data
- Redis for sessions
