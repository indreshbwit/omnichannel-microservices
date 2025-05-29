Markdown

# Permissions Component Overview

The Permissions component is designed to manage **fine-grained access control** within the system. It allows for the definition of individual permissions, which can then be assigned to various roles, enabling robust control over user access. This component supports a full suite of **CRUD (Create, Read, Update, Delete) operations** for permissions.

---

## Class Interfaces

### IPermission Interface

Defines the core attributes of a permission entity.

```python
class IPermission(ABC):
    id: UUID
    tenant_id: UUID
    name: str
    description: Optional[str]
    resource: str
    action: str
    created_at: datetime
    updated_at: datetime

    @abstractmethod
    def to_dict(self) -> Dict[str, Any]
IPermissionService Interface
Outlines the operations for managing permissions at the service layer.

Python

class IPermissionService(ABC):
    @abstractmethod
    def create_permission(self, request: CreatePermissionRequest) -> CreatePermissionResponse
    @abstractmethod
    def get_permission(self, request: GetPermissionRequest) -> GetPermissionResponse
    @abstractmethod
    def update_permission(self, request: UpdatePermissionRequest) -> UpdatePermissionResponse
    @abstractmethod
    def delete_permission(self, request: DeletePermissionRequest) -> DeletePermissionResponse
    @abstractmethod
    def list_permissions(self, request: ListPermissionsRequest) -> ListPermissionsResponse
IPermissionRepository Interface
Defines the methods for interacting with the persistent storage for permissions.

Python

class IPermissionRepository(ABC):
    @abstractmethod
    def create(self, permission: Permission) -> Permission
    @abstractmethod
    def get_by_id(self, permission_id: UUID) -> Optional[Permission]
    @abstractmethod
    def get_by_name(self, name: str, tenant_id: UUID) -> Optional[Permission]
    @abstractmethod
    def update(self, permission: Permission) -> Permission
    @abstractmethod
    def delete(self, permission_id: UUID) -> bool
    @abstractmethod
    def list_by_tenant(self, tenant_id: UUID, filters: PermissionFilters) -> List[Permission]
Data Models
Permission Entity
Represents the structure of a permission entity.

Python

@dataclass
class Permission:
    id: UUID
    tenant_id: UUID
    name: str
    description: Optional[str] = None
    resource: str
    action: str
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
PermissionFilters
Used to apply filters when listing permissions.

Python

@dataclass
class PermissionFilters:
    name_contains: Optional[str] = None
    resource: Optional[str] = None
    action: Optional[str] = None
Request/Response Models
CreatePermissionRequest
Defines the payload for creating a new permission.

Python

@dataclass
class CreatePermissionRequest:
    tenant_id: str
    name: str
    description: Optional[str] = None
    resource: str
    action: str
CreatePermissionResponse
Defines the response after a permission creation attempt.

Python

@dataclass
class CreatePermissionResponse:
    permission: PermissionResponse
    success: bool
    message: str
PermissionResponse
A standardized response model for permission details.

Python

@dataclass
class PermissionResponse:
    id: str
    tenant_id: str
    name: str
    description: Optional[str]
    resource: str
    action: str
    created_at: str
    updated_at: str
Data Flow
Permission Creation Flow
Illustrates the flow for creating a new permission.

Code snippet

sequenceDiagram
    participant C as Client
    participant A as API Gateway
    participant P as Permission Service
    participant Repo as Repository
    participant DB as Database
    participant E as Event Publisher
    
    C->>A: POST /permissions
    A->>P: CreatePermissionRequest
    P->>P: Validate Request
    P->>Repo: Create Permission
    Repo->>DB: INSERT Permission
    DB-->>Repo: Permission Created
    Repo-->>P: Permission Entity
    P->>E: Publish PermissionCreated Event
    P-->>A: CreatePermissionResponse
    A-->>C: HTTP 201 Created
API Endpoints
REST Endpoints
The RESTful API provides the following endpoints for permission management.

YAML

POST /api/v1/permissions:
  summary: Create new permission
  request_body: CreatePermissionRequest
  responses:
    201: CreatePermissionResponse
    400: ValidationError
    409: PermissionAlreadyExists

GET /api/v1/permissions/{permission_id}:
  summary: Get permission by ID
  parameters:
    - permission_id: string (UUID)
  responses:
    200: PermissionResponse
    404: PermissionNotFound

PUT /api/v1/permissions/{permission_id}:
  summary: Update permission
  request_body: UpdatePermissionRequest
  responses:
    200: PermissionResponse
    404: PermissionNotFound
    400: ValidationError

DELETE /api/v1/permissions/{permission_id}:
  summary: Delete permission
  responses:
    204: No Content
    404: PermissionNotFound

GET /api/v1/permissions:
  summary: List permissions
  parameters:
    - tenant_id: string (UUID)
    - page: integer
    - per_page: integer
    - filters: PermissionFilters
  responses:
    200: ListPermissionsResponse
gRPC Service Definition
The gRPC service defines the following methods for permission operations.

Protocol Buffers

service PermissionService {
  rpc CreatePermission(CreatePermissionRequest) returns (CreatePermissionResponse);
  rpc GetPermission(GetPermissionRequest) returns (GetPermissionResponse);
  rpc UpdatePermission(UpdatePermissionRequest) returns (UpdatePermissionResponse);
  rpc DeletePermission(DeletePermissionRequest) returns (DeletePermissionResponse);
  rpc ListPermissions(ListPermissionsRequest) returns (ListPermissionsResponse);
}
Event Definitions
PermissionEvents
Defines the types of events related to permission changes.

Python

class PermissionEvents:
    PERMISSION_CREATED = "permission.created"
    PERMISSION_UPDATED = "permission.updated"
    PERMISSION_DELETED = "permission.deleted"
Event Schemas
Schema for the PermissionCreatedEvent. Similar schemas would exist for PERMISSION_UPDATED and PERMISSION_DELETED.

Python

@dataclass
class PermissionCreatedEvent:
    event_type: str = "permission.created"
    timestamp: datetime
    permission_id: str
    tenant_id: str
    name: str
    description: Optional[str]
    resource: str
    action: str
Business Rules
Permission Creation Rules
Permission name must be unique within the tenant.
Resource and action fields define the scope and type of permission.
Deleting a permission removes it from all roles it is assigned to.
System permissions are managed by admins and cannot be modified by regular users.
Error Handling
Error Types
Custom error classes for specific permission-related issues.

Python

class PermissionServiceError(Exception):
    pass

class PermissionNotFoundError(PermissionServiceError):
    pass

class PermissionAlreadyExistsError(PermissionServiceError):
    pass

class InvalidPermissionError(PermissionServiceError):
    pass
Performance Considerations
Caching Strategy
Permissions are cached in Redis with a Time-To-Live (TTL) of 6 hours.
The cache is invalidated upon permission updates or deletions to ensure data consistency.
Database Indexing
Indexes are created on key columns to optimize database query performance.

SQL

CREATE INDEX idx_permissions_tenant_name ON permissions(tenant_id, name);
CREATE INDEX idx_permissions_resource_action ON permissions(resource, action);
