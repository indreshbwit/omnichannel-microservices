# ⚙️ gRPC Services in Core Service

This document outlines all gRPC services exposed by the Core Service, intended for internal service-to-service communication across the platform.

---

## 🔧 Services Overview

1. **TenantService**
   - `GetTenantById(tenant_id)`
   - `ListTenants()`

2. **UserService**
   - `GetUserById(user_id)`
   - `ListUsersByTenant(tenant_id)`

3. **RBACService**
   - `GetRolesForUser(user_id)`
   - `HasPermission(user_id, permission_name)`

---

## 📂 Folder Structure

All `.proto` files are located in the `proto/` folder and are compiled into language-specific stubs for use in services.

---

## 🔐 Security

- Metadata-based JWT authentication
- Interceptor chain includes:
  - **AuthInterceptor**
  - **TracingInterceptor**
  - **TenantContextInterceptor**

---

## 🧪 Testing

You can test gRPC services using tools like:
- Postman (gRPC tab)

---

## 📝 Notes

- All services use protocol buffers (`proto3`) for serialization.
- gRPC connections require mTLS in production environments.
- Versioned service naming pattern: `service UserServiceV1 {}`


