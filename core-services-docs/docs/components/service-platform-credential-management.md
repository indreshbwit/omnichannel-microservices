# Service Platform & Credential Management Component

## Overview

This component manages external service platforms and their credentials for OAuth integration and API access. It allows tenants to connect to external services (e.g., Microsoft, Google) securely by storing platform credentials and handling user-platform bindings.

---

## Responsibilities

- Register and manage supported external platforms (e.g., Microsoft Graph, Google APIs)  
- Store and secure platform credentials (client ID, secret) per tenant  
- Manage OAuth client configuration and secrets lifecycle  
- Maintain user bindings to external OAuth accounts  
- Support token refresh and revocation flows  
- Enable secure access for internal services to external APIs  

---

## Data Models

### PlatformCredential

| Field       | Type    | Description                      |
|-------------|---------|----------------------------------|
| `id`        | UUID    | Unique identifier                 |
| `tenant_id` | UUID    | Tenant owning the credential      |
| `platform_name` | String | Name of the external platform (e.g., "Microsoft") |
| `client_id` | String  | OAuth Client ID                   |
| `client_secret` | String | OAuth Client Secret (encrypted)  |
| `redirect_uris` | String[] | Allowed OAuth redirect URIs      |
| `created_at` | DateTime | Timestamp of creation             |
| `updated_at` | DateTime | Timestamp of last update          |

### OAuthBinding

| Field          | Type   | Description                        |
|----------------|--------|----------------------------------|
| `id`           | UUID   | Unique identifier                 |
| `user_id`      | UUID   | Internal User ID                  |
| `platform_id`  | UUID   | PlatformCredential ID             |
| `external_user_id` | String | External platform user ID       |
| `access_token` | String | OAuth access token (encrypted)    |
| `refresh_token`| String | OAuth refresh token (encrypted)   |
| `expires_at`   | DateTime | Access token expiration time     |

---

## Relationships

- PlatformCredential belongs to a Tenant  
- OAuthBinding links internal User with PlatformCredential and external user info  
- Many OAuthBindings per User for multiple external platforms  

---

## Class Diagram Reference

![Service Platform & Credential Management Class Diagram](../../diagrams/previews/class-diagram.png)

---

## API Endpoints

### gRPC - PlatformCredentialService

| Method                | Request Type                | Response Type            | Description                       |
|-----------------------|-----------------------------|--------------------------|---------------------------------|
| `CreatePlatformCredential` | `CreatePlatformCredentialRequest` | `PlatformCredentialResponse` | Add a new platform credential    |
| `GetPlatformCredential`    | `GetPlatformCredentialRequest`    | `PlatformCredentialResponse` | Get credential details           |
| `UpdatePlatformCredential` | `UpdatePlatformCredentialRequest` | `PlatformCredentialResponse` | Update existing credential       |
| `DeletePlatformCredential` | `DeletePlatformCredentialRequest` | `Empty`                      | Delete credential                |
| `ListPlatformCredentials`  | `ListPlatformCredentialsRequest`  | `ListPlatformCredentialsResponse` | List all credentials for tenant |

### gRPC - OAuthBindingService

| Method              | Request Type           | Response Type           | Description                     |
|---------------------|------------------------|-------------------------|--------------------------------|
| `CreateOAuthBinding` | `CreateOAuthBindingRequest` | `OAuthBindingResponse`  | Link user to external account   |
| `GetOAuthBinding`    | `GetOAuthBindingRequest`    | `OAuthBindingResponse`  | Get binding details             |
| `UpdateOAuthBinding` | `UpdateOAuthBindingRequest` | `OAuthBindingResponse`  | Update token info               |
| `DeleteOAuthBinding` | `DeleteOAuthBindingRequest` | `Empty`                 | Remove binding                  |
| `ListOAuthBindings`  | `ListOAuthBindingsRequest`  | `ListOAuthBindingsResponse` | List all bindings for user      |

---

## Security

- Credentials stored encrypted at rest  
- Restricted access to credential management APIs (tenant admins only)  
- OAuth tokens encrypted and rotated regularly  
- Strict validation of redirect URIs  

---

## Summary

This component is critical for secure integration with external platforms, managing OAuth credentials per tenant and mapping internal users to external accounts for seamless authentication and API access.


