# Todo List Application

**C4 Level:** System (L1)
**Type:** Internal System
**Owner:** Development Team

## Overview

A 3-tier web application for task management (add, view, complete, delete tasks). Built with Angular frontend, Spring Boot backend API, PostgreSQL database, and Redis cache. Deployed on Azure Kubernetes Service.

## Containers

| # | Container | File | Type | Technology |
|---|-----------|------|------|------------|
| 1 | Angular Web UI | [01-angular-web-ui.md](todo-list-application/01-angular-web-ui.md) | Web Application | [Angular 17, TypeScript 5.x] |
| 2 | Task Management Backend API | [02-task-management-backend-api.md](todo-list-application/02-task-management-backend-api.md) | API Service | [Spring Boot 3.2, Java 17] |
| 3 | PostgreSQL Task Database | [03-postgresql-task-database.md](todo-list-application/03-postgresql-task-database.md) | Database | [PostgreSQL 15] |
| 4 | Redis Cache | [04-redis-cache.md](todo-list-application/04-redis-cache.md) | Cache | [Azure Cache for Redis 7.0] |

## System Boundaries

This system encompasses all components required for the task management application:
- **Frontend**: Angular SPA served via Azure CDN
- **Backend**: Single Spring Boot application containing REST controllers, business logic services, and data access repositories (C3 components inside one container)
- **Data stores**: PostgreSQL for persistence, Redis for caching
- **Infrastructure**: Azure Kubernetes Service (AKS) for container orchestration

## Communication Patterns

- **User -> Angular Web UI**: HTTPS/TLS 1.3 (browser)
- **Angular Web UI -> Backend API**: REST/HTTPS (JSON payloads)
- **Backend API -> PostgreSQL**: JDBC over PostgreSQL wire protocol (TLS-encrypted)
- **Backend API -> Redis Cache**: Redis protocol (TLS-encrypted)
- **PostgreSQL -> Azure Blob Storage**: Automated daily backups (Azure-managed)
