# 🧱 Core Service Documentation

This documentation provides a comprehensive view of the **Core Service** in your scalable, multi-tenant, multi-platform communication system. It includes architectural designs, APIs, data models, component breakdowns, and system diagrams.

---

## 📁 Directory Structure

```
core-services-docs/
├── README.md
├── diagrams/
│   ├── *.drawio.xml         # Source Draw.io diagrams
│   └── previews/*.png       # Preview images
└── docs/
    ├── *.md                 # Design & architecture docs
    ├── api/                 # API specifications
    ├── communication/       # Messaging protocols & discovery
    ├── components/          # Logical service components
    └── models/              # Domain models
```

---

## 📐 Architecture Design

- [📌 High-Level Design (HLD)](docs/HLD.md)
- [📌 Low-Level Design (LLD)](docs/LLD.md)
- [📁 Folder Structure](docs/FolderStructure.md)
- [🧩 Domain Models](docs/DomainModels.md)

---

## 🧩 Component Documentation

- [Components Overview](docs/Components.md)
- [Access Control](docs/components/access-control.md)
- [Authentication](docs/components/authentication.md)
- [Authentication + Access Control](docs/components/authentication-access-control.md)
- [Internal User Management](docs/components/internal-user-management.md)
- [OAuth Bindings](docs/components/oauth-bindings.md)
- [Roles & Permissions](docs/components/roles-permissions.md)
- [Service Platform Credential Management](docs/components/service-platform-credential-management.md)
- [Tenant Management](docs/components/tenant.md)
- [User-Platform Bindings](docs/components/user-platform-bindings.md)
- [Users](docs/components/users.md)

---

## 📘 APIs

### gRPC Services
- [Auth Service](docs/api/grpc/auth-service.md)
- [User Service](docs/api/grpc/user-service.md)

### OpenAPI (REST)
- [OpenAPI YAML Spec](docs/api/openapi/openapi.yaml)

---

## 🔗 Communication Protocols

- [gRPC](docs/communication/grpc.md)
- [Kafka Events](docs/communication/kafka.md)
- [Feature Flags](docs/communication/feature-flags.md)
- [Service Discovery](docs/communication/service-discovery.md)

---

## 🧬 Domain Models

- [Classes & Interfaces](docs/ClassesInterfaces.md)
- [OAuth Binding](docs/models/oauth-binding.md)
- [Permission](docs/models/permission.md)
- [Platform Credential](docs/models/platform-credential.md)
- [Role](docs/models/role.md)
- [Tenant](docs/models/tenant.md)
- [User](docs/models/user.md)

---

## 🧭 Visual Diagrams

| Type       | Preview Image | Source (.drawio) |
|------------|---------------|------------------|
| ER Diagram | ![ERD](diagrams/previews/er-diagram.png) | [ERD.drawio](docs/Diagrams/ERD.drawio) |
| Component  | ![Component](diagrams/previews/component-diagram.png) | [ComponentDiagram.drawio](docs/Diagrams/ComponentDiagram.drawio) |
| Class      | ![Class](diagrams/previews/class-diagram.png) | [ClassDiagram.drawio](docs/Diagrams/ClassDiagram.drawio) |


---

## ✅ How to Use This Documentation

1. Navigate through the sections using the links above.
2. Use the visual diagrams to understand system design.
3. Reference API specs for implementation and integration.
4. Use communication docs to interface with other services via Kafka or gRPC.

---

## 📬 Contact

For issues or improvements, reach out to the architecture team or create a pull request with suggested documentation updates.

---
