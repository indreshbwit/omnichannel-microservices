# Interservice Communication Patterns

We adopt multiple patterns of interservice communication depending on the use case and performance requirements:

## 1. Synchronous Communication (Request/Response)
- **Used with:** gRPC, HTTP
- **When to use:** Real-time responses required (e.g., authentication, validation)
- **Pros:** Simpler to understand, immediate feedback
- **Cons:** Tight coupling, risk of cascading failure if a service is down

**Example:**
Service A requests user profile from Core-Service via gRPC.

## 2. Asynchronous Communication (Event-Driven)
- **Used with:** Kafka
- **When to use:** Fire-and-forget, eventual consistency (e.g., user creation, email sync)
- **Pros:** Decoupled services, more resilient, better for scaling
- **Cons:** Harder to debug, requires idempotent consumers

**Example:**
Mail-Service emits `email.received` event → Analytics-Service listens for analytics.

## 3. Hybrid Pattern
- Combine sync for real-time needs and async for propagation
- Use correlation IDs to trace requests across patterns

## Best Practices

- Use service discovery to locate services dynamically
- Use standard message formats (e.g., protobuf for gRPC, Avro/JSON for Kafka)
- Design APIs and events for backward compatibility

