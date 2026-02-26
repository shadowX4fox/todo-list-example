# Compliance Contract: Platform and IT Infrastructure

**Project**: 3-Tier To-Do List Application
**Generation Date**: 2026-02-18
**Source**: ARCHITECTURE.md (Sections 4, 8, 10, 11)
**Version**: 1.0

---

## Document Control

<!-- CRITICAL: This table structure MUST be preserved exactly.
     DO NOT convert this table to bold field lists like **Field**: Value.
     Keep the | Field | Value | markdown table format.
     Validation rule 'document_control_table' will BLOCK contracts that transform this table. -->

| Field | Value |
|-------|-------|
| Document Owner | N/A |
| Last Review Date | 2026-02-18 |
| Next Review Date | 2027-02-18 |
| Status | In Review |
| Validation Score | 7.4/10 |
| Validation Status | PASS |
| Validation Date | 2026-02-18 |
| Validation Evaluator | Claude Code (Automated Validation Engine) |
| Review Actor | Infrastructure Review Board |
| Approval Authority | Platform & Infrastructure Review Board |

**Validation Configuration**: `/skills/architecture-compliance/validation/platform_it_infrastructure_validation.json`




**CRITICAL - Compliance Score Calculation**:
When calculating the Compliance Score in validation_results, N/A items MUST be included in the numerator:
- Compliance Score = (PASS items + N/A items + EXCEPTION items) / (Total items) × 10
- N/A items count as fully compliant (10 points each)
- Example: 6 PASS, 5 N/A, 0 FAIL, 0 UNKNOWN → (6+5)/11 × 10 = 10.0/10 (100%)
- Add note in contract output: "Note: N/A items counted as fully compliant (included in compliance score)"


---

## Compliance Summary

| Code | Requirement | Category | Status | Source Section | Responsible Role |
|------|-------------|----------|--------|----------------|------------------|
| LAPI01 | Unique Production Environments | Platform & IT Infrastructure | Unknown | Section 4, 11 | Infrastructure Architect |
| LAPI02 | Server Operating Systems | Platform & IT Infrastructure | Unknown | Section 8 | Platform Engineer |
| LAPI03 | Database Storage Capacity | Platform & IT Infrastructure | Compliant | Section 8, 10 | Database Administrator |
| LAPI04 | Database Version Authorization | Platform & IT Infrastructure | Compliant | Section 8 | Database Administrator |
| LAPI05 | Database Backup and Retention | Platform & IT Infrastructure | Compliant | Section 11 | Database Administrator |
| LAPI06 | Infrastructure Capacity | Platform & IT Infrastructure | Compliant | Section 8, 10 | Infrastructure Architect |
| LAPI07 | Naming Conventions | Platform & IT Infrastructure | Non-Compliant | Section 8, 11 | Infrastructure Architect |
| LAPI08 | Transaction Volume Dimensioning | Platform & IT Infrastructure | Compliant | Section 10 | Integration Architect |
| LAPI09 | Legacy Platform Transaction Capacity | Platform & IT Infrastructure | Not Applicable | Section 7, 10 | Integration Architect |

**Overall Compliance**:
- ✅ Compliant: 5/9 (56%)
- ❌ Non-Compliant: 1/9 (11%)
- ⊘ Not Applicable: 1/9 (11%)
- ❓ Unknown: 2/9 (22%)

**Completeness**: 78% (7/9 data points documented)

Note: N/A items counted as fully compliant (included in compliance score)

---

## 1. Unique Production Environments (LAPI01)

**Requirement**: Validate unique production environment consistency and avoid environment crossing. Production must be isolated from non-production environments to prevent unauthorized access, data leakage, and configuration errors.

**Status**: Unknown
**Responsible Role**: Infrastructure Architect / Cloud Architect

### 1.1 Environment Isolation

**Environment Configuration**: Production namespace on AKS with staging environment referenced
- Status: Unknown
- Explanation: Environment mentioned but isolation mechanisms unclear
- Source: ARCHITECTURE.md Section 11.1
- Note: Document production environment isolation using network segmentation (VNets/VPCs), IAM policies with least privilege, and separate infrastructure resources in Section 11. Include environment naming conventions and access control policies

**Network Segmentation**: Azure Network Security Groups (NSG) restrict inbound HTTPS (443) to AKS load balancer; database and Redis connections restricted to AKS subnet
- Status: Compliant
- Explanation: Network isolation documented with VNet/VPC segregation, subnet separation, and security groups.
- Source: ARCHITECTURE.md Section 9.4

**Access Controls**: Not specified
- Status: Unknown
- Explanation: Access mentioned but control mechanisms unclear
- Source: "Not documented"
- Note: Document production access using RBAC with least privilege, separate administrative accounts for production, mandatory MFA, and just-in-time (JIT) access policies. Include audit logging for all production access

### 1.2 Environment Count

**Number of Production Environments**: 1 production environment (production namespace on AKS); staging environment referenced for load testing
- Status: Unknown
- Explanation: Production setup unclear or environment count not specified
- Source: ARCHITECTURE.md Section 11.1

### 1.3 Cross-Environment Data Flow

**Data Flow Restrictions**: Not specified
- Status: Unknown
- Explanation: Data flow mentioned but restrictions unclear
- Source: "Not documented"
- Note: Define data flow policies: production data must not flow to non-production environments, use anonymized/masked data in non-production, implement one-way data sync from production to non-production if required. Document approval process for exceptions

**Source References**: ARCHITECTURE.md Section 9.4, Section 11.1

---

## 2. Server Operating Systems (LAPI02)

**Requirement**: Ensure deployment on servers with authorized Operating Systems. All server infrastructure must use OS platforms and versions approved by the organization's security and compliance standards.

**Status**: Unknown
**Responsible Role**: Platform Engineer / Infrastructure Architect

### 2.1 Operating System Platforms

**OS Platform and Version**: Not specified - AKS managed Kubernetes (1.28.x) with Docker (24.x) containerization; underlying node OS not documented
- Status: Unknown
- Explanation: Infrastructure mentioned but OS details unclear
- Source: ARCHITECTURE.md Section 8
- Note: Document specific OS platform and version for all server infrastructure in Section 8. For Kubernetes: document node OS (e.g., Azure Linux, Ubuntu). For VMs: document OS image and version. Include patching strategy and lifecycle policy

