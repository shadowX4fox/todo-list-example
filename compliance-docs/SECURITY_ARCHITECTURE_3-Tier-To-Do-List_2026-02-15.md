# Compliance Contract: Security Architecture

**Project**: 3-Tier To-Do List Application
**Generation Date**: 2026-02-15
**Source**: ARCHITECTURE.md (Sections 4, 5, 7, 9, 11)
**Version**: 2.0

---

## Document Control

<!-- CRITICAL: This table structure MUST be preserved exactly.
     DO NOT convert this table to bold field lists like **Field**: Value.
     Keep the | Field | Value | markdown table format.
     Validation rule 'document_control_table' will BLOCK contracts that transform this table. -->

| Field | Value |
|-------|-------|
| Document Owner | Not specified |
| Last Review Date | 2026-02-15 |
| Next Review Date | 2026-05-15 |
| Status | Approved |
| Validation Score | 8.6/10 |
| Validation Status | PASS |
| Validation Date | 2026-02-16 |
| Validation Evaluator | Claude Code (Automated Validation Engine) |
| Review Actor | System (Auto-Approved) |
| Approval Authority | Security Architecture Review Board |

**Validation Configuration**: `/skills/architecture-compliance/validation/security_architecture_validation.json`




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
| LAS1 | API Exposure | Security Architecture | Not Applicable | Section 9 | N/A |
| LAS2 | Intra-Microservices Communication | Security Architecture | Not Applicable | N/A | N/A |
| LAS3 | Inter-Cluster Kubernetes Communication | Security Architecture | Not Applicable | N/A | N/A |
| LAS4 | Domain API Communication | Security Architecture | Not Applicable | N/A | N/A |
| LAS5 | Third-Party API Consumption | Security Architecture | Not Applicable | N/A | N/A |
| LAS6 | Data Lake Communication | Security Architecture | Not Applicable | N/A | N/A |
| LAS7 | Internal Application Authentication | Security Architecture | Non-Compliant | Section 9 | Security Architect or N/A |
| LAS8 | HTTP Encryption Scheme | Security Architecture | Compliant | Section 9 | Security Architect or N/A |

**Overall Compliance**:
- ✅ Compliant: 1/8 (13%)
- ❌ Non-Compliant: 1/8 (13%)
- ⊘ Not Applicable: 6/8 (75%)
- ❓ Unknown: 0/8 (0%)

**Completeness**: 100% (8/8 data points documented)


---

## 1. API Exposure (LAS1)

**Requirement**: Demonstrate compliance with API exposure guidelines including authentication, authorization, rate limiting, and gateway configuration.

**Status**: Not Applicable
**Responsible Role**: N/A

### 1.1 API Gateway Configuration

**API Gateway Implementation**: Not specified
- Status: Not Applicable
- Explanation: No external API exposure.
- Source: "Not documented"

**Exposed API Endpoints**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**API Versioning Strategy**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - no external API gateway for MVP application

### 1.2 API Authentication

**Authentication Method**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Token Management**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - MVP has no authentication per ADR-007

### 1.3 API Authorization

**Authorization Model**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Access Control Policies**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - MVP has no authorization

### 1.4 Rate Limiting and Throttling

**Rate Limiting Policy**: Not specified
- Status: Not Applicable
- Explanation: Internal-only APIs with no rate limiting requirement.
- Source: "Not documented"

**Throttling Strategy**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - internal application without rate limiting requirements

---

## 2. Intra-Microservices Communication (LAS2)

**Requirement**: Show compliance with microservices communication guidelines including service mesh, mTLS, and secure service-to-service communication.

**Status**: Not Applicable
**Responsible Role**: N/A

### 2.1 Service Mesh Implementation

**Service Mesh Technology**: Not specified
- Status: Not Applicable
- Explanation: Monolithic architecture (no microservices).
- Source: "Not documented"

