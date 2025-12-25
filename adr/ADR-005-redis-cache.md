# ADR-005: Redis for Caching

**Status**: Accepted
**Date**: 2025-12-23
**Authors**: Architecture Team
**Related**: [ADR-001](ADR-001-3tier-architecture.md), [ADR-002](ADR-002-spring-boot-backend.md), [ADR-004](ADR-004-postgresql-database.md)

---

## Context

### Problem Statement

We need to implement caching to meet the performance requirement of **20 TPS read operations** while reducing database load. The current architecture would struggle to meet this target with database-only queries.

**Performance Targets:**
- GET /tasks response time: <1000ms (p95) with cache miss
- GET /tasks response time: <200ms (p95) with cache hit
- Read throughput: 20 TPS sustained
- Cache hit rate: >80%

### Requirements

**Functional Requirements:**
- Cache GET /tasks responses (task list queries)
- Invalidate cache on write operations (POST, PATCH, DELETE)
- Share cache across all Application tier instances (stateless design)

**Non-Functional Requirements:**
- **Performance**: Sub-millisecond cache access latency
- **Availability**: 99.9% cache uptime (fallback to database on failure)
- **Scalability**: Support 20+ Application tier pods accessing cache concurrently

**Constraints:**
- **Infrastructure**: Must run on Azure (managed service preferred)
- **Cost**: Optimize for cost-effectiveness (~$50-100/month budget)

---

## Decision

### Summary

We will use **Redis 7.0** via **Azure Cache for Redis** (managed service) for distributed caching of task list queries.

### Detailed Decision

**Technology Stack:**
- **Cache**: Redis 7.0.x
- **Hosting**: Azure Cache for Redis (C1 Standard tier, 4GB capacity)
- **Integration**: Spring Cache abstraction with Lettuce client
- **Strategy**: Cache-aside pattern (lazy loading)

**Caching Strategy:**
- **Cache Key**: `tasks:all`
- **Cache Value**: JSON array of all tasks
- **TTL**: 5 minutes (300 seconds)
- **Invalidation**: Evict cache on POST/PATCH/DELETE operations

**Read Flow (Cache-Aside)**:
```
1. GET /tasks request → Check cache
2. If cache HIT (80% of requests) → Return cached JSON (response time: <200ms)
3. If cache MISS (20% of requests) → Query PostgreSQL → Populate cache → Return JSON (response time: <1000ms)
```

**Write Flow (Cache Invalidation)**:
```
1. POST/PATCH/DELETE request → Update PostgreSQL
2. Evict cache key "tasks:all"
3. Next GET request repopulates cache (cache miss)
```

---

## Rationale

### Primary Drivers

**1. Performance**
- **Description**: In-memory cache provides sub-millisecond latency
- **Impact**: Reduces database load by 80%, improves response time from ~500ms to <50ms
- **Evidence**: Redis benchmarks show 100,000+ ops/second at <1ms latency

**2. Shared Cache (Stateless Design)**
- **Description**: All Application tier pods share the same Redis instance
- **Impact**: Enables stateless Application tier, supports horizontal scaling
- **Evidence**: Kubernetes can scale Application tier from 2 to 10 pods without cache duplication

**3. Managed Service**
- **Description**: Azure Cache for Redis handles operations (patching, monitoring, failover)
- **Impact**: Reduced operational burden, high availability
- **Evidence**: Azure provides 99.9% SLA, automated updates

**4. Spring Boot Integration**
- **Description**: Spring Cache abstraction provides declarative caching with @Cacheable annotations
- **Impact**: Minimal code changes, automatic cache population/invalidation
- **Evidence**: Add caching with ~10 lines of configuration code

### Comparison Summary

| Criteria | Redis (Selected) | Memcached | In-Memory (Caffeine) |
|----------|-----------------|-----------|----------------------|
| **Performance** | ✅ Excellent (<1ms) | ✅ Excellent (<1ms) | ✅ Excellent (local) |
| **Data Structures** | ✅ Rich (lists, sets, hashes) | ⚠️ Basic (key-value) | ⚠️ Basic |
| **Persistence** | ✅ Optional (AOF, RDB) | ❌ None | ❌ None |
| **Shared Cache** | ✅ Yes (network) | ✅ Yes (network) | ❌ No (per-pod) |
| **Azure Managed Service** | ✅ Yes | ❌ No | N/A (library) |
| **Spring Integration** | ✅ Excellent | ✅ Good | ✅ Excellent |
| **Cost** | ⚠️ $50-100/month | ✅ Lower (self-hosted) | ✅ Free (library) |

---

## Consequences

### Positive Consequences

1. **80% Reduction in Database Load**
   - Cache hit rate target: >80%
   - Reduces PostgreSQL queries from 20 TPS to ~4 TPS
   - Lowers database costs, improves database headroom

2. **Improved Response Time**
   - Cache hit: <200ms (vs ~500ms database query)
   - 60% reduction in p95 response time

