[Architecture](../../../ARCHITECTURE.md) > [Components](../README.md) > [Todo List Application](.) > Redis Cache

# Redis Cache

**Type:** Cache
**Technology:** [Azure Cache for Redis 7.0]
**C4 Level:** Container (L2)
**Deploys as:** Azure Cache for Redis (managed service)
**Communicates via:** Redis protocol (TLS-encrypted), inbound from Backend API

**Version**: 7.0.x
**Location**: Azure-managed instance

**Purpose**:
Provide high-performance in-memory caching for task list queries, achieving 80%+ cache hit rate to reduce database load and improve response times.

**Responsibilities**:
- Cache GET /api/tasks responses (key: "tasks:all", value: JSON array)
- Invalidate cache on write operations (POST, PATCH, DELETE)
- TTL management: Expire cache after 5 minutes to ensure data freshness

**Cache Strategy**:
- **Pattern**: Cache-aside (lazy loading)
- **Read flow**: Check cache -> if miss, query DB -> populate cache -> return data
- **Write flow**: Update DB -> invalidate cache -> next read repopulates cache

**Dependencies**:
- **Depends on**: Azure Cache for Redis infrastructure
- **Depended by**: [Task Management Backend API](02-task-management-backend-api.md) (Redis protocol)

**Configuration**:
- `spring.cache.type`: redis
- `spring.data.redis.host`: Azure Redis endpoint
- `spring.data.redis.port`: 6379 (SSL), 6380 (non-SSL)
- `spring.cache.redis.time-to-live`: 300 seconds (5 minutes)

**Scaling**:
- **Horizontal**: Not applicable (single Azure Redis instance)
- **Vertical**: 4GB capacity (C1 Standard tier)

**Failure Modes**:
- **Redis unavailable**: Application falls back to database queries, logs warning
- **Cache eviction**: LRU (Least Recently Used) policy when memory limit reached

**Monitoring**:
- **Key metrics**: Cache hit rate, memory usage, operations per second
- **Alerts**: Cache hit rate < 70%, memory usage > 80%
- **Logs**: Cache connection errors, eviction events
