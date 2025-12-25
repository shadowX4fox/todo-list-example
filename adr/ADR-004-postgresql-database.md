# ADR-004: PostgreSQL for Database

**Status**: Accepted
**Date**: 2025-12-23
**Authors**: Architecture Team
**Related**: [ADR-001](ADR-001-3tier-architecture.md), [ADR-002](ADR-002-spring-boot-backend.md)

---

## Context

### Problem Statement

We need to select a database for the Data tier (Tier 3) that:
- **Critical Requirement**: DATA MUST BE PERSISTED (zero data loss)
- Supports ACID transactions for data integrity
- Handles 20 TPS read, 10 TPS write performance
- Integrates easily with Spring Boot + Spring Data JPA
- Provides automated backup and recovery capabilities
- Scales from 100 to 10,000+ users

### Requirements

**Functional Requirements:**
- Store tasks table with columns: id, description, status, created_at, updated_at
- Support CRUD operations (Create, Read, Update, Delete)
- Enforce data integrity (NOT NULL, CHECK constraints)
- Query optimization via indexes

**Non-Functional Requirements:**
- **Reliability**: Zero data loss, ACID compliance
- **Performance**: <50ms query response time (p95), 30 TPS throughput
- **Availability**: 99.99% uptime with automated backups
- **Scalability**: Support up to 1 million tasks (future growth)

**Constraints:**
- **Infrastructure**: Must run on Azure (managed service preferred)
- **Budget**: Optimize for cost-effectiveness
- **Team Skills**: Team has SQL experience

---

## Decision

### Summary

We will use **PostgreSQL 15** via **Azure Database for PostgreSQL** (managed service) as the primary database for persistent task storage.

### Detailed Decision

**Technology Stack:**
- **Database**: PostgreSQL 15.x
- **Hosting**: Azure Database for PostgreSQL (managed service)
- **ORM**: Spring Data JPA with Hibernate
- **Connection Pooling**: HikariCP (20 connections per app pod)

**Schema Design:**
```sql
CREATE TABLE tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    description VARCHAR(500) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'incomplete'
        CHECK (status IN ('incomplete', 'complete')),
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_created_at ON tasks(created_at DESC);
```

**Backup Strategy:**
- Daily automated backups (Azure managed)
- 30-day retention
- 5-minute transaction log backups (RPO: <1 hour)

---

## Rationale

### Primary Drivers

**1. ACID Compliance**
- **Description**: Full ACID transaction support guarantees data integrity
- **Impact**: Zero data loss, meets critical "DATA MUST BE PERSISTED" requirement
- **Evidence**: PostgreSQL provides full ACID compliance, proven in production for 30+ years

**2. Managed Service**
- **Description**: Azure Database for PostgreSQL handles operations (backups, patching, monitoring)
- **Impact**: Reduced operational burden, automated backups, high availability
- **Evidence**: Azure provides 99.99% SLA, automated daily backups

**3. Spring Boot Integration**
- **Description**: Excellent Spring Data JPA support, mature Hibernate dialect
- **Impact**: Minimal boilerplate code, automatic schema generation (dev), migration tools
- **Evidence**: Spring Data JPA reduces data access code by ~80%

**4. Performance**
- **Description**: Excellent query performance, advanced indexing (B-tree, GIN, GiST)
- **Impact**: Meets <50ms query latency target, supports 30 TPS throughput
- **Evidence**: TechEmpower benchmarks show PostgreSQL handles 10,000+ queries/second

### Comparison Summary

| Criteria | PostgreSQL (Selected) | MySQL | MongoDB |
|----------|----------------------|-------|---------|
| **ACID Compliance** | ✅ Full | ✅ Full | ⚠️ Eventual (default) |
| **Data Integrity** | ✅ Strong (constraints) | ✅ Strong | ❌ Weak (no schema) |
| **Performance** | ✅ Excellent | ✅ Excellent | ✅ Excellent |
| **Azure Managed Service** | ✅ Yes | ✅ Yes | ✅ Yes (Cosmos DB) |
| **Spring Data JPA** | ✅ Excellent | ✅ Excellent | ⚠️ Limited (ODM) |
| **Schema Flexibility** | ⚠️ JSON support | ⚠️ JSON support | ✅ Schemaless |
| **Maturity** | ✅ 30+ years | ✅ 28+ years | ⚠️ 15 years |

