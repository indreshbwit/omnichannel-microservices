# API Endpoints

## User Management

- `POST /users`
- `GET /users/{id}`
- `PUT /users/{id}`
- `DELETE /users/{id}`

## Tenant Management

- `POST /tenants`
- `GET /tenants/{id}`

## OAuth

- `POST /oauth/initiate`
- `GET /oauth/callback`

## gRPC Services

- `UserService.GetUser`
- `AuthService.ValidateToken`
