[Architecture](../../ARCHITECTURE.md) > [Components](README.md) > Task Service (Business Logic)

# Task Service (Business Logic)

**Type**: Application Service
**Technology**: Spring Boot 3.2, Java 17
**Version**: 1.0.0
**Location**: `src/main/java/com/example/todolist/service/TaskService.java`

**Purpose**:
Implement core business logic for task lifecycle management, coordinate cache operations, and enforce business rules.

**Responsibilities**:
- Create new tasks with validation (non-empty, max length 500, XSS sanitization)
- Retrieve all tasks from cache or database (cache-aside pattern)
- Update task status (toggle complete ↔ incomplete)
- Delete tasks and invalidate cache
- Enforce business rules (description required, valid status values)

**Public Methods/API**:
- `getAllTasks(): List<Task>`: Retrieve all tasks (cache-first, fallback to DB)
- `createTask(String description): Task`: Validate and create task, invalidate cache
- `toggleTaskStatus(UUID id): Task`: Toggle status, update DB, invalidate cache
- `deleteTask(UUID id): void`: Delete task, invalidate cache

**Business Rules**:
- Task description must be non-empty (min 1 character)
- Task description max length: 500 characters
- Task status must be "incomplete" or "complete"
- XSS prevention: Sanitize HTML entities in description

**Dependencies**:
- **Depends on**: [Task Repository](05-task-repository.md) (Tier 3), [Redis Cache Layer](06-redis-cache.md) (Spring Cache + Redis)
- **Depended by**: [Task REST API Controller](02-task-rest-api-controller.md) (Tier 2)

**Configuration**:
- `spring.cache.redis.time-to-live`: Cache TTL in seconds (default: 300)
- `task.description.max-length`: Max description length (default: 500)

**Scaling**:
- **Horizontal**: Yes, stateless service scales linearly
- **Vertical**: 2 vCPU, 2GB RAM per pod

**Failure Modes**:
- **Database unavailable**: Throw ServiceUnavailableException, return 503
- **Cache unavailable**: Fall back to database query, log warning
- **Invalid input**: Throw ValidationException, return 400

**Monitoring**:
- **Key metrics**: Service call rate, cache hit rate, business rule violations
- **Alerts**: Cache hit rate < 70%, slow DB queries (>100ms)
- **Logs**: All service calls, cache hits/misses, business rule violations, exceptions
