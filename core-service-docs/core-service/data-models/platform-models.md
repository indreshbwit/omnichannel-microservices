Markdown

# Platform Models

## Overview
The Platform Models define the **core entities and structures** that represent service platforms and their integration metadata within the multi-tenant architecture. These models enable managing and configuring external service platforms (e.g., Outlook, Gmail) and their platform-specific settings, credentials, and bindings to users or tenants.

---

## Class Interfaces

### IPlatform Interface
Defines the core attributes of a platform entity.
```python
class IPlatform(ABC):
    id: UUID
    name: str
    description: Optional[str]
    service_type: str  # e.g., "email", "chat", "sms"
    is_active: bool
    created_at: datetime
    updated_at: datetime
    
    @abstractmethod
    def to_dict(self) -> Dict[str, Any]
IPlatformService Interface
Outlines the operations for managing platforms at the service layer.

Python

class IPlatformService(ABC):
    @abstractmethod
    def create_platform(self, request: CreatePlatformRequest) -> CreatePlatformResponse
    @abstractmethod
    def get_platform(self, request: GetPlatformRequest) -> GetPlatformResponse
    @abstractmethod
    def update_platform(self, request: UpdatePlatformRequest) -> UpdatePlatformResponse
    @abstractmethod
    def delete_platform(self, request: DeletePlatformRequest) -> DeletePlatformResponse
    @abstractmethod
    def list_platforms(self, request: ListPlatformsRequest) -> ListPlatformsResponse
IPlatformRepository Interface
Defines the methods for interacting with the persistent storage for platforms.

Python

class IPlatformRepository(ABC):
    @abstractmethod
    def create(self, platform: Platform) -> Platform
    @abstractmethod
    def get_by_id(self, platform_id: UUID) -> Optional[Platform]
    @abstractmethod
    def get_by_name(self, name: str) -> Optional[Platform]
    @abstractmethod
    def update(self, platform: Platform) -> Platform
    @abstractmethod
    def delete(self, platform_id: UUID) -> bool
    @abstractmethod
    def list_all(self) -> List[Platform]
Data Models
Platform Entity
Represents the structure of a service platform.

Python

@dataclass
class Platform:
    id: UUID
    name: str
    description: Optional[str] = None
    service_type: str  # e.g., "email", "chat", "sms", "call"
    is_active: bool = True
    metadata: Dict[str, Any] = field(default_factory=dict)  # platform-specific config
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
PlatformCredential Entity
Represents credentials for a platform.

Python

@dataclass
class PlatformCredential:
    id: UUID
    platform_id: UUID
    tenant_id: UUID
    client_id: str
    client_secret: str
    auth_url: Optional[str] = None
    token_url: Optional[str] = None
    redirect_uris: List[str] = field(default_factory=list)
    scopes: List[str] = field(default_factory=list)
    is_active: bool = True
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
UserPlatformBinding Entity
Represents a binding between a user and an external platform identity.

Python

@dataclass
class UserPlatformBinding:
    id: UUID
    user_id: UUID
    platform_id: UUID
    tenant_id: UUID
    external_user_id: Optional[str]  # user ID on the external platform
    access_token: Optional[str] = None
    refresh_token: Optional[str] = None
    token_expiry: Optional[datetime] = None
    is_active: bool = True
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
Request/Response Models
CreatePlatformRequest
Defines the payload for creating a new platform.

Python

@dataclass
class CreatePlatformRequest:
    name: str
    description: Optional[str] = None
    service_type: str
    metadata: Dict[str, Any] = field(default_factory=dict)
CreatePlatformResponse
Defines the response after a platform creation attempt.

Python

@dataclass
class CreatePlatformResponse:
    platform: Platform
    success: bool
    message: str
PlatformResponse
A standardized response model for platform details.

Python

@dataclass
class PlatformResponse:
    id: str
    name: str
    description: Optional[str]
    service_type: str
    is_active: bool
    metadata: Dict[str, Any]
    created_at: str
    updated_at: str
Data Flow
Platform Registration Flow
Illustrates the flow for registering a new platform.

Code snippet

sequenceDiagram
    participant C as Client
    participant A as API Gateway
    participant P as Platform Service
    participant R as Repository
    participant D as Database
    participant E as Event Bus
    
    C->>A: POST /platforms
    A->>P: CreatePlatformRequest
    P->>P: Validate request
    P->>R: Create Platform Entity
    R->>D: INSERT platform record
    D-->>R: Platform created
    R-->>P: Platform entity
    P->>E: Publish PlatformCreated event
    P-->>A: CreatePlatformResponse
    A-->>C: HTTP 201 Created
API Endpoints
REST Endpoints
The RESTful API provides the following endpoints for platform management.

YAML

POST /api/v1/platforms:
  summary: Create new platform
  request_body: CreatePlatformRequest
  responses:
    201: CreatePlatformResponse
    400: ValidationError
    409: PlatformAlreadyExists

GET /api/v1/platforms/{platform_id}:
  summary: Get platform by ID
  parameters:
    - platform_id: string (UUID)
  responses:
    200: PlatformResponse
    404: PlatformNotFound

PUT /api/v1/platforms/{platform_id}:
  summary: Update platform
  request_body: UpdatePlatformRequest
  responses:
    200: PlatformResponse
    404: PlatformNotFound
    400: ValidationError

DELETE /api/v1/platforms/{platform_id}:
  summary: Delete platform
  responses:
    204: No Content
    404: PlatformNotFound

GET /api/v1/platforms:
  summary: List platforms
  responses:
    200: ListPlatformsResponse
gRPC Service Definition
The gRPC service defines the following methods for platform operations.

Protocol Buffers

service PlatformService {
  rpc CreatePlatform(CreatePlatformRequest) returns (CreatePlatformResponse);
  rpc GetPlatform(GetPlatformRequest) returns (GetPlatformResponse);
  rpc UpdatePlatform(UpdatePlatformRequest) returns (UpdatePlatformResponse);
  rpc DeletePlatform(DeletePlatformRequest) returns (DeletePlatformResponse);
  rpc ListPlatforms(ListPlatformsRequest) returns (ListPlatformsResponse);
}
Event Definitions
PlatformEvents
Defines the types of events related to platform changes.

Python

class PlatformEvents:
    PLATFORM_CREATED = "platform.created"
    PLATFORM_UPDATED = "platform.updated"
    PLATFORM_DELETED = "platform.deleted"
Event Schemas
Schemas for platform-related events.

Python

@dataclass
class PlatformCreatedEvent:
    event_type: str = "platform.created"
    timestamp: datetime
    platform_id: str
    name: str
    service_type: str
    metadata: Dict[str, Any]

@dataclass
class PlatformUpdatedEvent:
    event_type: str = "platform.updated"
    timestamp: datetime
    platform_id: str
    changes: Dict[str, Any]
    updated_by: str
Business Rules
Platform Management Rules
Platform names must be unique.
Service type must be one of the predefined types (e.g., email, chat, sms, call).
Only active platforms can be assigned to tenants and users.
Platform credentials must be securely stored and encrypted.
User platform bindings require valid access tokens for integrations.
Platform metadata should support extensible configuration for diverse services.
Error Handling
Error Types
Custom error classes for specific platform-related issues.

Python

class PlatformServiceError(Exception):
    pass

class PlatformNotFoundError(PlatformServiceError):
    pass

class PlatformAlreadyExistsError(PlatformServiceError):
    pass

class InvalidPlatformTypeError(PlatformServiceError):
    pass

class CredentialValidationError(PlatformServiceError):
    pass
Performance Considerations
Caching Strategy
Frequently accessed platform metadata cached in Redis (TTL: 1 hour).
User platform bindings access tokens cached securely for fast authentication.
Database Indexing
SQL

CREATE UNIQUE INDEX idx_platform_name ON platforms(name);
CREATE INDEX idx_platform_service_type ON platforms(service_type);
CREATE INDEX idx_platform_is_active ON platforms(is_active);
CREATE INDEX idx_platform_credential_platform_id ON platform_credentials(platform_id);
CREATE INDEX idx_user_platform_binding_user_id ON user_platform_bindings(user_id);
CREATE INDEX idx_user_platform_binding_platform_id ON user_platform_bindings(platform_id);
