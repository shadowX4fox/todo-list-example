<!-- managed by solutions-architect-skills:architecture-component-guardian — do not edit manually -->
[Architecture](../../ARCHITECTURE.md) > Components

# Component Details

This directory contains the C4 model component specifications for the 3-Tier To-Do List Application. Components are organized by system (C4 Level 1) with containers (C4 Level 2) in system subfolders. The backend REST API controller, business logic service, and data access repository are modeled as C3 components inside a single Backend API container, following the 3-Tier to C4 translation rule: tiers are logical code layers, not separate deployable containers.

### [Todo List Application](todo-list-application.md)

| # | Component | File | Type | Technology |
|---|-----------|------|------|------------|
| 5.1 | Angular Web UI | [01-angular-web-ui.md](todo-list-application/01-angular-web-ui.md) | Web Application | [Angular 17, TypeScript 5.x] |
| 5.2 | Task Management Backend API | [02-task-management-backend-api.md](todo-list-application/02-task-management-backend-api.md) | API Service | [Spring Boot 3.2, Java 17] |
| 5.3 | PostgreSQL Task Database | [03-postgresql-task-database.md](todo-list-application/03-postgresql-task-database.md) | Database | [PostgreSQL 15] |
| 5.4 | Redis Cache | [04-redis-cache.md](todo-list-application/04-redis-cache.md) | Cache | [Azure Cache for Redis 7.0] |

## Key Relationships

- **Angular Web UI** -> **Task Management Backend API**: REST/HTTPS (JSON payloads) for all task CRUD operations
- **Task Management Backend API** -> **PostgreSQL Task Database**: JDBC over PostgreSQL wire protocol (TLS-encrypted) for data persistence
- **Task Management Backend API** -> **Redis Cache**: Redis protocol (TLS-encrypted) for cache-aside pattern (80%+ hit rate target)
- **PostgreSQL Task Database** -> Azure Blob Storage: Automated daily backups (Azure-managed, 30-day retention)

## Related Documentation

- [Architecture Layers (3-Tier Model)](../03-architecture-layers.md)
- [Data Flow Patterns](../04-data-flow-patterns.md)
- [Integration Points](../05-integration-points.md)
- [Technology Stack](../06-technology-stack.md)
- [ADR-001: 3-Tier Architecture Pattern](../../adr/ADR-001-3tier-architecture.md)
- [ADR-002: Spring Boot for Backend](../../adr/ADR-002-spring-boot-backend.md)
- [ADR-003: Angular for Frontend](../../adr/ADR-003-angular-frontend.md)
- [ADR-004: PostgreSQL for Database](../../adr/ADR-004-postgresql-database.md)
- [ADR-005: Redis for Caching](../../adr/ADR-005-redis-cache.md)
