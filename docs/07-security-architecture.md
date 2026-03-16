# 9. Security Architecture

This section documents security controls, authentication, authorization, and compliance measures.

### 9.1 Authentication & Authorization

**Current State (MVP)**: No authentication required (single-user global task list)

**Future State (Phase 2+)**:
- **Authentication**: OAuth 2.0 with JWT tokens
- **Identity Provider**: Azure AD or Auth0
- **Authorization**: Role-Based Access Control (RBAC) with roles: User, Admin
- **Session Management**: Stateless JWT tokens with 24-hour expiration

**Justification for MVP**:
- Simplicity: Avoid authentication complexity for initial release
- Single-user assumption: All users see the same global task list (per Product Owner Spec)
- Trade-off: Security risk mitigated by deployment to internal network or VPN-only access

---

### 9.2 Input Validation & Sanitization

**Client-Side Validation (Angular)**:
- **Non-empty description**: Angular Forms required validator
- **Max length**: 500 characters (maxLength validator)
- **Real-time feedback**: Show inline error messages on blur/submit

**Server-Side Validation (Spring Boot)**:
- **Bean Validation**: `@NotNull @Size(min=1, max=500)` on Task entity
- **XSS Prevention**: HTML entity encoding using OWASP Java HTML Sanitizer
- **SQL Injection Prevention**: JPA parameterized queries (no raw SQL)

**Example**:
```java
@Entity
public class Task {
    @NotNull(message = "Description cannot be null")
    @Size(min = 1, max = 500, message = "Description must be 1-500 characters")
    private String description;
}
```

---

### 9.3 Data Protection

**Encryption in Transit**:
- **HTTPS**: TLS 1.3 for all client-server communication (Angular ↔ Spring Boot)
- **Database connections**: TLS 1.2+ for PostgreSQL connections
- **Cache connections**: TLS for Redis connections
- **Certificate Management**: Let's Encrypt or Azure-managed certificates

**Encryption at Rest**:
- **Database**: Azure Database for PostgreSQL Transparent Data Encryption (TDE) enabled by default
- **Backups**: Azure Blob Storage encryption enabled by default
- **Cache**: Azure Cache for Redis encryption at rest

**Secret Management**:
- **Azure Key Vault**: Store database passwords, Redis keys, API secrets
- **Environment Variables**: Inject secrets as environment variables in Kubernetes pods
- **No hardcoded secrets**: Enforce via code review and static analysis

---

### 9.4 Network Security

**Firewall Rules**:
- **Azure Network Security Groups (NSG)**: Allow only HTTPS (443) inbound to AKS load balancer
- **Database Firewall**: Allow connections only from AKS subnet
- **Redis Firewall**: Allow connections only from AKS subnet

**CORS Policy**:
- **Allow Origins**: Only approved frontend domains (e.g., `https://todoapp.example.com`)
- **Allowed Methods**: GET, POST, PATCH, DELETE
- **Allowed Headers**: Content-Type, Authorization (future)

**Example** (Spring Boot):
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("https://todoapp.example.com")
                .allowedMethods("GET", "POST", "PATCH", "DELETE");
    }
}
```

---

### 9.5 Security Headers

**HTTP Security Headers** (Spring Security):
- **Content-Security-Policy**: `default-src 'self'`
- **X-Content-Type-Options**: `nosniff`
- **X-Frame-Options**: `DENY`
- **X-XSS-Protection**: `1; mode=block`
- **Strict-Transport-Security**: `max-age=31536000; includeSubDomains`

---

### 9.6 Vulnerability Management

**Dependency Scanning**:
- **OWASP Dependency-Check**: Scan Maven dependencies for known CVEs
- **npm audit**: Scan Angular dependencies for vulnerabilities
- **Frequency**: Weekly automated scans, block builds on HIGH/CRITICAL findings

**Penetration Testing**:
- **Frequency**: Quarterly penetration tests (external vendor)
- **Scope**: OWASP Top 10 testing (XSS, SQLi, CSRF, etc.)

**Patching Policy**:
- **Critical vulnerabilities**: Patch within 7 days
- **High vulnerabilities**: Patch within 30 days
- **Medium/Low vulnerabilities**: Patch in next release cycle
