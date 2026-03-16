[Architecture](../../ARCHITECTURE.md) > [Components](README.md) > Task REST API Controller

# Task REST API Controller

**Type**: REST API Controller
**Technology**: Spring Boot 3.2, Java 17
**Version**: 1.0.0
**Location**: `src/main/java/com/example/todolist/controller/TaskController.java`

**Purpose**:
Expose RESTful API endpoints for task CRUD operations, handling HTTP requests/responses and delegating business logic to TaskService.

**Responsibilities**:
- Handle incoming HTTP requests (GET, POST, PATCH, DELETE)
- Validate request payloads using Spring Validation annotations
- Map DTOs (Data Transfer Objects) to domain entities
- Format responses as JSON with appropriate HTTP status codes
- Handle exceptions and return error responses (400, 404, 500)

**Endpoints/Routes**:
- `GET /api/tasks`: Retrieve all tasks (returns JSON array)
- `POST /api/tasks`: Create new task (request body: `{"description": "text"}`)
- `PATCH /api/tasks/{id}`: Toggle task status (no request body needed)
- `DELETE /api/tasks/{id}`: Delete task by ID

**Dependencies**:
- **Depends on**: [Task Service](03-task-service.md) (Tier 2), Spring Validation
- **Depended by**: [Angular Web UI](01-angular-web-ui.md) (Tier 1)

**Configuration**:
- `server.port`: HTTP server port (default: 8080)
- `spring.jackson.date-format`: JSON date format (default: ISO 8601)

**Scaling**:
- **Horizontal**: Yes, stateless controller scales linearly (Kubernetes horizontal pod autoscaler)
- **Vertical**: 1 vCPU, 1GB RAM per pod

**Failure Modes**:
- **TaskService exception**: Return 500 Internal Server Error with error ID for tracking
- **Validation failure**: Return 400 Bad Request with field-level error details
- **Task not found**: Return 404 Not Found

**Monitoring**:
- **Key metrics**: Request rate (req/s), p95 latency, 4xx/5xx error rates
- **Alerts**: Error rate > 1%, p99 latency > 1s
- **Logs**: All requests (HTTP method, path, status, duration), exceptions
