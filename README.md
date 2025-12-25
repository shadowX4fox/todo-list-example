# 3-Tier To-Do List Application

A cloud-native, high-performance task management application built with modern enterprise architecture patterns.

## Overview

The 3-Tier To-Do List Application is a production-ready web application designed to demonstrate enterprise-grade architecture using a classic 3-tier pattern. It provides a simple, reliable, and persistent task management interface that handles concurrent users with consistent performance while ensuring zero data loss.

**Key Highlights:**
- Zero data loss with ACID-compliant database persistence
- Sub-second response times for all operations
- Scalable cloud-native architecture (100 to 10,000+ users)
- 99.9% uptime SLA target

## Features

### Core Functionality

- **Add Tasks**: Create new to-do items with descriptions (up to 500 characters)
- **Display Tasks**: View all tasks with real-time updates and status indicators
- **Toggle Status**: Mark tasks as complete or incomplete with visual feedback
- **Delete Tasks**: Permanently remove tasks from your list

### Technical Features

- **High Performance**: Cache-optimized for 20 TPS read operations
- **Data Persistence**: All tasks saved to PostgreSQL with ACID guarantees
- **Responsive UI**: Angular-based single-page application
- **RESTful API**: Clean REST endpoints for all operations
- **Cloud-Native**: Deployed on Azure Kubernetes Service (AKS)

## Architecture

### System Design

```
┌─────────────────────────────────────────────────────────────┐
│                    3-Tier Architecture                       │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  Tier 1: Presentation Layer                                  │
│  ├─ Angular 17 Web UI                                        │
│  ├─ TypeScript 5.x                                           │
│  └─ Azure CDN (Global Distribution)                          │
│                                                               │
│  Tier 2: Application/Business Logic Layer                    │
│  ├─ Spring Boot 3.2 REST API                                 │
│  ├─ Java 17 (LTS)                                            │
│  ├─ Redis Cache (80% hit rate target)                        │
│  └─ Deployed on Azure Kubernetes Service                     │
│                                                               │
│  Tier 3: Data Layer                                          │
│  ├─ PostgreSQL 15 Database                                   │
│  ├─ Azure Database for PostgreSQL                            │
│  └─ Automated daily backups (30-day retention)               │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Architecture Principles

- **Separation of Concerns**: Clear boundaries between presentation, business logic, and data tiers
- **Scalability First**: Stateless design enabling horizontal scaling
- **High Availability**: 99.9% uptime with auto-scaling and self-healing
- **Security by Design**: Input validation, XSS prevention, SQL injection protection
- **Observability**: Comprehensive monitoring, logging, and alerting

## Technology Stack

### Frontend
- **Angular** 17.x - Modern web UI framework
- **TypeScript** 5.x - Type-safe development
- **RxJS** 7.x - Reactive programming
- **Angular Material** - UI components

### Backend
- **Java** 17 (LTS) - Programming language
- **Spring Boot** 3.2.x - Application framework
- **Spring Data JPA** - Data access layer
- **Hibernate** 6.x - ORM framework
- **HikariCP** - Connection pooling

### Data Tier
- **PostgreSQL** 15.x - Relational database
- **Redis** 7.0.x - Distributed cache
- **Azure Database for PostgreSQL** - Managed database service
- **Azure Cache for Redis** - Managed cache service

### Infrastructure & DevOps
- **Docker** 24.x - Containerization
- **Kubernetes** 1.28.x - Container orchestration
- **Azure Kubernetes Service (AKS)** - Managed Kubernetes
- **Helm** 3.x - Kubernetes package manager
- **Prometheus** & **Grafana** - Monitoring and visualization

## Performance Targets

| Metric | Target | Description |
|--------|--------|-------------|
| Add Task | <500ms (p95) | Task creation response time |
| Display Tasks (cache hit) | <200ms (p95) | Task list retrieval from cache |
| Display Tasks (cache miss) | <1000ms (p95) | Task list retrieval from database |
| Mark Complete/Delete | <300ms (p95) | Update/delete operation time |
| Read Throughput | 20 TPS | Sustained read transactions per second |
| Write Throughput | 10 TPS | Sustained write transactions per second |
| Cache Hit Rate | >80% | Cache effectiveness |
| System Availability | 99.9% | Uptime target |

## Project Structure

```
todo-list-example/
├── README.md                           # This file
├── ARCHITECTURE.md                     # Comprehensive architecture documentation
├── PRODUCT_OWNER_SPEC.md              # Product requirements and specifications
├── Todo-list-example.pdf              # Project documentation (PDF)
├── adr/                               # Architecture Decision Records
│   ├── ADR-001-3tier-architecture.md
│   ├── ADR-002-spring-boot-backend.md
│   ├── ADR-003-angular-frontend.md
│   ├── ADR-004-postgresql-database.md
│   ├── ADR-005-redis-cache.md
│   ├── ADR-006-azure-aks.md
│   ├── ADR-007-no-auth-mvp.md
│   └── ADR-008-hard-delete.md
└── compliance-docs/                   # Compliance and cloud architecture documentation
    ├── CLOUD_ARCHITECTURE_3-Tier-To-Do-List_2025-12-23.md
    └── COMPLIANCE_MANIFEST.md
