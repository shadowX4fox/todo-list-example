[Architecture](../ARCHITECTURE.md) > Scalability & Performance

# 10. Scalability & Performance

This section documents performance targets, scaling strategies, and capacity planning.

### 10.1 Performance Targets

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| **Add Task (POST)** | <500ms (p95) | Spring Boot Actuator metrics, Prometheus |
| **Display Tasks (GET - cache hit)** | <200ms (p95) | Spring Boot Actuator metrics |
| **Display Tasks (GET - cache miss)** | <1000ms (p95) | Spring Boot Actuator metrics |
| **Mark Complete (PATCH)** | <300ms (p95) | Spring Boot Actuator metrics |
| **Delete Task (DELETE)** | <300ms (p95) | Spring Boot Actuator metrics |
| **Read Throughput** | 20 TPS sustained | Load testing (JMeter, Gatling) |
| **Write Throughput** | 10 TPS sustained | Load testing (JMeter, Gatling) |
| **Cache Hit Rate** | >80% | Redis metrics, Micrometer |

See [Key Metrics](01-system-overview.md#key-metrics) for the authoritative capacity figures.

---

### 10.2 Scaling Strategy

**Horizontal Scaling**:

| Tier | Scaling Approach | Trigger | Max Instances |
|------|-----------------|---------|---------------|
| **Presentation (Angular)** | Azure CDN (global distribution) | N/A (CDN auto-scales) | Unlimited |
| **Application (Spring Boot)** | Kubernetes Horizontal Pod Autoscaler (HPA) | CPU >70% | 10 pods |
| **Database (PostgreSQL)** | Read replicas (future) | Read latency >50ms | 3 replicas |
| **Cache (Redis)** | Azure Cache scaling (future) | Memory >80% | C6 tier (26GB) |

**Vertical Scaling**:

| Tier | Current Resources | Max Resources | Trigger |
|------|------------------|---------------|---------|
| **Application Pod** | 1 vCPU, 1GB RAM | 2 vCPU, 2GB RAM | Memory >80% sustained |
| **Database** | 2 vCPU, 8GB RAM | 8 vCPU, 32GB RAM | CPU >70% or storage >80% |
| **Cache** | 4GB (C1 Standard) | 26GB (C6 Standard) | Memory >80% |

---

### 10.3 Caching Strategy

**Cache Layers**:

1. **Application-Level Cache (Redis)**:
   - **Key**: `tasks:all`
   - **Value**: JSON array of all tasks
   - **TTL**: 5 minutes
   - **Invalidation**: On POST/PATCH/DELETE operations
   - **Hit Rate Target**: >80%

2. **Database Query Cache (PostgreSQL)**:
   - Shared buffers: 2GB (25% of database RAM)
   - Effective cache size: 6GB (75% of database RAM)

**Cache Warming**:
- No pre-warming required (cache-aside pattern, lazy loading)

---

### 10.4 Database Optimization

**Indexes**:
```sql
CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_created_at ON tasks(created_at DESC);
```

**Connection Pooling**:
- HikariCP: 20 connections per Application tier pod
- Total connections: 20 × (number of pods) = 40 connections (2 pods) to 200 connections (10 pods max)

**Query Optimization**:
- **Avoid N+1 queries**: Use JPA `JOIN FETCH` or `@EntityGraph`
- **Pagination**: Implement pagination for GET /tasks if task count >1000 (future enhancement)

---

### 10.5 Load Testing

**Test Scenarios**:

1. **Read-Heavy Workload** (80% reads, 20% writes):
   - 16 concurrent users performing GET /tasks
   - 4 concurrent users performing POST/PATCH/DELETE
   - Sustained for 5 minutes
   - Expected: All requests <1s (p95), no errors

2. **Write-Heavy Workload** (50% reads, 50% writes):
   - 10 concurrent users performing GET /tasks
   - 10 concurrent users performing POST/PATCH/DELETE
   - Sustained for 5 minutes
   - Expected: All requests <1s (p95), <1% errors

**Load Testing Tools**:
- Apache JMeter or Gatling
- Run tests in Azure staging environment (identical to production)

---

### 10.6 Capacity Planning

**Initial Deployment** (100 users):
- Application tier: 2 pods (1 vCPU, 1GB RAM each)
- Database: 2 vCPU, 8GB RAM (Basic tier)
- Cache: 4GB (C1 Standard)
- **Estimated Cost**: $300-400/month

**Growth to 1,000 users**:
- Application tier: 5 pods (scale out)
- Database: 4 vCPU, 16GB RAM (scale up)
- Cache: 13GB (C3 Standard)
- **Estimated Cost**: $800-1,000/month

**Growth to 10,000 users**:
- Application tier: 10 pods (scale out)
- Database: 8 vCPU, 32GB RAM + 2 read replicas
- Cache: 26GB (C6 Standard)
- **Estimated Cost**: $2,000-3,000/month
