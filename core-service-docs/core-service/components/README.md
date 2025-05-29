# 🧩 Core Service Components

This section provides detailed documentation of the **Core Service's major internal components** in the Omnichannel Microservices Platform. These components form the backbone for identity, access, and platform management across all services.

---

## 🔐 Access Control & Identity

### [Users](./users.md)
Manages user entities, including user metadata, multi-tenant associations, and user lifecycle.

### [Internal Users](./internal-users.md)
Represents system users, admins, or service accounts with elevated or specialized access.

### [Authentication](./authentication.md)
Handles login, token issuance, and session validation using OAuth2, JWT, or API keys.

---

## 🛡️ Role-Based Access Control (RBAC)

### [Roles](./roles.md)
Defines logical user groups such as `Admin`, `Viewer`, or `Billing Manager`. Used to simplify access management.

### [Permissions](./permissions.md)
Granular actions (e.g., `read:emails`, `delete:users`) that can be assigned to roles. Permissions are tenant-scoped.

### [Access Control](./access-control.md)
Combines users, roles, and permissions to control who can do what within a tenant or platform.

---

## 🔑 Credential & Platform Binding

### [Credential Management](./credential-management.md)
Handles storage and encryption of service-specific credentials (e.g., API keys, OAuth secrets). Supports per-tenant isolation.

### [Service Platform](./service-platform.md)
Defines external service platforms like **Outlook**, **Gmail**, **WhatsApp**, etc., and their onboarding logic.

### [User Platform Bindings](./user-platform-bindings.md)
Tracks which users are connected to which service platforms, including metadata, scopes, and token mappings.

---

## 📘 Purpose

These components provide a **secure, extensible foundation** for managing tenants, users, and their access across services in a scalable multi-tenant system.

> Designed to be modular and isolated by concern, each component contributes to a clean separation of responsibilities and ease of auditing.

---

## 🧭 Next Steps

After understanding the components, explore:
- [API Endpoints](../api-endpoints/)
- [Data Models](../data-models/)
- [Service Communication](../../service-communication/)