```

## Getting Started

### Prerequisites

**Frontend Development:**
- Node.js 18+ and npm 10+
- Angular CLI 17+

**Backend Development:**
- Java 17 (LTS)
- Maven 3.9+
- PostgreSQL 15+
- Redis 7+

**Infrastructure:**
- Docker 24+
- Kubernetes 1.28+ or Azure AKS
- Azure account (for managed services)

### Local Development Setup

**Backend (Spring Boot):**
```bash
# Clone the repository
git clone https://github.com/shadowX4fox/todo-list-example.git
cd todo-list-example

# Configure database connection
# Edit src/main/resources/application.yml with your PostgreSQL credentials

# Build and run
mvn clean install
mvn spring-boot:run
```

**Frontend (Angular):**
```bash
# Navigate to frontend directory
cd todo-list-ui

# Install dependencies
npm install

# Configure API endpoint
# Edit src/environments/environment.ts with your backend URL

# Start development server
ng serve

# Access application at http://localhost:4200
```

**Database Setup:**
```sql
-- Create database
CREATE DATABASE todolist;

-- Create tasks table
CREATE TABLE tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    description VARCHAR(500) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'incomplete'
        CHECK (status IN ('incomplete', 'complete')),
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Create indexes
CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_created_at ON tasks(created_at DESC);
```

### Docker Deployment

```bash
# Build Docker images
docker build -t todolist-backend:latest ./backend
docker build -t todolist-frontend:latest ./frontend

# Run with Docker Compose (if available)
docker-compose up -d
```

### Kubernetes Deployment

```bash
# Deploy to Azure AKS
helm install todolist ./helm/todolist --namespace production

# Verify deployment
kubectl get pods -n production
kubectl get services -n production

