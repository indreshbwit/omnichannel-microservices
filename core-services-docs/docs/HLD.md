# High-Level Design (HLD)

## Overview

This document provides a high-level architecture of the Core Services system, including major components, data flow, and service responsibilities.

## Architecture Diagram

![Component Diagram](Diagrams/ComponentDiagram.png)

## Components

- Core Service (Tenant/User/RBAC)
- Integration Service (OAuth)
- Task Service
- gRPC Gateway
- Kafka Event Bus

## Data Flow

- User registration/authentication via REST
- OAuth binding with third-party platforms
- Kafka publishes events for cross-service sync
- gRPC for internal service calls
