# ADR-002: Spring Boot for Backend

**Status**: Accepted
**Date**: 2025-12-23
**Authors**: Architecture Team
**Related**: [ADR-001](ADR-001-3tier-architecture.md), [ADR-004](ADR-004-postgresql-database.md)

---

## Context

### Problem Statement

We need to select a backend framework for the Application tier (Tier 2) that:
- Provides RESTful API capabilities
- Integrates easily with PostgreSQL and Redis
- Supports our performance requirements (20 TPS read, 10 TPS write)
- Has strong ecosystem and community support
- Enables rapid development for MVP

### Requirements

**Functional Requirements:**
- REST API endpoints for task CRUD operations
- Database integration via ORM
- Cache integration (Redis)
- Input validation and error handling

**Non-Functional Requirements:**
- **Performance**: <500ms API response time (p95)
- **Throughput**: 20 TPS read, 10 TPS write
- **Developer Productivity**: Auto-configuration, minimal boilerplate
- **Production-Ready**: Health checks, metrics, logging

**Constraints:**
- **Team Skills**: Team has Java experience
- **Timeline**: Need rapid MVP development

---

## Decision

### Summary

We will use **Spring Boot 3.2** with **Java 17 LTS** as the backend framework for the Application tier.

### Detailed Decision

**Technology Stack:**
- **Framework**: Spring Boot 3.2.x
- **Language**: Java 17 (Long-Term Support)
- **Build Tool**: Maven 3.9.x
- **Key Dependencies**:
  - Spring Web (REST API)
  - Spring Data JPA (database access)
  - Spring Cache (Redis integration)
  - Spring Validation (input validation)
  - Spring Boot Actuator (health checks, metrics)

**Architecture Integration:**
- Exposes REST endpoints: GET /api/tasks, POST /api/tasks, PATCH /api/tasks/{id}, DELETE /api/tasks/{id}
- Uses Spring Data JPA repositories for PostgreSQL access
- Integrates Spring Cache with Redis for performance
- Stateless design for horizontal scaling in Kubernetes

---

## Rationale

### Primary Drivers

**1. Rapid Development**
- **Description**: Spring Boot auto-configuration eliminates boilerplate
- **Impact**: Faster MVP delivery, focus on business logic
- **Evidence**: Can scaffold REST API + database + cache in <1 day

**2. Production-Ready Features**
- **Description**: Built-in health checks, metrics, externalized configuration
- **Impact**: Easier deployment to Azure AKS, built-in observability
- **Evidence**: Spring Boot Actuator provides /actuator/health, /actuator/metrics out-of-box

**3. Ecosystem Maturity**
- **Description**: Extensive ecosystem for PostgreSQL, Redis, Kubernetes
- **Impact**: Pre-built integrations, extensive documentation, community support
- **Evidence**: Spring Data JPA for PostgreSQL, Spring Cache for Redis, Spring Cloud Kubernetes

**4. Team Experience**
- **Description**: Team has existing Java expertise
- **Impact**: No learning curve, leverage existing skills
- **Evidence**: Team has delivered previous Spring Boot applications

### Comparison Summary

| Criteria | Spring Boot (Selected) | Node.js + Express | .NET Core |
|----------|------------------------|-------------------|-----------|
| **Developer Productivity** | ✅ Excellent (auto-config) | ⚠️ Good (manual setup) | ✅ Excellent |
| **Performance** | ✅ Excellent | ✅ Excellent (async I/O) | ✅ Excellent |
| **Team Skills** | ✅ High (Java experience) | ⚠️ Medium | ❌ Low |
| **Ecosystem** | ✅ Mature, extensive | ✅ Large, growing | ✅ Mature |
| **Production Features** | ✅ Built-in (Actuator) | ⚠️ Manual (prom-client) | ✅ Built-in |
| **Cloud-Native** | ✅ Excellent (Spring Cloud) | ✅ Good | ✅ Excellent |

---

## Consequences

### Positive Consequences

1. **Rapid Development**
   - Spring Boot auto-configuration reduces boilerplate by ~70%
   - Can focus on business logic instead of framework setup

2. **Built-In Production Features**
   - Health checks: `/actuator/health` for Kubernetes probes
   - Metrics: Micrometer integration with Prometheus
   - Externalized configuration: Environment variables, ConfigMaps

3. **Strong Database Integration**
   - Spring Data JPA reduces data access code by ~80%
   - Automatic connection pooling (HikariCP)
   - Transaction management with @Transactional

### Negative Consequences

1. **JVM Memory Footprint**
   - Higher memory usage than Node.js (~500MB vs ~100MB)
   - **Mitigation**: Allocate 1GB RAM per pod, acceptable for cloud deployment
   - **Severity**: Low

2. **Startup Time**
   - Slower cold start than Node.js (~5s vs ~1s)
   - **Mitigation**: Keep pods running (not cold-start heavy workload)
   - **Severity**: Low

### Trade-offs

- **Memory Footprint vs Developer Productivity**: Accept higher memory usage for faster development
- **Startup Time vs Production Features**: Accept slower startup for built-in Actuator features

---

## Alternatives Considered

### Alternative 1: Node.js + Express

**Description:**
Use Node.js runtime with Express.js framework for REST API.

**Why Considered:**
- Excellent async I/O performance
- Lower memory footprint
- Large npm ecosystem

**Why Rejected:**
- **Team skills gap**: Team has limited Node.js/TypeScript experience
- **Learning curve**: Would slow down MVP delivery
- **Manual setup**: Requires manual configuration for health checks, metrics, logging

**Use Case:**
Node.js would be appropriate for real-time, I/O-heavy workloads with Node.js-experienced teams.

---

### Alternative 2: .NET Core

**Description:**
Use ASP.NET Core for REST API development.

**Why Considered:**
- Excellent performance (top TechEmpower benchmarks)
- Built-in dependency injection
- Strong Azure integration

**Why Rejected:**
- **Team skills gap**: Team has no C#/.NET experience
- **Steeper learning curve**: Would delay MVP
- **Licensing concerns**: Some dependencies may require licenses

**Use Case:**
.NET Core would be appropriate for teams with C# expertise or Microsoft-centric shops.

---

## References

### Documentation
- [Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)
- [Spring Data JPA Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
- [ARCHITECTURE.md Section 8: Technology Stack](/home/shadowx4fox/todo-list-example/ARCHITECTURE.md#8-technology-stack)

### Research & Analysis
- [TechEmpower Benchmarks](https://www.techempower.com/benchmarks/) - Spring Boot performance data
- [Spring Boot on Kubernetes Best Practices](https://spring.io/guides/gs/spring-boot-kubernetes/)

---

**Last Updated**: 2025-12-23
**Status**: ✅ Accepted