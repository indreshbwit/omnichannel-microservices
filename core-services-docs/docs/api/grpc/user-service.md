# User Service - gRPC API Documentation

## Overview

The User Service manages user accounts, roles, and permissions within the Core Service. It exposes CRUD operations over gRPC and supports querying users by tenant and role.

---

## Service Definition (Proto Snippet)

```proto
syntax = "proto3";

package user;

service UserService {
  rpc GetUser(GetUserRequest) returns (UserResponse);
  rpc CreateUser(CreateUserRequest) returns (UserResponse);
  rpc UpdateUser(UpdateUserRequest) returns (UserResponse);
  rpc DeleteUser(DeleteUserRequest) returns (DeleteUserResponse);
  rpc ListUsers(ListUsersRequest) returns (ListUsersResponse);
}

message GetUserRequest {
  string user_id = 1;
}

message CreateUserRequest {
  string tenant_id = 1;
  string email = 2;
  string name = 3;
  string role_id = 4;
}

message UpdateUserRequest {
  string user_id = 1;
  string email = 2;
  string name = 3;
  string role_id = 4;
}

message DeleteUserRequest {
  string user_id = 1;
}

message DeleteUserResponse {
  bool success = 1;
}

message ListUsersRequest {
  string tenant_id = 1;
  string role_id = 2; // optional filter
}

message ListUsersResponse {
  repeated User users = 1;
}

message User {
  string id = 1;
  string tenant_id = 2;
  string email = 3;
  string name = 4;
  string role_id = 5;
}

