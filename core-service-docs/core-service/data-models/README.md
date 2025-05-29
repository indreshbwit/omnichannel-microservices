Markdown

# Data Models Component Overview

The Data Models section defines the **core database schema and domain models** used by the core-service. These models are designed to be **multi-tenant, highly scalable, and domain-driven**, enabling fine-grained access control, service-level isolation, and extensibility for onboarding additional platform services (e.g., Gmail, Outlook, WhatsApp).

---

## Purpose

Each model is structured to:

* Represent a core business concept (e.g., User, Role, Permission).
* Support multi-tenancy and cross-platform integration.
* Ensure consistency and normalization.
* Enforce data ownership and scope boundaries.
* Be independently versioned and migrated using tools like Alembic or Django Migrations.

---

## Folder Structure

```plaintext
data-models/
├── README.md               # This file
├── user-models.md          # Schema and logic for User entities
├── role-models.md          # Role definitions and relations
├── permission-models.md    # Permission granularity and scope definitions
└── platform-models.md      # Platform bindings (e.g., Google, Outlook, WhatsApp)
Common Model Features
Feature	Description
id	UUID-based primary key
tenant_id	Foreign key reference for multitenancy
created_at	Timestamp of creation (auto-generated)
updated_at	Timestamp of last modification
is_active	Soft-delete support
scopes	JSON/Enum support for fine-grained access (e.g., ["self", "tenant", "global"])
metadata	JSON field to support dynamic or platform-specific extension

Export to Sheets
Entity Relationships
Illustrates the relationships between core entities.

Code snippet

erDiagram
    TENANT ||--o{ USER : has
    USER ||--o{ ROLE_BINDING : binds
    ROLE ||--o{ ROLE_BINDING : binds
    ROLE ||--o{ PERMISSION_BINDING : grants
    PERMISSION ||--o{ PERMISSION_BINDING : grants
    USER ||--o{ PLATFORM_CREDENTIAL : authenticates
Design Guidelines
All foreign keys are UUIDs and tenant-bound.
Use polymorphic tables (e.g., platform_credentials) for extensibility.
Normalize user and platform metadata in separate models.
Avoid joins across tenant boundaries.
Use partitioning and sharding for high-volume tables.
Integration with Other Services
Shared user_id across services using Kafka user-created events.
Credential records linked to external platform accounts.
Permissions and roles cached and pushed to AuthN/AuthZ layer.
Migration & Versioning
Use Alembic or Django ORM for managing schema migrations.
Track model version in each table if schema drift is expected.
Add schema_version field to allow backward compatibility.
Data Ownership & Security
All user-bound data must include tenant_id and owner_user_id.
Enforce row-level security via scoped queries.
Support field-level encryption for sensitive columns (e.g., OAuth tokens).

