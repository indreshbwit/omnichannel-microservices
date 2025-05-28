# OAuth Bindings Component

## Overview

The OAuth Bindings component manages the linkage between internal users and external OAuth platforms (e.g., Google, Microsoft). It allows users to authenticate via OAuth and enables the system to act on behalf of users when accessing third-party services.

---

## Responsibilities

- Store and manage OAuth tokens (access, refresh) securely per user.
- Handle token expiry and refresh workflows.
- Link OAuth accounts to internal user identities.
- Support multiple platforms and multiple OAuth accounts per user.
- Provide APIs for creating, updating, retrieving, and deleting OAuth bindings.
- Integrate with Credential Management to securely store client secrets.
- Emit relevant Kafka events on binding creation, update, or revocation.

---

## Data Model

| Field          | Type      | Description                           |
|----------------|-----------|-------------------------------------|
| id             | UUID      | Unique OAuth Binding identifier      |
| user_id        | UUID      | Internal user ID linked to binding   |
| platform_id    | UUID      | Platform credential ID (e.g., Google)|
| access_token   | String    | OAuth access token                   |
| refresh_token  | String    | OAuth refresh token                  |
| expires_at     | DateTime  | Expiration timestamp of access token |
| scopes         | List      | List of OAuth scopes granted         |
| created_at     | DateTime  | Record creation timestamp            |
| updated_at     | DateTime  | Last update timestamp                |

---

## API Surface

### gRPC Methods

| RPC Method       | Request Message          | Response Message        | Description                         |
|------------------|-------------------------|------------------------|-----------------------------------|
| `CreateOAuthBinding` | `CreateOAuthBindingRequest` | `OAuthBindingResponse` | Create a new OAuth binding for a user |
| `GetOAuthBinding` | `GetOAuthBindingRequest` | `OAuthBindingResponse` | Retrieve OAuth binding by ID       |
| `UpdateOAuthBinding` | `UpdateOAuthBindingRequest` | `OAuthBindingResponse` | Update tokens or scopes            |
| `DeleteOAuthBinding` | `DeleteOAuthBindingRequest` | `DeleteResponse`       | Remove an OAuth binding            |
| `RefreshToken`    | `RefreshTokenRequest`     | `RefreshTokenResponse` | Refresh access token using refresh token |

---

## Token Refresh Workflow

- On each API call requiring OAuth, check if the access token is expired.
- If expired, use stored refresh token to obtain a new access token from the OAuth provider.
- Update the `access_token`, `expires_at`, and optionally `refresh_token` in the binding.
- Emit a `OAuthBindingUpdated` Kafka event with updated token info.
- If refresh fails (e.g., revoked), mark binding as invalid and notify user/admin.

---

## Security Considerations

- All sensitive tokens (access, refresh) must be encrypted at rest using strong encryption.
- Token refresh operations must handle rate limits and error retries gracefully.
- Limit OAuth scopes to least privilege principle.
- Audit all OAuth binding operations for traceability.

---

## Integration Points

- **Credential Management:** Uses platform client_id and secrets for OAuth flows.
- **User Service:** OAuth bindings are associated with internal users.
- **Kafka Event Bus:** Emits events for changes in OAuth bindings.
- **Authentication Service:** Validates OAuth tokens during user authentication.

---

## References

- OAuth 2.0 Specification: https://oauth.net/2/
- Platform OAuth APIs (Google, Microsoft) documentation
- `/models/oauth-binding.md` for data model details

---

*End of OAuth Bindings Component Doc*