3. **Horizontal Scaling**
   - Shared cache enables stateless Application tier
   - Kubernetes can scale Application tier pods without cache duplication

4. **Operational Simplicity**
   - Azure manages Redis instance (no manual ops)
   - Automatic failover and monitoring

### Negative Consequences

1. **Cache Staleness**
   - Cache TTL (5 minutes) means data can be stale for up to 5 minutes
   - **Mitigation**: Invalidate cache on every write (POST/PATCH/DELETE)
   - **Severity**: Low (acceptable for task list; users expect eventual consistency)

2. **Additional Cost**
   - Azure Cache for Redis C1 Standard: ~$75/month
   - **Mitigation**: Cost justified by 80% database load reduction and improved performance
   - **Severity**: Low

3. **Cache Availability Dependency**
   - If Redis unavailable, fallback to database queries (performance degrades)
   - **Mitigation**: Azure provides 99.9% SLA, Application tier has fallback logic
   - **Severity**: Low

### Trade-offs

- **Eventual Consistency vs Performance**: Accept up to 5-minute staleness for 60% faster response time
- **Cost vs Performance**: Accept $75/month cost for 80% database load reduction

---

## Alternatives Considered

### Alternative 1: Memcached

**Description:**
Use Memcached for distributed caching (self-hosted on Kubernetes or managed service).

**Why Considered:**
- Similar performance to Redis
- Simpler protocol (key-value only)
- Lower memory footprint

**Why Rejected:**
- **No Azure Managed Service**: Would require self-hosting on Kubernetes (operational overhead)
- **Limited Features**: No data structure support (lists, sets), no persistence
- **Spring Integration**: Less mature than Redis integration

**Use Case:**
Memcached would be appropriate for simple key-value caching with self-hosted infrastructure.

---

### Alternative 2: In-Memory Cache (Caffeine)

**Description:**
Use Caffeine (in-process Java cache library) instead of distributed cache.

**Why Considered:**
- Zero cost (library, not service)
- Lowest latency (no network)
- Simpler deployment

**Why Rejected:**
- **Stateful Application Tier**: Each pod has its own cache (not shared)
- **Cache Duplication**: 10 pods = 10 cache copies (wasted memory)
- **Cache Invalidation Complexity**: Invalidate cache on all pods (requires messaging)
- **Scaling Issues**: Inconsistent cache state across pods

**Use Case:**
In-memory cache would be appropriate for single-instance applications or read-only data.

---

### Alternative 3: No Cache (Database Only)

**Description:**
Query PostgreSQL directly for every GET /tasks request.

**Why Considered:**
- Simplest architecture
- No cache staleness issues
- No additional cost

**Why Rejected:**
- **Performance**: Cannot meet 20 TPS read target with <1s response time
- **Database Load**: Excessive load on PostgreSQL (20 queries/second)
- **Scalability**: Database becomes bottleneck at scale

**Use Case:**
No cache would be appropriate for write-heavy workloads or systems with <5 TPS read load.

---

## Implementation Plan

### Phase 1: Setup Azure Cache for Redis (Week 1)
- Provision Azure Cache for Redis (C1 Standard, 4GB)
- Configure Spring Boot application.properties with Redis endpoint
- Add Spring Data Redis and Lettuce dependencies

### Phase 2: Implement Caching (Week 1)
- Add @Cacheable annotation to TaskService.getAllTasks()
- Add @CacheEvict annotation to TaskService create/update/delete methods
- Configure cache TTL (5 minutes)

### Phase 3: Testing & Validation (Week 2)
- Load test: Verify 20 TPS read throughput
- Validate cache hit rate >80%
- Verify cache invalidation on write operations

### Phase 4: Monitoring (Week 2)
- Configure Micrometer metrics for cache hit/miss rate
- Set up Grafana dashboard for cache metrics
- Create alerts: Cache hit rate <70%, Redis unavailable

---

## Success Metrics

### Quantitative Metrics

- **Cache Hit Rate**: Baseline 0% → Target >80%
- **GET /tasks Response Time**: Baseline ~500ms → Target <200ms (cache hit)
- **Database Load (Read)**: Baseline 20 TPS → Target <5 TPS
- **Read Throughput**: Baseline ~10 TPS → Target 20 TPS

### Qualitative Metrics

- **Developer Experience**: Declarative caching with @Cacheable reduces code complexity
- **Operational Simplicity**: Azure managed service eliminates manual Redis ops

---

## References

### Documentation
- [Redis Documentation](https://redis.io/docs/)
- [Azure Cache for Redis](https://learn.microsoft.com/en-us/azure/azure-cache-for-redis/)
- [Spring Cache Abstraction](https://docs.spring.io/spring-boot/docs/current/reference/html/io.html#io.caching)

### Research & Analysis
- [Redis Benchmarks](https://redis.io/docs/management/optimization/benchmarks/)
- [Cache-Aside Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cache-aside)

---

**Last Updated**: 2025-12-23
**Status**: ✅ Accepted