[Architecture](../../ARCHITECTURE.md) > Components

# Component Index

This directory contains detailed specifications for each component in the 3-Tier To-Do List Application.

## Component Table

| # | Component | Tier | Type | Technology |
|---|-----------|------|------|------------|
| 01 | [Angular Task Management Web UI](01-angular-web-ui.md) | Tier 1: Presentation | Web UI (SPA) | Angular 17, TypeScript 5.x |
| 02 | [Task REST API Controller](02-task-rest-api-controller.md) | Tier 2: Application | REST API Controller | Spring Boot 3.2, Java 17 |
| 03 | [Task Service (Business Logic)](03-task-service.md) | Tier 2: Application | Application Service | Spring Boot 3.2, Java 17 |
| 04 | [PostgreSQL Task Database](04-postgresql-database.md) | Tier 3: Data | Relational Database | PostgreSQL 15 (Azure Managed) |
| 05 | [Task Repository (Data Access Layer)](05-task-repository.md) | Tier 3: Data | DAO / Repository | Spring Data JPA, Hibernate ORM |
| 06 | [Redis Cache Layer](06-redis-cache.md) | Tier 3: Data | Distributed Cache | Azure Cache for Redis 7.0 |

## Component Dependency Map

```
Tier 1: Presentation
└── Angular Web UI (01)
        ↓ REST API calls
Tier 2: Application / Business Logic
├── Task REST API Controller (02)
│       ↓ delegates to
└── Task Service (03)
        ↓ persists via          ↓ caches via
Tier 3: Data
├── Task Repository (05) ──→ PostgreSQL Database (04)
└── Redis Cache Layer (06)
```
