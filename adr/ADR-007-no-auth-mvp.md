# ADR-007: No Authentication in MVP

**Status**: Accepted
**Date**: 2025-12-23
**Authors**: Architecture Team
**Related**: [ADR-001](ADR-001-3tier-architecture.md)

---

## Context

### Problem Statement

We need to decide whether to implement user authentication in the MVP (Minimum Viable Product) release of the To-Do List application.

**Authentication Options:**
1. **No Authentication (MVP)**: Global task list visible to all users
2. **User Authentication (Future)**: Each user has their own private task list

**Product Owner Assumption** (from PRODUCT_OWNER_SPEC.md):
> "Single-User Application (MVP): No user authentication or multi-user support initially. Each user sees the same global task list."

### Requirements

**Functional Requirements (MVP):**
- 4 core features: Add, Display, Mark Complete, Delete tasks
- Persist tasks to database

**Non-Functional Requirements (MVP):**
- **Time-to-Market**: Deliver MVP as quickly as possible
- **Simplicity**: Minimize complexity for initial release

**Future Requirements (Phase 2+):**
- User authentication (OAuth 2.0, JWT tokens)
- Per-user task lists
- Multi-user support

---

## Decision

### Summary

We will **NOT implement user authentication in the MVP**. The MVP will have a single global task list visible to all users. Authentication will be added in Phase 2 (post-MVP).

### Detailed Decision

**MVP (Phase 1)**:
- No login/signup pages
- No user authentication or authorization
- Single global task list (all users see the same tasks)
- **Security Mitigation**: Deploy to internal network or VPN-only access

**Future (Phase 2)**:
- Add OAuth 2.0 authentication (Azure AD or Auth0)
- Implement per-user task lists (add `user_id` column to tasks table)
- Add JWT token-based authorization
- Migrate existing global tasks to admin user or archive

---

## Rationale

### Primary Drivers

**1. Faster Time-to-Market**
- **Description**: Authentication adds 2-4 weeks of development time
- **Impact**: Delays MVP delivery, increases time-to-market
- **Evidence**: Authentication requires login/signup UI, backend auth logic, token management, testing

**2. Reduced Complexity**
- **Description**: No authentication eliminates session management, password hashing, token expiration
- **Impact**: Simpler codebase, fewer potential bugs, easier testing
- **Evidence**: Authentication adds ~30% more code (controllers, services, security config)

**3. Product Owner Approval**
- **Description**: Product Owner explicitly approved single-user assumption in PRODUCT_OWNER_SPEC.md
- **Impact**: Aligns with product vision for MVP
- **Evidence**: PRODUCT_OWNER_SPEC.md Section 2.3.1 Assumptions

**4. Focus on Core Features**
- **Description**: Prioritize 4 core features (Add, Display, Complete, Delete) over authentication
- **Impact**: Deliver core value faster, validate product-market fit
- **Evidence**: Product Owner prioritized core features as "Must Have", authentication as "Could Have"

### Comparison Summary

| Criteria | No Auth (Selected - MVP) | OAuth 2.0 + JWT | Basic Auth (Username/Password) |
|----------|-------------------------|-----------------|-------------------------------|
| **Time-to-Market** | ✅ Fast (0 weeks) | ❌ Slow (+3-4 weeks) | ⚠️ Medium (+1-2 weeks) |
| **Complexity** | ✅ Low | ❌ High | ⚠️ Medium |
| **Security** | ❌ None | ✅ Excellent | ⚠️ Good |
| **User Experience** | ✅ Simple (no login) | ⚠️ Login required | ⚠️ Login required |
| **Multi-User Support** | ❌ No | ✅ Yes | ✅ Yes |
| **Scalability** | ⚠️ Limited (global list) | ✅ Excellent | ✅ Good |

---

## Consequences

### Positive Consequences

1. **Faster MVP Delivery**
   - Save 2-4 weeks of development time
   - Deliver core features sooner, validate product-market fit faster

2. **Reduced Complexity**
   - No authentication logic (30% less code)
   - Simpler testing (no auth flows to test)
   - Fewer potential security vulnerabilities (no password storage, token management)

3. **Better User Experience (MVP)**
   - No login/signup friction for initial users
   - Instant access to application

### Negative Consequences

1. **Security Risk**
   - **Risk**: Anyone with URL can access and modify all tasks
   - **Mitigation**: Deploy to internal network or VPN-only access
   - **Severity**: High (if publicly accessible)

2. **Global Task List (Not Private)**
   - **Risk**: All users see the same tasks (no privacy)
   - **Mitigation**: Communicate clearly to users that tasks are shared
   - **Severity**: Medium (acceptable for MVP, unacceptable for production)

