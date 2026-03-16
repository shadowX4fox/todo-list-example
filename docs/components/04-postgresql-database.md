[Architecture](../../ARCHITECTURE.md) > [Components](README.md) > PostgreSQL Task Database

# PostgreSQL Task Database

**Type**: Relational Database (Managed Service)
**Technology**: PostgreSQL 15 (Azure Database for PostgreSQL)
**Version**: 15.x
**Location**: Azure-managed instance

**Purpose**:
Persist task data with ACID guarantees, ensuring zero data loss and supporting high-performance queries for 20 TPS read, 10 TPS write (see [Key Metrics](../01-system-overview.md#key-metrics)).

**Responsibilities**:
- Store tasks table with columns: id, description, status, created_at, updated_at
- Enforce data integrity via primary keys, NOT NULL constraints, CHECK constraints
- Execute queries: SELECT all, INSERT, UPDATE, DELETE
- Automated daily backups to Azure Blob Storage

**Schema**:
```sql
CREATE TABLE tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    description VARCHAR(500) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'incomplete' CHECK (status IN ('incomplete', 'complete')),
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_created_at ON tasks(created_at DESC);
```

**Dependencies**:
- **Depends on**: Azure Database for PostgreSQL infrastructure, Azure Blob Storage (backups)
- **Depended by**: [Task Repository](05-task-repository.md) (Tier 2)

**Configuration**:
- `DB_HOST`: Database host (Azure-managed endpoint)
- `DB_PORT`: Database port (default: 5432)
- `DB_NAME`: Database name (default: todolist)
- `DB_CONNECTION_POOL_SIZE`: HikariCP max connections (default: 20)

**Scaling**:
- **Horizontal**: Read replicas for read-heavy workloads (future enhancement)
- **Vertical**: 2 vCPU, 8GB RAM (Azure Basic tier)

**Backup & Recovery**:
- **Backup Frequency**: Daily automated backups (Azure managed)
- **Retention Policy**: 30 days
- **Recovery Time Objective (RTO)**: <2 hours
- **Recovery Point Objective (RPO)**: <1 hour (5-minute transaction log backups)

**Failure Modes**:
- **Primary database down**: Azure automatic failover to standby instance (<30s downtime)
- **Connection pool exhausted**: Queue requests, alert on timeout (>5s wait)
- **Slow query**: Log query, alert if p95 > 50ms

**Monitoring**:
- **Key metrics**: Query latency (p50/p95/p99), connection pool usage, disk I/O, storage used
- **Alerts**: Query latency > 50ms, connection pool >80% utilized, storage >80% full
- **Logs**: Slow queries (>20ms), connection errors, transaction rollbacks
