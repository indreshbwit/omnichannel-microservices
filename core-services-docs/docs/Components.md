# Components

## CoreService

Handles all tenant, user, RBAC, OAuth logic.

## KafkaConsumer

Listens to events and synchronizes data.

## AuthGateway

Authenticates incoming requests and enforces roles.

## gRPC Layer

Exposes internal APIs via protobuf contracts.
