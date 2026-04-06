[Architecture](../ARCHITECTURE.md) > Architecture Layers

# 4. Architecture Layers

<!-- ARCHITECTURE_TYPE: 3-TIER -->
<!-- DIAGRAM_THEME: dark -->

**Purpose**: Define the three-tier architecture model that separates presentation, business logic, and data concerns.

This architecture follows the **3-Tier Architecture** pattern, designed for standard web applications, REST APIs, and line-of-business systems.

---

## Tiers Overview

| Tier | Function |
|------|----------|
| **Tier 1: Presentation** | User interface layer handling all user interactions, including web pages, API consumption, and client-side logic. |
| **Tier 2: Application/Business Logic** | Core business logic, REST API services, orchestration, business rules, and cache management. |
| **Tier 3: Data** | Data persistence, database management, data access layer, and data integrity enforcement. |

---

## Architecture Diagrams

### Diagram 1: Logical View (3-Tier)

```
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                              TIER 1 — PRESENTATION                                      │
│                                                                                         │
│   ┌───────────────────────────────────────────────────────────────────────────────┐     │
│   │  Angular Web UI                                                               │     │
│   │  [Web Application]                                                            │     │
│   │  Responsive SPA for task CRUD operations                                      │     │
│   │  Client-side validation, optimistic UI updates                                │     │
│   └───────────────────────────────────────────────────────────────────────────────┘     │
│                                                                                         │
└─────────────────────────────────────────────────────────────────────────────────────────┘
                                          │
                                          ▼  REST/HTTPS (JSON)
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                              TIER 2 — APPLICATION / BUSINESS LOGIC                      │
│                                                                                         │
│   ┌───────────────────────────────────────────────────────────────────────────────┐     │
│   │  Task Management Backend API                                                  │     │
│   │  [API Service]                                                                │     │
│   │  REST endpoints, business logic, cache coordination, data access              │     │
│   │  C3 internals: TaskController, TaskService, TaskRepository                    │     │
│   └───────────────────────────────────────────────────────────────────────────────┘     │
│                                                                                         │
└─────────────────────────────────────────────────────────────────────────────────────────┘
                              │                               │
                              ▼  JDBC/TLS                     ▼  Redis protocol/TLS
┌─────────────────────────────────────────────────────────────────────────────────────────┐
│                              TIER 3 — DATA                                              │
│                                                                                         │
│   Data stores:                                                                          │
│   ┌─────────────────────────────────┐ ┌─────────────────────────────────────────┐      │
│   │  PostgreSQL Task Database       │ │  Redis Cache                            │      │
│   │  [Database]                     │ │  [Cache]                                │      │
│   │  ACID task persistence          │ │  Cache-aside pattern, 80%+ hit rate     │      │
│   │  20 TPS read, 10 TPS write     │ │  5-minute TTL on tasks:all key          │      │
│   └─────────────────────────────────┘ └─────────────────────────────────────────┘      │
│                                                                                         │
└─────────────────────────────────────────────────────────────────────────────────────────┘

Legend:
  ▼  = Synchronous call (request flows top → down)
  [Type] = C4 L2 container type
  All application services: Spring Boot 3.2 on Azure AKS (min 2 replicas)
  Response path: implicit — bottom → up via same protocol
```

**Reading the diagram:**
- Solid arrows (`▼`) represent synchronous runtime calls with the protocol labeled
- Each box is a C4 Level 2 container — an independently deployable unit
- The Backend API is a single container; its internal layers (Controller, Service, Repository) are C3 components visible in the [component file](components/todo-list-application/02-task-management-backend-api.md)

---

### Diagram 2: C4 Level 1 — System Context

```mermaid
%%{init: {'theme': 'dark'}}%%
C4Context
    title System Context Diagram — Todo List Application

    Person(user, "User", "Manages personal tasks via web browser")

    System(todoApp, "Todo List Application", "3-tier web application for task management — Angular, Spring Boot, PostgreSQL")

    System_Ext(azureBlob, "Azure Blob Storage", "Automated database backup storage")
    System_Ext(azureMonitor, "Azure Monitor", "Centralized logging and metrics")
    System_Ext(azureKV, "Azure Key Vault", "Secret management for credentials")

    Rel(user, todoApp, "Add, view, complete, delete tasks", "HTTPS/TLS 1.3")
    Rel(todoApp, azureBlob, "Daily automated backups", "HTTPS")
    Rel(todoApp, azureMonitor, "Logs and metrics", "Azure SDK")
    Rel(todoApp, azureKV, "Retrieve secrets", "Azure SDK")
```

