# Compliance Contract: Integration Architecture

**Project**: 3-Tier To-Do List Application
**Generation Date**: 2026-02-20
**Source**: ARCHITECTURE.md (Sections 5, 6, 7, 9)
**Version**: 2.0

---

## Document Control

<!-- CRITICAL: This table structure MUST be preserved exactly.
     DO NOT convert this table to bold field lists like **Field**: Value.
     Keep the | Field | Value | markdown table format.
     Validation rule 'document_control_table' will BLOCK contracts that transform this table. -->

| Field | Value |
|-------|-------|
| Document Owner | N/A |
| Last Review Date | 2026-02-20 |
| Next Review Date | 2026-08-20 |
| Status | Draft |
| Validation Score | 5.3/10 |
| Validation Status | CONDITIONAL |
| Validation Date | 2026-02-20 |
| Validation Evaluator | Claude Code (Automated Validation Engine) |
| Review Actor | Architecture Team |
| Approval Authority | Integration Architecture Review Board |

**Validation Configuration**: `/skills/architecture-compliance/validation/integration_architecture_validation.json`




**CRITICAL - Compliance Score Calculation**:
When calculating the Compliance Score in validation_results, N/A items MUST be included in the numerator:
- Compliance Score = (PASS items + N/A items + EXCEPTION items) / (Total items) × 10
- N/A items count as fully compliant (10 points each)
- Example: 6 PASS, 5 N/A, 0 FAIL, 0 UNKNOWN → (6+5)/11 × 10 = 10.0/10 (100%)
- Add note in contract output: "Note: N/A items counted as fully compliant (included in compliance score)"


---

## Compliance Summary

This Integration Architecture compliance contract validates 7 LAI (Integration Architecture) requirements to ensure integration best practices, security, technology currency, governance compliance, third-party documentation, traceability, and async event decoupling standards.

| Code | Requirement | Category | Status | Source Section | Responsible Role |
|------|-------------|----------|--------|----------------|------------------|
| LAI1 | Best Practices Adoption | Integration Architecture | Unknown | Section 7 | Integration Architect |
| LAI2 | Secure Integrations | Integration Architecture | Non-Compliant | Section 9 | Security Architect / Integration Architect |
| LAI3 | No Obsolete Integration Technologies | Integration Architecture | Compliant | Section 7 | Integration Architect / Enterprise Architect |
| LAI4 | Integration Governance Standards | Integration Architecture | Unknown | Section 7 | Integration Architect / Governance Board |
| LAI5 | Third-Party Documentation | Integration Architecture | Not Applicable | Section 7 | Integration Architect / API Product Owner |
| LAI6 | Traceability and Audit | Integration Architecture | Unknown | Section 7 | Integration Architect / SRE Team |
| LAI7 | Async Event Decoupling Integration Compliance | Integration Architecture | Not Applicable | Section 6 | Integration Architect / Event Architect |

**Overall Compliance**:
- ✅ Compliant: 1/7 (14%)
- ❌ Non-Compliant: 1/7 (14%)
- ⊘ Not Applicable: 2/7 (29%)
- ❓ Unknown: 3/7 (43%)

**Completeness**: 57% (12/21 data points documented)


---

## 1. Best Practices Adoption (LAI1)

**Requirement**: Ensure each domain microservice is accessible via a domain API following integration best practices including API design, versioning, error handling, and documentation.

**Status**: Unknown
**Responsible Role**: Integration Architect

### 1.1 Domain API Accessibility

**Domain Microservices with APIs**: Single Spring Boot REST API (monolithic application tier)
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: ARCHITECTURE.md Section 7
- Note: Document domain API endpoints for each microservice in Section 7. Ensure each bounded context exposes a well-defined API

**API Catalog Completeness**: Endpoints documented inline (GET /api/tasks, POST /api/tasks, PATCH /api/tasks/{id}, DELETE /api/tasks/{id})
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: ARCHITECTURE.md Section 7
- Note: Create comprehensive API catalog in Section 7 listing all domain APIs with endpoints, authentication, and SLAs

### 1.2 API Design Best Practices

**RESTful API Design Compliance**: REST API with standard HTTP verbs (GET, POST, PATCH, DELETE), JSON format
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: ARCHITECTURE.md Section 7
- Note: Document REST API design standards in Section 7. Include resource naming conventions, HTTP verb usage (GET, POST, PUT, DELETE), status codes