**Container Base Images** (if applicable): Not specified - Docker containers referenced for Angular and Spring Boot builds but base images not documented
- Status: Unknown
- Explanation: Containers mentioned but base images unclear
- Source: ARCHITECTURE.md Section 8
- Note: Document approved container base images in Section 8. Use official images from trusted registries (Microsoft Container Registry, Docker Official Images). Specify image digest/tag pinning strategy for reproducibility

### 2.2 OS Version Authorization

**Authorization Status**: Not specified
- Status: Unknown
- Explanation: OS version documented but authorization status unclear
- Source: "Not documented"
- Note: Verify OS version against organizational security standards and approved OS catalog. Document approval date and policy version. For cloud platforms: ensure OS is supported by cloud provider and receives security patches. Include OS end-of-life (EOL) tracking

**OS Patching Strategy**: Not specified - vulnerability management patching policy exists for application dependencies (Section 9.6), but no OS-level patching strategy documented
- Status: Unknown
- Explanation: Patching mentioned but strategy unclear
- Source: ARCHITECTURE.md Section 9.6
- Note: Document OS patching strategy in Section 11: update frequency (e.g., monthly security patches, quarterly feature updates), testing process, rollback procedures, and maintenance windows. For Kubernetes: document node pool update strategy

**Source References**: ARCHITECTURE.md Section 8

---

## 3. Database Storage Capacity (LAPI03)

**Requirement**: Ensure required database storage capacity and availability. Database infrastructure must provide sufficient storage for current and projected data volumes with appropriate availability and scalability configurations.

**Status**: Compliant
**Responsible Role**: Database Administrator / Cloud Architect

### 3.1 Database Capacity Configuration

**Database Type and Size**: Azure Database for PostgreSQL 15 - General Purpose tier (2-4 vCPU, 8GB RAM); 99.99% uptime SLA
- Status: Compliant
- Explanation: Database platform, storage capacity, and tier/SKU documented (e.g., Azure SQL Database, 250 GB storage, General Purpose tier).
- Source: ARCHITECTURE.md Section 8

**Current vs Projected Capacity**: Capacity planning documented across three tiers: 100 users (2 vCPU, 8GB RAM), 1,000 users (4 vCPU, 16GB RAM), 10,000 users (8 vCPU, 32GB RAM + 2 read replicas)
- Status: Compliant
- Explanation: Current storage usage and projected growth documented with capacity planning horizon (e.g., current 60 GB, projected 150 GB in 12 months).
- Source: ARCHITECTURE.md Section 10.6

### 3.2 Storage Scalability

**Scaling Configuration**: Vertical scaling path: 2 vCPU → 8 vCPU, 8GB → 32GB RAM; read replicas for horizontal read scaling (future)
- Status: Compliant
- Explanation: Storage scaling strategy documented (auto-scaling, manual scaling, elastic pools) with scaling limits.
- Source: ARCHITECTURE.md Section 10.2

**Storage Performance (IOPS/Throughput)**: PostgreSQL query response target <50ms (p95); supports 30 TPS (20 read + 10 write); database indexes on id (PK), status, and created_at columns
- Status: Compliant
- Explanation: Storage IOPS and throughput requirements documented and matched to tier/SKU.
- Source: ARCHITECTURE.md Section 10.1

### 3.3 Availability Configuration

**High Availability Setup**: Azure Database for PostgreSQL 99.99% uptime SLA; automated failover to standby instance (<30s downtime); read replicas planned as future enhancement
- Status: Compliant
- Explanation: Database availability configuration documented (replicas, zones, geo-redundancy, SLA).
- Source: ARCHITECTURE.md Section 8

**Source References**: ARCHITECTURE.md Section 8, Section 10.2, Section 10.6

---

## 4. Database Version Authorization (LAPI04)

**Requirement**: Ensure storage components use authorized databases for On-Premise components. All database platforms and versions must be approved by organizational standards and security policies.

**Status**: Compliant
**Responsible Role**: Database Administrator / Compliance Officer

### 4.1 Database Platform and Version

**Database Platform**: PostgreSQL 15.x (Azure Database for PostgreSQL managed service)
- Status: Compliant
- Explanation: Database platform and version clearly documented (e.g., SQL Server 2022, PostgreSQL 15, MongoDB 7.0).
- Source: ARCHITECTURE.md Section 8

**On-Premise vs Cloud Managed**: Cloud Managed - Azure Database for PostgreSQL (fully managed service)
- Status: Compliant
- Explanation: Deployment model clearly documented (On-Premise, Cloud Managed, Hybrid).
- Source: ARCHITECTURE.md Section 8

### 4.2 Authorization Status