---

### Diagram 3: C4 Level 2 — Container

```mermaid
%%{init: {'theme': 'dark'}}%%
C4Container
    title Container Diagram — Todo List Application

    Person(user, "User", "Web browser user")

    Container_Boundary(sys, "Todo List Application") {

        Container(webUI, "Angular Web UI", "Angular 17, TypeScript 5.x", "Responsive SPA for task CRUD operations")
        Container(backendAPI, "Task Management Backend API", "Spring Boot 3.2, Java 17", "REST endpoints, business logic, cache coordination, data access")

        ContainerDb(postgres, "PostgreSQL Task Database", "PostgreSQL 15", "ACID task persistence with UUID primary keys")
        ContainerDb(redis, "Redis Cache", "Azure Cache for Redis 7.0", "Cache-aside pattern for task list queries, 80%+ hit rate")

    }

    System_Ext(azureBlob, "Azure Blob Storage", "Database backup storage")

    Rel(user, webUI, "Manages tasks", "HTTPS/TLS 1.3")
    Rel(webUI, backendAPI, "API calls (CRUD)", "REST/HTTPS")
    Rel(backendAPI, postgres, "Reads/writes task data", "JDBC/TLS")
    Rel(backendAPI, redis, "Cache read/write/invalidate", "Redis protocol/TLS")
    Rel(postgres, azureBlob, "Daily automated backups", "Azure managed")
```

---

### Diagram 4: Detailed View

```mermaid
%%{init: {'theme': 'dark'}}%%
graph TB
    subgraph Tier1["Tier 1: Presentation"]
        USER["User\n(Web Browser)"]
        CDN["Azure CDN\n(Global Distribution)"]
        ANGULAR["Angular Web UI\n[Web Application]\nAngular 17, TypeScript 5.x\nTask CRUD SPA"]
    end

    subgraph Tier2["Tier 2: Application / Business Logic\n(Azure Kubernetes Service)"]
        LB["Azure Load Balancer"]
        BACKEND["Task Management Backend API\n[API Service]\nSpring Boot 3.2, Java 17\nREST + Business Logic + Data Access"]
        HPA["HPA\n(CPU > 70%)"]
    end

    subgraph Tier3["Tier 3: Data"]
        REDIS["Redis Cache\n[Cache]\nAzure Cache for Redis 7.0\nkeys: tasks:all, TTL: 5min"]
        POSTGRES["PostgreSQL Task Database\n[Database]\nPostgreSQL 15 (Azure Managed)\ntasks table, ACID, 99.99% SLA"]
        BACKUP["Azure Blob Storage\nDatabase Backups\n30-day retention"]
    end

    subgraph AzureSvc["Azure Cloud Services"]
        KV["Azure Key Vault\nSecrets Management"]
        MON["Azure Monitor\nLogs and Metrics"]
    end

    %% Tier 1 flows
    USER -->|"HTTPS/TLS 1.3"| CDN
    CDN -->|"Static Assets\n(HTML, CSS, JS)"| ANGULAR

    %% Tier 1 → Tier 2
    ANGULAR -->|"REST/HTTPS\nGET/POST/PATCH/DELETE\n/api/tasks"| LB
    LB -->|"Round-robin"| BACKEND

    %% Tier 2 → Tier 3
    BACKEND -->|"JDBC/TLS\nSQL queries\n(writes + cache miss reads)"| POSTGRES
    BACKEND -.->|"Redis protocol/TLS\n1. Check cache (80% hit)\n3. Invalidate on write"| REDIS

    %% Backups
    POSTGRES -->|"Daily automated\nbackups"| BACKUP

    %% Auto-scaling
    HPA -.->|"Monitors and scales\n2-10 pods"| BACKEND

    %% Azure services
    BACKEND -.->|"Retrieve secrets"| KV
    BACKEND -.->|"Logs and metrics"| MON
    POSTGRES -.->|"Metrics"| MON
    REDIS -.->|"Metrics"| MON

    %% Styling (dark theme — 3-TIER palette)
    classDef presentation fill:#5BA0F2,stroke:#7DB8FF,stroke-width:2px,color:#fff
    classDef logic fill:#FFB83D,stroke:#FFCE6D,stroke-width:2px,color:#000
    classDef data fill:#5CAE20,stroke:#78D040,stroke-width:2px,color:#000
    classDef azure fill:#0078D4,stroke:#409CFF,stroke-width:2px,color:#fff

    class USER,CDN,ANGULAR presentation
    class LB,BACKEND,HPA logic
    class REDIS,POSTGRES,BACKUP data
    class KV,MON azure
```