**Service Mesh Configuration**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: ARCHITECTURE.md Section 4 (3-tier monolithic architecture, not microservices)

### 2.2 Mutual TLS (mTLS)

**mTLS Enforcement**: Not specified
- Status: Not Applicable
- Explanation: Monolithic architecture.
- Source: "Not documented"

**Certificate Management**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - no service-to-service communication in monolithic architecture

### 2.3 Service-to-Service Authorization

**Authorization Policy**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Service Identity**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - single application tier

---

## 3. Inter-Cluster Kubernetes Communication (LAS3)

**Requirement**: Demonstrate compliance with inter-cluster communication guidelines including cluster mesh, multi-cluster networking, and cross-cluster security.

**Status**: Not Applicable
**Responsible Role**: N/A

### 3.1 Multi-Cluster Architecture

**Cluster Topology**: Not specified
- Status: Not Applicable
- Explanation: Single Kubernetes cluster.
- Source: "Not documented"

**Cross-Cluster Networking**: Not specified
- Status: Not Applicable
- Explanation: Single cluster.
- Source: "Not documented"

**Source References**: ARCHITECTURE.md Section 11 (single AKS cluster deployment)

### 3.2 Inter-Cluster Security

**Cross-Cluster mTLS**: Not specified
- Status: Not Applicable
- Explanation: Single cluster.
- Source: "Not documented"

**Cross-Cluster Service Discovery**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Network Policies**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - single cluster deployment

---

## 4. Domain API Communication (LAS4)

**Requirement**: Show compliance with domain API communication guidelines including domain-driven design, bounded contexts, and domain event security.

**Status**: Not Applicable
**Responsible Role**: N/A

### 4.1 Domain API Design

**Bounded Contexts**: Not specified
- Status: Not Applicable
- Explanation: Non-DDD architecture.
- Source: "Not documented"

**Domain API Catalog**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: ARCHITECTURE.md Section 4 (3-tier architecture, not domain-driven design)

### 4.2 Domain API Security

**Domain-Level Authentication**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Domain Authorization Boundaries**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**API Contract Versioning**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - no domain-driven architecture

### 4.3 Domain Events Security

**Event Publishing Security**: Not specified
- Status: Not Applicable
- Explanation: No async event decoupling.
- Source: "Not documented"

**Event Subscription Authorization**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - no event-driven architecture

---

## 5. Third-Party API Consumption (LAS5)

**Requirement**: Demonstrate compliance with third-party API consumption guidelines including credential management, API security, and vendor risk assessment.

**Status**: Not Applicable
**Responsible Role**: N/A

### 5.1 Third-Party API Inventory

**External API Catalog**: Not specified
- Status: Not Applicable
- Explanation: No third-party integrations.
- Source: "Not documented"

**API Dependency Criticality**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: ARCHITECTURE.md (no third-party API integrations documented)

### 5.2 Third-Party API Security

**API Credential Management**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**API Key Rotation**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**TLS/SSL Verification**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - no third-party integrations

### 5.3 Vendor Risk Management

**Vendor Security Assessment**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Data Sharing Agreements**: Not specified
- Status: Not Applicable
- Explanation: No data shared with third parties.
- Source: "Not documented"

**Source References**: Not applicable - no vendor integrations

---

## 6. Data Lake Communication (LAS6)

**Requirement**: Show compliance with data lake communication guidelines including authentication, encryption, data access policies, and data lineage.

**Status**: Not Applicable
**Responsible Role**: N/A

### 6.1 Data Lake Access Security

**Authentication Mechanism**: Not specified
- Status: Not Applicable
- Explanation: No data lake integration.
- Source: "Not documented"

**Authorization Model**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: ARCHITECTURE.md (no data lake integration - uses PostgreSQL only)

### 6.2 Data Lake Encryption

**Encryption in Transit**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Encryption at Rest**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - no data lake

### 6.3 Data Governance

