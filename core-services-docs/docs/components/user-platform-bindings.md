# User-Platform Bindings (OAuth Accounts) Component

## Overview

This component manages the association between internal users and their external OAuth accounts on various service platforms (e.g., Microsoft, Google). It tracks OAuth tokens, refresh tokens, and metadata necessary for ongoing OAuth-based authentication and API access on behalf of users.

---

## Responsibilities

- Bind internal users to external OAuth platform accounts  
- Store and securely manage OAuth access and refresh tokens per user-platform binding  
- Track token expiry and manage token refresh lifecycle  
- Support multiple OAuth accounts per user across different platforms  
- Provide APIs to create, retrieve, update, and delete user-platform bindings  
- Enforce token encryption at rest for security  
- Integrate with authentication and authorization flows for seamless user login  

---

## Data Models

### OAuthBinding

| Field          | Type      | Description                         |
|----------------|-----------|-----------------------------------|
| `id`           | UUID      | Unique identifier for the binding  |
| `user_id`      | UUID      | Internal user ID                   |
| `platform_id`  | UUID      | PlatformCredential ID (foreign key)|
| `access_token` | String    | OAuth access token (encrypted)    |
| `refresh_token`| String    | OAuth refresh token (encrypted)   |
| `expires_at`   | DateTime  | Access token expiration timestamp |
| `scopes`       | String[]  | OAuth scopes granted               |
| `created_at`   | DateTime  | Binding creation timestamp        |
| `updated_at`   | DateTime  | Last updated timestamp            |

---

## Token Management Lifecycle

1. **Creation:** Created during OAuth login/consent flow after successful token exchange.  
2. **Storage:** Tokens are encrypted before being stored to ensure confidentiality.  
3. **Usage:** Access tokens used for API calls on behalf of the user.  
4. **Refresh:** Refresh tokens used to renew access tokens before expiration.  
5. **Revocation:** Tokens are revoked upon logout, credential revocation, or security policy enforcement.  

---

## API Endpoints (gRPC)

### OAuthBindingService

| Method                 | Request Type                | Response Type                | Description                               |
|------------------------|-----------------------------|------------------------------|-------------------------------------------|
| `CreateBinding`        | `CreateBindingRequest`      | `OAuthBindingResponse`       | Create a new user-platform binding        |
| `GetBinding`           | `GetBindingRequest`         | `OAuthBindingResponse`       | Retrieve binding by ID                     |
| `UpdateBinding`        | `UpdateBindingRequest`      | `OAuthBindingResponse`       | Update tokens or scopes                    |
| `DeleteBinding`        | `DeleteBindingRequest`      | `Empty`                      | Delete a user-platform binding             |
| `ListBindingsByUser`   | `ListBindingsByUserRequest` | `ListOAuthBindingsResponse`  | List all bindings for a given user         |

---

## Security Considerations

- Encrypt access and refresh tokens at rest using strong encryption algorithms (e.g., AES-256).  
- Limit access to token management APIs strictly to authorized services.  
- Regularly audit token usage and binding changes.  
- Handle token expiration proactively and securely refresh tokens.  
- Ensure tokens are never exposed in logs or error messages.  

---

## Class Diagram Reference

![User-Platform Bindings Class Diagram](../../diagrams/previews/class-diagram.png)

---

## Summary

This component handles the binding between internal users and their external OAuth platform accounts. It securely stores and manages OAuth tokens required for authentication and API interactions on behalf of the user, supporting multiple platforms and accounts per user.