3. **Migration Required (Phase 2)**
   - **Risk**: Adding authentication later requires database migration (add user_id column)
   - **Mitigation**: Plan migration strategy in Phase 2 (archive global tasks or assign to admin)
   - **Severity**: Low (planned refactoring)

### Trade-offs

- **Time-to-Market vs Security**: Prioritize fast MVP delivery over authentication (mitigate with network restrictions)
- **Simplicity vs Multi-User Support**: Accept single global task list for MVP simplicity
- **MVP Speed vs Future Rework**: Accept database migration cost in Phase 2 for faster MVP

---

## Alternatives Considered

### Alternative 1: OAuth 2.0 + JWT (Azure AD or Auth0)

**Description:**
Implement full OAuth 2.0 authentication with JWT tokens in MVP.

**Why Considered:**
- Industry-standard authentication
- Secure (no password storage, token-based)
- Multi-user support from day one

**Why Rejected:**
- **Time-to-Market**: Adds 3-4 weeks to MVP timeline
- **Complexity**: Requires login/signup UI, backend auth logic, token refresh, logout
- **Over-Engineering**: Not required for MVP (Product Owner approved single-user assumption)

**Use Case:**
OAuth 2.0 would be appropriate for Phase 2 (post-MVP) when multi-user support is required.

---

### Alternative 2: Basic Authentication (Username/Password)

**Description:**
Implement simple username/password authentication (store hashed passwords in database).

**Why Considered:**
- Simpler than OAuth 2.0
- Multi-user support
- Lower time-to-market (1-2 weeks)

**Why Rejected:**
- **Still Adds Complexity**: Requires password hashing, login/signup UI, session management
- **Security Risks**: Password storage, brute-force attacks, password reset flow
- **Product Owner Decision**: Explicitly approved no-auth MVP

**Use Case:**
Basic auth would be appropriate for internal tools with minimal security requirements.

---

### Alternative 3: Single Hardcoded User (Pseudo-Authentication)

**Description:**
Hardcode a single user (e.g., "admin") in the application without login UI.

**Why Considered:**
- Allows per-user data model (user_id column)
- Easier Phase 2 migration

**Why Rejected:**
- **No Real Benefit**: Doesn't provide actual security (hardcoded user)
- **Added Complexity**: Requires user_id in database schema for no security gain
- **Confusing**: Fake authentication is worse than no authentication

**Use Case:**
Not recommended; either implement real authentication or skip entirely.

---

## Security Mitigations (MVP)

Since the MVP has no authentication, we must implement network-level security:

**1. Network Restrictions**
- **Option A (Recommended)**: Deploy to internal corporate network (VPN-only access)
- **Option B**: Use Azure Network Security Groups (NSG) to whitelist IP addresses
- **Option C**: Implement IP-based access control in Spring Boot

**2. Communication to Users**
- **Disclaimer**: Display prominent message: "This is a shared task list. All users see the same tasks."
- **Privacy Warning**: Warn users not to store sensitive information in tasks

**3. Input Validation**
- Prevent XSS attacks (HTML entity encoding)
- Prevent SQL injection (JPA parameterized queries)
- Limit task description length (500 characters)

---

## Phase 2 Migration Plan (Future)

When authentication is added in Phase 2:

**Database Migration:**
```sql
-- Add user_id column to tasks table
ALTER TABLE tasks ADD COLUMN user_id UUID REFERENCES users(id);

-- Migrate existing tasks to admin user or archive
UPDATE tasks SET user_id = '<admin-user-id>' WHERE user_id IS NULL;

-- Make user_id NOT NULL after migration
ALTER TABLE tasks ALTER COLUMN user_id SET NOT NULL;
```

**Code Changes:**
- Add User entity and UserRepository
- Implement OAuth 2.0 authentication (Azure AD or Auth0)
- Add JWT token generation and validation
- Update TaskService to filter tasks by user_id
- Add login/signup UI pages

**Estimated Effort**: 3-4 weeks for Phase 2 authentication implementation

---

## References

### Documentation
- [PRODUCT_OWNER_SPEC.md Assumptions](/home/shadowx4fox/todo-list-example/PRODUCT_OWNER_SPEC.md#assumptions--open-questions)
- [ARCHITECTURE.md Section 9: Security Architecture](/home/shadowx4fox/todo-list-example/ARCHITECTURE.md#9-security-architecture)

### Research & Analysis
- [OAuth 2.0 Specification](https://oauth.net/2/)
- [Spring Security OAuth 2.0 Guide](https://spring.io/guides/tutorials/spring-boot-oauth2/)

---

**Last Updated**: 2025-12-23
**Status**: ✅ Accepted
**Next Review**: Phase 2 planning (post-MVP)