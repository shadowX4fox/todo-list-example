# 8. Technology Stack

This section documents all technologies, frameworks, libraries, and tools used in the system.

### Frontend Technologies

| Technology | Version | Purpose | Justification |
|------------|---------|---------|---------------|
| **Angular** | 17.x | Web UI framework | Industry-standard SPA framework, TypeScript support, reactive programming (RxJS) |
| **TypeScript** | 5.x | Programming language | Type safety, better IDE support, catches errors at compile-time |
| **RxJS** | 7.x | Reactive programming | Handle async operations (HTTP calls), event streams |
| **Angular Material** | 17.x | UI component library | Pre-built Material Design components (buttons, forms, lists) |
| **Angular Forms** | 17.x | Form validation | Client-side validation, reactive forms |

### Backend Technologies

| Technology | Version | Purpose | Justification |
|------------|---------|---------|---------------|
| **Java** | 17 (LTS) | Programming language | Long-term support, mature ecosystem, Spring Boot compatibility |
| **Spring Boot** | 3.2.x | Application framework | Rapid development, auto-configuration, production-ready features |
| **Spring Web** | 3.2.x | REST API framework | RESTful web services, JSON serialization, exception handling |
| **Spring Data JPA** | 3.2.x | Data access layer | ORM abstraction, repository pattern, query methods |
| **Hibernate** | 6.x | ORM framework | Object-relational mapping, HQL queries, lazy loading |
| **Spring Cache** | 3.2.x | Caching abstraction | Cache-aside pattern, Redis integration |
| **Spring Validation** | 3.2.x | Input validation | Bean validation annotations (@NotNull, @Size) |
| **HikariCP** | 5.x | Connection pooling | Fast, lightweight JDBC connection pool |
| **Logback** | 1.4.x | Logging framework | Structured JSON logging, log levels, log rotation |

### Data Tier Technologies

| Technology | Version | Purpose | Justification |
|------------|---------|---------|---------------|
| **PostgreSQL** | 15.x | Relational database | ACID compliance, JSON support, mature ecosystem |
| **Azure Database for PostgreSQL** | Managed service | Managed PostgreSQL | Automated backups, high availability, monitoring |
| **Redis** | 7.0.x | Distributed cache | In-memory cache, sub-millisecond latency, TTL support |
| **Azure Cache for Redis** | Managed service | Managed Redis | High availability, scaling, monitoring |

### Infrastructure & DevOps

| Technology | Version | Purpose | Justification |
|------------|---------|---------|---------------|
| **Docker** | 24.x | Containerization | Package application with dependencies, portable across environments |
| **Kubernetes** | 1.28.x | Container orchestration | Auto-scaling, self-healing, rolling updates |
| **Azure Kubernetes Service (AKS)** | Managed service | Managed Kubernetes | Azure-managed control plane, integration with Azure services |
| **Helm** | 3.x | Kubernetes package manager | Templated deployments, versioning, rollback |
| **Azure Key Vault** | Managed service | Secret management | Secure storage for passwords, API keys, certificates |
| **Prometheus** | 2.x | Metrics collection | Time-series metrics, PromQL queries |
| **Grafana** | 10.x | Metrics visualization | Dashboards, alerting, multi-source data |

### Build & Development Tools

| Technology | Version | Purpose | Justification |
|------------|---------|---------|---------------|
| **Maven** | 3.9.x | Java build tool | Dependency management, build lifecycle, plugin ecosystem |
| **npm** | 10.x | Node package manager | Frontend dependency management |
| **Angular CLI** | 17.x | Angular tooling | Code generation, dev server, build optimization |
| **JUnit 5** | 5.x | Unit testing (Java) | Assertions, test lifecycle, parameterized tests |
| **Mockito** | 5.x | Mocking framework | Mock dependencies, verify interactions |
| **Jasmine** | 5.x | Unit testing (Angular) | BDD-style tests, assertions |
| **Karma** | 6.x | Test runner (Angular) | Run tests in browsers, code coverage |

### Monitoring & Observability

| Technology | Version | Purpose | Justification |
|------------|---------|---------|---------------|
| **Spring Boot Actuator** | 3.2.x | Application monitoring | Health checks, metrics endpoints (/actuator/health, /actuator/metrics) |
| **Micrometer** | 1.12.x | Metrics instrumentation | Export metrics to Prometheus, custom metrics |
| **Azure Monitor** | Managed service | Application monitoring | Log analytics, performance monitoring, alerts |
