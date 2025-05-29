# Omnichannel Microservices Documentation

Welcome to the **Omnichannel Microservices System** documentation. This repository provides detailed architecture, design, and implementation documentation for the Core Service, Service Communication mechanisms, data models, components, and templates, along with supporting diagrams.

---

## 📁 Documentation Structure

### 1. [Core Service](./core-service/README.md)

Comprehensive documentation for the core microservice that handles tenants, users, roles, permissions, credentials, authentication, and more.

- [API Endpoints](./core-service/api-endpoints/README.md)
- [Communication Patterns](./core-service/communication-patterns/README.md)
- [Components](./core-service/components/)
- [Data Models](./core-service/data-models/README.md)

---

### 2. [Service Communication](./service-communication/README.md)

Details about gRPC, Kafka, service discovery, feature flags, and interservice communication mechanisms.

- gRPC  
  - [Service Definitions](./service-communication/grpc/service-definitions.md)  
  - [Client/Server Setup](./service-communication/grpc/client-server.md)  
  - [Interceptors](./service-communication/grpc/interceptors.md)

- Kafka  
  - [Event Streaming](./service-communication/kafka/event-streaming.md)  
  - [Producers/Consumers](./service-communication/kafka/producers-consumers.md)  
  - [Topics & Schemas](./service-communication/kafka/topics-schemas.md)

- Interservice Communication  
  - [Patterns](./service-communication/interservice/communication-patterns.md)  
  - [Error Handling](./service-communication/interservice/error-handling.md)

- Feature Flags  
  - [Implementation](./service-communication/feature-flags/implementation.md)  
  - [Configuration](./service-communication/feature-flags/configuration.md)

- Service Discovery  
  - [Registration](./service-communication/service-discovery/registration.md)  
  - [Discovery](./service-communication/service-discovery/discovery.md)

---

### 3. [Diagrams](./diagrams/)

Architecture and system-level visual diagrams in various forms to help with understanding and onboarding.

- [System Architecture](./diagrams/architecture/)
- [Component Diagrams](./diagrams/component-diagrams/)
- [Data Flow Diagrams](./diagrams/data-flow/)
- [ER Models](./diagrams/er-models/)
- [Class Diagrams](./diagrams/class-diagrams/)

---

### 4. [Templates](./templates/)

Reusable markdown templates to standardize the addition of new services, components, or APIs.

- [Service Template](./templates/service-template.md)
- [API Template](./templates/api-template.md)
- [Component Template](./templates/component-template.md)

---

## 🧭 Getting Started

To navigate through the documentation, follow the folder structure or use the links above to dive directly into specific areas.

---

## 📌 Contribution

All documentation contributions should follow the templates and file structure to maintain consistency. Please ensure diagrams are committed in `.drawio` format for ease of updates.

---

## 📞 Contact

For architecture questions or support, contact the system maintainers or refer to the diagrams section for an overview.


