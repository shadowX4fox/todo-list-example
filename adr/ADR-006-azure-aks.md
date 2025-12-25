# ADR-006: Azure Kubernetes Service (AKS)

**Status**: Accepted
**Date**: 2025-12-23
**Authors**: Architecture Team
**Related**: [ADR-001](ADR-001-3tier-architecture.md), [ADR-002](ADR-002-spring-boot-backend.md)

---

## Context

### Problem Statement

We need to select a deployment platform for the 3-Tier To-Do List Application that:
- Supports containerized deployments (Docker)
- Provides auto-scaling for Application tier (Spring Boot)
- Enables high availability (99.9% uptime target)
- Integrates with Azure services (PostgreSQL, Redis, Blob Storage)
- Supports horizontal scaling from 2 to 10+ pods

**Constraint**: Must deploy to **Azure cloud** as specified in architectural requirements.

### Requirements

**Functional Requirements:**
- Deploy Angular (Presentation) and Spring Boot (Application) as containers
- Auto-scaling based on CPU/memory utilization
- Health checks for automatic pod restart
- Rolling updates with zero downtime

**Non-Functional Requirements:**
- **Availability**: 99.9% uptime (8.76 hours/year downtime)
- **Scalability**: Scale from 2 to 10+ Application tier pods
- **Performance**: <100ms orchestration overhead
- **Cost**: Optimize for cost-effectiveness

**Constraints:**
- **Cloud Provider**: Azure (mandated by architecture)
- **Team Skills**: Team has Docker experience, limited Kubernetes knowledge

---

## Decision

### Summary

We will use **Azure Kubernetes Service (AKS)** as the deployment platform for containerized workloads (Angular, Spring Boot).

### Detailed Decision

**Infrastructure:**
- **Platform**: Azure Kubernetes Service (AKS)
- **Node Pool**: 2-4 nodes (Standard_D2s_v3: 2 vCPU, 8GB RAM each)
- **Workloads**:
  - Spring Boot Application tier: 2-10 pods (HPA based on CPU >70%)
  - Angular Presentation tier: Served via Azure CDN (not Kubernetes)

**Deployment Strategy:**
- **Container Registry**: Azure Container Registry (ACR)
- **Deployment Tool**: Helm charts for templated deployments
- **Update Strategy**: Rolling update (maxUnavailable: 1, maxSurge: 1)

**High Availability:**
- Kubernetes auto-restart failed pods (liveness probes)
- Horizontal Pod Autoscaler (HPA) scales based on CPU >70%
- Multi-zone node distribution (future enhancement)

---

## Rationale

### Primary Drivers

**1. Azure Integration**
- **Description**: Native integration with Azure services (PostgreSQL, Redis, Key Vault, Monitor)
- **Impact**: Seamless connectivity, managed service discovery, unified monitoring
- **Evidence**: AKS pods can access Azure services via managed identities, no credential management

**2. Auto-Scaling**
- **Description**: Horizontal Pod Autoscaler (HPA) automatically scales pods based on metrics
- **Impact**: Handle traffic spikes (2 to 10 pods), optimize cost during low traffic
- **Evidence**: HPA can scale up in ~30 seconds based on CPU/memory metrics

**3. Self-Healing**
- **Description**: Kubernetes automatically restarts failed pods
- **Impact**: Improved availability (99.9% uptime), reduced manual intervention
- **Evidence**: Liveness/readiness probes detect failures and trigger pod replacement

**4. Managed Service**
- **Description**: Azure manages Kubernetes control plane (API server, scheduler, controller)
- **Impact**: Reduced operational burden, automatic Kubernetes version upgrades
- **Evidence**: No need to manage etcd, API server, or control plane nodes

### Comparison Summary

| Criteria | AKS (Selected) | Azure App Service | Azure Container Instances (ACI) |
|----------|---------------|-------------------|--------------------------------|
| **Auto-Scaling** | ✅ Excellent (HPA) | ✅ Good | ⚠️ Limited |
| **Container Support** | ✅ Native (Docker/K8s) | ✅ Docker support | ✅ Native |
| **Azure Integration** | ✅ Excellent | ✅ Excellent | ✅ Good |
| **Operational Complexity** | ⚠️ Medium (K8s learning curve) | ✅ Low | ✅ Low |
| **Cost** | ⚠️ Medium ($100-300/month) | ⚠️ Medium | ✅ Low (pay-per-second) |
| **High Availability** | ✅ Excellent (self-healing) | ✅ Good | ⚠️ Manual setup |
| **Flexibility** | ✅ Full K8s features | ⚠️ PaaS limitations | ❌ Limited |

---

## Consequences

### Positive Consequences

1. **Auto-Scaling**
   - HPA scales Application tier from 2 to 10 pods based on CPU >70%
   - Handles traffic spikes automatically, reduces cost during low traffic

2. **Self-Healing**
   - Kubernetes restarts failed pods within 30 seconds
   - Improves availability (99.9% target), reduces downtime

3. **Rolling Updates (Zero Downtime)**
   - Deploy new versions with zero downtime (maxUnavailable: 1, maxSurge: 1)
   - Rollback to previous version in <2 minutes if deployment fails