**API Versioning Strategy**: "Not specified"
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: "Not documented"
- Note: Define API versioning strategy in Section 7 (e.g., /v1/, /v2/ in URL path). Document backward compatibility policy

**Error Handling Standards**: HTTP status codes used (400, 404, 500, 201, 204, 200) with JSON error responses
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: ARCHITECTURE.md Section 7
- Note: Document error handling standards in Section 7. Include HTTP status code usage (4xx client errors, 5xx server errors), error response schema with error codes and messages

### 1.3 API Documentation

**API Documentation Standards**: OpenAPI/Swagger referenced as standard (Section 3 Principle 9) but no spec file documented
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: ARCHITECTURE.md Section 7
- Note: Implement OpenAPI 3.0 specification for all APIs in Section 7. Include endpoint descriptions, authentication requirements, request/response examples

---

## 2. Secure Integrations (LAI2)

**Requirement**: Demonstrate secure integration of APIs and microservices following cybersecurity guidelines including authentication, authorization, encryption, and security monitoring.

**Status**: Non-Compliant
**Responsible Role**: Security Architect / Integration Architect

### 2.1 Integration Authentication

**API Authentication Mechanism**: No authentication in MVP (ADR-007); future state plans OAuth 2.0 with JWT
- Status: Non-Compliant
- Explanation: Microservices exist without domain API exposure
- Source: ARCHITECTURE.md Section 9
- Note: Document API authentication in Section 9. Implement OAuth 2.0 or JWT for user-context APIs, mTLS for service-to-service

**Service-to-Service Authentication**: No service-to-service authentication documented; monolithic application tier
- Status: Not Applicable
- Explanation: No domain microservices architecture
- Source: ARCHITECTURE.md Section 9

### 2.2 Integration Authorization

**API Authorization Model**: No authorization in MVP; future RBAC with User and Admin roles planned
- Status: Non-Compliant
- Explanation: No authorization or all-or-nothing access
- Source: ARCHITECTURE.md Section 9
- Note: Define authorization model in Section 9 with roles, scopes, and permissions. Implement RBAC or ABAC for API access control

### 2.3 Integration Encryption

**Data in Transit Encryption**: TLS 1.3 for client-server, TLS 1.2+ for database and Redis connections
- Status: Compliant
- Explanation: All integrations use TLS 1.2+ encryption for data in transit
- Source: ARCHITECTURE.md Section 9

**Secrets Management for Integration**: Azure Key Vault stores database passwords, Redis keys, API secrets; injected as environment variables in Kubernetes pods
- Status: Compliant
- Explanation: API credentials and secrets stored in vault (Azure Key Vault, HashiCorp Vault, AWS Secrets Manager)
- Source: ARCHITECTURE.md Section 9

### 2.4 Integration Security Monitoring

**Integration Security Logging**: No security event logging for integrations documented
- Status: Unknown
- Explanation: Missing data in ARCHITECTURE.md
- Source: "Not documented"
- Note: Implement security event logging for all integrations in Section 9. Include authentication attempts, authorization decisions, and security exceptions

---

## 3. No Obsolete Integration Technologies (LAI3)

**Requirement**: Confirm no use of obsolete integration technologies, ensuring all integration protocols, message formats, and middleware are current and supported.

**Status**: Compliant
**Responsible Role**: Integration Architect / Enterprise Architect

### 3.1 Integration Protocol Currency

**REST API Protocol Version**: HTTP REST with JSON format; TLS 1.3 for external, TLS 1.2+ for internal
- Status: Compliant
- Explanation: REST APIs use HTTP/1.1 or HTTP/2, JSON format, modern standards
- Source: ARCHITECTURE.md Section 7

**SOAP Version (if applicable)**: No SOAP integrations; REST-only architecture
- Status: Not Applicable
- Explanation: No SOAP integrations
- Source: ARCHITECTURE.md Section 7

### 3.2 Messaging Technology Currency

**Message Broker Technology**: No message broker; synchronous REST API only
- Status: Not Applicable
- Explanation: No messaging middleware
- Source: ARCHITECTURE.md Section 7

**Event Streaming Platform**: No event streaming; synchronous request/response pattern only
- Status: Not Applicable
- Explanation: No event streaming
- Source: ARCHITECTURE.md Section 6

### 3.3 Integration Middleware Currency

**ESB/Integration Platform**: No ESB or integration middleware; direct service integration via REST APIs
- Status: Not Applicable
- Explanation: No ESB (microservices with direct integration)
- Source: ARCHITECTURE.md Section 7

