Markdown

# gRPC Interceptors

gRPC interceptors provide a way to implement **reusable middleware logic** that can be executed before and/or after RPC handlers are invoked. They are analogous to HTTP middleware but tailored for RPC calls, enabling cross-cutting concerns like logging, authentication, metrics, and error handling.

---

## Types of Interceptors

### 1. Unary Interceptors

* Intercept single request-response RPC calls.
* Wrap around the handler to execute custom logic before or after the handler.
* Used for unary RPC methods.

### 2. Streaming Interceptors

* Intercept streaming RPC calls (client streaming, server streaming, bidirectional).
* Enable middleware logic around streams of messages.
* Can modify or observe messages as they pass through.

---

## Use Cases for Interceptors

* Authentication and authorization
* Request logging and tracing
* Metrics collection and monitoring
* Retry policies and circuit breaking
* Error handling and transformation
* Rate limiting

---

## Implementation

### Example (Unary Interceptor in Python)

```python
import grpc

def unary_interceptor(func):
    def wrapper(request, context):
        # Pre-processing logic (e.g., authentication)
        metadata = dict(context.invocation_metadata())
        token = metadata.get('authorization')
        if not token or token != 'expected-token':
            context.abort(grpc.StatusCode.UNAUTHENTICATED, "Invalid token")

        # Call the actual RPC handler
        response = func(request, context)

        # Post-processing logic (e.g., logging)
        print(f"Handled request: {request}")
        return response
    return wrapper

class MyServiceServicer(user_pb2_grpc.UserServiceServicer):
    
    @unary_interceptor
    def GetUser(self, request, context):
        # Implementation of GetUser RPC
        return user_pb2.UserResponse(username="alice", email="alice@example.com")

Registering Interceptors
Interceptors are registered when creating the gRPC server instance. Multiple interceptors can be chained.


Python

server = grpc.server(futures.ThreadPoolExecutor(max_workers=10),
                     interceptors=[unary_interceptor_instance])

Similar concept applies on the client side. Used for logging, retry, authentication metadata injection. Supported in many gRPC client libraries.
