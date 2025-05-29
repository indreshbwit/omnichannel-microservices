Markdown

# Service Platform Component Overview

The Service Platform component manages which **third-party services** (e.g., Outlook, Gmail, WhatsApp) are available and configured for each tenant. It enables the **registration, activation, and configuration** of service providers on a per-tenant basis, facilitating pluggable integrations and dynamic service enablement within the system.

---

## Class Interfaces

### IServicePlatform Interface

Defines the core attributes of a service platform entity.

```python
class IServicePlatform(ABC):
    id: UUID
    tenant_id: UUID
    service_name: str
    config: Dict[str, Any]
    is_active: bool
    created_at: datetime
    updated_at: datetime

    @abstractmethod
    def to_dict(self) -> Dict[str, Any]
IServicePlatformService Interface
Outlines the operations for managing service platforms at the service layer.

Python

class IServicePlatformService(ABC):
    @abstractmethod
    def register_platform(self, request: RegisterPlatformRequest) -> RegisterPlatformResponse
    @abstractmethod
    def get_platform(self, request: GetPlatformRequest) -> GetPlatformResponse
    @abstractmethod
    def update_platform(self, request: UpdatePlatformRequest) -> UpdatePlatformResponse
    @abstractmethod
    def deactivate_platform(self, request: DeactivatePlatformRequest) -> DeactivatePlatformResponse
    @abstractmethod
    def list_platforms(self, request: ListPlatformsRequest) -> ListPlatformsResponse
IServicePlatformRepository Interface
Defines the methods for interacting with the persistent storage for service platforms.

Python

class IServicePlatformRepository(ABC):
    @abstractmethod
    def create(self, platform: ServicePlatform) -> ServicePlatform
    @abstractmethod
    def get_by_id(self, platform_id: UUID) -> Optional[ServicePlatform]
    @abstractmethod
    def get_by_tenant_service(self, tenant_id: UUID, service_name: str) -> Optional[ServicePlatform]
    @abstractmethod
    def update(self, platform: ServicePlatform) -> ServicePlatform
    @abstractmethod
    def deactivate(self, platform_id: UUID) -> bool
    @abstractmethod
    def list_by_tenant(self, tenant_id: UUID) -> List[ServicePlatform]
Data Models
ServicePlatform Entity
Represents the structure of a service platform entity.

Python

@dataclass
class ServicePlatform:
    id: UUID
    tenant_id: UUID
    service_name: str
    config: Dict[str, Any]
    is_active: bool = True
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
PlatformConfiguration Schema
Defines the schema for platform configuration data.

Python

@dataclass
class PlatformConfiguration:
    client_id: str
    client_secret: str
    tenant_id: Optional[str]
    additional_data: Optional[Dict[str, Any]] = None
Request/Response Models
RegisterPlatformRequest
Defines the payload for registering a new service platform.

Python

@dataclass
class RegisterPlatformRequest:
    tenant_id: str
    service_name: str
    config: PlatformConfiguration
RegisterPlatformResponse
Defines the response after a platform registration attempt.

Python

@dataclass
class RegisterPlatformResponse:
    platform: PlatformResponse
    success: bool
    message: str
PlatformResponse
A standardized response model for platform details.

Python

@dataclass
class PlatformResponse:
    id: str
    tenant_id: str
    service_name: str
    config: Dict[str, Any]
    is_active: bool
    created_at: str
    updated_at: str
API Endpoints
REST Endpoints
The RESTful API provides the following endpoints for service platform management.

YAML

POST /api/v1/platforms:
  summary: Register new service platform for a tenant
  request_body: RegisterPlatformRequest
  responses:
    201: RegisterPlatformResponse
    400: ValidationError
    409: PlatformAlreadyExists

GET /api/v1/platforms/{platform_id}:
  summary: Get platform configuration by ID
  parameters:
    - platform_id: string (UUID)
  responses:
    200: PlatformResponse
    404: PlatformNotFound

PUT /api/v1/platforms/{platform_id}:
  summary: Update platform configuration
  request_body: UpdatePlatformRequest
  responses:
    200: PlatformResponse
    400: ValidationError
    404: PlatformNotFound

POST /api/v1/platforms/{platform_id}/deactivate:
  summary: Deactivate platform
  responses:
    200: DeactivatePlatformResponse
    404: PlatformNotFound

GET /api/v1/platforms:
  summary: List all platforms for a tenant
  parameters:
    - tenant_id: string (UUID)
  responses:
    200: ListPlatformsResponse
gRPC Service Definition
The gRPC service defines the following methods for platform operations.

Protocol Buffers

service PlatformService {
  rpc RegisterPlatform(RegisterPlatformRequest) returns (RegisterPlatformResponse);
  rpc GetPlatform(GetPlatformRequest) returns (GetPlatformResponse);
  rpc UpdatePlatform(UpdatePlatformRequest) returns (UpdatePlatformResponse);
  rpc DeactivatePlatform(DeactivatePlatformRequest) returns (DeactivatePlatformResponse);
  rpc ListPlatforms(ListPlatformsRequest) returns (ListPlatformsResponse);
}
Event Definitions
PlatformEvents
Defines the types of events related to platform changes.

Python

class PlatformEvents:
    PLATFORM_REGISTERED = "platform.registered"
    PLATFORM_UPDATED = "platform.updated"
    PLATFORM_DEACTIVATED = "platform.deactivated"
PlatformRegisteredEvent
Schema for the PlatformRegisteredEvent.

Python

@dataclass
class PlatformRegisteredEvent:
    event_type: str = "platform.registered"
    timestamp: datetime
    platform_id: str
    tenant_id: str
    service_name: str
    config: Dict[str, Any]
Business Rules
A tenant may register only one configuration per service (e.g., one Outlook config per tenant).
All platforms must include required credentials (e.g., client ID, secret).
Inactive platforms are excluded from integration flows.
Sensitive config data is encrypted in storage.
Error Handling
Exceptions
Custom error classes for specific platform-related issues.

Python

class PlatformServiceError(Exception): pass
class PlatformAlreadyExistsError(PlatformServiceError): pass
class PlatformNotFoundError(PlatformServiceError): pass
class InvalidPlatformConfigError(PlatformServiceError): pass
Data Flow Diagram
Illustrates the flow for registering a new service platform.

Code snippet

sequenceDiagram
    participant C as Client
    participant A as API Gateway
    participant P as Platform Service
    participant Repo as Repository
    participant DB as Database
    participant E as Event Bus
    
    C->>A: POST /platforms
    A->>P: RegisterPlatformRequest
    P->>P: Validate config
    P->>Repo: Create platform entry
    Repo->>DB: INSERT platform
    DB-->>Repo: Return created record
    Repo-->>P: Platform Entity
    P->>E: Emit platform.registered
    P-->>A: RegisterPlatformResponse
    A-->>C: HTTP 201 Created
Security & Audit
Configs are encrypted using AES256 or a secrets manager.
All changes logged in audit trail (actor, change diff, timestamp).
API endpoints protected by RBAC policies.
Performance
Frequently accessed platforms cached per tenant.
Redis cache TTL: 1 hour; refreshed on update.
SQL

CREATE INDEX idx_service_platform_tenant_service ON service_platforms(tenant_id, service_name);
