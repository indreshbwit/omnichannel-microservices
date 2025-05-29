Markdown

# Internal Users Component Overview

The Internal Users component is responsible for managing **system-level users** such as admins, support engineers, and automated system agents (e.g., background workers, integration daemons). These users operate within the tenant scope but are **not tied to external platform identities**. They are primarily used for managing access, support tasks, and performing automated or system-level functions in a secure and isolated manner.

---

## Responsibilities

* Manage creation, update, deactivation of internal users.
* Assign roles and permissions to internal users.
* Distinguish between internal users and platform-bound users.
* Support auditing, activity tracking, and privileged operations.
* Manage system agents for automated processes.

---

## Types of Internal Users

| Type            | Description                                                |
| :-------------- | :--------------------------------------------------------- |
| **Admin** | Full access to tenant resources                            |
| **Support Engineer** | Limited access for troubleshooting and assistance          |
| **System Agent** | Used by daemons, schedulers, workers, integration bots     |

---

## Interface Definitions

### IInternalUser Interface

Defines the core attributes of an internal user entity.

```python
class IInternalUser(ABC):
    id: UUID
    tenant_id: UUID
    username: str
    email: str
    full_name: str
    type: str  # admin, support, system
    is_active: bool
    roles: List[str]
    metadata: Dict[str, Any]
    created_at: datetime
    updated_at: datetime
IInternalUserService Interface
Outlines the operations for managing internal users at the service layer.

Python

class IInternalUserService(ABC):
    def create_internal_user(self, request: CreateInternalUserRequest) -> InternalUserResponse
    def get_internal_user(self, user_id: UUID) -> InternalUser
    def list_internal_users(self, tenant_id: UUID) -> List[InternalUser]
    def deactivate_internal_user(self, user_id: UUID) -> bool
    def update_roles(self, user_id: UUID, roles: List[str]) -> bool
Data Model
InternalUser Entity
Represents the structure of an internal user entity.

Python

@dataclass
class InternalUser:
    id: UUID
    tenant_id: UUID
    username: str
    email: str
    full_name: str
    type: str
    is_active: bool = True
    roles: List[str] = field(default_factory=list)
    metadata: Dict[str, Any] = field(default_factory=dict)
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
Request/Response Schemas
CreateInternalUserRequest
Defines the payload for creating a new internal user.

Python

@dataclass
class CreateInternalUserRequest:
    tenant_id: str
    username: str
    email: str
    full_name: str
    type: str  # admin, support, system
    roles: List[str]
    metadata: Dict[str, Any]
InternalUserResponse
A standardized response model for internal user details.

Python

@dataclass
class InternalUserResponse:
    id: str
    username: str
    email: str
    full_name: str
    type: str
    roles: List[str]
    is_active: bool
API Endpoints
REST Endpoints
The RESTful API provides the following endpoints for internal user management.

YAML

POST /api/v1/internal-users:
  summary: Create an internal user
  requestBody: CreateInternalUserRequest
  responses:
    201: InternalUserResponse
    400: ValidationError

GET /api/v1/internal-users/{user_id}:
  summary: Retrieve a single internal user
  responses:
    200: InternalUserResponse
    404: NotFound

GET /api/v1/internal-users:
  summary: List all internal users for a tenant
  queryParams:
    tenant_id: str
  responses:
    200: List[InternalUserResponse]

PUT /api/v1/internal-users/{user_id}/roles:
  summary: Update internal user's roles
  requestBody:
    roles: List[str]
  responses:
    200: Success
    400: ValidationError

DELETE /api/v1/internal-users/{user_id}:
  summary: Deactivate an internal user
  responses:
    200: Success
    404: NotFound
gRPC Interface
The gRPC service defines the following methods for internal user operations.

Protocol Buffers

service InternalUserService {
  rpc CreateInternalUser(CreateInternalUserRequest) returns (InternalUserResponse);
  rpc GetInternalUser(GetInternalUserRequest) returns (InternalUserResponse);
  rpc ListInternalUsers(ListInternalUsersRequest) returns (InternalUserListResponse);
  rpc DeactivateInternalUser(DeactivateInternalUserRequest) returns (GenericResponse);
  rpc UpdateUserRoles(UpdateUserRolesRequest) returns (GenericResponse);
}
Event Topics
Kafka Events
Defines the types of events related to internal user changes.

Python

class InternalUserEvents:
    INTERNAL_USER_CREATED = "internal_user.created"
    INTERNAL_USER_DEACTIVATED = "internal_user.deactivated"
    INTERNAL_USER_ROLE_UPDATED = "internal_user.roles_updated"
Permissions and Security
Only tenant administrators can manage internal users.
Role-based access control applies to internal users.
Support users may have temporary scoped access (via feature flag or token expiration).
All sensitive actions are logged in audit logs.
Monitoring & Auditing
Audit logs:
For all changes to internal users.
Metrics:
internal_users_active_total
internal_user_role_changes_total
Alerts:
Excessive deactivation or failed logins.
System agents failing health checks.
Validation Rules
Email must be unique per tenant.
Username must follow naming convention: system_agent_*, support_*, or admin_*.
Type must be one of: admin, support, system.
Cannot assign restricted roles (e.g., root, superadmin) from API — only super-admins can do this.
Usage Examples
Create Admin User
JSON

POST /api/v1/internal-users
{
  "tenant_id": "tenant-xyz",
  "username": "admin_001",
  "email": "admin@example.com",
  "full_name": "Tenant Admin",
  "type": "admin",
  "roles": ["tenant_admin"],
  "metadata": {
    "created_by": "system"
  }
}
Update Roles
JSON

PUT /api/v1/internal-users/uuid-123/roles
{
  "roles": ["support_engineer", "read_only"]
}
Data Flow Diagram
Illustrates the flow for creating a new internal user.

Code snippet

sequenceDiagram
    participant UI as Admin Panel
    participant API as Core API
    participant SVC as InternalUserService
    participant DB as Database
    participant Kafka as Event Bus

    UI->>API: CreateInternalUserRequest
    API->>SVC: CreateInternalUser
    SVC->>DB: INSERT InternalUser
    SVC->>Kafka: Emit internal_user.created
    SVC-->>API: InternalUserResponse
    API-->>UI: 201 Created