**Diagram Legend:**

**Arrow Types:**
- **Solid arrows (-->)**: Synchronous calls (REST, JDBC)
- **Dashed arrows (-.->)**: Async/infrastructure flows (cache, monitoring, secrets)

**Component Colors (dark theme):**
- **Blue**: Presentation tier (Angular Web UI, CDN)
- **Orange**: Application/Logic tier (Backend API, Load Balancer, HPA)
- **Green**: Data tier (PostgreSQL, Redis, Blob Storage)
- **Azure Blue**: Azure cloud services (Key Vault, Monitor)

**Performance Targets** (see [Key Metrics](01-system-overview.md#key-metrics)):
- Read operations: <1000ms (p95)
- Write operations: <500ms (p95)
- Throughput: 20 TPS read, 10 TPS write
- System Availability: 99.9% uptime

---

### Tier 1: Presentation Layer

**Purpose**: Provide a responsive web interface for task management, enabling users to add, view, complete, and delete tasks through an intuitive UI.

**Components**:
- **Web UI**: Angular 17 single-page application (SPA)
- **Client-Side Logic**: TypeScript business logic, form validation, state management
- **HTTP Client**: Angular HttpClient for REST API communication

**Technologies**:
- **Primary**: Angular 17, TypeScript 5.x
- **Supporting**: RxJS (reactive programming), Angular Material (UI components), Angular Forms (validation)

**Key Responsibilities**:
- Render task list with real-time updates
- Client-side input validation (non-empty text, max length 500 characters)
- User input sanitization to prevent XSS attacks
- Manage UI state (loading indicators, error messages)
- Communicate with Application tier via RESTful APIs

**Communication Patterns**:
- **Inbound**: User interactions (clicks, form submissions) from web browsers
- **Outbound**: HTTPS REST API calls to Spring Boot Application tier
- **Protocols**: HTTPS (TLS 1.3), JSON payload format

**Non-Functional Requirements**:
- **Performance**: <2s initial page load, <100ms UI interaction response
- **Availability**: Stateless client, no availability requirements (depends on Application tier)
- **Scalability**: Served via Azure CDN, scales with CDN capacity

---

### Tier 2: Application/Business Logic Layer

**Purpose**: Execute core business logic for task CRUD operations, manage cache for performance, and provide RESTful APIs for Presentation tier.

**Components**:
- **Task REST API**: Spring Boot REST controllers for task operations
- **Task Service**: Business logic for add, display, complete, delete operations
- **Cache Manager**: Redis integration for task list caching (80% cache hit rate target)
- **Validation Service**: Server-side input validation and sanitization

**Technologies**:
- **Primary**: Java 17 (LTS), Spring Boot 3.2
- **Supporting**: Spring Data JPA, Spring Cache (Redis), Hibernate, HikariCP (connection pooling)

**Key Responsibilities**:
- Expose REST API endpoints: GET /tasks, POST /tasks, PATCH /tasks/{id}, DELETE /tasks/{id}
- Execute business logic: validate input, enforce business rules (max length, sanitization)
- Manage Redis cache: cache GET /tasks responses, invalidate cache on POST/PATCH/DELETE
- Transaction management: ACID-compliant database operations via Spring @Transactional
- Error handling: return appropriate HTTP status codes (400, 404, 500) with error details

**Communication Patterns**:
- **Inbound**: HTTPS REST requests from Angular Presentation tier
- **Outbound**: Database queries to PostgreSQL via JPA, Redis cache operations
- **Protocols**: HTTPS (REST), JDBC (PostgreSQL), Redis protocol

**Non-Functional Requirements**:
- **Performance**: <500ms for POST (p95), <1000ms for GET (p95), <300ms for PATCH/DELETE (p95)
- **Throughput**: Support 20 TPS read, 10 TPS write
- **Availability**: 99.9% uptime, run 2+ instances for redundancy
- **Scalability**: Stateless design, horizontal scaling via Kubernetes (target 5 pods under peak load)

---

### Tier 3: Data Layer

**Purpose**: Persist task data with ACID guarantees, ensuring zero data loss and supporting query performance requirements.

**Components**:
- **Database Management System**: PostgreSQL 15 (Azure Database for PostgreSQL)
- **Data Access Layer**: Spring Data JPA repositories with Hibernate ORM
- **Cache Layer**: Azure Cache for Redis (4GB capacity)
- **Connection Pooling**: HikariCP with 20 connections per Application tier instance

**Technologies**:
- **Primary**: PostgreSQL 15 (managed service), Azure Cache for Redis 7.0
- **Supporting**: Hibernate ORM, HikariCP, pg_stat_statements (query monitoring)

**Key Responsibilities**:
- Persist tasks table with columns: id (UUID), description (VARCHAR 500), status (ENUM), created_at, updated_at
- Enforce data integrity via primary keys, NOT NULL constraints
- Execute queries: SELECT all tasks, INSERT task, UPDATE task status, DELETE task
- Automated daily backups to Azure Blob Storage (30-day retention)
- Query performance optimization via indexes on id (PK) and status

**Schema**:
```sql
CREATE TABLE tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    description VARCHAR(500) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'incomplete' CHECK (status IN ('incomplete', 'complete')),
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_tasks_status ON tasks(status);
```

**Communication Patterns**:
- **Inbound**: SQL queries from Application tier via JDBC/JPA
- **Outbound**: Backups to Azure Blob Storage, replication to read replicas (future)
- **Protocols**: PostgreSQL wire protocol (TLS-encrypted), Azure Blob Storage HTTPS

**Non-Functional Requirements**:
- **Performance**: <50ms query response time (p95), support 30 TPS (20 read + 10 write)
- **Availability**: 99.99% uptime (Azure managed service SLA), automated failover
- **Scalability**: Read replicas for horizontal read scaling (future enhancement)
- **Backup**: Daily automated backups, 30-day retention, <1 hour RPO, <2 hour RTO

---

## Data Flow

**Typical Request Flow: Add Task (Write Operation)**

```
1. User enters task description in Angular web UI and clicks "Add"
   ↓
2. Presentation Tier (Angular)
   - Validates input client-side (non-empty, max 500 chars)
   - Sends POST /api/tasks with JSON: {"description": "Buy groceries"}
   ↓
3. Application Tier (Spring Boot)
   - REST controller receives request
   - Validates input server-side (sanitize for XSS, check constraints)
   - Calls TaskService.createTask()
   - TaskService calls TaskRepository.save()
   ↓
4. Data Tier (PostgreSQL)
   - Hibernate executes INSERT INTO tasks (description, status, created_at, updated_at)
   - Database commits transaction, returns generated UUID
   ↓
5. Application Tier (Spring Boot)
   - Cache invalidation: Redis DELETE /tasks cache entry
   - Returns 201 Created with JSON: {"id": "uuid", "description": "Buy groceries", "status": "incomplete"}
   ↓
6. Presentation Tier (Angular)
   - Receives response, updates local state
   - Renders new task in task list without page reload
```

**Typical Request Flow: Display Tasks (Read Operation - Cache Hit)**

```
1. User navigates to application or refreshes page
   ↓
2. Presentation Tier (Angular)
   - Sends GET /api/tasks
   ↓
3. Application Tier (Spring Boot)
   - REST controller receives request
   - Checks Redis cache for key "tasks:all"
   - Cache HIT (80% of requests) → returns cached JSON array
   ↓
4. Presentation Tier (Angular)
   - Receives JSON array: [{"id": "uuid1", ...}, {"id": "uuid2", ...}]
   - Renders task list
```

**Typical Request Flow: Display Tasks (Read Operation - Cache Miss)**

```
1. User navigates to application or refreshes page
   ↓
2. Presentation Tier (Angular)
   - Sends GET /api/tasks
   ↓
3. Application Tier (Spring Boot)
   - REST controller receives request
   - Checks Redis cache for key "tasks:all"
   - Cache MISS (20% of requests)
   ↓
4. Data Tier (PostgreSQL)
   - Hibernate executes SELECT * FROM tasks ORDER BY created_at DESC
   - Database returns result set
   ↓
5. Application Tier (Spring Boot)
   - Populates Redis cache with key "tasks:all", TTL = 5 minutes
   - Returns JSON array to client
   ↓
6. Presentation Tier (Angular)
   - Receives JSON array, renders task list
```
