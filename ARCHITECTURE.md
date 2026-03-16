# ARCHITECTURE.md

**System**: 3-Tier To-Do List Application
**Version**: 1.0.0
**Date**: 2025-12-23
**Status**: Draft

> This file is a **navigation index**. All section content lives under [`docs/`](docs/).

---

## Documentation

| # | Section | File | Description |
|---|---------|------|-------------|
| 1+2 | Executive Summary & System Overview | [docs/01-system-overview.md](docs/01-system-overview.md) | Purpose, key metrics, business value, use cases |
| 3 | Architecture Principles | [docs/02-architecture-principles.md](docs/02-architecture-principles.md) | 9 guiding principles with trade-offs |
| 4 | Architecture Layers | [docs/03-architecture-layers.md](docs/03-architecture-layers.md) | 3-tier model, Mermaid diagram, data flows |
| 5 | Component Details | [docs/components/README.md](docs/components/README.md) | Index of all 6 components |
| 6 | Data Flow Patterns | [docs/04-data-flow-patterns.md](docs/04-data-flow-patterns.md) | 5 detailed data flow sequences |
| 7 | Integration Points | [docs/05-integration-points.md](docs/05-integration-points.md) | Azure AKS, PostgreSQL, Redis, Blob Storage |
| 8 | Technology Stack | [docs/06-technology-stack.md](docs/06-technology-stack.md) | All technologies, versions, justifications |
| 9 | Security Architecture | [docs/07-security-architecture.md](docs/07-security-architecture.md) | Auth, validation, encryption, network security |
| 10 | Scalability & Performance | [docs/08-scalability-and-performance.md](docs/08-scalability-and-performance.md) | SLAs, scaling strategy, capacity planning |
| 11 | Operational Considerations | [docs/09-operational-considerations.md](docs/09-operational-considerations.md) | Deployment, monitoring, logging, DR |
| 12 | References & ADRs | [docs/10-references.md](docs/10-references.md) | ADR summary table and links |

**Index Last Updated:** 2026-03-15

---

## Components

| # | Component | Tier | File |
|---|-----------|------|------|
| 01 | Angular Task Management Web UI | Tier 1: Presentation | [docs/components/01-angular-web-ui.md](docs/components/01-angular-web-ui.md) |
| 02 | Task REST API Controller | Tier 2: Application | [docs/components/02-task-rest-api-controller.md](docs/components/02-task-rest-api-controller.md) |
| 03 | Task Service (Business Logic) | Tier 2: Application | [docs/components/03-task-service.md](docs/components/03-task-service.md) |
| 04 | PostgreSQL Task Database | Tier 3: Data | [docs/components/04-postgresql-database.md](docs/components/04-postgresql-database.md) |
| 05 | Task Repository (Data Access Layer) | Tier 3: Data | [docs/components/05-task-repository.md](docs/components/05-task-repository.md) |
| 06 | Redis Cache Layer | Tier 3: Data | [docs/components/06-redis-cache.md](docs/components/06-redis-cache.md) |

---

## Architecture Decision Records (ADRs)

| ADR | Title | Status | Date | Impact |
|-----|-------|--------|------|--------|
| [ADR-001](adr/ADR-001-3tier-architecture.md) | 3-Tier Architecture Pattern | Accepted | 2025-12-23 | High |
| [ADR-002](adr/ADR-002-spring-boot-backend.md) | Spring Boot for Backend | Accepted | 2025-12-23 | High |
| [ADR-003](adr/ADR-003-angular-frontend.md) | Angular for Frontend | Accepted | 2025-12-23 | High |
| [ADR-004](adr/ADR-004-postgresql-database.md) | PostgreSQL for Database | Accepted | 2025-12-23 | High |
| [ADR-005](adr/ADR-005-redis-cache.md) | Redis for Caching | Accepted | 2025-12-23 | Medium |
| [ADR-006](adr/ADR-006-azure-aks.md) | Azure Kubernetes Service (AKS) | Accepted | 2025-12-23 | High |
| [ADR-007](adr/ADR-007-no-auth-mvp.md) | No Authentication in MVP | Accepted | 2025-12-23 | Medium |
| [ADR-008](adr/ADR-008-hard-delete.md) | Hard Delete vs Soft Delete | Accepted | 2025-12-23 | Low |
