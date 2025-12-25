# ADR-008: Hard Delete vs Soft Delete

**Status**: Accepted
**Date**: 2025-12-23
**Authors**: Architecture Team
**Related**: [ADR-004](ADR-004-postgresql-database.md)

---

## Context

### Problem Statement

We need to decide how to implement the "Delete Task" feature:

**Option 1: Hard Delete**
- Permanently remove task record from database (`DELETE FROM tasks WHERE id = ?`)
- Task is unrecoverable after deletion

**Option 2: Soft Delete**
- Mark task as deleted without removing from database (`UPDATE tasks SET deleted_at = NOW() WHERE id = ?`)
- Task remains in database but hidden from UI
- Can be recovered or permanently deleted later

### Requirements

**Functional Requirements:**
- User can delete tasks from their list
- Deleted tasks disappear from UI

**Product Owner Guidance** (from PRODUCT_OWNER_SPEC.md):
> "Deletion is permanent (no 'undo' in MVP - consider for future phase)"

**Non-Functional Requirements:**
- **Simplicity**: Minimize complexity for MVP
- **Data Integrity**: Ensure no orphaned records
- **Performance**: Delete operation <300ms (p95)

---

## Decision

### Summary

We will implement **Hard Delete** in the MVP. Tasks will be permanently removed from the database when deleted, with no recovery option.

### Detailed Decision

**Implementation:**
- DELETE operation executes SQL: `DELETE FROM tasks WHERE id = ?`
- Task record is permanently removed from PostgreSQL database
- No deleted_at column in tasks table
- No "undo" or "restore" functionality

**User Experience:**
- Optional confirmation dialog: "Are you sure you want to delete this task?"
- After deletion, task immediately disappears from UI
- **No undo** - deletion is permanent (communicated to user)

**Future (Phase 2 - If Needed):**
- Can add soft delete if user feedback indicates need for "undo" feature
- Requires database migration: `ALTER TABLE tasks ADD COLUMN deleted_at TIMESTAMP`

---

## Rationale

### Primary Drivers

**1. Product Owner Guidance**
- **Description**: Product Owner explicitly stated "Deletion is permanent (no 'undo' in MVP)"
- **Impact**: Aligns with product vision, no soft delete required for MVP
- **Evidence**: PRODUCT_OWNER_SPEC.md Section 2.3 Use Case 4: Delete Task

**2. Simplicity**
- **Description**: Hard delete requires no additional schema columns or query filtering
- **Impact**: Simpler code, fewer bugs, faster development
- **Evidence**: Soft delete adds ~20% more code (deleted_at column, query filters, recovery logic)

**3. Database Storage Efficiency**
- **Description**: Hard delete frees up storage immediately
- **Impact**: Lower storage costs, faster queries (no deleted records in table)
- **Evidence**: PostgreSQL VACUUM reclaims space after hard delete

**4. No Regulatory Requirement**
- **Description**: To-Do List application has no compliance requirement for audit trails
- **Impact**: No legal obligation to retain deleted data
- **Evidence**: No GDPR/HIPAA/SOX requirements for task data

### Comparison Summary

| Criteria | Hard Delete (Selected) | Soft Delete |
|----------|----------------------|-------------|
| **Simplicity** | ✅ Simple (DELETE query) | ⚠️ Complex (UPDATE + filter WHERE deleted_at IS NULL) |
| **Code Complexity** | ✅ Low | ⚠️ Medium (+20% code) |
| **Storage Efficiency** | ✅ High (freed immediately) | ❌ Low (grows indefinitely) |
| **Recovery** | ❌ Not possible | ✅ Possible (restore deleted_at = NULL) |
| **Audit Trail** | ❌ No | ✅ Yes (deletion timestamp) |
| **Query Performance** | ✅ Faster (no deleted records) | ⚠️ Slower (filter WHERE deleted_at IS NULL) |
| **Undo Feature** | ❌ Not possible | ✅ Possible |

---

## Consequences

### Positive Consequences

1. **Simpler Code**
   - Single DELETE query, no UPDATE + filter logic
   - 20% less code than soft delete
   - Fewer potential bugs

2. **Better Performance**
   - Queries don't need WHERE deleted_at IS NULL filter
   - Table scan excludes deleted records
   - VACUUM reclaims storage immediately

3. **Storage Efficiency**
   - Deleted tasks don't consume database storage
   - Lower storage costs

4. **Faster Development**
   - No need to implement soft delete logic (deleted_at column, filters, recovery UI)
   - Saves ~1-2 days of development time

### Negative Consequences

1. **No Undo/Recovery**
   - **Risk**: User accidentally deletes task, cannot recover
   - **Mitigation**: Add confirmation dialog ("Are you sure?")
   - **Severity**: Medium (user feedback may require Phase 2 soft delete)

2. **No Audit Trail**
   - **Risk**: Cannot track who deleted what and when
   - **Mitigation**: Not required for MVP (no compliance requirements)
   - **Severity**: Low (acceptable for MVP)

