# API Endpoints

This document details the Core Service gRPC APIs and the REST/OpenAPI gateway endpoints.

---

## UserService (gRPC)

| RPC Method        | Request Message          | Response Message        | Description                           |
|-------------------|--------------------------|------------------------|-------------------------------------|
| `GetUser`         | `GetUserRequest`          | `UserResponse`          | Retrieves user information by ID    |
| `CreateUser`      | `CreateUserRequest`       | `UserResponse`          | Creates a new user                  |
| `UpdateUser`      | `UpdateUserRequest`       | `UserResponse`          | Updates an existing user            |
| `DeleteUser`      | `DeleteUserRequest`       | `DeleteResponse`        | Deletes a user by ID                |

---

### Example: GetUser

```proto
message GetUserRequest {
  string user_id = 1;
}

message UserResponse {
  string id = 1;
  string tenant_id = 2;
  string email = 3;
  string name = 4;
  string role_id = 5;
}

