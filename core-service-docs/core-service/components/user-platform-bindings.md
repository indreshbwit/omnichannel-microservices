Markdown

# User-Platform Bindings Component Overview

The User-Platform Bindings component manages **associations between internal system users and external platform identities** (e.g., Outlook email, Google account, WhatsApp number). These mappings are essential for cross-platform communication, integration syncing, and permission resolution at runtime. Each binding links a user to a third-party platform identity and stores metadata like service, account ID, sync status, and last sync time.

---

## Responsibilities

* Create and manage bindings between internal users and platform-specific identities.
* Handle multi-platform, multi-service mappings per user.
* Track sync status and errors.
* Provide APIs/gRPC endpoints to retrieve bindings for a user or platform.
* Emit events for binding created/updated/deleted.
* Authorize service requests based on binding metadata.

---

## Class Interfaces

### IUserPlatformBinding Interface

Defines the core attributes of a user-platform binding entity.

```python
class IUserPlatformBinding(ABC):
    id: UUID
    user_id: UUID
    tenant_id: UUID
    platform_user_id: str  # e.g., outlook_id, whatsapp_number
    platform: str  # outlook, gmail, whatsapp
    service: str  # mail, chat, calendar
    is_active: bool
    last_synced_at: Optional[datetime]
    sync_status: str  # synced, pending, failed
    metadata: Dict[str, Any]
    created_at: datetime
    updated_at: datetime
IUserPlatformBindingService Interface
Outlines the operations for managing user-platform bindings at the service layer.

Python

class IUserPlatformBindingService(ABC):
    def create_binding(self, request: CreateBindingRequest) -> CreateBindingResponse
    def get_bindings_by_user(self, user_id: UUID) -> List[UserPlatformBinding]
    def get_bindings_by_platform(self, platform: str, platform_user_id: str) -> List[UserPlatformBinding]
    def update_sync_status(self, binding_id: UUID, status: str) -> bool
    def deactivate_binding(self, binding_id: UUID) -> bool
Data Model
UserPlatformBinding Entity
Represents the structure of a user-platform binding entity.

Python

@dataclass
class UserPlatformBinding:
    id: UUID
    user_id: UUID
    tenant_id: UUID
    platform_user_id: str
    platform: str
    service: str
    sync_status: str = "pending"
    last_synced_at: Optional[datetime] = None
    metadata: Dict[str, Any] = field(default_factory=dict)
    is_active: bool = True
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
Request/Response Schemas
CreateBindingRequest
Defines the payload for creating a new binding.

Python

@dataclass
class CreateBindingRequest:
    user_id: str
    tenant_id: str
    platform: str
    service: str
    platform_user_id: str
    metadata: Dict[str, Any]
CreateBindingResponse
Defines the response after a binding creation attempt.

Python

@dataclass
class CreateBindingResponse:
    binding_id: str
    success: bool
    message: str
API Endpoints
REST Endpoints
The RESTful API provides the following endpoints for user-platform binding management.

YAML

POST /api/v1/user-platform-bindings:
  summary: Bind a user to a platform identity
  requestBody: CreateBindingRequest
  responses:
    201: CreateBindingResponse
    400: ValidationError
    409: BindingAlreadyExists

GET /api/v1/user-platform-bindings/by-user/{user_id}:
  summary: List all bindings for a user
  responses:
    200: List[UserPlatformBinding]
    404: UserNotFound

GET /api/v1/user-platform-bindings/by-platform:
  summary: Query bindings by platform and platform_user_id
  parameters:
    - name: platform
    - name: platform_user_id
  responses:
    200: List[UserPlatformBinding]
    404: BindingNotFound

PUT /api/v1/user-platform-bindings/{binding_id}/sync-status:
  summary: Update sync status
  body:
    status: str
  responses:
    200: Success
    400: ValidationError
gRPC Interface
The gRPC service defines the following methods for binding operations.

Protocol Buffers

service UserPlatformBindingService {
  rpc CreateBinding(CreateBindingRequest) returns (CreateBindingResponse);
  rpc GetBindingsByUser(GetBindingsByUserRequest) returns (UserPlatformBindingsResponse);
  rpc GetBindingsByPlatform(GetBindingsByPlatformRequest) returns (UserPlatformBindingsResponse);
  rpc UpdateSyncStatus(UpdateSyncStatusRequest) returns (GenericResponse);
}
Events
UserPlatformBindingEvents
Defines the types of events related to binding changes.

Python

class UserPlatformBindingEvents:
    BINDING_CREATED = "user_platform.binding_created"
    SYNC_STATUS_UPDATED = "user_platform.sync_status_updated"
    BINDING_DEACTIVATED = "user_platform.binding_deactivated"
Indexing & Performance
SQL

CREATE INDEX idx_user_platform ON user_platform_bindings(user_id, platform, service, is_active);
CREATE INDEX idx_platform_identity ON user_platform_bindings(platform, platform_user_id, is_active);
Designed for fast reverse lookups (e.g., from Outlook ID to internal user ID).
Cached metadata for hot keys (e.g., access tokens, scopes).
Data Flow Diagram
Illustrates the flow for creating a new user-platform binding.

Code snippet

sequenceDiagram
    participant C as Client
    participant A as API Gateway
    participant B as UserPlatformBindingService
    participant DB as Database
    participant EB as EventBus

    C->>A: POST /user-platform-bindings
    A->>B: CreateBindingRequest
    B->>DB: INSERT UserPlatformBinding
    DB-->>B: BindingRecord
    B->>EB: Emit binding_created
    B-->>A: CreateBindingResponse
    A-->>C: HTTP 201 Created
Usage Examples
Create New Binding
JSON

POST /api/v1/user-platform-bindings
{
  "user_id": "user-123",
  "tenant_id": "tenant-456",
  "platform": "outlook",
  "service": "mail",
  "platform_user_id": "outlook-user@example.com",
  "metadata": {
    "email": "outlook-user@example.com",
    "access_token": "****"
  }
}
Update Sync Status
JSON

PUT /api/v1/user-platform-bindings/{binding_id}/sync-status
{
  "status": "synced"
}
Validation Rules
A user can bind to multiple platforms but only one per platform/service combo.
Duplicate bindings for the same platform_user_id are not allowed.
Sync status must be one of: synced, pending, failed.
Platform identity must be unique within the platform/service scope.
Security
All platform_user_ids are hashed internally.
Access tokens stored in metadata must be encrypted.
Deactivation requires admin privileges or tenant ownership.
Monitoring
Metrics:
user_platform_bindings_active_total
user_platform_binding_sync_failed_total
Alerts:
High sync failure rate
Missing bindings for active users
Logging:
Audit logs for binding creation, update, deletion