---

## Consequences

### Positive Consequences

1. **Zero Data Loss**
   - ACID transactions guarantee data integrity
   - Automated daily backups with 30-day retention
   - Meets critical "DATA MUST BE PERSISTED" requirement

2. **Operational Simplicity**
   - Azure manages backups, patching, monitoring
   - Automated failover to standby instance (<30s downtime)
   - No manual DBA tasks required

3. **Strong Data Integrity**
   - NOT NULL, CHECK, UNIQUE constraints enforce business rules
   - Prevents invalid data at database level
   - Foreign keys support future enhancements (user relationships)

4. **Excellent Performance**
   - B-tree indexes on id (PK), status, created_at
   - Query planner optimizes complex queries
   - Connection pooling (HikariCP) reduces overhead

### Negative Consequences

1. **Schema Rigidity**
   - Schema changes require migrations (ALTER TABLE)
   - **Mitigation**: Use Flyway or Liquibase for versioned migrations
   - **Severity**: Low (schema is stable for MVP)

2. **Vertical Scaling Limits**
   - Single-instance write bottleneck (no sharding in MVP)
   - **Mitigation**: Read replicas for horizontal read scaling (future)
   - **Severity**: Low (acceptable up to 100,000+ users)

### Trade-offs

- **Schema Flexibility vs Data Integrity**: Chose strict schema for data integrity over schemaless flexibility
- **Operational Simplicity vs Cost**: Accept Azure managed service cost (~$100-300/month) for reduced ops burden

---

## Alternatives Considered

### Alternative 1: MySQL

**Description:**
Use MySQL 8.0 with Azure Database for MySQL.

**Why Considered:**
- Similar performance to PostgreSQL
- ACID compliant
- Azure managed service available
- Lower memory footprint

**Why Rejected:**
- **Feature Parity**: PostgreSQL has more advanced features (CTEs, window functions, JSON)
- **Team Preference**: Team prefers PostgreSQL syntax and ecosystem
- **Minor Advantages**: PostgreSQL's advanced indexing (GIN, GiST) may be useful for future features

**Use Case:**
MySQL would be appropriate if team has strong MySQL expertise or requires specific MySQL features.

---

### Alternative 2: MongoDB (NoSQL)

**Description:**
Use MongoDB (Azure Cosmos DB with MongoDB API) for document storage.

**Why Considered:**
- Schema flexibility
- Excellent horizontal scaling
- JSON-native storage

**Why Rejected:**
- **ACID Compliance**: Eventual consistency by default (tunable, but complex)
- **Data Integrity**: No schema validation (can store invalid data)
- **ORM Mismatch**: Spring Data MongoDB (ODM) less mature than JPA
- **Overkill for MVP**: Simple relational schema doesn't need document model

**Use Case:**
MongoDB would be appropriate for rapidly evolving schemas or document-centric data (e.g., CMS, catalogs).

---

### Alternative 3: SQLite (Embedded Database)

**Description:**
Use SQLite embedded database (file-based).

**Why Considered:**
- Zero operational overhead
- No separate database server
- Simplest deployment

**Why Rejected:**
- **Concurrency**: Limited support for concurrent writes (locks entire database)
- **No Azure Managed Service**: Manual backups required
- **Scaling**: Cannot scale horizontally (single file)
- **Production Risk**: Not suitable for production web applications with concurrent users

**Use Case:**
SQLite would be appropriate for local development, single-user desktop apps, or embedded systems.

---

## References

### Documentation
- [PostgreSQL Documentation](https://www.postgresql.org/docs/15/index.html)
- [Azure Database for PostgreSQL](https://learn.microsoft.com/en-us/azure/postgresql/)
- [ARCHITECTURE.md Section 5: Component Details](/home/shadowx4fox/todo-list-example/ARCHITECTURE.md#5-component-details)

### Research & Analysis
- [TechEmpower Benchmarks](https://www.techempower.com/benchmarks/) - PostgreSQL performance
- [PostgreSQL vs MySQL Comparison](https://www.postgresql.org/about/)

---

**Last Updated**: 2025-12-23
**Status**: ✅ Accepted