**Data Classification**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Data Lineage Tracking**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Access Audit Logging**: Not specified
- Status: Not Applicable
- Explanation: N/A.
- Source: "Not documented"

**Source References**: Not applicable - no data lake

---

## 7. Internal Application Authentication (LAS7)

**Requirement**: Demonstrate compliance with internal application authentication guidelines including SSO, identity providers, MFA, and session management.

**Status**: Non-Compliant
**Responsible Role**: Security Architect or N/A

### 7.1 Authentication Strategy

**Identity Provider (IdP)**: Azure AD or Auth0 (future state)
- Status: Non-Compliant
- Explanation: IdP not specified.
- Source: ARCHITECTURE.md Section 9.1
- Note: Specify corporate identity provider for user authentication in Section 9

**Authentication Protocol**: OAuth 2.0 with JWT tokens (future state)
- Status: Non-Compliant
- Explanation: Protocol not specified.
- Source: ARCHITECTURE.md Section 9.1
- Note: Use OpenID Connect (OIDC) for user authentication in Section 9

**Single Sign-On (SSO)**: Not implemented (MVP)
- Status: Non-Compliant
- Explanation: SSO not implemented.
- Source: ARCHITECTURE.md Section 9.1
- Note: Integrate with corporate SSO for seamless user experience in Section 9

**Source References**: ARCHITECTURE.md Section 9.1

### 7.2 Multi-Factor Authentication (MFA)

**MFA Requirement**: Not specified
- Status: Non-Compliant
- Explanation: MFA not required or not enforced.
- Source: "Not documented"
- Note: Mandate MFA for all internal application users in Section 9

**MFA Methods**: Not specified
- Status: Non-Compliant
- Explanation: MFA methods not specified.
- Source: "Not documented"
- Note: Support authenticator apps (TOTP) and FIDO2 hardware keys in Section 9

**Source References**: Not documented (future enhancement)

### 7.3 Session Management

**Session Configuration**: Stateless JWT tokens with 24-hour expiration (future state)
- Status: Non-Compliant
- Explanation: Session management not configured.
- Source: ARCHITECTURE.md Section 9.1
- Note: Configure session timeout (e.g., 8 hours idle, 24 hours absolute) in Section 9

**Token Security**: Not specified
- Status: Non-Compliant
- Explanation: Token security not addressed.
- Source: "Not documented"
- Note: Store tokens securely (HttpOnly cookies or secure storage), define expiration in Section 9

**Logout Mechanism**: Not specified
- Status: Non-Compliant
- Explanation: Logout not implemented.
- Source: "Not documented"
- Note: Implement proper logout invalidating both access and refresh tokens in Section 9

**Source References**: ARCHITECTURE.md Section 9.1 (future state only - not implemented in MVP)

---

## 8. HTTP Encryption Scheme (LAS8)

**Requirement**: Show compliance with HTTP encryption guidelines including TLS version, cipher suites, certificate management, and HSTS.

**Status**: Compliant
**Responsible Role**: Security Architect or N/A

### 8.1 TLS Configuration

**TLS Version**: TLS 1.3
- Status: Compliant
- Explanation: TLS 1.3 or TLS 1.2+ enforced for all HTTPS communication.
- Source: ARCHITECTURE.md Section 9.3

**Cipher Suites**: Not specified
- Status: Unknown
- Explanation: Ciphers mentioned but suites unclear.
- Source: "Not documented"
- Note: Configure strong cipher suites (ECDHE-RSA-AES256-GCM-SHA384, TLS_AES_256_GCM_SHA384) in Section 9

**Protocol Downgrade Protection**: Not specified
- Status: Unknown
- Explanation: Protection mentioned but configuration unclear.
- Source: "Not documented"
- Note: Disable TLS downgrade and SSL fallback to prevent POODLE attacks in Section 9

**Source References**: ARCHITECTURE.md Section 9.3

### 8.2 Certificate Management