---

## 4. Integration Governance Standards (LAI4)

**Requirement**: Ensure all APIs and microservices follow the integration governance playbook including naming conventions, API lifecycle management, change control, and compliance with organizational standards.

**Status**: Unknown
**Responsible Role**: Integration Architect / Governance Board

### 4.1 API Naming Conventions

**API Naming Standards Compliance**: REST endpoints follow resource-based pattern (/api/tasks) but no formal naming standard documented
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: ARCHITECTURE.md Section 7
- Note: Document API naming conventions in Section 7. Follow RESTful resource naming (plural nouns, lowercase, hyphens for multi-word resources)

**Endpoint Standardization**: Base path /api/{resource} and /api/{resource}/{id} pattern used consistently
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: ARCHITECTURE.md Section 7
- Note: Standardize endpoint structure in Section 7 (e.g., https://api.domain.com/v1/{resource}/{id})

### 4.2 API Lifecycle Management

**API Lifecycle Governance**: No API lifecycle stages defined
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: "Not documented"
- Note: Document API lifecycle stages in Section 7. Include approval gates for production promotion and deprecation policies

**API Change Control**: No API change control process documented
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: "Not documented"
- Note: Implement API change control process in Section 7. Require impact analysis and consumer notification for breaking changes

### 4.3 Governance Playbook Compliance

**Integration Governance Playbook Reference**: No integration governance playbook referenced
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: "Not documented"
- Note: Reference integration governance playbook in Section 7. Document compliance or exceptions with justification

**API Review and Approval Process**: No API review or approval process documented
- Status: Unknown
- Explanation: Integration patterns unclear
- Source: "Not documented"
- Note: Establish API review process in Section 7 with architecture board approval gate

---

## 5. Third-Party Documentation (LAI5)

**Requirement**: Ensure all third-party APIs, microservices, and events provide proper documentation including API specifications, SLAs, support contacts, and integration guides.

**Status**: Not Applicable
**Responsible Role**: Integration Architect / API Product Owner

### 5.1 Third-Party API Inventory

**Third-Party API Catalog**: No third-party APIs consumed in MVP; future integrations (SendGrid, Azure AD, Auth0, Analytics) planned but not yet integrated
- Status: Not Applicable
- Explanation: No third-party API consumption
- Source: ARCHITECTURE.md Section 7

**External Service Dependencies**: No external service API dependencies in MVP; Azure managed services (AKS, PostgreSQL, Redis, Blob Storage) used but without third-party API contracts
- Status: Not Applicable
- Explanation: No external dependencies
- Source: ARCHITECTURE.md Section 7

### 5.2 Third-Party API Documentation Standards

**API Specification Availability**: No third-party APIs in MVP
- Status: Not Applicable
- Explanation: No third-party APIs
- Source: ARCHITECTURE.md Section 7

**Integration Guides and Examples**: No third-party integrations in MVP
- Status: Not Applicable
- Explanation: No third-party integrations
- Source: ARCHITECTURE.md Section 7

### 5.3 Third-Party SLA and Support

**Third-Party SLA Documentation**: No third-party API SLAs to document in MVP
- Status: Not Applicable
- Explanation: No third-party APIs
- Source: ARCHITECTURE.md Section 7

**Support Contact Information**: No third-party vendor support contacts required in MVP
- Status: Not Applicable
- Explanation: No third-party APIs
- Source: ARCHITECTURE.md Section 7

---

## 6. Traceability and Audit (LAI6)

**Requirement**: Guarantee integration with standard formats for logs and traces, ensuring distributed tracing, correlation IDs, structured logging, and integration with observability platforms.

**Status**: Unknown
**Responsible Role**: Integration Architect / SRE Team

### 6.1 Distributed Tracing

**Distributed Tracing Implementation**: No distributed tracing implementation documented (no OpenTelemetry, Jaeger, or Zipkin)
- Status: Unknown
- Explanation: Missing data in ARCHITECTURE.md
- Source: "Not documented"
- Note: Implement distributed tracing in Section 7 using OpenTelemetry standard. Instrument all API calls and service-to-service communications

**Trace Context Propagation**: No trace context propagation documented
- Status: Unknown
- Explanation: Missing data in ARCHITECTURE.md
- Source: "Not documented"
- Note: Implement W3C Trace Context standard in Section 7. Propagate traceparent and tracestate headers across all integrations

### 6.2 Structured Logging

**Structured Logging Format**: Structured JSON logging with Logback; log fields include timestamp, level, logger, message, method, path, status, duration_ms
- Status: Compliant
- Explanation: Structured logging with standard format (JSON, key-value pairs, consistent schema)
- Source: ARCHITECTURE.md Section 7

**Log Correlation IDs**: correlation_id field present in structured JSON log example (line 1724)
- Status: Compliant
- Explanation: All logs include correlation IDs for request tracing across services
- Source: ARCHITECTURE.md Section 7

### 6.3 Observability Platform Integration

**Centralized Logging Platform**: Azure Monitor Logs with 90-day retention; log aggregation via Kusto Query Language (KQL)
- Status: Compliant
- Explanation: Logs sent to centralized platform (ELK Stack, Splunk, Azure Monitor, AWS CloudWatch)
- Source: ARCHITECTURE.md Section 7

**Trace and Log Integration**: No trace-log integration documented; logs contain correlation_id but no distributed trace ID linking
- Status: Unknown
- Explanation: Missing data in ARCHITECTURE.md
- Source: "Not documented"
- Note: Integrate traces and logs in Section 7. Include trace IDs in log entries and provide log query links in trace UIs

---

## 7. Async Event Decoupling Integration Compliance (LAI7)

**Requirement**: Ensure compliance with async event decoupling guidelines including event schema standards, event versioning, event catalog, consumer contracts, and event delivery guarantees.

**Status**: Not Applicable
**Responsible Role**: Integration Architect / Event Architect

### 7.1 Event Schema Standards

**Event Schema Definition**: No async event decoupling; synchronous REST request/response only
- Status: Not Applicable
- Explanation: No async event decoupling
- Source: ARCHITECTURE.md Section 6

**CloudEvents Compliance**: No async events; not applicable to synchronous REST architecture
- Status: Not Applicable
- Explanation: No async event decoupling
- Source: ARCHITECTURE.md Section 6

### 7.2 Event Versioning and Compatibility

**Event Versioning Strategy**: No events; not applicable
- Status: Not Applicable
- Explanation: No events
- Source: ARCHITECTURE.md Section 6

**Schema Registry Implementation**: No schema registry; no async event decoupling in architecture
- Status: Not Applicable
- Explanation: No async event decoupling
- Source: ARCHITECTURE.md Section 6

### 7.3 Event Catalog and Governance

**Event Catalog**: No events to catalog; synchronous architecture only
- Status: Not Applicable
- Explanation: No events
- Source: ARCHITECTURE.md Section 6

**Consumer Contracts**: No consumer contracts; no events in architecture
- Status: Not Applicable
- Explanation: No events
- Source: ARCHITECTURE.md Section 7

### 7.4 Event Delivery Guarantees

**Event Delivery Semantics**: No async events; not applicable
- Status: Not Applicable
- Explanation: No events
- Source: ARCHITECTURE.md Section 6

**Dead Letter Queue (DLQ) Handling**: No asynchronous events; DLQ not applicable
- Status: Not Applicable
- Explanation: No asynchronous events
- Source: ARCHITECTURE.md Section 6

---

## Appendix: Source Traceability and Completion Status

### A.1 Definitions and Terminology

**Integration Architecture**: The design and implementation of connections between systems, services, and data sources using APIs, events, messaging, and other integration patterns.

**Domain API**: RESTful API exposing a bounded context's capabilities following domain-driven design principles.

**LAI (Integration Architecture Requirements)**: Organizational standards for integration architecture covering best practices, security, technology currency, governance, documentation, traceability, and selective async patterns.

**API Catalog**: Comprehensive inventory of all APIs including endpoints, authentication, SLAs, consumers, and documentation.

**Distributed Tracing**: Observability technique tracking requests across distributed services using correlation IDs and trace context.

**CloudEvents**: CNCF specification for describing events in a common format to ensure interoperability.

**Schema Registry**: Centralized repository for event and message schemas with versioning and compatibility enforcement.

**Dead Letter Queue (DLQ)**: Queue for messages/events that fail processing after retry attempts, enabling manual inspection and remediation.

**Status Codes**:
- **Compliant**: Requirement fully satisfied with documented evidence
- **Non-Compliant**: Requirement not met or missing from ARCHITECTURE.md
- **Not Applicable**: Requirement does not apply to this solution
- **Unknown**: Partial information exists but insufficient to determine compliance


---

### A.2 Validation Methodology

This document is validated using an automated scoring system defined in `/skills/architecture-compliance/validation/integration_architecture_validation.json`.

**Validation Process**:

1. **Completeness Check (30% weight)**:
   - Counts filled data points across all LAI requirements
   - Formula: (Filled fields / Total required fields) × 10
   - Example: 19 out of 21 fields = 9.0/10 completeness

2. **Compliance Check (60% weight)**:
   - Evaluates each validation item as PASS/FAIL/N/A/UNKNOWN/EXCEPTION
   - Formula: (PASS + N/A + EXCEPTION items) / Total items × 10
   - **CRITICAL**: N/A items MUST be included in numerator
   - Example: 15 PASS + 4 N/A + 0 EXCEPTION out of 21 items = (15+4)/21 × 10 = 9.0/10

3. **Quality Check (10% weight)**:
   - Assesses source traceability (ARCHITECTURE.md section references)
   - Verifies explanation quality and actionable notes
   - Formula: (Items with valid sources / Total items) × 10

4. **Final Score Calculation**:
   ```
   Final Score = (Completeness × 0.3) + (Compliance × 0.6) + (Quality × 0.1)
   ```

**Validation Item Statuses**:
- ✅ **PASS** (10 points): Complies with LAI requirement
- ❌ **FAIL** (0 points): Non-compliant or uses deprecated/insecure technologies
- ⚪ **N/A** (10 points): Not applicable to this architecture (counts as compliant)
- ❓ **UNKNOWN** (0 points): Missing data in ARCHITECTURE.md
- 🔓 **EXCEPTION** (10 points): Documented and approved exception

**Outcome Determination**:
| Score Range | Document Status | Review Actor | Action |
|-------------|----------------|--------------|--------|
| 8.0-10.0 | Approved | System (Auto-Approved) | Ready for implementation |
| 7.0-7.9 | In Review | Integration Architecture Review Board | Manual review required |
| 5.0-6.9 | Draft | Architecture Team | Address integration gaps before review |
| 0.0-4.9 | Rejected | N/A (Blocked) | Cannot proceed - critical integration failures |

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

**Common Integration Architecture Gaps and Remediation**:

| Gap Description | Impact | ARCHITECTURE.md Section to Update | Recommended Action |
|-----------------|--------|----------------------------------|-------------------|
| API catalog missing or incomplete | LAI1 Non-Compliant | Section 7 (Integration View) | List all domain APIs with endpoints, authentication, consumers, SLAs |
| API design standards not documented | LAI1 Non-Compliant | Section 7 (Integration View) | Document REST principles, versioning strategy, error handling standards |
| API authentication not specified | LAI2 Non-Compliant | Section 9 (Security Architecture) | Specify OAuth 2.0, JWT, or mTLS for API security |
| Integration protocols undefined | LAI3 Unknown | Section 7 (Integration View) | Document HTTP/2, REST, message brokers (Kafka, RabbitMQ), no legacy ESB |
| API governance not defined | LAI4 Unknown | Section 7 (Integration View) | Define API naming conventions, lifecycle management, change control |
| Third-party API inventory missing | LAI5 Unknown | Section 7 (Integration View) | Inventory external APIs with vendors, endpoints, SLAs, support contacts |
| Distributed tracing not implemented | LAI6 Unknown | Section 7 (Integration View) | Implement OpenTelemetry, correlation IDs, structured logging |
| Event schemas not documented | LAI7 Unknown | Section 6 or 7 (Data Model/Integration) | Define event schemas with JSON Schema/Avro, CloudEvents compliance |
| Schema registry not specified | LAI7 Unknown | Section 7 (Integration View) | Specify schema registry (Confluent, AWS Glue), backward compatibility |
| Dead letter queue handling undefined | LAI7 Unknown | Section 7 (Integration View) | Define DLQ strategy, retention, reprocessing workflow |

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


**Integration Architecture-Specific Examples**:

**Example 1: Adding Comprehensive API Catalog**
- **Gap**: Missing comprehensive API catalog
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add API catalog to Section 7 with table format:
   API Name, Endpoint, Authentication, Consumers, SLA, Owner.
   Include all domain APIs: User Service, Payment Service,
   Notification Service, Inventory Service"
  ```
- **Expected Outcome**: Section 7 with complete API catalog table including SLAs and ownership
- **Impact**: LAI1 → Compliant (+0.6 points)

**Example 2: Implementing Distributed Tracing**
- **Gap**: No distributed tracing implementation documented
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add distributed tracing to Section 7:
   OpenTelemetry SDK in all services,
   Jaeger backend for trace storage,
   W3C Trace Context propagation,
   100% sampling in prod with adaptive sampling"
  ```
- **Expected Outcome**: Section 7 with tracing architecture, propagation standards, backend config
- **Impact**: LAI6 → Compliant (+0.5 points)

**Example 3: Defining Schema Registry**
- **Gap**: Missing schema registry for async event decoupling
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add schema registry to Section 7:
   Confluent Schema Registry with Kafka,
   Avro schemas for all events,
   backward compatibility enforcement,
   schema evolution policy"
  ```
- **Expected Outcome**: Section 7 with schema registry config, versioning, compatibility rules
- **Impact**: LAI7 → Compliant (+0.4 points)

**Example 4: API Governance Framework**
- **Gap**: API governance not defined
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add API governance to Section 7:
   Naming convention (kebab-case, plural nouns),
   Versioning strategy (URI versioning /v1/),
   Lifecycle stages (alpha, beta, stable, deprecated),
   Change control process with stakeholder approvals"
  ```
- **Expected Outcome**: Section 7 with governance standards, playbook compliance
- **Impact**: LAI4 → Compliant (+0.5 points)

**Example 5: Third-Party API Inventory**
- **Gap**: Third-party API inventory incomplete
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add third-party API inventory to Section 7:
   Stripe Payment API (REST, OAuth 2.0, 99.9% SLA, support@stripe.com),
   SendGrid Email API (REST, API key, 99.95% SLA, support@sendgrid.com),
   Twilio SMS API (REST, Basic Auth, 99.95% SLA, help@twilio.com)
   Include API specs, integration guides, rate limits"
  ```
- **Expected Outcome**: Section 7 with third-party API catalog, SLAs, support contacts
- **Impact**: LAI5 → Compliant (+0.4 points)

---

#### A.3.3 Achieving Auto-Approve Status (8.0+ Score)

**Target Score Breakdown**:
- Completeness (30% weight): Fill all required integration architecture fields
- Compliance (60% weight): Convert UNKNOWN/FAIL to PASS
- Quality (10% weight): Add source traceability for all integration points

**To Achieve AUTO_APPROVE Status (8.0+ score):**

1. **Complete API & Integration Documentation** (estimated impact: +0.6 points)
   - Create comprehensive API catalog with endpoints, auth, consumers, SLAs (Section 7)
   - Document API design standards: REST principles, versioning strategy, error handling standards (Section 7)
   - Define API governance: naming, lifecycle, change control (Section 7)
   - Add third-party API inventory with vendors, endpoints, SLAs (Section 7)
   - Specify integration protocols: HTTP/2, REST, message brokers (Section 7)

2. **Enhance Observability & Decouple Through Events** (estimated impact: +0.3 points)
   - Implement distributed tracing: OpenTelemetry, correlation IDs (Section 7)
   - Add structured logging with centralized platform (Section 11)
   - Define event schemas with JSON Schema/Avro (Section 6 or 7)
   - Implement schema registry with backward compatibility (Section 7)
   - Document DLQ handling and reprocessing workflow (Section 7)

3. **Strengthen Security & Standards Compliance** (estimated impact: +0.2 points)
   - Document API authentication: OAuth 2.0, JWT, or mTLS (Section 9)
   - Add TLS 1.2+ encryption for all API communications (Section 9)
   - Define secrets management for API keys (Vault, Key Vault) in Section 9
   - Ensure CloudEvents compliance for selective async patterns (Section 7)
   - Add API security logging and threat detection (Section 9)

**Priority Order**: LAI1 (API catalog) → LAI2 (API auth) → LAI4 (API governance) → LAI6 (distributed tracing) → LAI5 (third-party APIs) → LAI3 (protocols) → LAI7 (event schemas)

**For FAIL Items**:
- **Obsolete Technologies**: Upgrade SOAP 1.0, WebSphere MQ, legacy ESB to REST, Kafka, modern integration
- **Security Gaps**: Implement OAuth 2.0/mTLS, TLS 1.2+, vault-based secrets
- **Missing Standards**: Implement API governance, event schema registry, CloudEvents compliance
- **Approved Exceptions**: Document exception with risk acceptance in Section 12 (ADRs)

**Estimated Final Score After Remediation**: 8.3-8.8/10 (AUTO_APPROVE)

---

### A.4 Change History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 2.0 | 2026-02-20 | Complete template replacement with 7 LAI requirements (LAI1-LAI7). Replaced generic integration catalog format with requirement-specific subsections following Cloud Architecture v2.0 structure. Added comprehensive validation framework with 21 subsections across 7 requirements. Added Data Extracted Successfully, Missing Data, Not Applicable, and Unknown Status sections. Aligned with standardized appendix format. | Integration Architecture Team |
| 1.0 | [ORIGINAL_DATE] | Initial release with basic integration catalog and patterns. | Integration Architecture Team |

---

<!-- CRITICAL: The sections below use @include directives that expand to H2 headers.
     DO NOT add section numbers (A.5, A.6, etc.) to these headers.
     The resolved content will be ## Header format - preserve it exactly.
     Validation rule 'forbidden_section_numbering' will BLOCK numbered sections after A.4. -->

## Data Extracted Successfully

- LAI2 - Data in Transit Encryption: TLS 1.3 (client-server), TLS 1.2+ (database/Redis) (Source: ARCHITECTURE.md Section 9)
- LAI2 - Secrets Management: Azure Key Vault for database passwords, Redis keys, API secrets (Source: ARCHITECTURE.md Section 9)
- LAI3 - REST API Protocol: HTTP REST with JSON, TLS 1.3/1.2+ — no obsolete protocols (Source: ARCHITECTURE.md Section 7)
- LAI5 - No third-party APIs in MVP; future integrations documented as planned (Source: ARCHITECTURE.md Section 7)
- LAI6 - Structured JSON Logging: Logback with JSON format, timestamp/level/method/path/status/duration fields (Source: ARCHITECTURE.md Section 7)
- LAI6 - Correlation IDs: correlation_id field present in log schema (Source: ARCHITECTURE.md Section 7)
- LAI6 - Centralized Logging: Azure Monitor Logs with 90-day retention, KQL query support (Source: ARCHITECTURE.md Section 7)
- LAI7 - No async event decoupling; synchronous REST request/response only (Source: ARCHITECTURE.md Section 6)


---

## Missing Data Requiring Attention

| Requirement | Missing Data Point | Responsible Role | Recommended Action |
|-------------|-------------------|------------------|-------------------|
| LAI1 | Formal API catalog with endpoints, authentication, consumers, SLAs | Integration Architect | Create comprehensive API catalog in Section 7 |
| LAI1 | API versioning strategy (/v1/, header versioning, or query parameter) | Integration Architect | Define versioning strategy in Section 7 |
| LAI1 | OpenAPI 3.0 specification file reference | Integration Architect | Publish OpenAPI spec and reference in Section 7 |
| LAI2 | API authentication mechanism for current MVP | Security Architect / Integration Architect | Document authentication plan or interim controls in Section 9 |
| LAI2 | Integration security logging (auth failures, access denials) | Security Architect / Integration Architect | Implement security event logging in Section 9 |
| LAI4 | API naming conventions and standards | Integration Architect / Governance Board | Document naming standards in Section 7 |
| LAI4 | API lifecycle management stages | Integration Architect / Governance Board | Define lifecycle stages in Section 7 |
| LAI4 | API change control process | Integration Architect / Governance Board | Document change control process in Section 7 |
| LAI4 | Integration governance playbook reference | Integration Architect / Governance Board | Reference or create governance playbook in Section 7 |
| LAI6 | Distributed tracing implementation (OpenTelemetry, Jaeger, Zipkin) | Integration Architect / SRE Team | Implement distributed tracing in Section 7 |
| LAI6 | W3C Trace Context propagation across service boundaries | Integration Architect / SRE Team | Document trace propagation in Section 7 |
| LAI6 | Trace-log integration (trace IDs linked to log entries) | Integration Architect / SRE Team | Integrate traces and logs in Section 7 |


---

## Not Applicable Items

- LAI2 - Service-to-Service Authentication: Monolithic application tier; no microservice-to-microservice calls requiring service identity
- LAI3 - SOAP Version: No SOAP integrations; REST-only architecture
- LAI3 - Message Broker Technology: No messaging middleware; synchronous REST only
- LAI3 - Event Streaming Platform: No event streaming; synchronous request/response pattern only
- LAI3 - ESB/Integration Platform: No ESB; direct REST integration without middleware
- LAI5 - Third-Party API Catalog: No third-party APIs consumed in MVP
- LAI5 - External Service Dependencies: No external API dependencies; Azure managed services used without API contracts
- LAI5 - API Specification Availability: No third-party APIs in scope
- LAI5 - Integration Guides and Examples: No third-party integrations in MVP
- LAI5 - Third-Party SLA Documentation: No third-party API SLAs to document
- LAI5 - Support Contact Information: No third-party vendor support contacts required
- LAI7 - All sub-requirements: No async event decoupling architecture; synchronous REST only


---

## Unknown Status Items Requiring Investigation

| Requirement | Data Point | Issue | Responsible Role | Action Needed |
|-------------|------------|-------|------------------|---------------|
| LAI1 | Domain API Accessibility | REST endpoints exist but no formal API catalog or domain API contract documented | Integration Architect | Create formal API catalog with endpoints, auth, SLAs in Section 7 |
| LAI1 | API Catalog Completeness | Endpoints mentioned inline in component details but no structured catalog | Integration Architect | Formalize API catalog in Section 7 |
| LAI1 | RESTful API Design Compliance | REST verbs used but no formal API design standard documented | Integration Architect | Document REST design standards (resource naming, HTTP verbs, status codes) in Section 7 |
| LAI1 | API Versioning Strategy | No versioning mentioned anywhere in ARCHITECTURE.md | Integration Architect | Define and document versioning strategy in Section 7 |
| LAI1 | Error Handling Standards | HTTP status codes referenced in component details but no formal error schema | Integration Architect | Document error response schema in Section 7 |
| LAI1 | API Documentation Standards | OpenAPI referenced as principle (Section 3) but no spec file or documentation approach documented | Integration Architect | Reference or publish OpenAPI spec in Section 7 |
| LAI2 | Integration Security Logging | Security logging not documented in Section 9 | Security Architect | Add security event logging for API calls in Section 9 |
| LAI4 | API Naming Standards Compliance | Resource path /api/tasks used consistently but no naming standard documented | Integration Architect / Governance Board | Document naming conventions in Section 7 |
| LAI4 | Endpoint Standardization | Consistent pattern used but not formally specified | Integration Architect / Governance Board | Formalize endpoint structure in Section 7 |
| LAI4 | API Lifecycle Governance | No lifecycle management documentation | Integration Architect / Governance Board | Document lifecycle stages in Section 7 |
| LAI4 | API Change Control | No change control process documented | Integration Architect / Governance Board | Define change control process in Section 7 |
| LAI4 | Integration Governance Playbook Reference | No governance playbook referenced | Integration Architect / Governance Board | Reference governance playbook in Section 7 |
| LAI4 | API Review and Approval Process | No review process documented | Integration Architect / Governance Board | Establish review process in Section 7 |
| LAI6 | Distributed Tracing Implementation | No OpenTelemetry, Jaeger, or Zipkin referenced | Integration Architect / SRE Team | Implement distributed tracing in Section 7 |
| LAI6 | Trace Context Propagation | No W3C Trace Context or traceparent header propagation documented | Integration Architect / SRE Team | Document trace propagation in Section 7 |
| LAI6 | Trace and Log Integration | correlation_id in logs but no distributed trace IDs linking logs to traces | Integration Architect / SRE Team | Integrate trace IDs in log schema in Section 7 |


---

## Generation Metadata

**Template Version**: 2.0 (Updated with compliance evaluation system)
**Generation Date**: 2026-02-20
**Source Document**: ARCHITECTURE.md
**Primary Source Sections**: 3 (Component View), 7 (Integration View), 10 (Non-Functional Requirements)
**Completeness**: 57% (12/21 data points documented)
**Template Language**: English
**Compliance Framework**: LAI (Integration Architecture) (Integration Architecture) with requirements for integration patterns, API design, messaging, async event decoupling, and data integration
**Status Labels**: Compliant, Non-Compliant, Not Applicable, Unknown


---

**Note**: This compliance contract is automatically generated from ARCHITECTURE.md. Status labels (Compliant/Non-Compliant/Not Applicable/Unknown) and responsible roles must be populated during generation based on available data. Items marked as Non-Compliant or Unknown require stakeholder action to complete the architecture documentation. To update this document, modify the source architecture file and regenerate. All [PLACEHOLDER] items indicate missing data that should be added to ARCHITECTURE.md for complete compliance validation.