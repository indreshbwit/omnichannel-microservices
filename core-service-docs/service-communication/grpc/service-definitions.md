Markdown

# gRPC Service Definitions

gRPC service definitions are written using **Protocol Buffers** (`.proto` files). They define the structure of the RPC services, including the service methods and the message types they use.

---

## Basic Structure

A typical `.proto` file includes:

* **Syntax version** declaration
* **Package** name for namespace
* **Message** definitions for request and response payloads
* **Service** definition listing RPC methods with their request and response message types

### Example

```protobuf
syntax = "proto3";

package core;

message GetUserRequest {
  string user_id = 1;
}

message GetUserResponse {
  string user_id = 1;
  string username = 2;
  string email = 3;
}

service UserService {
  rpc GetUser(GetUserRequest) returns (GetUserResponse);
}
Naming Conventions
Use PascalCase for message and service names.
Use camelCase for message fields.
Service method names should be verbs describing the operation (e.g., GetUser, CreateTenant).
Versioning
Include a version number in the package or service name (e.g., core.v1) to enable backward compatibility.
Avoid breaking changes; add new RPC methods or message fields instead.
Best Practices
Use unary RPC calls for simple request-response interactions.
Use streaming RPCs (client-side, server-side, or bidirectional) for large or continuous data.
Define clear and concise messages; avoid unnecessary nesting.
Keep services focused and cohesive.
Tools
Use protoc compiler with appropriate plugins to generate client and server code in supported languages.
Use buf for linting and managing proto files to enforce style and consistency.
