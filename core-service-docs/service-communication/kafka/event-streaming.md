# Event Streaming with Kafka

## Why Kafka?

- High-throughput, low-latency messaging
- Horizontal scalability
- Strong durability guarantees

## Typical Use Cases

- User lifecycle events: `user.created`, `user.updated`
- Mail processing: `email.received`, `email.failed`
- System-wide notifications: `audit.logged`, `billing.invoice.generated`

## Event Flow Example

1. `Core-Service` emits `user.created` to Kafka.
2. `Mail-Service` and `Analytics-Service` consume this event.
3. Each service updates its own local DB or triggers workflows.

## Observability

- Use Prometheus exporters for Kafka lag
- Use logs to trace correlation IDs
- Use dashboards to monitor throughput, errors, retries

## Event Versioning Strategy

- Add new fields as optional
- Use schema version headers
- Consumers must be forward-compatible

