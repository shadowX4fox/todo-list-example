[Architecture](../../ARCHITECTURE.md) > [Components](README.md) > Task Repository (Data Access Layer)

# Task Repository (Data Access Layer)

**Type**: Data Access Object (DAO) / Repository
**Technology**: Spring Data JPA, Hibernate ORM
**Version**: 1.0.0
**Location**: `src/main/java/com/example/todolist/repository/TaskRepository.java`

**Purpose**:
Provide data access layer for Task entity persistence, abstracting raw SQL queries through JPA repository pattern.

**Responsibilities**:
- CRUD operations for Task entity
- Query optimization via JPA methods
- Transaction management (Spring @Transactional)
- Connection pooling via HikariCP

**Data Access Methods**:
```java
public interface TaskRepository extends JpaRepository<Task, UUID> {
    List<Task> findAll(); // Retrieve all tasks
    Optional<Task> findById(UUID id); // Find by ID
    Task save(Task task); // Insert or update
    void deleteById(UUID id); // Delete by ID
}
```

**Dependencies**:
- **Depends on**: [PostgreSQL Task Database](04-postgresql-database.md)
- **Depended by**: [Task Service](03-task-service.md) (Tier 2)

**Configuration**:
- `spring.datasource.hikari.maximum-pool-size`: Max connections (default: 20)
- `spring.jpa.hibernate.ddl-auto`: Schema management (default: validate)

**Scaling**:
- **Horizontal**: Not applicable (repository is part of Application tier pods)
- **Vertical**: Depends on Application tier pod resources

**Failure Modes**:
- **Database connection lost**: HikariCP retries, throw exception after 5s timeout
- **Transaction deadlock**: Hibernate retries once, then throws exception

**Monitoring**:
- **Key metrics**: Query execution time, connection acquisition time
- **Alerts**: Query >100ms, connection wait time >500ms
- **Logs**: Slow queries (>20ms), Hibernate SQL statements (debug mode only)
