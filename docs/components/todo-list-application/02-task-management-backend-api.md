[Architecture](../../../ARCHITECTURE.md) > [Components](../README.md) > [Todo List Application](.) > Task Management Backend API

# Task Management Backend API

**Type:** API Service
**Technology:** [Spring Boot 3.2, Java 17]
**C4 Level:** Container (L2)
**Deploys as:** Docker container on Azure Kubernetes Service (AKS)
**Communicates via:** REST/HTTPS (inbound from Angular Web UI), JDBC (to PostgreSQL), Redis protocol (to Redis Cache)

**Version**: 1.0.0
**Location**: `src/main/java/com/example/todolist/`

**Purpose**:
Single backend container encompassing REST API controllers, business logic services, and data access repositories. Exposes RESTful API endpoints for task CRUD operations and coordinates caching, validation, and persistence.

**Responsibilities**:
- Expose REST API endpoints: GET /api/tasks, POST /api/tasks, PATCH /api/tasks/{id}, DELETE /api/tasks/{id}
- Validate request payloads (Spring Validation annotations, XSS sanitization)
- Execute business logic: enforce business rules (max length 500, non-empty, valid status)
- Manage Redis cache: cache-aside pattern for GET /tasks, invalidate on write operations
- Persist data via Spring Data JPA / Hibernate ORM to PostgreSQL
- Transaction management via Spring @Transactional
- Error handling: return appropriate HTTP status codes (400, 404, 500) with error details

**Endpoints/Routes**:
- `GET /api/tasks`: Retrieve all tasks (returns JSON array)
- `POST /api/tasks`: Create new task (request body: `{"description": "text"}`)
- `PATCH /api/tasks/{id}`: Toggle task status (no request body needed)
- `DELETE /api/tasks/{id}`: Delete task by ID

**C3 Internal Components** (code layers inside this container):

| Component | Layer | Class | Responsibility |
|-----------|-------|-------|---------------|
| Task REST API Controller | Presentation | `TaskController.java` | HTTP request handling, DTO mapping, response formatting |
| Task Service | Application | `TaskService.java` | Business logic, cache coordination, validation |
| Task Repository | Data Access | `TaskRepository.java` | JPA repository, CRUD operations, query abstraction |

**Business Rules**:
- Task description must be non-empty (min 1 character)
- Task description max length: 500 characters
- Task status must be "incomplete" or "complete"
- XSS prevention: Sanitize HTML entities in description

**Dependencies**:
- **Depends on**: [PostgreSQL Task Database](03-postgresql-task-database.md) (JDBC), [Redis Cache](04-redis-cache.md) (Redis protocol)
- **Depended by**: [Angular Web UI](01-angular-web-ui.md) (REST/HTTPS)

**Configuration**:
- `server.port`: HTTP server port (default: 8080)
- `spring.jackson.date-format`: JSON date format (default: ISO 8601)
- `spring.cache.redis.time-to-live`: Cache TTL in seconds (default: 300)
- `task.description.max-length`: Max description length (default: 500)
- `spring.datasource.hikari.maximum-pool-size`: Max DB connections (default: 20)
- `spring.jpa.hibernate.ddl-auto`: Schema management (default: validate)

**Scaling**:
- **Horizontal**: Yes, stateless service scales linearly via Kubernetes HPA (target 5 pods under peak load)
- **Vertical**: 1 vCPU, 1GB RAM per pod (limit: 2 vCPU, 2GB RAM)

**Failure Modes**:
- **Database unavailable**: Throw ServiceUnavailableException, return 503
- **Cache unavailable**: Fall back to database queries, log warning
- **Invalid input**: Throw ValidationException, return 400
- **Task not found**: Return 404 Not Found
- **Connection pool exhausted**: Queue requests, alert on timeout (>5s wait)

**Monitoring**:
- **Key metrics**: Request rate (req/s), p95 latency, 4xx/5xx error rates, cache hit rate, service call rate
- **Alerts**: Error rate > 1%, p99 latency > 1s, cache hit rate < 70%, slow DB queries (>100ms)
- **Logs**: All requests (HTTP method, path, status, duration), cache hits/misses, business rule violations, exceptions