3. **Future Migration Complexity**
   - **Risk**: Adding soft delete in Phase 2 requires database migration
   - **Mitigation**: Acceptable trade-off for faster MVP delivery
   - **Severity**: Low (planned refactoring)

### Trade-offs

- **Simplicity vs Recovery**: Prioritize simple MVP over undo feature
- **Storage Efficiency vs Audit Trail**: Accept no audit trail for storage savings
- **Time-to-Market vs Future Rework**: Accept Phase 2 migration cost for faster MVP

---

## Alternatives Considered

### Alternative 1: Soft Delete

**Description:**
Add deleted_at TIMESTAMP column, mark tasks as deleted instead of removing them.

**Schema:**
```sql
ALTER TABLE tasks ADD COLUMN deleted_at TIMESTAMP DEFAULT NULL;
CREATE INDEX idx_tasks_deleted_at ON tasks(deleted_at);

-- Delete operation
UPDATE tasks SET deleted_at = NOW() WHERE id = ?;

-- Queries must filter out deleted tasks
SELECT * FROM tasks WHERE deleted_at IS NULL;
```

**Why Considered:**
- Allows undo/recovery feature
- Provides audit trail (who deleted what and when)
- Common pattern in enterprise applications

**Why Rejected:**
- **Product Owner Decision**: Explicitly stated "no undo in MVP"
- **Complexity**: Requires WHERE deleted_at IS NULL in all queries
- **Storage**: Deleted tasks consume database storage indefinitely
- **Performance**: Queries slower due to additional filter

**Use Case:**
Soft delete would be appropriate for applications with:
- Compliance requirements (audit trails)
- Critical data that must never be lost
- Undo/restore features
- Multi-user collaboration (track who deleted)

---

### Alternative 2: Hybrid Approach (Soft Delete + Purge)

**Description:**
Soft delete tasks initially, then permanently purge after 30 days.

**Implementation:**
```sql
-- Soft delete on user action
UPDATE tasks SET deleted_at = NOW() WHERE id = ?;

-- Scheduled purge job (cron)
DELETE FROM tasks WHERE deleted_at < NOW() - INTERVAL '30 days';
```

**Why Considered:**
- 30-day recovery window
- Eventual storage cleanup
- Best of both worlds

**Why Rejected:**
- **Over-Engineered for MVP**: Adds complexity (scheduled purge job, cron setup)
- **Product Owner Decision**: No undo required in MVP
- **Operational Overhead**: Requires scheduled job monitoring

**Use Case:**
Hybrid approach would be appropriate for:
- Applications with short-term undo requirements (email "deleted items")
- GDPR compliance (right to deletion after 30 days)

---

## User Communication

Since hard delete is permanent, we must clearly communicate this to users:

**Option 1: Confirmation Dialog (Recommended)**
```
Are you sure you want to delete this task?
This action cannot be undone.

[Cancel] [Delete]
```

**Option 2: Disclaimer (On Delete Button Hover)**
```
Delete (permanent - cannot be undone)
```

**Option 3: Warning Banner (First Time)**
```
⚠️ Deleted tasks cannot be recovered. Are you sure?
[ ] Don't show this again
[Cancel] [Delete]
```

**Recommended**: Option 1 (Confirmation Dialog) for best UX

---

## Phase 2 Migration Plan (If Needed)

If user feedback indicates need for undo feature in Phase 2:

**Database Migration:**
```sql
-- Add deleted_at column
ALTER TABLE tasks ADD COLUMN deleted_at TIMESTAMP DEFAULT NULL;
CREATE INDEX idx_tasks_deleted_at ON tasks(deleted_at);

-- Update all existing queries
-- OLD: SELECT * FROM tasks
-- NEW: SELECT * FROM tasks WHERE deleted_at IS NULL
```

**Code Changes:**
- Update TaskRepository queries to filter WHERE deleted_at IS NULL
- Change TaskService.deleteTask() from hard delete to soft delete
- Add TaskService.restoreTask() method for undo
- Add "Restore" button in UI for deleted tasks
- Add scheduled purge job (optional)

**Estimated Effort**: 1-2 weeks for soft delete implementation

---

## References

### Documentation
- [PRODUCT_OWNER_SPEC.md Use Case 4: Delete Task](/home/shadowx4fox/todo-list-example/PRODUCT_OWNER_SPEC.md#use-case-4-delete-task)
- [ARCHITECTURE.md Section 5: Component Details](/home/shadowx4fox/todo-list-example/ARCHITECTURE.md#5-component-details)

### Research & Analysis
- [PostgreSQL DELETE Documentation](https://www.postgresql.org/docs/15/sql-delete.html)
- [Soft Delete Pattern](https://martinfowler.com/eaaDev/TombstonePattern.html)

---

**Last Updated**: 2025-12-23
**Status**: ✅ Accepted
**Next Review**: Phase 2 planning (if user feedback indicates need for undo)