# 📡 Core Service – Communication Patterns

This section documents how various communication models are implemented in the core service. It includes both synchronous and asynchronous interaction strategies between services and components.

## 📘 Files

- [`data-flow.md`](./data-flow.md)  
  Describes the movement of data within and across services, illustrating how requests and responses are propagated.

- [`event-driven.md`](./event-driven.md)  
  Covers event-driven architecture, focusing on Kafka-based asynchronous communication and pub/sub interactions.

- [`request-response.md`](./request-response.md)  
  Documents the synchronous communication pattern, including REST and gRPC-based service calls.

---

## 🔧 Purpose

These patterns are foundational to ensure:
- Consistent data flow
- Scalable service interaction
- Clear decoupling between microservices

Use these documents to guide the implementation and debugging of communication logic across the system.

