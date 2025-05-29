# Service Registration

## Registration Mechanism

Each microservice registers itself with the service discovery system on startup. This is typically done via:

- Kubernetes Service and Endpoints
- Consul, Eureka, or etcd (optional if not using Kubernetes)

## Kubernetes-Based Registration

Kubernetes handles service registration automatically using `Service` and `Endpoints` resources.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mail-service
spec:
  selector:
    app: mail-service
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
Service Labels
Use labels and annotations to organize services by tenant, version, or feature flag:

yaml

metadata:
  labels:
    tenant: tenant-a
    version: v1
    team: messaging
Health Probes
Each service should expose /health or /readyz endpoints for liveness and readiness checks.

yaml

livenessProbe:
  httpGet:
    path: /health
    port: 8000
  initialDelaySeconds: 10
  periodSeconds: 10
Dynamic Updates
Services re-register automatically on restarts.

DNS-based service discovery (e.g., mail-service.namespace.svc.cluster.local) is supported natively.
