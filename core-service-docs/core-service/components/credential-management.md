Markdown

# Credential Management Component Overview

The Credential Management component handles the **secure storage, retrieval, rotation, and validation** of third-party service credentials (e.g., Microsoft, Google, WhatsApp). It ensures that each tenant's credentials are **encrypted at rest** and scoped to the correct platform and service integration. This component is critical for enabling service-specific onboarding, authentication flows, and API integrations.

---

## Responsibilities

* Store encrypted credentials securely for each tenant-service combination.
* Support automatic credential rotation.
* Manage metadata like expiry, scopes, token types.
* Enable per-user, per-tenant, or global credential access.
* Emit events on credential lifecycle changes.
* Provide secure access for downstream services via APIs/gRPC.

---

---
### Data Flow Diagram (DFD) for Credential Management

```mermaid
flowchart TD

    %% === Clients & Entrypoints ===
    Client[Client (User/Admin/Service)]
    APIGW[API Gateway]
    
    %% === Credential Management Core ===
    CS[CredentialService]
    ENC[Encryption Module / KMS]
    DB[(Credential DB)]
    Cache[Redis (Non-sensitive Metadata Cache)]

    %% === Event System ===
    EB[Kafka / EventBus]
    Audit[Audit Logger]

    %% === Integration Services ===
    Mail[Mail Service]
    Chat[Chat Service]
    WhatsApp[WhatsApp Service]

    %% === Flow Arrows ===
    Client -->|POST /credentials| APIGW
    APIGW -->|StoreCredentialRequest| CS
    CS --> ENC
    ENC -->|Encrypted Blob| CS
    CS --> DB
    CS --> Cache
    CS -->|Emit credential.stored| EB
    EB --> Audit

    %% === Fetch Credential Flow ===
    Mail -->|gRPC GetCredential| CS
    Chat -->|gRPC GetCredential| CS
    WhatsApp -->|gRPC GetCredential| CS

    CS -->|Read Encrypted Credential| DB
    CS --> ENC
    ENC -->|Decrypted Credential| CS

    %% Style
    classDef service fill:#f9f,stroke:#333,stroke-width:1px;
    class CS,ENC,DB,Cache service;

    classDef external fill:#bbf,stroke:#333,stroke-width:1px;
    class Mail,Chat,WhatsApp,Client external;
```


### Communication Flow Between Services
```mermaid

flowchart TD
    subgraph Client Layer
        U[User / Admin / Service]
    end

    subgraph API Layer
        API[API Gateway]
    end

    subgraph Credential Management
        CS[CredentialService]
        ENC[Encryption Module / KMS]
        DB[(Credential DB)]
        CCache[Redis Cache (non-sensitive)]
    end

    subgraph Platform Integration Services
        Mail[Mail Service]
        Chat[Chat Service]
        WhatsApp[WhatsApp Service]
    end

    subgraph Event & Messaging
        EB[Kafka / EventBus]
        Audit[Audit Logger]
    end

    U -->|POST /credentials| API
    API -->|REST/gRPC: StoreCredentialRequest| CS
    CS --> ENC
    ENC --> CS
    CS --> DB
    CS --> CCache
    CS --> EB
    EB --> Audit
    Mail -->|gRPC: GetCredential| CS
    Chat -->|gRPC: GetCredential| CS
    WhatsApp -->|gRPC: GetCredential| CS
    CS -->|Read from| DB
    CS -->|Decryption| ENC
``` 
## Class Interfaces

### ICredential Interface

Defines the core attributes of a credential entity.