4. **Azure Integration**
   - Managed identities for secure access to Azure PostgreSQL, Redis, Key Vault
   - Azure Monitor integration for centralized logging and metrics

### Negative Consequences

1. **Kubernetes Learning Curve**
   - Team needs to learn Kubernetes concepts (pods, services, deployments, HPA)
   - **Mitigation**: Provide Kubernetes training, use Helm charts to abstract complexity
   - **Severity**: Medium (acceptable for long-term benefits)

2. **Operational Complexity**
   - Managing Kubernetes manifests, Helm charts, ConfigMaps, Secrets
   - **Mitigation**: Use Helm for templated deployments, automate via CI/CD pipeline
   - **Severity**: Medium

3. **Cost**
   - AKS cluster costs ~$100-300/month (2-4 nodes)
   - **Mitigation**: Right-size node pool, use HPA to scale down during low traffic
   - **Severity**: Low (acceptable for production-grade platform)

### Trade-offs

- **Operational Complexity vs Flexibility**: Accept Kubernetes learning curve for full control and flexibility
- **Cost vs Features**: Accept higher cost for auto-scaling, self-healing, and zero-downtime deployments

---

## Alternatives Considered

### Alternative 1: Azure App Service (PaaS)

**Description:**
Deploy Spring Boot as Azure App Service (PaaS) instead of Kubernetes.

**Why Considered:**
- Lower operational complexity (no Kubernetes)
- Built-in auto-scaling
- Azure integration
- Lower learning curve

**Why Rejected:**
- **Architectural Requirement**: Spec mandates Azure AKS deployment
- **Less Flexibility**: PaaS limitations (cannot run custom sidecars, limited networking)
- **Containerization Benefits**: Lose Docker/Kubernetes portability

**Use Case:**
App Service would be appropriate for simpler applications without Kubernetes requirement.

---

### Alternative 2: Azure Container Instances (ACI)

**Description:**
Deploy Spring Boot as standalone Azure Container Instances.

**Why Considered:**
- Simplest container deployment
- Pay-per-second billing (lowest cost)
- No cluster management

**Why Rejected:**
- **No Auto-Scaling**: Manual scaling, no Horizontal Pod Autoscaler
- **No Self-Healing**: No automatic pod restart on failure
- **Limited HA**: No built-in load balancing or redundancy
- **Architectural Requirement**: Spec mandates Azure AKS

**Use Case:**
ACI would be appropriate for batch jobs, short-lived workloads, or single-container apps.

---

### Alternative 3: Self-Hosted Kubernetes (Azure VMs)

**Description:**
Install Kubernetes on Azure Virtual Machines (self-managed cluster).

**Why Considered:**
- Full control over Kubernetes version and configuration
- Potentially lower cost (no AKS management fee)

**Why Rejected:**
- **High Operational Burden**: Manage control plane, etcd, networking, upgrades
- **Team Bandwidth**: Team lacks Kubernetes expertise to manage cluster
- **Reliability Risk**: Self-managed cluster increases failure risk
- **Cost**: Labor costs outweigh AKS management fee

**Use Case:**
Self-hosted Kubernetes would be appropriate for organizations with dedicated Kubernetes platform teams.

---

## Implementation Plan

### Phase 1: AKS Cluster Provisioning (Week 1)
- Provision AKS cluster (2 nodes, Standard_D2s_v3)
- Configure Azure Container Registry (ACR) integration
- Set up kubectl access and RBAC

### Phase 2: Deploy Application (Week 1-2)
- Create Dockerfile for Spring Boot Application
- Build and push Docker images to ACR
- Create Helm charts for deployment manifests
- Deploy to AKS: `helm install todolist ./helm/todolist`

### Phase 3: Configure Auto-Scaling (Week 2)
- Create Horizontal Pod Autoscaler (HPA) for Application tier
- Configure resource requests/limits (1 vCPU, 1GB RAM)
- Test scaling: Simulate load, verify HPA scales to 10 pods

### Phase 4: Monitoring & Health Checks (Week 2)
- Configure liveness/readiness probes: `/actuator/health`
- Integrate with Azure Monitor for metrics and logs
- Create Grafana dashboards for pod CPU/memory, request rate

---

## Success Metrics

### Quantitative Metrics

- **Availability**: Baseline N/A → Target 99.9% uptime
- **Auto-Scaling Speed**: Baseline manual → Target <60s to scale from 2 to 10 pods
- **Deployment Downtime**: Baseline ~5 minutes → Target 0 seconds (rolling update)
- **Pod Restart Time**: Baseline N/A → Target <30s (Kubernetes auto-restart)

### Qualitative Metrics

- **Developer Experience**: Helm charts simplify deployments, `helm upgrade` for updates
- **Operational Burden**: Azure manages control plane, team focuses on application

---

## References

### Documentation
- [Azure Kubernetes Service (AKS) Documentation](https://learn.microsoft.com/en-us/azure/aks/)
- [Kubernetes Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Helm Documentation](https://helm.sh/docs/)

### Research & Analysis
- [AKS Best Practices](https://learn.microsoft.com/en-us/azure/aks/best-practices)
- [Spring Boot on Kubernetes Guide](https://spring.io/guides/gs/spring-boot-kubernetes/)

---

**Last Updated**: 2025-12-23
**Status**: ✅ Accepted