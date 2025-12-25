# ADR-001: 3-Tier Architecture Pattern

**Status**: Accepted
**Date**: 2025-12-23
**Authors**: Architecture Team
**Related**: []

---

## Context

### Problem Statement

We need to select an architectural pattern for the To-Do List application that:
- Clearly separates UI, business logic, and data concerns
- Supports the performance requirements (20 TPS read, 10 TPS write)
- Enables independent scaling of each layer
- Is appropriate for a small-to-medium team (2-8 developers)
- Can be deployed to Azure Kubernetes Service

### Requirements

**Functional Requirements:**
- Support 4 core features: Add, Display, Mark Complete, Delete tasks
- Provide RESTful API for frontend-backend communication
- Persist all task data with zero data loss

**Non-Functional Requirements:**
- **Performance**: <1s response time (p95) for all operations
- **Scalability**: Scale from 100 to 10,000+ users
- **Maintainability**: Low to medium complexity, suitable for small team
- **Reliability**: 99.9% uptime target

**Constraints:**
- **Team**: Small development team (2-8 developers)
- **Timeline**: MVP delivery as priority
- **Technology**: Must deploy to Azure AKS

---

## Decision

### Summary

We will use a **3-Tier Architecture** pattern with clear separation between Presentation (Angular), Application/Business Logic (Spring Boot + Redis cache), and Data (PostgreSQL) tiers.

### Detailed Decision

The system will be organized into three distinct tiers:

**Tier 1: Presentation Layer**
- Angular 17 web application
- Client-side validation and state management
- Communicates with Application tier via REST APIs

**Tier 2: Application/Business Logic Layer**
- Spring Boot 3.2 REST API services
- Business logic for task CRUD operations
- Redis cache for performance optimization
- Stateless design for horizontal scaling

**Tier 3: Data Layer**
- PostgreSQL 15 database for persistent storage
- Spring Data JPA for data access
- Automated backups to Azure Blob Storage

---

## Rationale

### Primary Drivers

**1. Simplicity**
- **Description**: 3-tier is a well-understood, proven pattern
- **Impact**: Faster development, easier onboarding, lower risk
- **Evidence**: Industry-standard pattern for web applications, extensive documentation and community support

**2. Clear Separation of Concerns**
- **Description**: Each tier has distinct responsibilities
- **Impact**: Independent development, testing, and scaling
- **Evidence**: Presentation tier can be updated without changing backend, database schema changes isolated to Data tier

**3. Appropriate for Team Size**
- **Description**: 3-tier architecture is suitable for small-to-medium teams
- **Impact**: Not over-engineered like microservices would be for this scale
- **Evidence**: Team of 2-8 developers can manage 3 tiers effectively

### Comparison Summary

| Criteria | 3-Tier (Selected) | Microservices | Monolith |
|----------|-------------------|---------------|----------|
| **Complexity** | ✅ Low-Medium | ❌ High | ✅ Low |
| **Scalability** | ✅ Good (per-tier) | ✅ Excellent (per-service) | ⚠️ Limited |
| **Team Size Fit** | ✅ 2-8 developers | ❌ 10+ developers | ✅ 1-5 developers |
| **Operational Overhead** | ✅ Low | ❌ High | ✅ Very Low |
| **Performance** | ✅ Excellent (with cache) | ✅ Excellent | ⚠️ Good |
| **Development Speed** | ✅ Fast | ⚠️ Medium | ✅ Very Fast |

---

## Consequences

### Positive Consequences

1. **Clear Architectural Boundaries**
   - Each tier has well-defined responsibilities
   - Developers can specialize (frontend, backend, database)
   - Easier to test each tier independently

2. **Independent Scaling**
   - Presentation tier scales via CDN
   - Application tier scales horizontally (Kubernetes HPA)
   - Database tier scales vertically or with read replicas

3. **Technology Flexibility**
   - Can replace Angular with React without changing backend
   - Can swap PostgreSQL for another database without affecting UI
   - Decoupled technology choices

### Negative Consequences

1. **API Layer Overhead**
   - Additional latency from REST API calls (50-100ms)
   - **Mitigation**: Redis cache reduces database round trips
   - **Severity**: Low (acceptable for <1s target)

2. **Network Chattiness**
   - Multiple network hops (UI → API → DB)
   - **Mitigation**: Batch operations where possible, cache frequently accessed data
   - **Severity**: Low

### Trade-offs

- **Simplicity vs Flexibility**: Chose simpler 3-tier over microservices, accepting some limitations in per-component scaling
- **Performance vs Complexity**: Acceptable network overhead in exchange for clearer separation
- **Time-to-Market vs Future Scalability**: Fast MVP delivery, can refactor to microservices if needed at scale

---

## Alternatives Considered

### Alternative 1: Microservices Architecture

**Description:**
Decompose the application into independent microservices (Task Service, User Service, Notification Service, etc.).

**Why Considered:**
- Excellent scalability per service
- Independent deployment cycles
- Modern cloud-native pattern

**Why Rejected:**
- **Over-engineered for MVP**: Only 4 features (Add, Display, Complete, Delete)
- **High operational complexity**: Requires service mesh, API gateway, distributed tracing
- **Team size mismatch**: Requires 10+ developers to manage effectively
- **Premature optimization**: Can refactor to microservices later if needed

**Use Case:**
Microservices would be appropriate if the application grows to 20+ services with independent teams.

---

### Alternative 2: Monolith (Single-Tier)

**Description:**
Build the entire application as a single deployable unit (Angular + Spring Boot in same artifact).

**Why Considered:**
- Simplest deployment model
- Lowest operational overhead
- Fastest initial development

**Why Rejected:**
- **Limited scalability**: Cannot scale frontend and backend independently
- **Tight coupling**: Frontend and backend changes require full redeployment
- **Technology lock-in**: Harder to replace Angular or Spring Boot separately

**Use Case:**
Monolith would be appropriate for internal tools with <50 users and minimal scaling needs.

---

## References

### Documentation
- [ARCHITECTURE.md Section 4: Architecture Layers](/home/shadowx4fox/todo-list-example/ARCHITECTURE.md#4-architecture-layers)
- [PRODUCT_OWNER_SPEC.md](/home/shadowx4fox/todo-list-example/PRODUCT_OWNER_SPEC.md)

### Research & Analysis
- Microsoft: [3-Tier Architecture Pattern](https://learn.microsoft.com/en-us/azure/architecture/guide/architecture-styles/n-tier)
- Martin Fowler: [Patterns of Enterprise Application Architecture](https://martinfowler.com/books/eaa.html)

---

**Last Updated**: 2025-12-23
**Status**: ✅ Accepted