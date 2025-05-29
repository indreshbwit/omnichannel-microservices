# Kafka Topics & Schemas

## Topic Naming Conventions

- Use `<service>.<entity>.<action>` format
- Examples:
  - `core.user.created`
  - `mail.email.received`
  - `auth.login.failed`

## Topic Properties

| Property       | Value             |
|----------------|------------------|
| Partitions     | 6 (default)       |
| Replication    | 3 (HA setup)      |
| Retention      | 7 days (default)  |
| Cleanup Policy | `delete` or `compact` depending on use case |

## Avro/JSON Schema Guidelines

- All messages must follow strict schema versioning.
- Store schemas in a centralized schema registry (e.g., Confluent Schema Registry).
- Example JSON schema:
```json
{
  "type": "record",
  "name": "UserCreatedEvent",
  "fields": [
    {"name": "user_id", "type": "string"},
    {"name": "email", "type": "string"},
    {"name": "tenant_id", "type": "string"},
    {"name": "created_at", "type": "string"}
  ]
}

