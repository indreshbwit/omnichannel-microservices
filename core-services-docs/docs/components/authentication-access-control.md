# Authentication & Access Control Component (RBAC + OAuth)

## Overview

This component manages authentication (verifying user identity) and authorization (access control) across the Core Service. It supports multiple authentication mechanisms including password-based login, OAuth2 social logins, and token-based sessions (JWT). Access control is implemented using Role-Based Access Control (RBAC) combined with OAuth scopes.

---

## Responsibilities

- Authenticate users via:
  - Password + email (with secure hashing and salting)
  - OAuth2 providers (Microsoft, Google, etc.)
- Issue and validate JWT tokens for session management  
- Support OAuth2 authorization code and refresh token flows  
- Implement RBAC to control user permissions and access rights  
- Manage OAuth client credentials securely per tenant  
- Provide APIs for login, logout, token refresh, and authorization checks  
- Enforce permission checks in service APIs using RBAC policies  
- Support multi-factor authentication (MFA) integration  

---

## Data Models

### UserCredential

| Field          | Type    | Description                         |
|----------------|---------|-----------------------------------|
| `user_id`      | UUID    | Link to User entity                |
| `password_hash`| String  | Hashed and salted password         |
| `salt`         | String  | Salt used for hashing              |
| `created_at`   | DateTime| Credential creation timestamp      |
| `updated_at`   | DateTime| Last password update timestamp     |

### OAuthCredential

| Field           | Type    | Description                        |
|-----------------|---------|----------------------------------|
| `id`            | UUID    | Credential ID                    |
| `tenant_id`     | UUID    | Tenant owning this credential    |
| `client_id`     | String  | OAuth client ID                  |
| `client_secret` | String  | OAuth client secret (encrypted) |
| `provider`      | String  | OAuth provider name (e.g., MS, Google) |
| `redirect_uris` | String[]| Allowed redirect URIs            |

### AccessToken (JWT)

| Field        | Type    | Description                     |
|--------------|---------|---------------------------------|
| `token`      | String  | JWT access token                |
| `expires_at` | DateTime| Expiration timestamp            |
| `scopes`     | String[]| OAuth scopes/permissions         |

---

## Authentication Flow

1. **Password Authentication:**  
   User submits email and password → verify against stored hash → issue JWT if valid.

2. **OAuth2 Authentication:**  
   Redirect user to OAuth provider → get authorization code → exchange for access and refresh tokens → create or update user session.

3. **Token Refresh:**  
   Use refresh token to obtain new access token without re-authenticating.

4. **Multi-Factor Authentication (Optional):**  
   Enforce second-factor verification (e.g., TOTP) for sensitive actions.

---

## Authorization (RBAC)

- Users assigned one or more **Roles**.  
- Each Role contains multiple **Permissions** (action + resource).  
- API endpoints check user permissions based on JWT claims and RBAC rules.  
- Example roles: `Admin`, `TenantUser`, `Viewer`, `Support`.  
- Permissions example: `user:create`, `user:read`, `tenant:update`.

---

## API Endpoints (gRPC)

### AuthService

| Method               | Request Type            | Response Type             | Description                         |
|----------------------|------------------------|---------------------------|-----------------------------------|
| `Authenticate`       | `AuthRequest`          | `AuthResponse`            | Authenticate user, returns JWT    |
| `RefreshToken`       | `RefreshTokenRequest`  | `AuthResponse`            | Refresh JWT token                 |
| `RevokeToken`        | `RevokeTokenRequest`   | `Empty`                   | Invalidate access/refresh tokens  |
| `GetUserPermissions` | `UserPermissionRequest`| `UserPermissionResponse`  | Get user permissions via RBAC     |
| `ValidateToken`      | `ValidateTokenRequest` | `ValidateTokenResponse`   | Validate access token             |

---

## Security Considerations

- Passwords stored securely with bcrypt/Argon2 and salt.  
- JWT signed with strong keys; use short expiration times.  
- OAuth credentials encrypted at rest.  
- Validate all OAuth redirect URIs to prevent phishing.  
- RBAC policies enforced consistently across services.  
- Logs and audit trails for all auth-related operations.  

---

## Class Diagram Reference

![Authentication & Access Control Class Diagram](../../diagrams/previews/class-diagram.png)

---

## Summary

This component ensures secure and flexible user authentication and authorization across the Core Service. It supports modern OAuth2 standards alongside traditional password methods, backed by strong RBAC enforcement to manage permissions effectively.