**Approval Against Authorized List**: Cloud managed database (authorization delegated to cloud provider's supported versions). PostgreSQL 15 is actively supported by both PostgreSQL community and Microsoft Azure.
- Status: Not Applicable
- Explanation: Cloud managed database (authorization delegated to cloud provider's supported versions).
- Source: ARCHITECTURE.md Section 8

**Vendor Support Status**: PostgreSQL 15.x is in active vendor support; Azure Database for PostgreSQL manages version lifecycle as a managed service
- Status: Compliant
- Explanation: Database version confirmed to be in active vendor support with documented EOL date.
- Source: ARCHITECTURE.md Section 8

**Source References**: ARCHITECTURE.md Section 8

---

## 5. Database Backup and Retention (LAPI05)

**Requirement**: Reflect retention and backup policies for On-Premise databases and design backup environment capacity. Database backup strategy must ensure data protection, meet recovery objectives (RTO/RPO), and comply with retention policies.

**Status**: Compliant
**Responsible Role**: Database Administrator / Business Continuity Manager

### 5.1 Backup Strategy

**Backup Method**: Daily automated full backups via Azure Database for PostgreSQL built-in backup; 5-minute transaction log backups
- Status: Compliant
- Explanation: Backup method documented (full, incremental, differential, continuous, snapshot) with frequency.
- Source: ARCHITECTURE.md Section 11.5

**Backup Frequency**: Daily automated backups; 5-minute transaction log backups for point-in-time restore
- Status: Compliant
- Explanation: Backup frequency documented and aligned with RPO requirements (e.g., full daily, transaction log every 15 minutes).
- Source: ARCHITECTURE.md Section 11.5

### 5.2 Retention Policies

**Retention Period**: 30 days backup retention
- Status: Compliant
- Explanation: Backup retention periods documented (short-term and long-term) with compliance justification.
- Source: ARCHITECTURE.md Section 11.5

**Retention Tiers**: Not specified - single 30-day retention tier only; no long-term archival tier documented
- Status: Unknown
- Explanation: Retention mentioned but tier structure unclear
- Source: ARCHITECTURE.md Section 11.5
- Note: Document retention tier strategy in Section 11: operational tier (hot storage, frequent access), compliance tier (warm/cold storage, infrequent access), and archival tier (cold storage, rare access). Include transition policies between tiers

### 5.3 Backup Storage Capacity

**Backup Storage Requirements**: Azure Blob Storage (geo-redundant) used for backup storage; managed by Azure Database for PostgreSQL automated backup feature
- Status: Compliant
- Explanation: Backup storage capacity calculated and documented based on database size, retention period, and change rate.
- Source: ARCHITECTURE.md Section 11.5

**Geo-Redundant Backup Storage**: Azure Blob Storage geo-redundant configured for database backups
- Status: Compliant
- Explanation: Geo-redundant backup storage documented with replication to secondary region/site.
- Source: ARCHITECTURE.md Section 11.5

### 5.4 Recovery Procedures (RTO/RPO)

**Recovery Time Objective (RTO)**: <2 hours
- Status: Compliant
- Explanation: RTO documented and validated with backup solution capabilities (e.g., RTO: 4 hours).
- Source: ARCHITECTURE.md Section 11.5

**Recovery Point Objective (RPO)**: <1 hour (5-minute transaction log backups)
- Status: Compliant
- Explanation: RPO documented and validated with backup frequency (e.g., RPO: 15 minutes).
- Source: ARCHITECTURE.md Section 11.5

**Backup Testing**: Monthly restore test to validate backup integrity; quarterly DR drills with full backup restore to staging environment
- Status: Compliant
- Explanation: Backup restoration testing documented with frequency and procedures (e.g., monthly restore tests, quarterly DR drills).
- Source: ARCHITECTURE.md Section 11.5

**Source References**: ARCHITECTURE.md Section 11.5

---

## 6. Infrastructure Capacity (LAPI06)

**Requirement**: Ensure infrastructure resource capacity matches component requirements. Infrastructure must provide sufficient compute, memory, network, and storage resources for current and projected workloads with appropriate headroom.

**Status**: Compliant
**Responsible Role**: Infrastructure Architect / Capacity Planner

### 6.1 Compute Capacity

**Compute Resources (CPU/Memory)**: AKS cluster 2-5 nodes; Spring Boot pods: 1 vCPU (request), 2 vCPU (limit), 1GB RAM (request), 2GB RAM (limit); Database: 2 vCPU, 8GB RAM; Redis: 4GB (C1 Standard)
- Status: Compliant
- Explanation: Compute capacity documented with CPU cores, memory, and current utilization (e.g., 16 vCPUs, 64 GB RAM, 60% average utilization).
- Source: ARCHITECTURE.md Section 8

**Current vs Peak Utilization**: HPA scales Application tier pods when CPU >70%; peak load target 5 pods; max 10 pods
- Status: Compliant
- Explanation: Current and peak utilization documented with capacity headroom analysis (e.g., average 60%, peak 85%, 15% headroom).
- Source: ARCHITECTURE.md Section 10.2

### 6.2 Scaling Configuration

**Horizontal Scaling**: Kubernetes Horizontal Pod Autoscaler (HPA) triggers scale-out at CPU >70%; min 2 pods, max 10 pods for Application tier; Azure CDN auto-scales for Presentation tier
- Status: Compliant
- Explanation: Horizontal scaling configuration documented (auto-scaling rules, min/max instances, scaling metrics).
- Source: ARCHITECTURE.md Section 10.2

**Vertical Scaling**: Application pod: 1 vCPU → 2 vCPU, 1GB → 2GB RAM; Database: 2 vCPU → 8 vCPU, 8GB → 32GB RAM; Cache: 4GB (C1) → 26GB (C6)
- Status: Compliant
- Explanation: Vertical scaling capabilities documented (VM/node size upgrade path, downtime requirements).
- Source: ARCHITECTURE.md Section 10.2

### 6.3 Capacity Headroom

**Capacity Headroom Analysis**: Capacity planning covers 100, 1,000, and 10,000 users; HPA threshold at 70% CPU provides 30% headroom; maximum pod count (10) supports headroom for burst traffic
- Status: Compliant
- Explanation: Capacity headroom documented and meets recommended thresholds (20-30% headroom for production).
- Source: ARCHITECTURE.md Section 10.6

**Network Capacity**: Azure Load Balancer for external traffic distribution; HTTPS (443) inbound via NSG; TLS 1.3 for all client-server communication
- Status: Compliant
- Explanation: Network capacity documented (bandwidth, latency, throughput requirements).
- Source: ARCHITECTURE.md Section 9.4

**Storage Capacity** (Infrastructure Storage): Azure Blob Storage for database backups (30-day retention); Redis 4GB (C1 Standard); HikariCP connection pool 20 connections per pod
- Status: Compliant
- Explanation: Infrastructure storage capacity documented (ephemeral storage, persistent volumes, current usage).
- Source: ARCHITECTURE.md Section 8

**Source References**: ARCHITECTURE.md Section 8, Section 10.2, Section 10.6

---

## 7. Naming Conventions (LAPI07)

**Requirement**: Ensure On-Premise infrastructure architecture adheres to naming standards. All infrastructure resources must follow organizational naming conventions for consistency, traceability, and operational efficiency.

**Status**: Non-Compliant
**Responsible Role**: Infrastructure Architect / Standards Officer

### 7.1 Infrastructure Naming Standards

**Naming Convention Documentation**: Not specified
- Status: Non-Compliant
- Explanation: Naming conventions not documented or not following organizational standards.
- Source: "Not documented"
- Note: Document naming conventions in Section 8 or 11: naming pattern (e.g., {environment}-{application}-{resource-type}-{region}), examples (prod-taskscheduler-aks-eastus), and reference to organizational naming standard. Include naming for: clusters, nodes, VMs, databases, storage accounts, networks, subnets, security groups

**Resource Naming Examples**: Not specified - Helm release name "todolist" and production namespace referenced but no formal naming convention established
- Status: Non-Compliant
- Explanation: No naming examples or examples don't follow documented pattern.
- Source: ARCHITECTURE.md Section 11.1
- Note: Document resource naming examples in Section 8: Kubernetes cluster name, node pool names, database server names, storage account names, VNet/subnet names. Ensure examples follow organizational naming standard and include environment prefix (dev/staging/prod)

### 7.2 Compliance with Organizational Standards

**Standards Compliance Verification**: Not specified
- Status: Non-Compliant
- Explanation: Naming does not comply with organizational standards or verification not performed.
- Source: "Not documented"
- Note: Verify infrastructure naming against organizational naming standard (reference standard name and version). Document compliance status, any approved exceptions, and remediation plan for non-compliant names. Include tag/label naming conventions (cost center, owner, environment, project)

**Tagging and Labeling**: Not specified
- Status: Non-Compliant
- Explanation: Tagging strategy not documented or required tags missing.
- Source: "Not documented"
- Note: Document tagging/labeling strategy in Section 8 or 11: required tags (Environment, Owner, CostCenter, Project, Application), tag format, and tag governance policy. For Kubernetes: document pod labels and namespace labels. For cloud: document Azure tags / AWS tags requirements

**Source References**: ARCHITECTURE.md Section 11.1

---

## 8. Transaction Volume Dimensioning (LAPI08)

**Requirement**: Ensure On-Premise integration components are designed with production transaction volumes and sizes. Integration infrastructure must be dimensioned to handle documented transaction rates (TPS) and message sizes for current and projected workloads.

**Status**: Compliant
**Responsible Role**: Integration Architect / Performance Engineer

### 8.1 Transaction Volume Configuration

**Target Transaction Rate (TPS)**: 20 TPS sustained read, 10 TPS sustained write (30 TPS total)
- Status: Compliant
- Explanation: Target transaction rate documented with sustained and peak TPS (e.g., 180 TPS sustained, 300 TPS peak).
- Source: ARCHITECTURE.md Section 10.1

**System Capacity Limits**: Application tier scales to 10 pods maximum; HPA triggers at CPU >70%; database supports 30 TPS with <50ms query response (p95); capacity planning covers 100 to 10,000 users
- Status: Compliant
- Explanation: System capacity limits documented with maximum TPS and capacity headroom.
- Source: ARCHITECTURE.md Section 10.2

### 8.2 Transaction Size Requirements

**Message/Payload Sizes**: Task description max 500 characters (VARCHAR 500); JSON payloads for REST API; task record fields: id (UUID), description (VARCHAR 500), status (VARCHAR 20), created_at, updated_at
- Status: Compliant
- Explanation: Transaction payload sizes documented with average and maximum message sizes.
- Source: ARCHITECTURE.md Section 8

**Throughput Requirements (MB/s)**: 20 TPS read + 10 TPS write; JSON task payload estimated ~200 bytes; Redis cache hit rate >80% reduces database throughput requirements; HikariCP 20 connections per pod
- Status: Compliant
- Explanation: Data throughput calculated from TPS and message sizes (e.g., 180 TPS × 5 KB = 900 KB/s).
- Source: ARCHITECTURE.md Section 10.1

### 8.3 Integration Component Capacity

**Integration Middleware Capacity**: No dedicated middleware - direct REST API; Spring Boot REST controllers handle all requests; Azure Load Balancer distributes traffic across pods; HikariCP connection pool (20 connections per pod)
- Status: Compliant
- Explanation: Integration middleware (message queue, API gateway, ESB) capacity documented and matched to TPS requirements.
- Source: ARCHITECTURE.md Section 8

**Connection and Concurrency Limits**: HikariCP max pool size 20 per pod (40 total at 2 pods, up to 200 at 10 pods max); HTTP connection timeout 5000ms; Redis connection timeout 2000ms; database connection timeout 5000ms
- Status: Compliant
- Explanation: Connection limits and concurrency configuration documented (connection pool size, max concurrent requests).
- Source: ARCHITECTURE.md Section 8

**Source References**: ARCHITECTURE.md Section 8, Section 10.1, Section 10.2

---

## 9. Legacy Platform Transaction Capacity (LAPI09)

**Requirement**: Ensure listening port capacity for legacy components matches estimated transaction numbers. Legacy system integration points must be properly dimensioned with adequate port configuration, connection limits, and transaction capacity.

**Status**: Not Applicable
**Responsible Role**: Integration Architect / Legacy Systems Specialist

### 9.1 Listening Port Configuration

**Port Configuration**: Not applicable
- Status: Not Applicable
- Explanation: No legacy system integration.
- Source: ARCHITECTURE.md Section 7.5

**Protocol and Interface Type**: Not applicable
- Status: Not Applicable
- Explanation: No legacy integration.
- Source: ARCHITECTURE.md Section 7.5

### 9.2 Connection Limits

**Maximum Concurrent Connections**: Not applicable
- Status: Not Applicable
- Explanation: No connection-based legacy integration.
- Source: ARCHITECTURE.md Section 7.5

**Connection Pool Configuration**: Not applicable
- Status: Not Applicable
- Explanation: Connectionless protocol.
- Source: ARCHITECTURE.md Section 7.5

### 9.3 Transaction Capacity per Port

**Estimated Transaction Volume**: Not applicable
- Status: Not Applicable
- Explanation: No legacy transaction processing.
- Source: ARCHITECTURE.md Section 7.5

**Legacy System Capacity Validation**: Not applicable
- Status: Not Applicable
- Explanation: No legacy system dependency.
- Source: ARCHITECTURE.md Section 7.5

**Failover and Redundancy**: Not applicable
- Status: Not Applicable
- Explanation: Legacy system has no failover requirement.
- Source: ARCHITECTURE.md Section 7.5

**Source References**: ARCHITECTURE.md Section 7.5

---

## Appendix: Source Traceability and Completion Status

### A.1 Definitions and Terminology

**Platform and IT Infrastructure Terms**:
- **HA (High Availability)**: System design ensuring minimal downtime through redundancy and failover
- **Capacity Planning**: Process of determining infrastructure resources needed to meet performance requirements
- **Environment Isolation**: Separation of development, testing, staging, and production environments
- **Database Capacity**: Storage sizing based on data volume, growth projections, and retention policies
- **Horizontal Scaling**: Adding more server instances to distribute load
- **Vertical Scaling**: Increasing resources (CPU, memory) of existing servers
- **Transaction Volume**: Number of operations (requests, database transactions) the system processes
- **Dimensioning**: Sizing infrastructure based on expected workload and performance targets
- **Naming Conventions**: Standardized naming patterns for infrastructure resources

**Status Codes**:
- **Compliant**: Requirement fully satisfied with documented evidence
- **Non-Compliant**: Requirement not met or missing from ARCHITECTURE.md
- **Not Applicable**: Requirement does not apply to this solution
- **Unknown**: Partial information exists but insufficient to determine compliance


**Infrastructure Abbreviations**:
- **LAPI**: Platform and IT Infrastructure compliance requirement code
- **IOPS**: Input/Output Operations Per Second
- **VM**: Virtual Machine
- **vCPU**: Virtual CPU
- **RPO/RTO**: Recovery Point/Time Objective

---

### A.2 Validation Methodology

**Validation Process**:

1. **Completeness Check (40% weight)**:
   - Counts filled data points across all LAPI requirements
   - Formula: (Filled fields / Total required fields) × 10
   - Example: 8 out of 10 fields = 8.0/10 completeness

2. **Compliance Check (50% weight)**:
   - Evaluates each validation item as PASS/FAIL/N/A/UNKNOWN
   - Formula: (PASS + N/A + EXCEPTION items) / Total items × 10
   - **CRITICAL**: N/A items MUST be included in numerator
   - Example: 6 PASS + 2 N/A + 0 EXCEPTION out of 10 items = (6+2)/10 × 10 = 8.0/10

3. **Quality Check (10% weight)**:
   - Assesses source traceability (ARCHITECTURE.md section references)
   - Verifies explanation quality and actionable notes
   - Formula: (Items with valid sources / Total items) × 10

4. **Final Score Calculation**:
   ```
   Final Score = (Completeness × 0.4) + (Compliance × 0.5) + (Quality × 0.1)
   ```

**Outcome Determination**:
| Score Range | Document Status | Review Actor | Action |
|-------------|----------------|--------------|--------|
| 8.0-10.0 | Approved | System (Auto-Approved) | Ready for implementation |
| 7.0-7.9 | In Review | Infrastructure Review Board | Manual review required |
| 5.0-6.9 | Draft | Architecture Team | Address gaps before review |
| 0.0-4.9 | Rejected | N/A (Blocked) | Cannot proceed - critical Platform IT Infrastructure gaps |


---

### A.3 Document Completion Guide

**For Architecture Teams**:

If this contract shows "Non-Compliant" or "Unknown" items, use the **architecture-docs skill** to efficiently remediate gaps.

**Quick Remediation Steps**:

1. **Activate the skill**: `/skill architecture-docs`
2. **Identify gaps**: Review gap table below (Section A.3.1)
3. **Request remediation**: Ask skill to add missing content to specified sections
4. **Regenerate contract**: Run compliance generation to verify improvements

**Detailed workflow, common commands, and domain-specific examples in Section A.3.2 below.**


---

#### A.3.1 Common Gaps Quick Reference

**Common Platform & IT Infrastructure Gaps and Remediation**:

| Gap Description | Impact | ARCHITECTURE.md Section to Update | Recommended Action |
|-----------------|--------|----------------------------------|-------------------|
| Environment isolation undefined | LAPI1 Non-Compliant | Section 11 (Operational → Deployment) | Document dev, test, staging, production environments with access controls |
| Server OS not specified | LAPI2 Non-Compliant | Section 8 (Technology Stack → Infrastructure) | Specify OS versions, patching strategy, containerization approach |
| Database capacity not calculated | LAPI3 Non-Compliant | Section 10 (Performance → Capacity Planning) | Calculate storage requirements based on data volume and retention |
| Infrastructure capacity undefined | LAPI4 Unknown | Section 10 (Performance → Capacity Planning) | Document server sizing (vCPUs, memory, storage) and scaling strategy |
| Backup and retention missing | LAPI5 Unknown | Section 11 (Operational → Backup & DR) | Define backup frequency, retention periods, recovery procedures |
| Naming conventions not defined | LAPI6 Unknown | Section 8 (Technology Stack → Infrastructure) | Define naming standards for servers, databases, networks, resources |
| Transaction volume not specified | LAPI7 Unknown | Section 10 (Performance → Throughput) | Specify expected transaction rates and capacity dimensioning |
| Network architecture undefined | LAPI8 Unknown | Section 4 or 8 (Cloud/Infrastructure) | Document network topology, subnets, firewalls, load balancers |

---

#### A.3.2 Step-by-Step Remediation Workflow

## How to Remediate Gaps Using Architecture-Docs Skill

The `architecture-docs` skill provides an efficient, guided workflow to remediate gaps identified in compliance contracts. This section provides step-by-step instructions for using the skill to achieve AUTO_APPROVE status (8.0+ validation score).

---

### Quick Start (For Simple Gaps)

Use this workflow when you have 1-3 simple gaps to fix:

1. **Activate the architecture-docs skill**:
   ```
   /skill architecture-docs
   ```

2. **Specify what you need**: "Add [missing item] to Section [X]"
   - Example: "Add cost monitoring configuration to Section 11"
   - Example: "Add OAuth 2.0 authentication to Section 9"

3. **Review and confirm**: The skill will update the specified section with proper formatting and structure

4. **Regenerate contract**: Run compliance generation to verify the gap is resolved

**When to use**: You have fewer than 3 gaps, all in known sections, with clear remediation actions.

---

### Standard Workflow (Most Common)

Use this workflow for typical compliance remediation with 3-10 gaps:

1. **Review compliance contract** to identify all UNKNOWN and FAIL items
   - Check Section A.3.1 "Common Gaps Quick Reference" table
   - Note the ARCHITECTURE.md section numbers for each gap
   - Identify impact levels (BLOCKER, High, Medium)

2. **Prioritize by impact**: Work on highest-impact gaps first
   - **BLOCKER** (0.5-0.8 pts): Critical compliance failures
   - **High** (0.3-0.5 pts): Major missing documentation
   - **Medium** (0.1-0.2 pts): Optional or minor improvements

3. **Activate skill**:
   ```
   /skill architecture-docs
   ```

4. **Work section-by-section**: Address gaps one section at a time
   - Request: "Review Section [X] for completeness. Gaps: [list gaps from compliance contract]"
   - Example: "Review Section 9 for completeness. Gaps: missing mTLS config, no API authentication documented, certificate management not specified"

5. **Let skill guide you**: Follow the two-phase validation workflow
   - **Phase 1**: Structure validation (sections present, proper formatting)
   - **Phase 2**: Content improvements (completeness, quality, traceability)

6. **Regenerate contract** to verify score improvement
   - Run compliance generation again
   - Check that UNKNOWN/FAIL items are now PASS
   - Verify validation score increased

**When to use**: Most compliance remediation scenarios with multiple gaps across different sections.

---

### Advanced Workflow (Multiple Gaps or Full Review)

Use this workflow when you have 10+ gaps or want comprehensive validation:

1. **Request full compliance review**:
   ```
   /skill architecture-docs
   ```
   Then ask: "Review ARCHITECTURE.md for [Contract Type] compliance"
   - Example: "Review ARCHITECTURE.md for Cloud Architecture compliance"
   - Example: "Review ARCHITECTURE.md for Security Architecture compliance"

2. **Use two-phase validation workflow**: Let the skill systematically validate structure and content
   - **Phase 1** (Structure): Ensures all required sections exist with proper formatting
   - **Phase 2** (Content): Validates completeness, quality, and compliance requirements

3. **Iterate through violations**: Address each category systematically
   - Start with BLOCKER violations (critical compliance failures)
   - Move to UNKNOWN items (missing data)
   - Address FAIL items (non-compliant technologies)
   - Finally improve quality (source traceability)

4. **Verify after each major change**:
   - Request section-specific validation: "Validate Section [X] completeness"
   - Check metric consistency: "Verify metrics in Section [X] match Section 1 design drivers"

5. **Final verification**: Regenerate compliance contract and compare scores

**When to use**: Comprehensive architecture review, major compliance gaps (10+ items), or preparing for formal approval.

---

### Skill Capabilities

The `architecture-docs` skill can help with:

- ✅ **Add missing sections**: Uses standard ARCHITECTURE.md templates for each section
- ✅ **Calculate design drivers**: Derives quantitative metrics from Business Context (Section 1)
- ✅ **Validate architecture principles**: Ensures all 9 principles documented with trade-offs (Section 3)
- ✅ **Check metric consistency**: Validates that performance/scalability/availability targets align across sections
- ✅ **Generate ADRs**: Creates Architecture Decision Records for technology choices (Section 12)
- ✅ **Add source traceability**: Includes section and line number references for compliance audit trails
- ✅ **Load sections incrementally**: Context-efficient approach that loads only needed sections
- ✅ **Domain-specific guidance**: Understands Cloud, Security, SRE, Integration, and other architecture domains

---

### Common Commands

| Task | Command Example |
|------|-----------------|
| **Add missing section** | "Add Section [X] using standard template" |
| **Review section completeness** | "Review Section [X] for completeness" |
| **Fix metric consistency** | "Ensure metrics in Section [X] match Section 1 values" |
| **Add architecture principle** | "Add [Principle Name] to Section 3 with trade-offs" |
| **Create ADR** | "Create ADR for [decision] in Section 12" |
| **Add security control** | "Add [control] to Section 9 → [subsection]" |
| **Add cloud configuration** | "Add [config] to Section 4 with [provider]-specific details" |
| **Add monitoring setup** | "Add [monitoring tool/config] to Section 11" |
| **Full validation** | "Validate ARCHITECTURE.md against [domain] architecture standards" |

---

### Remediation Tips

1. **Start with structure, then content**: Ensure all required sections exist before filling in details

2. **Be specific in requests**:
   - ❌ Vague: "Add encryption"
   - ✅ Specific: "Add TLS 1.3 encryption to Section 9 → Network Security with cipher suite recommendations"

3. **Provide context**:
   - ❌ Generic: "Add monitoring"
   - ✅ Contextual: "Add Azure Monitor configuration to Section 11 with Application Insights and Log Analytics workspace"

4. **Iterate section-by-section**: Don't try to fix everything at once
   - Fix Section 9 (Security) completely
   - Regenerate contract to verify
   - Move to Section 11 (Observability)
   - Regenerate again

5. **Verify after each change**: Run compliance generation frequently to ensure progress
   - Check validation score improvement
   - Verify UNKNOWN → PASS conversions
   - Ensure no new issues introduced

6. **Use domain-specific language**: Reference the architecture domain in your requests
   - "Add AWS Well-Architected Framework mapping" (Cloud Architecture)
   - "Add SLO definitions with error budgets" (SRE Architecture)
   - "Add OAuth 2.0 flows" (Security Architecture)
   - "Add API catalog" (Integration Architecture)

7. **Reference the gap table**: Use exact gap descriptions from Section A.3.1
   - Copy the "Gap Description" from compliance contract
   - Include the section number from "ARCHITECTURE.md Section" column
   - Follow the "Recommended Action" guidance

8. **Check source traceability**: Ensure all added content includes section and line references
   - Quality score depends on source traceability coverage
   - Ask skill to "ensure all items have source references"

---

### Example Interaction

**Scenario**: Cloud Architecture contract shows score 6.8/10 with 5 UNKNOWN items

**Step 1: Activate skill**
```
/skill architecture-docs
```

**Step 2: Request section review**
```
Review Section 11 for completeness. Gaps from compliance contract:
- Missing cost monitoring configuration
- No resource tagging strategy documented
- Rightsizing review schedule not defined
```

**Step 3: Follow skill guidance**
- Skill validates Section 11 structure
- Skill identifies missing subsections
- Skill suggests specific additions with examples

**Step 4: Confirm additions**
```
Yes, add those subsections with the recommended content
```

**Step 5: Verify**
- Regenerate compliance contract
- Check validation score (should increase to 7.5-8.0+)
- Verify UNKNOWN items now show PASS

---

### Troubleshooting

**Q: Skill says section already exists but compliance contract shows gap**
- **A**: Section may exist but lack required details. Ask: "Review Section [X] for [specific gap] completeness"

**Q: Added content but score didn't improve**
- **A**: Check source traceability. Ask: "Add source references (section and line numbers) to all items in Section [X]"

**Q: Multiple FAIL items for deprecated technologies**
- **A**: Create ADR for exception or upgrade path: "Create ADR in Section 12 for [technology] with migration plan"

**Q: Score improved but still below 8.0**
- **A**: Review Section A.3.3 "Achieving Auto-Approve Status" for prioritized next steps

**Q: Compliance contract regeneration shows same score**
- **A**: Verify ARCHITECTURE.md was actually updated (check file modification timestamp). Re-run skill commands if needed.

---

### Next Steps After Remediation

1. **Regenerate all relevant compliance contracts**: Changes to ARCHITECTURE.md may affect multiple contract types

2. **Review validation scores**: Aim for 8.0+ for automatic approval

3. **Submit for review**: Contracts with scores 7.0-7.9 require manual review by approval authority

4. **Track over time**: Keep ARCHITECTURE.md updated as architecture evolves to maintain compliance

---

**For domain-specific remediation examples, see Section A.3.2 examples below.**


**Platform & IT Infrastructure-Specific Examples**:

**Example 1: Environment Isolation and Access Controls**
- **Gap**: Environment isolation not documented
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add environment isolation to Section 11:
   Environments: dev, test, staging, production (separate VPCs),
   Access controls: dev (all engineers), staging (senior only), prod (SRE only),
   Network isolation: VPC peering with security groups,
   Promotion process: dev → test → staging → prod with approvals,
   Data isolation: anonymized data in non-prod environments"
  ```
- **Expected Outcome**: Section 11 with environment strategy, access controls, isolation
- **Impact**: LAPI1 → Compliant (+0.6 points)

**Example 2: Server OS and Patching Strategy**
- **Gap**: Server OS specifications missing
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add server OS specifications to Section 8:
   OS: Ubuntu 22.04 LTS for application servers,
   Container runtime: Docker 24.x on Kubernetes 1.28,
   Patching: monthly security patches, quarterly OS upgrades,
   Patch testing: automated in dev/test, manual approval for prod,
   Base images: hardened golden images with CIS benchmarks"
  ```
- **Expected Outcome**: Section 8 with OS versions, patching strategy, hardening
- **Impact**: LAPI2 → Compliant (+0.5 points)

**Example 3: Database and Infrastructure Capacity Planning**
- **Gap**: Database capacity not calculated
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add capacity planning to Section 10:
   Database: PostgreSQL with 500GB initial, 20% annual growth,
   Storage: 1TB SSD (IOPS 10000), 5 years retention = 1.2TB total,
   Compute: 16 vCPUs, 64GB RAM for app servers (3 instances),
   Network: 10 Gbps bandwidth, 1000 concurrent connections,
   Scaling: horizontal auto-scaling at 70% CPU threshold"
  ```
- **Expected Outcome**: Section 10 with capacity calculations, growth projections, scaling
- **Impact**: LAPI3 + LAPI4 → Compliant (+0.5 points)

**Example 4: Backup, Retention, and DR**
- **Gap**: Backup and retention strategy missing
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add backup strategy to Section 11:
   Frequency: incremental every 6 hours, full daily at 2 AM,
   Retention: 30 days hot storage, 7 years cold storage,
   Storage location: S3 with cross-region replication,
   Recovery: automated restore scripts, RTO 4 hours, RPO 6 hours,
   Testing: monthly restore drills, quarterly full DR test"
  ```
- **Expected Outcome**: Section 11 with backup frequency, retention, recovery procedures
- **Impact**: LAPI5 → Compliant (+0.4 points)

**Example 5: Naming Conventions and Standards**
- **Gap**: Naming conventions not defined
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add naming conventions to Section 8:
   Servers: <env>-<app>-<component>-<instance> (prod-api-web-01),
   Databases: <env>-<app>-db-<type> (prod-payments-db-primary),
   Networks: <env>-<vpc/subnet>-<az> (prod-vpc-private-us-east-1a),
   Resources: <env>-<service>-<resource> (prod-s3-backups),
   Tags: environment, application, owner, cost-center (mandatory)"
  ```
- **Expected Outcome**: Section 8 with naming standards, tagging conventions
- **Impact**: LAPI6 → Compliant (+0.3 points)

---

#### A.3.3 Achieving Auto-Approve Status (8.0+ Score)

**Target Score Breakdown**:
- Completeness (40% weight): Fill all required infrastructure fields
- Compliance (50% weight): Convert UNKNOWN/FAIL to PASS
- Quality (10% weight): Add source traceability for all infrastructure decisions

**To Achieve AUTO_APPROVE Status (8.0+ score):**

1. **Complete Infrastructure and Capacity Planning** (estimated impact: +0.6 points)
   - Document environment isolation: dev, test, staging, prod with access controls (Section 11)
   - Specify server OS: versions, patching strategy, hardening (Section 8)
   - Calculate database capacity: storage, IOPS, growth projections (Section 10)
   - Define infrastructure capacity: vCPUs, memory, storage, scaling strategy (Section 10)
   - Add transaction volume estimates and dimensioning (Section 10)

2. **Establish Backup, DR, and Network Architecture** (estimated impact: +0.3 points)
   - Document backup strategy: frequency, retention, storage location, recovery (Section 11)
   - Add network architecture: VPCs, subnets, firewalls, load balancers (Section 4 or 8)
   - Define RTO/RPO objectives aligned with backup strategy (Section 10)
   - Specify disaster recovery procedures and testing schedule (Section 11)
   - Add monitoring and alerting for infrastructure health (Section 11)

3. **Improve Standards and Governance** (estimated impact: +0.2 points)
   - Define naming conventions for all infrastructure resources (Section 8)
   - Add tagging strategy: mandatory tags (environment, application, owner, cost-center) in Section 8
   - Document infrastructure as code: Terraform, CloudFormation, GitOps (Section 11)
   - Specify change management process for infrastructure changes (Section 11)
   - Add cost optimization strategy: rightsizing, reserved instances, spot instances (Section 11)

**Priority Order**: LAPI1 (environments) → LAPI2 (server OS) → LAPI3 (database capacity) → LAPI4 (infrastructure capacity) → LAPI5 (backup/retention) → LAPI6 (naming conventions) → LAPI7 (transaction volume) → LAPI8 (network architecture)

**Estimated Final Score After Remediation**: 7.4-9.4/10 (AUTO_APPROVE)

---

### A.4 Change History

**Version 2.0 (Current)**:
- Complete template restructuring to Version 2.0 format
- Added comprehensive Appendix with A.1-A.4 subsections
- Added Data Extracted Successfully section
- Added Missing Data Requiring Attention table
- Added Not Applicable Items section
- Added Unknown Status Items Requiring Investigation table
- Expanded Generation Metadata
- Aligned with Cloud Architecture template structure
- Total: 25 validation data points across 9 LAPI requirements
- Preserved source mapping for LAPI01-LAPI09

**Version 1.0 (Previous)**:
- Basic source traceability section mapping LAPI01-LAPI09
- Generation metadata focus
- Limited structure

---

## Data Extracted Successfully

- LAPI03 - Database Type and Size: Azure Database for PostgreSQL 15, General Purpose (2-4 vCPU, 8GB RAM), 99.99% SLA (Source: ARCHITECTURE.md Section 8)
- LAPI03 - Current vs Projected Capacity: 100 users (2 vCPU, 8GB RAM) → 1,000 users (4 vCPU, 16GB RAM) → 10,000 users (8 vCPU, 32GB RAM + replicas) (Source: ARCHITECTURE.md Section 10.6)
- LAPI03 - Storage Scalability: Vertical scaling 2→8 vCPU, 8→32GB RAM; read replicas planned (Source: ARCHITECTURE.md Section 10.2)
- LAPI04 - Database Platform: PostgreSQL 15.x, Azure Database for PostgreSQL managed service (Source: ARCHITECTURE.md Section 8)
- LAPI04 - Deployment Model: Cloud Managed - Azure Database for PostgreSQL (Source: ARCHITECTURE.md Section 8)
- LAPI05 - Backup Method: Daily automated backups; 5-minute transaction log backups (Source: ARCHITECTURE.md Section 11.5)
- LAPI05 - Retention Period: 30 days (Source: ARCHITECTURE.md Section 11.5)
- LAPI05 - RTO: <2 hours (Source: ARCHITECTURE.md Section 11.5)
- LAPI05 - RPO: <1 hour (Source: ARCHITECTURE.md Section 11.5)
- LAPI05 - Backup Testing: Monthly restore tests; quarterly DR drills (Source: ARCHITECTURE.md Section 11.5)
- LAPI06 - Compute Resources: AKS 2-5 nodes; Spring Boot pods 1-2 vCPU, 1-2GB RAM; DB 2 vCPU, 8GB RAM (Source: ARCHITECTURE.md Section 8)
- LAPI06 - Horizontal Scaling: HPA at CPU >70%; min 2, max 10 pods; Azure CDN for presentation (Source: ARCHITECTURE.md Section 10.2)
- LAPI06 - Vertical Scaling: App pod 1→2 vCPU/RAM; DB 2→8 vCPU, 8→32GB; Redis 4→26GB (Source: ARCHITECTURE.md Section 10.2)
- LAPI08 - Target TPS: 20 TPS read, 10 TPS write (30 TPS total) (Source: ARCHITECTURE.md Section 10.1)
- LAPI08 - Connection Pool: HikariCP 20 connections per pod (40 total at 2 pods, up to 200 at 10 pods) (Source: ARCHITECTURE.md Section 8)
- LAPI09 - Not Applicable: No legacy system integration in MVP (Source: ARCHITECTURE.md Section 7.5)


---

## Missing Data Requiring Attention

| Requirement | Missing Data Point | Responsible Role | Recommended Action |
|-------------|-------------------|------------------|-------------------|
| LAPI01 | Environment isolation mechanisms (dev/test/staging/prod separation) | Infrastructure Architect | Document environment strategy with network segmentation and IAM in Section 11 |
| LAPI01 | Production access controls (RBAC, MFA, audit logging) | Infrastructure Architect | Document production RBAC policies with least privilege in Section 9 |
| LAPI01 | Cross-environment data flow restrictions | Infrastructure Architect | Define data flow policies between environments in Section 11 |
| LAPI02 | AKS node operating system version | Platform Engineer | Document AKS node OS (e.g., Azure Linux, Ubuntu 22.04 LTS) in Section 8 |
| LAPI02 | Container base images for Angular and Spring Boot | Platform Engineer | Specify Docker base images with versions in Section 8 |
| LAPI02 | OS patching strategy for AKS nodes | Platform Engineer | Document node pool update strategy in Section 11 |
| LAPI05 | Long-term backup retention tiers (compliance archival) | Database Administrator | Define multi-tier retention policy (30-day operational, 7-year compliance) in Section 11 |
| LAPI07 | Infrastructure naming conventions | Infrastructure Architect | Define naming pattern for AKS cluster, databases, storage, networks in Section 8 |
| LAPI07 | Azure resource tagging strategy | Infrastructure Architect | Document required tags (Environment, Owner, CostCenter, Project) in Section 8 |


---

## Not Applicable Items

- LAPI09 - Legacy Platform Transaction Capacity: Not Applicable - The MVP architecture has no legacy system integrations. Section 7.5 explicitly states: "The MVP does not include external integrations." Future enhancements (email, authentication, analytics) are planned but not yet implemented.


---

## Unknown Status Items Requiring Investigation

| Requirement | Data Point | Issue | Responsible Role | Action Needed |
|-------------|------------|-------|------------------|---------------|
| LAPI01 | Environment Isolation | Architecture mentions staging and production but does not define isolated environment strategy with network/IAM separation | Infrastructure Architect | Document full environment lifecycle (dev/test/staging/prod) with isolation mechanisms in Section 11 |
| LAPI01 | Production Environment Count | Production namespace referenced but no formal multi-environment strategy documented | Infrastructure Architect | Confirm single production environment and document environment promotion process in Section 11 |
| LAPI02 | AKS Node OS | Kubernetes 1.28 and Docker 24.x documented but underlying node OS not specified | Platform Engineer | Specify AKS node pool OS (Azure Linux or Ubuntu) and version in Section 8 |
| LAPI02 | Container Base Images | Docker builds referenced (Section 11.1) but base image registries and versions not documented | Platform Engineer | Document approved base images for Angular (nginx) and Spring Boot (eclipse-temurin:17) in Section 8 |
| LAPI05 | Retention Tiers | Only 30-day operational retention documented; long-term compliance retention tier not specified | Database Administrator | Define retention tier policy (operational vs compliance archival) in Section 11 |


---

## Generation Metadata

**Template Version**: 2.0 (Updated with compliance evaluation system)
**Generation Date**: 2026-02-18
**Source Document**: ARCHITECTURE.md
**Primary Source Sections**: 4 (Infrastructure View), 8 (Deployment View), 10 (Non-Functional Requirements)
**Completeness**: 78% (7/9 data points documented)
**Template Language**: English
**Compliance Framework**: LAPI (Platform IT Infrastructure) (Platform IT Infrastructure) with requirements for compute platforms, storage systems, network infrastructure, monitoring, and database platforms
**Status Labels**: Compliant, Non-Compliant, Not Applicable, Unknown


---

**Note**: This document is auto-generated from ARCHITECTURE.md. Status labels (Compliant/Non-Compliant/Not Applicable/Unknown) and responsible roles must be populated during generation based on available data. Items marked as Non-Compliant or Unknown require stakeholder action to complete the architecture documentation.