```python
class ICredential(ABC):
    id: UUID
    tenant_id: UUID
    service_name: str
    type: str  # e.g., oauth, basic, api_key
    credentials: Dict[str, Any]
    scopes: List[str]
    expires_at: Optional[datetime]
    is_active: bool
    created_at: datetime
    updated_at: datetime

    @abstractmethod
    def is_expired(self) -> bool
ICredentialService Interface
Outlines the operations for managing credentials at the service layer.

Python

class ICredentialService(ABC):
    @abstractmethod
    def store_credential(self, request: StoreCredentialRequest) -> StoreCredentialResponse
    @abstractmethod
    def get_credential(self, request: GetCredentialRequest) -> GetCredentialResponse
    @abstractmethod
    def rotate_credential(self, request: RotateCredentialRequest) -> RotateCredentialResponse
    @abstractmethod
    def revoke_credential(self, request: RevokeCredentialRequest) -> RevokeCredentialResponse
Data Model
Credential Entity
Represents the structure of a credential entity.

Python

@dataclass
class Credential:
    id: UUID
    tenant_id: UUID
    service_name: str
    type: str  # "oauth" | "api_key" | "basic"
    credentials: Dict[str, Any]  # encrypted blob
    scopes: List[str]
    expires_at: Optional[datetime]
    is_active: bool = True
    created_at: datetime = field(default_factory=datetime.utcnow)
    updated_at: datetime = field(default_factory=datetime.utcnow)
Request/Response Schemas
StoreCredentialRequest
Defines the payload for storing a new credential.

Python

@dataclass
class StoreCredentialRequest:
    tenant_id: str
    service_name: str
    type: str
    credentials: Dict[str, str]  # e.g., {"access_token": "..."}
    scopes: List[str]
    expires_at: Optional[str]
StoreCredentialResponse
Defines the response after a credential storage attempt.

Python

@dataclass
class StoreCredentialResponse:
    credential_id: str
    success: bool
    message: str
API Endpoints
REST Endpoints
The RESTful API provides the following endpoints for credential management.

YAML

POST /api/v1/credentials:
  summary: Store service credentials
  request_body: StoreCredentialRequest
  responses:
    201: StoreCredentialResponse
    400: ValidationError
    409: CredentialAlreadyExists

GET /api/v1/credentials/{id}:
  summary: Fetch service credential by ID
  responses:
    200: CredentialResponse
    404: CredentialNotFound

PUT /api/v1/credentials/{id}/rotate:
  summary: Rotate existing credential
  responses:
    200: RotateCredentialResponse
    400: ValidationError
    404: CredentialNotFound

POST /api/v1/credentials/{id}/revoke:
  summary: Revoke credential (mark inactive)
  responses:
    200: RevokeCredentialResponse
gRPC Interface
The gRPC service defines the following methods for credential operations.

Protocol Buffers

service CredentialService {
  rpc StoreCredential(StoreCredentialRequest) returns (StoreCredentialResponse);
  rpc GetCredential(GetCredentialRequest) returns (GetCredentialResponse);
  rpc RotateCredential(RotateCredentialRequest) returns (RotateCredentialResponse);
  rpc RevokeCredential(RevokeCredentialRequest) returns (RevokeCredentialResponse);
}
Events
CredentialEvents
Defines the types of events related to credential changes.

Python

class CredentialEvents:
    CREDENTIAL_STORED = "credential.stored"
    CREDENTIAL_ROTATED = "credential.rotated"
    CREDENTIAL_REVOKED = "credential.revoked"
CredentialStoredEvent
Schema for the CredentialStoredEvent.

Python

@dataclass
class CredentialStoredEvent:
    event_type: str = "credential.stored"
    credential_id: str
    tenant_id: str
    service_name: str
    type: str
    scopes: List[str]
    timestamp: datetime
Security & Encryption
Credentials are encrypted using AES-256 or a secret management system (e.g., Vault, AWS KMS).
Only selected internal services have decryption privileges.
Rotation keys and tokens are never stored in plain text.
All sensitive fields masked in logs.
Python

from cryptography.fernet import Fernet
import json

def encrypt_blob(data: Dict[str, Any], key: str) -> str:
    f = Fernet(key)
    return f.encrypt(json.dumps(data).encode()).decode()

def decrypt_blob(blob: str, key: str) -> Dict[str, Any]:
    f = Fernet(key)
    return json.loads(f.decrypt(blob.encode()).decode())
Validation Rules
Service credentials must be scoped to a tenant and a specific service.
Expired credentials are auto-revoked via background jobs.
Only one active credential per service per tenant is allowed.
Credentials cannot be fetched unless service is active.
Data Flow Diagram
Illustrates the flow for storing a new credential.


sequenceDiagram
    participant C as Client
    participant A as API Gateway
    participant CS as CredentialService
    participant DB as Database
    participant ENC as EncryptionLib
    participant EB as EventBus

    C->>A: POST /credentials
    A->>CS: StoreCredentialRequest
    CS->>ENC: Encrypt(credentials)
    ENC-->>CS: encrypted_blob
    CS->>DB: INSERT Credential
    DB-->>CS: CredentialRecord
    CS->>EB: Emit credential.stored
    CS-->>A: StoreCredentialResponse
    A-->>C: HTTP 201 Created
Indexing & Performance
SQL

CREATE INDEX idx_credentials_tenant_service ON credentials(tenant_id, service_name, is_active);
Credential lookup optimized by tenant_id + service_name.
Redis-based cache for non-sensitive metadata (e.g., scopes, expiration).
Audit & Monitoring
Audit log entries:
Created by, modified by
Field diffing for updates
Prometheus metrics:
credential_active_total{service="outlook"}
credential_expired_total
Alerting for near-expiry credentials
Failure Scenarios
Scenario	Mitigation
Credential expired	Emit expiration event, notify tenant
Credential revoked by provider	Retry + escalate alert
Encryption key rotation	Support re-encryption pipeline