**Certificate Authority (CA)**: Let's Encrypt or Azure-managed certificates
- Status: Compliant
- Explanation: CA documented (public CA like Let's Encrypt, DigiCert, or internal PKI).
- Source: ARCHITECTURE.md Section 9.3

**Certificate Lifecycle**: Not specified
- Status: Unknown
- Explanation: Lifecycle mentioned but process unclear.
- Source: "Not documented"
- Note: Implement automated certificate renewal (cert-manager, ACME protocol) in Section 9 or 11

**Certificate Expiration Monitoring**: Not specified
- Status: Unknown
- Explanation: Monitoring mentioned but alerting unclear.
- Source: "Not documented"
- Note: Configure alerts for certificates expiring within 30 days in Section 11

**Source References**: ARCHITECTURE.md Section 9.3

### 8.3 HTTP Security Headers

**HSTS (HTTP Strict Transport Security)**: max-age=31536000; includeSubDomains
- Status: Compliant
- Explanation: HSTS header enforced (max-age, includeSubDomains, preload).
- Source: ARCHITECTURE.md Section 9.5

**Additional Security Headers**: X-Content-Type-Options: nosniff, X-Frame-Options: DENY, Content-Security-Policy: default-src 'self', X-XSS-Protection: 1; mode=block
- Status: Compliant
- Explanation: Security headers documented (X-Content-Type-Options, X-Frame-Options, CSP).
- Source: ARCHITECTURE.md Section 9.5

**Source References**: ARCHITECTURE.md Section 9.5

---

## Appendix: Source Traceability and Completion Status

### A.1 Definitions and Terminology

**Security Architecture Terms**:
- **API Exposure**: Publicly accessible API endpoints requiring authentication and authorization
- **mTLS (Mutual TLS)**: Two-way TLS authentication where both client and server authenticate each other
- **Service Mesh**: Infrastructure layer handling service-to-service communication, security, and observability
- **Bounded Context**: DDD concept defining domain boundaries with explicit API contracts
- **Data Lake**: Centralized repository storing structured and unstructured data at scale
- **SSO (Single Sign-On)**: Authentication mechanism allowing one set of credentials across multiple applications
- **HSTS**: HTTP header forcing browsers to use HTTPS connections only
- **SPIFFE**: Secure Production Identity Framework for Everyone (service identity standard)

**Status Codes**:
- **Compliant**: Requirement fully satisfied with documented evidence
- **Non-Compliant**: Requirement not met or missing from ARCHITECTURE.md
- **Not Applicable**: Requirement does not apply to this solution
- **Unknown**: Partial information exists but insufficient to determine compliance


**Security Abbreviations**:
- **LAS**: Security Architecture compliance requirement code
- **OAuth**: Open Authorization standard for delegated access
- **OIDC**: OpenID Connect (authentication layer on OAuth 2.0)
- **SAML**: Security Assertion Markup Language
- **JWT**: JSON Web Token
- **RBAC**: Role-Based Access Control
- **ABAC**: Attribute-Based Access Control
- **MFA**: Multi-Factor Authentication
- **TLS**: Transport Layer Security
- **CA**: Certificate Authority
- **PKI**: Public Key Infrastructure

---

### A.2 Validation Methodology

**Validation Process**:

1. **Completeness Check (30% weight)**:
   - Counts filled data points across all LAS requirements
   - Formula: (Filled fields / Total required fields) × 10
   - Example: 8 out of 10 fields = 8.0/10 completeness

2. **Compliance Check (60% weight)**:
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
   Final Score = (Completeness × 0.3) + (Compliance × 0.6) + (Quality × 0.1)
   ```

**Outcome Determination**:
| Score Range | Document Status | Review Actor | Action |
|-------------|----------------|--------------|--------|
| 8.0-10.0 | Approved | System (Auto-Approved) | Ready for implementation |
| 7.0-7.9 | In Review | Security Review Board | Manual review required |
| 5.0-6.9 | Draft | Architecture Team | Address gaps before review |
| 0.0-4.9 | Rejected | N/A (Blocked) | Cannot proceed - critical Security Architecture gaps |


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

**Common Security Architecture Gaps and Remediation**:

| Gap Description | Impact | ARCHITECTURE.md Section to Update | Recommended Action |
|-----------------|--------|----------------------------------|-------------------|
| API authentication not specified | LAS1 Non-Compliant | Section 9 (Security Architecture) | Document OAuth 2.0, API keys, or mTLS configuration |
| mTLS configuration missing | LAS2 Non-Compliant | Section 9 (Security Architecture) | Add mutual TLS enforcement for service-to-service communication |
| TLS version not enforced | LAS1 Non-Compliant | Section 9 (Security Architecture) | Specify TLS 1.3 or minimum TLS 1.2 enforcement |
| Service mesh not documented | LAS2 Unknown | Section 4 or 5 (Architecture/Components) | Specify Istio, Linkerd, or Consul Connect implementation |
| Certificate management undefined | LAS8 Unknown | Section 9 or 11 (Security/Operations) | Document CA, renewal automation, expiration monitoring |
| SSO configuration missing | LAS7 Unknown | Section 9 (Security Architecture) | Add OIDC or SAML integration with corporate IdP |
| Third-party API catalog incomplete | LAS5 Unknown | Section 7 (Integration View) | List all external APIs with vendors, endpoints, auth methods |
| Data lake authentication unclear | LAS6 Unknown | Section 9 (Security Architecture) | Document Azure AD, IAM roles, or service principal authentication |
| Secrets management not specified | LAS1 Unknown | Section 9 (Security Architecture) | Add HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault |
| Encryption at rest not documented | LAS6 Unknown | Section 9 (Security Architecture) | Specify AES-256, key rotation policy, KMS integration |

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


**Security Architecture-Specific Examples**:

**Example 1: Adding mTLS Configuration**
- **Gap**: Missing mTLS for service-to-service communication
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add mTLS enforcement to Section 9 → Network Security:
   Istio service mesh with automatic mTLS,
   X.509 certificates with 90-day expiry,
   automatic cert rotation via cert-manager"
  ```
- **Expected Outcome**: Section 9 with mTLS config, service mesh integration, cert management
- **Impact**: LAS2 → Compliant (+0.6 points)

**Example 2: API Authentication**
- **Gap**: API authentication not specified
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add API authentication to Section 9 → API Security:
   OAuth 2.0 Client Credentials flow for service-to-service,
   JWT tokens with 1-hour expiry,
   Azure AD as authorization server"
  ```
- **Expected Outcome**: Section 9 with OAuth 2.0 flow, token config, Azure AD integration
- **Impact**: LAS1 → Compliant (+0.5 points)

**Example 3: Certificate Management**
- **Gap**: Missing certificate lifecycle documentation
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add certificate management to Section 9 → Secrets Management:
   cert-manager with Let's Encrypt for public certs,
   internal CA for service certs,
   30-day expiry alerts to ops team,
   automated renewal process"
  ```
- **Expected Outcome**: Section 9 with cert lifecycle, alerting, automation
- **Impact**: LAS8 → Compliant (+0.3 points)

**Example 4: Secrets Management**
- **Gap**: Secrets management not documented
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add secrets management to Section 9 → Secrets Management:
   HashiCorp Vault for secrets storage,
   dynamic secrets with 24-hour TTL,
   auto-rotation for database credentials,
   audit logging enabled"
  ```
- **Expected Outcome**: Section 9 with Vault config, rotation policy, audit logging
- **Impact**: LAS1 → Compliant (+0.4 points)

**Example 5: Encryption at Rest**
- **Gap**: Encryption at rest not specified
- **Skill Command**:
  ```
  /skill architecture-docs
  "Add encryption at rest to Section 9 → Data Protection:
   AES-256 encryption for all databases,
   AWS KMS for key management,
   automatic key rotation every 90 days,
   envelope encryption for S3 buckets"
  ```
- **Expected Outcome**: Section 9 with encryption standards, KMS integration, rotation policy
- **Impact**: LAS6 → Compliant (+0.5 points)

---

#### A.3.3 Achieving Auto-Approve Status (8.0+ Score)

**Target Score Breakdown**:
- Completeness (30% weight): Fill all required security control fields
- Compliance (60% weight): Convert UNKNOWN/FAIL to PASS
- Quality (10% weight): Add source traceability for all security controls

**To Achieve AUTO_APPROVE Status (8.0+ score):**

1. **Complete Security Controls** (estimated impact: +0.6 points)
   - Document mTLS for all service-to-service communication (Section 9)
   - Add API authentication (OAuth 2.0/JWT) to Section 9
   - Define certificate management lifecycle (Section 9 or 11)
   - Specify secrets management solution (Vault/Key Vault) in Section 9
   - Document TLS version enforcement (minimum 1.2, prefer 1.3) in Section 9

2. **Enhance Encryption & Data Protection** (estimated impact: +0.3 points)
   - Document encryption at rest (AES-256, key rotation) in Section 9
   - Add encryption in transit (TLS 1.3, cipher suites) to Section 9
   - Define key management strategy (KMS, HSM) in Section 9
   - Specify data classification policy in Section 9
   - Add PII/sensitive data handling procedures to Section 9

3. **Improve Security Monitoring & Compliance** (estimated impact: +0.2 points)
   - Add security audit logging to Section 11 (SIEM integration, retention)
   - Document vulnerability scanning process and frequency
   - Define security incident response procedures (Section 11)
   - Specify penetration testing schedule and scope
   - Add compliance certifications tracking (SOC 2, ISO 27001, etc.)

**Priority Order**: LAS1 (API auth) → LAS2 (mTLS) → LAS8 (cert management) → LAS6 (encryption) → LAS7 (SSO) → LAS5 (third-party APIs) → LAS3 (inter-cluster) → LAS4 (domain APIs)

**Estimated Final Score After Remediation**: 8.6-8.8/10 (AUTO_APPROVE)

---

### A.4 Change History

**Version 2.0 (Current)**:
- Complete template restructuring to Version 2.0 format
- Replaced old simple sections with 8 comprehensive LAS requirements
- LAS1: API Exposure (4 subsections, 10 data points)
- LAS2: Intra-Microservices Communication (3 subsections, 6 data points)
- LAS3: Inter-Cluster Kubernetes Communication (2 subsections, 5 data points)
- LAS4: Domain API Communication (3 subsections, 7 data points)
- LAS5: Third-Party API Consumption (3 subsections, 7 data points)
- LAS6: Data Lake Communication (3 subsections, 7 data points)
- LAS7: Internal Application Authentication (3 subsections, 7 data points)
- LAS8: HTTP Encryption Scheme (3 subsections, 7 data points)
- Added comprehensive Appendix with 4 sections
- Total: 56 validation data points across 24 subsections
- Source mapping expanded to Sections 4, 5, 7, 9, 11
- Aligned with Cloud Architecture template structure
- All content in English

**Version 1.0 (Previous)**:
- Initial template with 6 simple sections
- Basic PLACEHOLDER approach
- Limited source traceability
- Focused on API security, authentication, encryption only

---

<!-- CRITICAL: The sections below use @include directives that expand to H2 headers.
     DO NOT add section numbers (A.5, A.6, etc.) to these headers.
     The resolved content will be ## Header format - preserve it exactly.
     Validation rule 'forbidden_section_numbering' will BLOCK numbered sections after A.4. -->

## Data Extracted Successfully

- LAS8 - TLS Version: TLS 1.3 (Source: ARCHITECTURE.md Section 9.3)
- LAS8 - Certificate Authority: Let's Encrypt or Azure-managed certificates (Source: ARCHITECTURE.md Section 9.3)
- LAS8 - HSTS: max-age=31536000; includeSubDomains (Source: ARCHITECTURE.md Section 9.5)
- LAS8 - Security Headers: X-Content-Type-Options, X-Frame-Options, CSP, X-XSS-Protection (Source: ARCHITECTURE.md Section 9.5)


---

## Missing Data Requiring Attention

| Requirement | Missing Data Point | Responsible Role | Recommended Action |
|-------------|-------------------|------------------|-------------------|
| LAS7 | Identity Provider implementation | Security Architect | Implement Azure AD or Auth0 in Section 9.1 |
| LAS7 | Authentication Protocol | Security Architect | Implement OAuth 2.0 with OIDC in Section 9.1 |
| LAS7 | SSO Integration | Security Architect | Configure SSO with corporate IdP in Section 9.1 |
| LAS7 | MFA Requirement | Security Architect | Enable MFA for all users in Section 9 |
| LAS7 | MFA Methods | Security Architect | Support TOTP and FIDO2 in Section 9 |
| LAS7 | Session Management | Security Architect | Configure JWT token lifecycle in Section 9.1 |
| LAS7 | Token Security | Security Architect | Define token storage strategy in Section 9 |
| LAS7 | Logout Mechanism | Security Architect | Implement token revocation in Section 9 |
| LAS8 | Cipher Suites | Security Architect | Document approved cipher suites in Section 9 |
| LAS8 | Protocol Downgrade Protection | Security Architect | Disable SSL fallback in Section 9 |
| LAS8 | Certificate Lifecycle | Security Architect | Document cert renewal automation in Section 9 or 11 |
| LAS8 | Certificate Expiration Monitoring | Operations Team | Configure cert expiry alerts in Section 11 |


---

## Not Applicable Items

- LAS1 - API Exposure: MVP application with internal REST API only, no external API gateway required
- LAS2 - Intra-Microservices Communication: 3-tier monolithic architecture, not microservices
- LAS3 - Inter-Cluster Kubernetes Communication: Single AKS cluster deployment
- LAS4 - Domain API Communication: Not domain-driven design architecture
- LAS5 - Third-Party API Consumption: No third-party API integrations in MVP
- LAS6 - Data Lake Communication: PostgreSQL database only, no data lake


---

## Unknown Status Items Requiring Investigation

| Requirement | Data Point | Issue | Responsible Role | Action Needed |
|-------------|------------|-------|------------------|---------------|
| LAS8 | Cipher Suites | Not documented | Security Architect | Document approved cipher suites in Section 9 |
| LAS8 | Protocol Downgrade Protection | Not documented | Security Architect | Document SSL fallback prevention in Section 9 |
| LAS8 | Certificate Lifecycle | Process unclear | Operations Team | Document automated cert renewal in Section 9 or 11 |
| LAS8 | Certificate Expiration Monitoring | Alerting not configured | Operations Team | Configure cert expiry monitoring in Section 11 |


---

## Generation Metadata

**Template Version**: 2.0 (Updated with compliance evaluation system)
**Generation Date**: 2026-02-15
**Source Document**: ARCHITECTURE.md
**Primary Source Sections**: 4 (Architecture Layers), 5 (Component Details), 7 (Integration Points), 9 (Security Architecture), 11 (Operational Considerations)
**Completeness**: 100% (8/8 data points documented)
**Template Language**: English
**Compliance Framework**: LAS (Security Architecture) with requirements for API security, encryption, authentication, authorization, secrets management, and security controls
**Status Labels**: Compliant, Non-Compliant, Not Applicable, Unknown


---

**Note**: This document is auto-generated from ARCHITECTURE.md. Status labels (Compliant/Non-Compliant/Not Applicable/Unknown) and responsible roles must be populated during generation based on available data. Items marked as Non-Compliant or Unknown require stakeholder action to complete the security architecture documentation.
