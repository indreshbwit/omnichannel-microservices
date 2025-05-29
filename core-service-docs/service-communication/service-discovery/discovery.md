### 📄 `discovery.md`

```markdown
# Service Discovery

## Overview

Service discovery enables services to locate one another at runtime. This is crucial in a dynamic environment where IPs may change.

## Discovery Methods

| Method       | Description                               |
|--------------|-------------------------------------------|
| DNS-based    | Native in Kubernetes using internal DNS   |
| Registry-based | Via systems like Consul or Eureka        |

## DNS-Based in Kubernetes

- Each service is assigned a DNS name automatically.
- Example: `mail-service.default.svc.cluster.local`

Services can call each other using:

```bash
curl http://mail-service.default.svc.cluster.local:8000/api/emails
Load Balancing
Kubernetes Service objects distribute traffic across pods using round-robin.

External load balancers (e.g., Istio, Envoy) can provide smart routing.

Resilience Strategies
Circuit breakers via Istio or Hystrix

Retries with exponential backoff

Timeout and fallback mechanisms

Service Mesh Integration (Optional)
If using a service mesh like Istio:

Automatic mTLS between services

Traffic routing rules (canary, weighted, etc.)

Observability with distributed tracing



