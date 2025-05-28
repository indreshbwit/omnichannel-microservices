# Internal Users Management Component

## Overview

This component manages the lifecycle of internal users such as administrators, system operators, and support staff. It includes user creation, role assignment, password management, user status, and audit logging. Internal users have elevated privileges compared to normal tenant users and can perform administrative functions across tenants or platform-wide.

---

## Responsibilities

- Create, update, and delete internal user accounts  
- Assign and manage roles with granular permissions  
- Manage internal user credentials and password policies  
- Enable/disable accounts and track status (active, suspended, deactivated)  
- Maintain audit logs for internal user activities  
- Enforce multi-factor authentication (MFA) support  
- Provide APIs for internal user management (CRUD)  
- Ensure compliance with security standards and access control policies  

---

## Data Models

### InternalUser

| Field          | Type       | Description                       |
|----------------|------------|---------------------------------|
| `id`           | UUID       | Unique identifier for internal user |
| `username`     | String     | Login username                   |
| `email`        | String     | Email address                   |
| `password_hash`| String     | Hashed password (bcrypt or similar) |
| `role_id`      | UUID       | Assigned role identifier         |
| `status`       | Enum       | User status (Active, Suspended, Disabled) |
| `created_at`   | DateTime   | Account creation timestamp       |
| `updated_at`   | DateTime   | Last updated timestamp           |
| `last_login`   | DateTime?  | Last login timestamp (nullable)  |

---

## Role Model (for internal users)

| Field       | Type    | Description               |
|-------------|---------|---------------------------|
| `id`        | UUID    | Role identifier           |
| `name`      | String  | Role name (e.g., Admin)   |
| `permissions`| String[]| List of assigned permissions |

---

## API Endpoints (gRPC)

### InternalUserService

| Method             | Request Type            | Response Type           | Description                      |
|--------------------|-------------------------|------------------------|----------------------------------|
| `CreateInternalUser`| `CreateInternalUserReq` | `InternalUserResponse`  | Creates a new internal user      |
| `GetInternalUser`   | `GetInternalUserReq`    | `InternalUserResponse`  | Retrieves internal user by ID    |
| `UpdateInternalUser`| `UpdateInternalUserReq` | `InternalUserResponse`  | Updates internal user details    |
| `DeleteInternalUser`| `DeleteInternalUserReq` | `Empty`                | Deletes an internal user account |
| `ListInternalUsers` | `ListInternalUsersReq`  | `ListInternalUsersRes`  | Lists all internal users         |

---

## Password & Security Policies

- Passwords stored with strong hashing algorithms (bcrypt, Argon2)  
- Enforce password complexity and expiration policies  
- Support password reset via secure token flows  
- Support MFA enforcement and management per user  
- Track login attempts and lock accounts on repeated failures  

---

## Audit Logging

- Record creation, update, and deletion events for internal users  
- Log successful and failed login attempts with timestamps and IP addresses  
- Provide APIs for audit log retrieval for compliance and monitoring  

---

## Class Diagram Reference

![Internal Users Management Class Diagram](../../diagrams/previews/class-diagram.png)

---

## Summary

The Internal Users Management component governs the secure management of privileged system users with administrative capabilities. It enforces role-based access control, password and security policies, and audit logging to ensure accountability and secure administration.


