[Architecture](../ARCHITECTURE.md) > Integration Points

# 7. Integration Points

This section documents external systems, APIs, and services that the To-Do List application integrates with.

### 7.1 Azure Kubernetes Service (AKS)

**Type**: Infrastructure / Container Orchestration
**Direction**: Application hosted on AKS
**Protocol**: Kubernetes API (kubectl, Helm)

**Purpose**:
Deploy and orchestrate containerized Application tier (Spring Boot) and Presentation tier (Angular) with auto-scaling and self-healing capabilities.

**Integration Details**:
- **Deployment**: Docker containers deployed via Kubernetes Deployment manifests
- **Scaling**: Horizontal Pod Autoscaler (HPA) based on CPU utilization (target 70%)
- **Service Exposure**: Kubernetes LoadBalancer service for external access
- **Health Checks**: Liveness probe (`/actuator/health`), Readiness probe (`/actuator/health/readiness`)

**Configuration**:
- `replicas`: Minimum 2 pods for Application tier
- `resources.requests`: 1 vCPU, 1GB RAM per pod
- `resources.limits`: 2 vCPU, 2GB RAM per pod

**Error Handling**:
- Pod failure: Kubernetes auto-restarts failed pods
- Node failure: Kubernetes reschedules pods to healthy nodes

**Monitoring**:
- **Metrics**: Pod CPU/memory usage, pod restart count, deployment status
- **Alerts**: Pod crash loop (>3 restarts in 10 minutes)

---

### 7.2 Azure Database for PostgreSQL

**Type**: Managed Database Service
**Direction**: Application tier connects to database
**Protocol**: PostgreSQL wire protocol (TLS-encrypted)

**Purpose**:
Provide managed PostgreSQL database with automated backups, high availability, and monitoring.

**Integration Details**:
- **Connection**: JDBC URL via Spring Boot datasource configuration
- **Authentication**: Username/password (Azure Key Vault for secret management)
- **Connection Pooling**: HikariCP with max 20 connections per Application tier pod
- **TLS**: Enforce TLS 1.2+ for all connections

**Configuration**:
```yaml
spring:
  datasource:
    url: jdbc:postgresql://<server>.postgres.database.azure.com:5432/todolist
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
    hikari:
      maximum-pool-size: 20
      connection-timeout: 5000
```

**Error Handling**:
- Connection failure: Retry 3 times with exponential backoff, then return 503
- Slow query: Log warning if query >50ms, alert if p95 >100ms

**Monitoring**:
- **Metrics**: Connection count, query latency, transaction throughput
- **Alerts**: Connection pool >80% utilized, query latency >100ms (p95)

---

### 7.3 Azure Cache for Redis

**Type**: Managed Cache Service
**Direction**: Application tier connects to Redis
**Protocol**: Redis protocol (TLS-encrypted)

**Purpose**:
Provide distributed in-memory cache for task list queries, improving read performance and reducing database load.

**Integration Details**:
- **Connection**: Spring Data Redis with Lettuce client
- **Authentication**: Redis access key (Azure Key Vault for secret management)
- **Cache Strategy**: Cache-aside pattern with 5-minute TTL
- **TLS**: Enforce TLS for all connections

**Configuration**:
```yaml
spring:
  cache:
    type: redis
  data:
    redis:
      host: <cache-name>.redis.cache.windows.net
      port: 6380
      ssl: true
      password: ${REDIS_PASSWORD}
      timeout: 2000ms
```

**Error Handling**:
- Cache unavailable: Fall back to database queries, log warning
- Cache timeout: Timeout after 2s, fall back to database

**Monitoring**:
- **Metrics**: Cache hit rate, memory usage, operations per second
- **Alerts**: Cache hit rate <70%, memory usage >80%

---

### 7.4 Azure Blob Storage (Backups)

**Type**: Object Storage
**Direction**: PostgreSQL automated backups
**Protocol**: HTTPS (Azure Storage API)

**Purpose**:
Store daily PostgreSQL database backups for disaster recovery.

**Integration Details**:
- **Backup Schedule**: Daily full backup at 2:00 AM UTC
- **Retention**: 30 days
- **Access**: Azure Database for PostgreSQL automated backup feature

**Monitoring**:
- **Metrics**: Backup success rate, backup size, storage used
- **Alerts**: Backup failure, storage >80% capacity

---

### 7.5 External Integrations (Future)

**Note**: The MVP does not include external integrations. Future enhancements may include:

- **Email Service** (SendGrid, AWS SES): Send task reminders
- **Authentication Provider** (Azure AD, Auth0): User authentication
- **Analytics** (Google Analytics, Mixpanel): Usage tracking
