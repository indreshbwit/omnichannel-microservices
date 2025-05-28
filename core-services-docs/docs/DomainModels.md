# Domain Models

## Tenant

- id: UUID
- name: string
- created_at: datetime

## User

- id: UUID
- email: string
- password_hash: string
- tenant_id: UUID

## Role

- id: UUID
- name: string
- tenant_id: UUID

## Permission

- id: UUID
- action: string
- resource: string
