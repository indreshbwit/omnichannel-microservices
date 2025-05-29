
---

### 📄 `producers-consumers.md`

```markdown
# Kafka Producers & Consumers

## Producer Responsibilities

- Serialize messages using predefined schemas
- Set headers like `correlation_id`, `event_type`, `origin_service`
- Ensure delivery guarantees (at-least-once or exactly-once)

## Consumer Responsibilities

- Subscribe to appropriate topics
- Deserialize and validate schema
- Idempotent processing to avoid duplicates
- Send acknowledgment only after successful processing

## Retry Strategy

- Use exponential backoff for transient failures
- Use Dead Letter Topics (DLT) after N retries

## Example Producer (Python)

```python
producer.produce(
    topic='core.user.created',
    key=user_id,
    value=event_json,
    headers={'correlation_id': cid}
)

