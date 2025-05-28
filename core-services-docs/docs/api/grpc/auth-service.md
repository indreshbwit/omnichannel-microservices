# Auth Service - gRPC API Documentation

## Overview

The Auth Service provides authentication capabilities via gRPC. It supports username/password and OAuth-based authentication methods, issuing JWT tokens for authorized access.

---

## Service Definition (Proto Snippet)

```proto
syntax = "proto3";

package auth;

service AuthService {
  // Authenticate user with username/password or OAuth token
  rpc Authenticate (AuthRequest) returns (AuthResponse);
}

message AuthRequest {
  string username = 1;        // Username for password auth
  string password = 2;        // Password for password auth
  string oauth_token = 3;     // OAuth token (optional)
}

message AuthResponse {
  string jwt_token = 1;       // JWT token issued on success
  int64 expires_in = 2;       // Token expiration in seconds
}

