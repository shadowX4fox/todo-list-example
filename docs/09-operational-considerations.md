# 11. Operational Considerations

This section documents deployment, monitoring, logging, and operational procedures.

### 11.1 Deployment

**Deployment Platform**: Azure Kubernetes Service (AKS)

**Deployment Strategy**: Rolling Update
- Update pods one at a time to maintain availability
- MaxUnavailable: 1 pod
- MaxSurge: 1 pod (deploy new pod before terminating old pod)

**Deployment Steps**:
1. Build Docker images (Angular, Spring Boot)
2. Push images to Azure Container Registry (ACR)
3. Update Helm chart with new image tags
4. Deploy to AKS: `helm upgrade todolist ./helm/todolist --namespace production`
5. Verify health checks pass: `kubectl get pods -n production`
6. Monitor metrics for 15 minutes post-deployment

**Rollback Procedure**:
- If deployment fails or errors spike: `helm rollback todolist`
- Kubernetes automatically reverts to previous working version

---

### 11.2 Monitoring

**Metrics Collection**:
- **Spring Boot Actuator**: Expose `/actuator/metrics` endpoint
- **Micrometer**: Export metrics to Prometheus
- **Prometheus**: Scrape metrics every 15 seconds
- **Grafana**: Visualize metrics with dashboards

**Key Metrics**:

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| **http.server.requests** | Request rate, latency, status codes | p95 latency >1s, error rate >1% |
| **cache.gets** | Cache hit/miss rate | Hit rate <70% |
| **hikaricp.connections.active** | Database connection pool usage | >80% utilized |
| **jvm.memory.used** | Application memory usage | >80% of heap |
| **system.cpu.usage** | Pod CPU usage | >80% sustained |

**Dashboards**:
- **Application Performance**: Request rate, latency, error rate
- **Database Performance**: Query latency, connection pool, storage
- **Cache Performance**: Hit rate, memory usage, operations per second
- **Infrastructure**: Pod CPU/memory, node health, deployment status

---

### 11.3 Logging

**Logging Framework**: Logback (Spring Boot default)

**Log Format**: Structured JSON logs for parsing by Azure Monitor

**Log Levels**:
- **INFO** (production): Application startup, API requests (method, path, status, duration), cache hits/misses
- **WARN**: Slow queries (>50ms), cache unavailable (fallback to DB), configuration issues
- **ERROR**: Exceptions, database connection failures, uncaught errors
- **DEBUG** (development only): SQL statements, cache operations, detailed request/response

**Log Storage**:
- **Azure Monitor Logs**: Centralized log aggregation, query via Kusto Query Language (KQL)
- **Retention**: 90 days

**Example Log Entry**:
```json
{
  "timestamp": "2025-12-23T10:30:45.123Z",
  "level": "INFO",
  "logger": "com.example.todolist.controller.TaskController",
  "message": "HTTP request completed",
  "method": "POST",
  "path": "/api/tasks",
  "status": 201,
  "duration_ms": 234,
  "correlation_id": "abc123"
}
```

---

### 11.4 Alerting

**Alerting Platform**: Prometheus Alertmanager + Azure Monitor Alerts

**Alert Channels**:
- **Email**: DevOps team distribution list
- **Slack**: #alerts channel
- **PagerDuty**: Critical alerts only (P1 incidents)

**Alert Rules**:

| Alert | Condition | Severity | Response |
|-------|-----------|----------|----------|
| **High Error Rate** | Error rate >1% for 5 minutes | P2 (High) | Investigate logs, check recent deployments |
| **High Latency** | p95 latency >1s for 10 minutes | P2 (High) | Check database performance, cache hit rate |
| **Pod Crash Loop** | Pod restarts >3 in 10 minutes | P1 (Critical) | Check logs, rollback if recent deployment |
| **Database Unavailable** | PostgreSQL connection failure | P1 (Critical) | Check Azure Database status, contact Azure support |
| **Cache Unavailable** | Redis connection failure | P2 (High) | Verify Redis service, fallback to DB queries |
| **Low Cache Hit Rate** | Cache hit rate <70% for 30 minutes | P3 (Medium) | Investigate cache invalidation logic, increase TTL |

---

### 11.5 Backup & Disaster Recovery

**Database Backups**:
- **Frequency**: Daily automated backups (Azure Database for PostgreSQL)
- **Retention**: 30 days
- **Storage**: Azure Blob Storage (geo-redundant)
- **Testing**: Monthly restore test to validate backup integrity

**Recovery Objectives**:
- **RTO (Recovery Time Objective)**: <2 hours
- **RPO (Recovery Point Objective)**: <1 hour (5-minute transaction log backups)

**Disaster Recovery Procedure**:
1. Identify failure scope (database corruption, data center outage)
2. Restore from latest backup (Azure portal or CLI)
3. Validate data integrity (check task count, sample queries)
4. Update connection strings to point to restored database
5. Redeploy Application tier to reconnect to new database
6. Verify application health checks pass
7. Communicate restoration to users

**Disaster Recovery Testing**:
- **Frequency**: Quarterly DR drills
- **Scope**: Full backup restore to staging environment, validate functionality

---

### 11.6 Incident Response

**Incident Severity Levels**:

| Level | Description | Response Time | Example |
|-------|-------------|---------------|---------|
| **P1 (Critical)** | System down, data loss | <15 minutes | Database unavailable, pod crash loop |
| **P2 (High)** | Degraded performance, partial outage | <1 hour | High error rate, slow queries |
| **P3 (Medium)** | Minor issues, workarounds available | <4 hours | Low cache hit rate, non-critical warnings |
| **P4 (Low)** | Cosmetic issues, feature requests | Next release | UI typos, minor UX improvements |

**Incident Response Process**:
1. **Detection**: Alert triggered (Prometheus, Azure Monitor) or user report
2. **Triage**: Assess severity, assign incident commander
3. **Investigation**: Review logs, metrics, recent changes
4. **Mitigation**: Apply fix (rollback, scale resources, manual intervention)
5. **Verification**: Confirm issue resolved, monitor for 30 minutes
6. **Postmortem**: Document root cause, action items to prevent recurrence
