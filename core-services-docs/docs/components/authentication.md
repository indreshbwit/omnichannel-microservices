# Authentication Component

## Overview

The Authentication Component is responsible for securely verifying user identities and issuing tokens to enable authorized access to the system. It supports multiple authentication methods including password-based login, OAuth2 integration, token refresh, and multi-factor authentication (MFA). This component ensures secure credential validation and token lifecycle management.

---

## Responsibilities

- User login authentication (password + OAuth2)  
- JWT token issuance and validation  
- Refresh token management and rotation  
- Support for multi-factor authentication (MFA)  
- Password reset and recovery workflows  
- Integration with external identity providers (via OAuth2)  
- Token revocation and session management  
- Security logging of authentication attempts  

---

## Data Models

### AuthRequest

| Field          | Type   | Description                       |
|----------------|--------|---------------------------------|
| `username`     | string | Username or email for login      |
| `password`     | string | Password (plaintext, sent securely) |
| `oauth_provider`| string | Optional OAuth provider name (e.g., Google, Microsoft) |
| `oauth_token`  | string | Optional OAuth token for verification |

### AuthResponse

| Field          | Type   | Description                       |
|----------------|--------|---------------------------------|
| `access_token` | string | JWT access token                 |
| `refresh_token`| string | Token used to get new access tokens |
| `expires_in`   | int    | Access token expiration time (seconds) |
| `token_type`   | string | Token type (usually "Bearer")    |

### RefreshTokenRequest

| Field          | Type   | Description                       |
|----------------|--------|---------------------------------|
| `refresh_token`| string | Valid refresh token              |

### RefreshTokenResponse

| Field          | Type   | Description                       |
|----------------|--------|---------------------------------|
| `access_token` | string | New JWT access token             |
| `expires_in`   | int    | Access token expiration time (seconds) |

---

## API Endpoints (gRPC)

### AuthService

| Method           | Request Type          | Response Type          | Description                         |
|------------------|-----------------------|------------------------|-------------------------------------|
| `Authenticate`   | `AuthRequest`         | `AuthResponse`         | Authenticate user (password or OAuth) |
| `RefreshToken`   | `RefreshTokenRequest` | `RefreshTokenResponse` | Refresh access token using refresh token |
| `RevokeToken`    | `RevokeTokenRequest`  | `Empty`                | Revoke a refresh token or logout    |

---

## Token Handling

- Use JWT with asymmetric encryption (RS256) for token signing  
- Access tokens are short-lived (e.g., 15 minutes)  
- Refresh tokens are longer-lived and securely stored  
- Token revocation list maintained for security  
- Token claims include user ID, roles, tenant ID, and scopes  

---

## Multi-Factor Authentication (MFA)

- Support Time-based One-Time Password (TOTP) via authenticator apps  
- Backup codes generation and management  
- Enforcement of MFA on sensitive operations or login from new devices  
- APIs for enabling/disabling MFA per user  

---

## Security Considerations

- All authentication endpoints enforce rate limiting  
- Passwords never stored or transmitted in plaintext  
- Secure HTTPS mandatory for all communication  
- Logging of failed and successful authentication attempts with metadata  

---

## Class Diagram Reference

![Authentication Component Class Diagram](../../diagrams/previews/class-diagram.png)

---

## Summary

The Authentication Component provides a secure, flexible framework to authenticate users via passwords or OAuth, issue and manage JWT tokens, and enforce multi-factor authentication. It is critical to maintaining system security and user trust.

## Integration Points

- Works closely with User component for identity lookup.
- OAuth Bindings for external account linkage.
- Access Control component consumes JWT claims for authorization.
- API Gateway validates JWTs in requests.

---

## Example AuthRequest / AuthResponse (gRPC)

```proto
message AuthRequest {
  string username = 1;
  string password = 2;
  string oauth_token = 3; // optional OAuth token
}

message AuthResponse {
  string jwt_token = 1;
  int64 expires_in = 2;
}


