[Architecture](../ARCHITECTURE.md) > System Overview

# 1. Executive Summary & 2. System Overview

**System**: 3-Tier To-Do List Application
**Version**: 1.0.0
**Date**: 2025-12-23

---

## 1. Executive Summary

### System Purpose

The **3-Tier To-Do List Application** is a cloud-native web application that enables users to manage their daily tasks through a simple, reliable, and persistent task management interface. The system is designed to handle concurrent users with consistent performance while ensuring zero data loss through robust database persistence.

### Key Metrics

**Design Capacity:**
- **Average Read TPS**: 20 transactions/second (task list retrieval, task queries)
- **Average Write TPS**: 10 transactions/second (create, update, delete operations)
- **Measurement Period**: Average expected load in production

**Performance Targets:**
- Add Task: <500ms (p95)
- Display Task List: <1000ms (p95)
- Mark Complete/Delete: <300ms (p95)

**System Availability:**
- Target SLA: 99.9% uptime (8.76 hours/year planned downtime)
- Deployment: Azure Kubernetes Service (AKS)

### Business Value

**Primary Benefits:**
- **Zero Data Loss**: All tasks persisted to database with ACID guarantees
- **Fast Response Times**: Sub-second operations for all core features
- **Scalable Architecture**: Cloud-native design supports growth from 100 to 10,000+ users
- **Simple User Experience**: 4 essential features eliminate complexity

**Target Users:**
- **Productive Professionals** (Primary): Daily task management for work and personal life
- **General Users** (Secondary): Simple task tracking without feature bloat

### Architecture Highlights

**Architecture Type**: 3-Tier Classic Web Application

**Core Technologies:**
- **Presentation Tier**: Angular (web UI)
- **Application Tier**: Spring Boot (REST API + business logic) with Redis cache
- **Data Tier**: PostgreSQL (relational database)
- **Infrastructure**: Azure Kubernetes Service (AKS)

**Key Design Decisions:**
- 3-tier architecture for clear separation of concerns
- Cache layer in Application tier for read performance (20 TPS target)
- RESTful API design for frontend-backend communication
- Stateless Application tier for horizontal scalability

---

## 2. System Overview

### 2.1 Problem Statement

**Current State:**
- Users currently lack a reliable, persistent task management solution for organizing their daily to-do items
- Existing solutions may not meet performance requirements for concurrent users
- Manual task tracking on paper/notes leads to lost tasks and forgotten items
- Users need a cloud-based solution accessible from anywhere

**Pain Points:**
- Tasks get lost when using sticky notes or paper lists
- Complex task management tools have too many features and steep learning curves
- No guarantee of data persistence across browser sessions or device changes
- Inconsistent performance under concurrent user load

**Desired Future State:**
- Users have access to a fast, reliable web-based to-do list application
- All task data is persisted and never lost
- System can scale to handle concurrent users with consistent performance
- Simple interface with only essential features (add, display, complete, delete)

### 2.2 Solution Overview

The **3-Tier To-Do List Application** solves these problems through a cloud-native architecture deployed on Azure AKS with three distinct tiers:

**Tier 1: Presentation Layer**
- Angular-based responsive web UI
- Client-side validation and state management
- RESTful API consumption

**Tier 2: Application/Business Logic Layer**
- Spring Boot REST API services
- Business logic for task CRUD operations
- Redis cache for read performance optimization
- Stateless design for horizontal scalability

**Tier 3: Data Layer**
- PostgreSQL database for persistent task storage
- ACID-compliant transactions for data integrity
- Automated backups for disaster recovery

**Key Differentiators:**
- **Simplicity**: Only 4 essential features, no feature bloat
- **Performance**: Cache-optimized for 20 TPS read operations
- **Reliability**: Zero data loss with database persistence
- **Scalability**: Cloud-native deployment on Azure AKS

### 2.3 Primary Use Cases

#### Use Case 1: Add New Task

**Actors**: User (web application user)

**Description**: User creates a new to-do item by entering text and adding it to their list.

**Primary Flow**:
1. User navigates to the to-do list application
2. User enters task description in text input field (max 500 characters)
3. User clicks "Add" button or presses Enter
4. System validates input (non-empty, sanitized for XSS)
5. System creates new task in database with status = "incomplete"
6. System invalidates cache
7. System returns success response
8. UI refreshes task list display with new task

**Success Metrics:**
- Task creation success rate: >99.5%
- Task creation response time: <500ms (p95)
- Zero data loss incidents

#### Use Case 2: Display All Tasks

**Actors**: User (web application user)

**Description**: User views their complete list of tasks, including both incomplete and completed items.

**Primary Flow**:
1. User navigates to the to-do list application or refreshes page
2. System checks Redis cache for task list
3. If cache hit: Return cached task list (80% of requests)
4. If cache miss: Query PostgreSQL database, populate cache, return results
5. System renders tasks in list format with status indicators

**Success Metrics:**
- List load time: <1000ms (p95)
- Cache hit rate: >80%
- Data accuracy: 100% (list reflects database state)

#### Use Case 3: Mark Task Complete/Incomplete

**Actors**: User (web application user)

**Description**: User toggles task status between "complete" and "incomplete" to track progress.

**Primary Flow**:
1. User views task list with tasks displayed
2. User clicks checkbox next to a task
3. System sends PATCH request to backend API with task ID
4. Backend updates task status in PostgreSQL (toggle)
5. Backend invalidates cache entry
6. Backend returns success response
7. UI updates task display (strikethrough, checkmark icon)

**Success Metrics:**
- Update success rate: >99.5%
- Update response time: <300ms (p95)
- UI responsiveness: Immediate visual feedback (<100ms)

#### Use Case 4: Delete Task

**Actors**: User (web application user)

**Description**: User permanently removes a task from their list.

**Primary Flow**:
1. User views task list
2. User clicks "Delete" button next to a task
3. System displays confirmation dialog (optional - UX decision)
4. User confirms deletion
5. System sends DELETE request to backend API with task ID
6. Backend deletes task from PostgreSQL database (hard delete)
7. Backend invalidates cache
8. Backend returns success response
9. UI removes task from displayed list

**Success Metrics:**
- Delete success rate: >99.5%
- Delete response time: <300ms (p95)
- Zero accidental deletions (if confirmation enabled)