# Check application health
kubectl port-forward service/todolist-backend 8080:8080 -n production
curl http://localhost:8080/actuator/health
```

## API Documentation

### REST Endpoints

| Method | Endpoint | Description | Request Body | Response |
|--------|----------|-------------|--------------|----------|
| GET | `/api/tasks` | Retrieve all tasks | - | `200 OK` with JSON array |
| POST | `/api/tasks` | Create new task | `{"description": "text"}` | `201 Created` with task object |
| PATCH | `/api/tasks/{id}` | Toggle task status | - | `200 OK` with updated task |
| DELETE | `/api/tasks/{id}` | Delete task | - | `204 No Content` |

**Example: Create Task**
```bash
curl -X POST http://localhost:8080/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"description": "Buy groceries"}'
```

**Example: Get All Tasks**
```bash
curl http://localhost:8080/api/tasks
```

## Documentation

### Core Documentation
- **[ARCHITECTURE.md](ARCHITECTURE.md)** - Comprehensive technical architecture documentation (1,800+ lines)
  - System overview and design principles
  - Component details and data flows
  - Security architecture
  - Scalability and performance
  - Operational considerations

- **[PRODUCT_OWNER_SPEC.md](PRODUCT_OWNER_SPEC.md)** - Business requirements and product specifications
  - Business objectives and success criteria
  - User personas and use cases
  - User stories and acceptance criteria
  - Performance requirements

### Architecture Decision Records (ADRs)
Located in the `/adr` directory, documenting key architectural decisions:

- [ADR-001: 3-Tier Architecture Pattern](adr/ADR-001-3tier-architecture.md)
- [ADR-002: Spring Boot for Backend](adr/ADR-002-spring-boot-backend.md)
- [ADR-003: Angular for Frontend](adr/ADR-003-angular-frontend.md)
- [ADR-004: PostgreSQL for Database](adr/ADR-004-postgresql-database.md)
- [ADR-005: Redis for Caching](adr/ADR-005-redis-cache.md)
- [ADR-006: Azure Kubernetes Service](adr/ADR-006-azure-aks.md)
- [ADR-007: No Authentication in MVP](adr/ADR-007-no-auth-mvp.md)
- [ADR-008: Hard Delete vs Soft Delete](adr/ADR-008-hard-delete.md)

### Compliance Documentation
- **[Compliance Manifest](compliance-docs/COMPLIANCE_MANIFEST.md)** - Standards compliance tracking
- **[Cloud Architecture Document](compliance-docs/CLOUD_ARCHITECTURE_3-Tier-To-Do-List_2025-12-23.md)** - Cloud deployment specifications

## Monitoring & Observability

### Metrics
- **Application**: Request rate, latency (p50/p95/p99), error rates
- **Database**: Query performance, connection pool usage, storage
- **Cache**: Hit rate, memory usage, operations per second
- **Infrastructure**: Pod CPU/memory, node health, deployment status

### Dashboards
Access Grafana dashboards at: `http://<grafana-url>:3000`
- Application Performance
- Database Performance
- Cache Performance
- Infrastructure Health

### Logs
Centralized logging via Azure Monitor with 90-day retention.

## Security

### Security Features
- **Input Validation**: Client-side and server-side validation
- **XSS Prevention**: HTML entity encoding, Content Security Policy
- **SQL Injection Prevention**: Parameterized queries via JPA
- **HTTPS Enforcement**: TLS 1.3 for all communications
- **Encryption at Rest**: Database and backup encryption
- **Secret Management**: Azure Key Vault for credentials

### Security Headers
- Content-Security-Policy
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- Strict-Transport-Security

## Roadmap

### Current: MVP (Phase 1)
- [x] Core task management (Add, Display, Complete, Delete)
- [x] Database persistence with PostgreSQL
- [x] Redis caching for performance
- [x] Azure AKS deployment
- [x] Comprehensive documentation

### Future: Phase 2+
- [ ] User authentication (OAuth 2.0, JWT)
- [ ] Multi-user support with per-user task lists
- [ ] Task editing capability
- [ ] Task prioritization and due dates
- [ ] Mobile-responsive design
- [ ] Progressive Web App (PWA) with offline support
- [ ] Email notifications for task reminders

## Contributing

### Development Workflow
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes (`git commit -m 'Add some feature'`)
4. Push to the branch (`git push origin feature/your-feature`)
5. Open a Pull Request

### Code Standards
- **Java**: Follow Google Java Style Guide
- **TypeScript**: Follow Angular Style Guide
- **Commits**: Use conventional commits format

### Testing Requirements
- Unit tests: Minimum 80% code coverage
- Integration tests for API endpoints
- End-to-end tests for critical user flows

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For questions, issues, or feature requests:
- **Issues**: [GitHub Issues](https://github.com/shadowX4fox/todo-list-example/issues)
- **Documentation**: See [ARCHITECTURE.md](ARCHITECTURE.md) for detailed technical information
- **Email**: Contact the development team

## Acknowledgments

- Built with Spring Boot, Angular, PostgreSQL, and Redis
- Deployed on Microsoft Azure
- Architecture follows industry best practices and OWASP security guidelines

---

**Version**: 1.0.0
**Last Updated**: 2025-12-24
**Status**: Production-Ready MVP