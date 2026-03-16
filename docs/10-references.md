# 12. Architecture Decision Records (ADRs)

This section tracks key architectural decisions made for the To-Do List application.

**Purpose**: Document the "why" behind technical choices to provide context for future team members and explain trade-offs.

**Format**: Each ADR follows the template defined in [ADR_GUIDE.md](../adr/ADR_GUIDE.md)

**ADR Location**: All ADRs are stored in the `/adr` directory.

---

### ADR Summary Table

| ADR | Title | Status | Date | Impact |
|-----|-------|--------|------|--------|
| [ADR-001](../adr/ADR-001-3tier-architecture.md) | 3-Tier Architecture Pattern | Accepted | 2025-12-23 | High |
| [ADR-002](../adr/ADR-002-spring-boot-backend.md) | Spring Boot for Backend | Accepted | 2025-12-23 | High |
| [ADR-003](../adr/ADR-003-angular-frontend.md) | Angular for Frontend | Accepted | 2025-12-23 | High |
| [ADR-004](../adr/ADR-004-postgresql-database.md) | PostgreSQL for Database | Accepted | 2025-12-23 | High |
| [ADR-005](../adr/ADR-005-redis-cache.md) | Redis for Caching | Accepted | 2025-12-23 | Medium |
| [ADR-006](../adr/ADR-006-azure-aks.md) | Azure Kubernetes Service (AKS) | Accepted | 2025-12-23 | High |
| [ADR-007](../adr/ADR-007-no-auth-mvp.md) | No Authentication in MVP | Accepted | 2025-12-23 | Medium |
| [ADR-008](../adr/ADR-008-hard-delete.md) | Hard Delete vs Soft Delete | Accepted | 2025-12-23 | Low |

---

### How to Create New ADRs

1. Copy the ADR template: `cp adr/ADR-000-template.md adr/ADR-009-new-decision.md`
2. Fill in the ADR sections (Context, Decision, Rationale, Consequences, Alternatives)
3. Update the ADR Summary Table above
4. Reference the ADR in code comments or documentation where relevant

**For detailed ADR guidelines**, see [ADR_GUIDE.md](../adr/ADR_GUIDE.md).
