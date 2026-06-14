# Security and Data Protection Architecture

**Agent:** security_analyst
**Job:** Client Responsive Webform

---

# Security Architecture Document
## Client Responsive Webform - UK Wealth Management Firm

**Document Version:** 1.0  
**Date:** 2024  
**Classification:** CONFIDENTIAL  
**Author:** Security Analyst Agent

---

## Executive Summary

This document outlines the comprehensive security architecture for a client onboarding webform designed for UK wealth management firms. The solution addresses FCA regulatory requirements, UK GDPR compliance, and industry best practices for protecting sensitive financial and personal data.

**Key Security Objectives:**
- Protect sensitive client personal and financial information
- Ensure compliance with UK GDPR, FCA regulations, and relevant standards
- Implement defense-in-depth security controls
- Enable comprehensive audit trails for regulatory requirements
- Maintain data integrity and availability

---

## 1. Security Risk Assessment

### 1.1 Asset Classification

| Asset Type | Sensitivity Level | Examples |
|------------|------------------|----------|
| Personal Identifiable Information (PII) | HIGH | Name, address, date of birth, National Insurance number |
| Financial Information | CRITICAL | Income details, assets, liabilities, investment portfolio |
| Contact Information | MEDIUM | Email, phone numbers |
| Authentication Credentials | CRITICAL | Passwords, session tokens, API keys |
| Audit Logs | HIGH | Access records, data modifications |

### 1.2 Threat Model

#### Threat Actors
1. **External Attackers** - Financially motivated cybercriminals
2. **Insider Threats** - Malicious or negligent employees
3. **Nation-State Actors** - Advanced persistent threats
4. **Automated Bots** - Credential stuffing, scraping attempts

#### Attack Vectors and Mitigations

| Attack Vector | Likelihood | Impact | Mitigation Strategy |
|--------------|------------|--------|---------------------|
| SQL Injection | HIGH | CRITICAL | Parameterized queries, ORM, input validation |
| Cross-Site Scripting (XSS) | HIGH | HIGH | Content Security Policy, output encoding, sanitization |
| Cross-Site Request Forgery (CSRF) | MEDIUM | HIGH | Anti-CSRF tokens, SameSite cookies |
| Man-in-the-Middle (MITM) | MEDIUM | CRITICAL | TLS 1.3, HSTS, certificate pinning |
| Session Hijacking | MEDIUM | CRITICAL | Secure session management, HTTPOnly/Secure flags |
| Brute Force Authentication | HIGH | HIGH | Rate limiting, account lockout, MFA |
| Data Breach via Storage | MEDIUM | CRITICAL | Encryption at rest, key management, access controls |
| API Abuse | MEDIUM | MEDIUM | Rate limiting, API authentication, input validation |
| Phishing/Social Engineering | HIGH | HIGH | User education, email verification, 2FA |
| DDoS Attacks | MEDIUM | MEDIUM | Rate limiting, WAF, CDN protection |
| Insider Data Exfiltration | LOW | CRITICAL | Role-based access, audit logging, DLP controls |

---

## 2. Security Requirements and Control Specifications

### 2.1 Functional Security Requirements

**SR-001: Authentication**
- Multi-factor authentication (MFA) for administrative access
- Strong password policy (minimum 12 characters, complexity requirements)
- Account lockout after 5 failed attempts
- Password reset via verified email with time-limited tokens

**SR-002: Authorization**
- Role-Based Access Control (RBAC) implementation
- Principle of least privilege enforcement
- Separation of duties for critical operations

**SR-003: Data Protection**
- AES-256 encryption for data at rest
- TLS 1.3 for data in transit
- Field-level encryption for highly sensitive data (NI numbers, financial details)
- Secure key management using hardware security modules (HSM) or cloud KMS

**SR-004: Audit and Logging**
- Comprehensive audit trail of all data access and modifications
- Tamper-evident logging mechanism
- Log retention for minimum 7 years (FCA requirement)
- Real-time security event monitoring

**SR-005: Data Validation**
- Server-side validation for all inputs
- Whitelist-based validation approach
- File upload restrictions (type, size, malware scanning)

### 2.2 Non-Functional Security Requirements

**SR-006: Performance**
- Security controls must not degrade form completion time by >10%
- Encryption/decryption operations <100ms

**SR-007: Availability**
- 99.9% uptime SLA
- DDoS protection capable of handling 10Gbps attacks
- Automated failover mechanisms

**SR-008: Compliance**
- UK GDPR compliance including data minimization
- FCA compliance for client data handling
- ISO 27001 alignment for information security management

---

## 3. Architecture Design

### 3.1 Security Zones and Network Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Internet                              │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│  DMZ Zone                                                    │
│  ┌──────────────┐      ┌──────────────┐                    │
│  │   WAF/CDN    │─────▶│  Load        │                    │
│  │   (Cloudflare│      │  Balancer    │                    │
│  │   /AWS Shield)│      └──────┬───────┘                    │
│  └──────────────┘              │                            │
└────────────────────────────────┼────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────┐
│  Application Zone (Private Subnet)                          │
│  ┌──────────────────────────────────────────────────┐      │
│  │  Web Application Servers (Auto-scaling)          │      │
│  │  - HTTPS only (TLS 1.3)                          │      │
│  │  - Stateless architecture                        │      │
│  │  - Container-based (ECS/Kubernetes)              │      │
│  └────────────────┬─────────────────────────────────┘      │
└───────────────────┼─────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────────────────┐
│  Data Zone (Isolated Private Subnet)                        │
│  ┌──────────────────┐    ┌─────────────────────┐           │
│  │  Application DB   │    │  Audit/Log DB       │           │
│  │  (Encrypted RDS)  │    │  (Write-once store) │           │
│  └──────────────────┘    └─────────────────────┘           │
│  ┌──────────────────┐    ┌─────────────────────┐           │
│  │  KMS/HSM         │    │  Backup Storage     │           │
│  │  (Key Management)│    │  (Encrypted S3)     │           │
│  └──────────────────┘    └─────────────────────┘           │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 Data Flow Security

**Client Submission Flow:**
1. Client accesses form via HTTPS (TLS 1.3)
2. CSP headers prevent XSS attacks
3. Anti-CSRF token validated on each request
4. Input validation and sanitization (server-side)
5. Data encrypted before database storage
6. Audit log entry created
7. Confirmation sent via secure channel

---

## 4. Encryption Standards

### 4.1 Data at Rest

**Database Encryption:**
- **Algorithm:** AES-256-GCM
- **Implementation:** Transparent Data Encryption (TDE) at database level
- **Field-Level Encryption:** Additional encryption for:
  - National Insurance numbers
  - Bank account details
  - Income/asset information
  - Date of birth

**Key Management:**
- **Primary Solution:** AWS KMS / Azure Key Vault / Google Cloud KMS
- **Key Rotation:** Automatic 90-day rotation for data encryption keys
- **Master Key:** Protected by HSM (FIPS 140-2 Level 3)
- **Access Control:** Strict IAM policies, no human access to plaintext keys
- **Backup Keys:** Encrypted and stored in geographically separate location

**File Storage:**
- All uploaded documents encrypted with AES-256
- Unique encryption key per file
- Malware scanning before storage
- Metadata encryption including filenames

### 4.2 Data in Transit

**Transport Layer:**
- **Protocol:** TLS 1.3 only (TLS 1.2 minimum)
- **Cipher Suites:** 
  - TLS_AES_256_GCM_SHA384
  - TLS_CHACHA20_POLY1305_SHA256
  - TLS_AES_128_GCM_SHA256
- **Certificate:** Extended Validation (EV) SSL certificate
- **Configuration:**
  - Perfect Forward Secrecy (PFS) enabled
  - HSTS header with preload (max-age=31536000)
  - Certificate pinning for mobile applications
  - OCSP stapling enabled

**API Communications:**
- Mutual TLS (mTLS) for backend service communication
- API Gateway with TLS termination
- No sensitive data in URL parameters or headers

---

## 5. Authentication and Authorization Design

### 5.1 Authentication Mechanisms

#### For Clients (Form Access)

**Option A: Email-Based Verification (Recommended for Initial Access)**
```
1. Client receives unique, time-limited magic link (valid 24 hours)
2. Link contains cryptographically secure token (256-bit)
3. Single-use token invalidated after access
4. Session established with secure cookie
5. Optional SMS verification for high-value clients
```

**Option B: Account-Based Access**
```
1. Username/email + password authentication
2. Password requirements:
   - Minimum 12 characters
   - Uppercase, lowercase, number, special character
   - No common passwords (checked against breach databases)
   - No password reuse (last 12 passwords)
3. MFA options:
   - TOTP (Time-based One-Time Password) - Google Authenticator
   - SMS (secondary option)
   - Email confirmation code
4. Biometric authentication for mobile apps (future consideration)
```

**Session Management:**
- Session timeout: 15 minutes of inactivity
- Absolute session timeout: 2 hours
- Secure, HTTPOnly, SameSite=Strict cookies
- Session token: Cryptographically random, 256-bit
- Session binding to IP address (with warning on change)
- Concurrent session limit: 1 active session per user

#### For Administrators/Staff

**Requirements:**
- Mandatory Multi-Factor Authentication (MFA)
- Hardware security keys (FIDO2/WebAuthn) preferred
- Privileged Access Management (PAM) for database access
- Single Sign-On (SSO) integration with corporate IdP (SAML 2.0/OAuth 2.0)
- Separate privileged accounts for administrative tasks

### 5.2 Authorization Model

**Role-Based Access Control (RBAC):**

| Role | Permissions | Restrictions |
|------|------------|--------------|
| Client | Create/view own submissions | Cannot access other clients' data |
| Advisor | View assigned clients, add notes | Cannot modify submitted data |
| Compliance Officer | View all submissions, audit logs | Read-only access to submissions |
| Administrator | User management, system configuration | Cannot access client data without approval |
| Data Controller | Handle GDPR requests (erasure, portability) | Requires dual authorization |
| Auditor | Read-only access to logs and data | Time-limited access (audit period only) |

**Access Control Implementation:**
- Attribute-Based Access Control (ABAC) for complex scenarios
- Dynamic policy evaluation at runtime
- Deny-by-default principle
- Segregation of duties for critical operations
- Just-in-time (JIT) access for privileged operations

**API Authorization:**
- OAuth 2.0 with JWT tokens
- Scope-based permissions
- Token expiration: 15 minutes (access), 24 hours (refresh)
- Token revocation support

---

## 6. Secure Data Storage Architecture

### 6.1 Database Security

**Configuration:**
- **Database:** PostgreSQL 14+ or MySQL 8+ with enterprise security features
- **Network:** Isolated private subnet, no direct internet access
- **Encryption:** TDE enabled, encrypted backups
- **Authentication:** Certificate-based authentication from application tier
- **Connection Pooling:** Limited connections, prepared statements only

**Data Minimization:**
- Collect only necessary information
- Pseudonymization where possible
- Data retention policies enforced automatically
- Regular data audits for compliance

**Backup Strategy:**
- Automated daily backups with encryption
- Point-in-time recovery capability
- Geo-redundant backup storage
- Backup retention: 7 years (regulatory requirement)
- Regular backup restoration tests (quarterly)

### 6.2 Data Classification and Handling

**Classification Levels:**

| Level | Data Types | Encryption | Access |
|-------|-----------|------------|--------|
| Critical | NI numbers, financial details | Field-level + DB encryption | Strictly controlled, logged |
| High | Full name, DOB, address | Database encryption | Role-based, logged |
| Medium | Email, phone | Database encryption | Role-based |
| Low | Form metadata, timestamps | Database encryption | Standard controls |

**Data Sanitization:**
- Secure deletion using cryptographic erasure (delete encryption keys)
- Data overwriting for decommissioned storage
- Certificate of destruction for physical media

---

## 7. Application Security Controls

### 7.1 Input Validation and Sanitization

**Validation Strategy:**
```javascript
// Example validation rules
{
  "name": {
    "type": "string",
    "pattern": "^[a-zA-Z\\s'-]{1,100}$",
    "required": true,
    "sanitize": "trim, escape"
  },
  "email": {
    "type": "email",
    "maxLength": 254,
    "required": true,
    "validation": "RFC5322"
  },
  "postcode": {
    "type": "string",
    "pattern": "^[A-Z]{1,2}\\d{1,2}[A-Z]?\\s?\\d[A-Z]{2}$",
    "required": true
  },
  "phone": {
    "type": "string",
    "pattern": "^\\+?44\\d{10}$",
    "required": false
  }
}
```

**Implementation:**
- Server-side validation (never trust client-side)
- Whitelist approach for all inputs
- Content-type validation for file uploads
- File size limits (max 10MB per file)
- Allowed file types: PDF, JPG, PNG only
- Anti-virus scanning for uploaded files

### 7.2 CSRF Protection

**Implementation:**
- Synchronizer token pattern
- Double-submit cookie pattern (backup)
- SameSite cookie attribute (Strict mode)
- Origin and Referer header validation
- Token rotation on each form submission

**Token Specifications:**
- Cryptographically random, 256-bit
- Unique per session
- Bound to user session
- Short-lived (expires with session)

### 7.3 XSS Prevention

**Content Security Policy (CSP):**
```
Content-Security-Policy:
  default-src 'none';
  script-src 'self';
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
  font-src 'self';
  connect-src 'self';
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self';
  upgrade-insecure-requests;
```

**Additional Controls:**
- Output encoding (context-aware)
- DOM-based XSS prevention
- X-XSS-Protection header
- X-Content-Type-Options: nosniff
- Template engine with auto-escaping

### 7.4 Security Headers

**Required Headers:**
```
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), microphone=(), camera=()
Cache-Control: no-store, no-cache, must-revalidate, private
```

### 7.5 Rate Limiting and DDoS Protection

**Application-Level Rate Limiting:**
- 10 requests per minute per IP for form submission
- 100 requests per minute for general browsing
- Progressive delays for repeated violations
- CAPTCHA after 3 failed submissions

**Infrastructure Protection:**
- WAF (Web Application Firewall) - AWS WAF, Cloudflare, Imperva
- DDoS protection at network edge
- Geographic blocking (allow UK + specific countries only)
- Bot detection and mitigation

---

## 8. API Security Design

### 8.1 API Authentication

**OAuth 2.0 Implementation:**
- Authorization Code Flow with PKCE
- Client credentials for service-to-service
- JWT tokens with RS256 signing
- Token introspection endpoint

**API Key Management:**
- Separate keys for development, staging, production
- Key rotation every 90 days
- Revocation capability
- Usage monitoring and alerting

### 8.2 API Security Controls

**Request Validation:**
- JSON schema validation
- Request size limits (max 1MB)
- Content-Type enforcement
- API versioning (v1, v2, etc.)

**Response Security:**
- No sensitive data in error messages
- Generic error responses to prevent information disclosure
- Rate limiting per API key
- Response signing for critical endpoints

**API Gateway Configuration:**
- TLS termination
- Request/response transformation
- Throttling and quotas
- IP whitelisting for administrative APIs

---

## 9. UK GDPR Compliance Measures

### 9.1 Legal Basis and Consent

**Consent Management:**
- Explicit opt-in for data processing
- Granular consent options (marketing, third-party sharing)
- Easy withdrawal mechanism
- Consent audit trail
- Age verification (16+ or parental consent)

**Privacy Notice:**
- Clear, plain language privacy policy
- Purpose specification for data collection
- Data retention periods disclosed
- Third-party data sharing transparency
- Contact details for Data Protection Officer

### 9.2 Data Subject Rights Implementation

**Right of Access (Subject Access Request):**
- Automated portal for requests
- Response within 30 days (max)
- Machine-readable format (JSON, CSV)
- Identity verification before disclosure

**Right to Erasure:**
```
Erasure Process:
1. Verify identity and legal basis for erasure
2. Check for legal retention requirements (FCA: 7 years)
3. If eligible:
   - Mark for deletion in active systems
   - Cryptographic erasure of encryption keys
   - Remove from backups (or flag for exclusion)
   - Notify data processors
   - Document erasure in audit log
4. Confirm completion to data subject
```

**Right to Data Portability:**
- Export in structured, commonly used format (CSV, JSON)
- Include all personal data processed with consent
- Secure download link (encrypted, time-limited)
- Option to transmit directly to another controller

**Right to Rectification:**
- Self-service portal for corrections
- Verification workflow for significant changes
- Audit trail of modifications
- Notification to data recipients

### 9.3 Privacy by Design Implementation

**Data Minimization:**
- Only collect essential information
- Optional fields clearly marked
- Progressive disclosure in multi-step form
- Automatic data deletion after retention period

**Purpose Limitation:**
- Data used only for stated purposes
- Separate consent for additional uses
- Regular data usage audits

**Storage Limitation:**
- Automated retention policy enforcement
- Active data: retained while client relationship active
- Archived data: 7 years post-relationship (FCA requirement)
- Automatic deletion after retention period

### 9.4 Data Protection Impact Assessment (DPIA)

**DPIA Triggers:**
- Processing sensitive financial data
- Large-scale systematic monitoring
- Automated decision-making

**DPIA Process:**
1. Describe processing operations
2. Assess necessity and proportionality
3. Identify risks to data subjects
4. Implement mitigations
5. Document and review annually

### 9.5 Breach Notification

**Breach Response Plan:**
- Detection within 24 hours (automated monitoring)
- Assessment within 48 hours
- ICO notification within 72 hours (if high risk)
- Data subject notification without undue delay
- Breach register maintenance

---

## 10. FCA Regulatory Compliance

### 10.1 Client Data Requirements

**Know Your Customer (KYC) Compliance:**
- Identity verification (document upload, verification service)
- Address verification (utility bill, bank statement)
- Source of wealth/funds documentation
- PEP (Politically Exposed Person) screening
- Sanctions list checking

**Record Keeping:**
- Minimum 7-year retention for client records
- Tamper-evident storage
- Easy retrieval for regulatory inspections
- Secure destruction after retention period

### 10.2 Suitability and Appropriateness

**Fact-Find Data Collection:**
- Financial circumstances (income, assets, liabilities)
- Investment objectives and risk tolerance
- Investment knowledge and experience
- Time horizons
- Capacity for loss

**Data Accuracy:**
- Client confirmation of information accuracy
- Regular data updates (annual review)
- Audit trail of changes

### 10.3 Financial Crime Prevention

**Anti-Money Laundering (AML):**
- Enhanced due diligence for high-risk clients
- Transaction monitoring (if applicable)
- Suspicious activity reporting workflow
- Staff training records

**Sanctions Screening:**
- Real-time screening against UK/EU/US sanctions lists
- Automated alerts for matches
- Manual review workflow
- Documentation of screening results

---

## 11. Audit Logging and Monitoring

### 11.1 Audit Log Requirements

**Events to Log:**

| Event Type | Log Level | Retention |
|------------|-----------|-----------|
| Authentication attempts (success/failure) | INFO/WARN | 7 years |
| Data access (view, export) | INFO | 7 years |
| Data modifications (create, update, delete) | INFO | 7 years |
| Permission changes | WARN | 7 years |
| System configuration changes | WARN | 7 years |
| Failed authorization attempts | WARN | 7 years |
| Security events (CSP violations, rate limiting) | ERROR | 7 years |
| Consent changes | INFO | 7 years |
| GDPR requests (access, erasure, portability) | INFO | Permanent |

**Log Format (JSON):**
```json
{
  "timestamp": "2024-01-15T14:23:45.123Z",
  "eventType": "DATA_ACCESS",
  "userId": "user_12345",
  "userRole": "ADVISOR",
  "ipAddress": "192.168.1.100",
  "userAgent": "Mozilla/5.0...",
  "resource": "client_record",
  "resourceId": "client_67890",
  "action": "VIEW",
  "result": "SUCCESS",
  "dataFields": ["name", "email", "address"],
  "sessionId": "sess_abc123",
  "requestId": "req_xyz789"
}
```

### 11.2 Log Security

**Protection Measures:**
- Write-only access for application
- Separate log database/storage
- Encryption at rest and in transit
- Integrity verification (cryptographic hashing)
- Immutable storage (WORM - Write Once Read Many)
- Geographic replication

**Access Control:**
- Restricted access to security/compliance personnel
- Dual authorization for log export
- All log access logged separately

### 11.3 Security Monitoring

**SIEM Integration:**
- Real-time event correlation
- Automated alerting for suspicious patterns
- Integration with incident response workflow

**Key Metrics and Alerts:**
- Failed login attempts (>3 in 5 minutes)
- Unusual data access patterns (bulk downloads)
- Access from unexpected locations
- Multiple account access from same IP
- Administrative actions outside business hours
- Data export activities
- Error rate spikes
- Performance degradation (possible DDoS)

**Monitoring Tools:**
- SIEM: Splunk, ELK Stack, Azure Sentinel
- Application Performance Monitoring (APM)
- Infrastructure monitoring (CloudWatch, Datadog)
- File Integrity Monitoring (FIM)

---

## 12. Incident Response Plan

### 12.1 Incident Classification

| Severity | Definition | Response Time | Examples |
|----------|------------|---------------|----------|
| Critical | Data breach, system compromise | Immediate | Database breach, ransomware |
| High | Unauthorized access attempt, DDoS | 1 hour | Brute force attack, service outage |
| Medium | Policy violation, failed security control | 4 hours | Excessive failed logins, misconfiguration |
| Low | Minor security event | 24 hours | Single failed login, blocked request |

### 12.2 Response Procedures

**Incident Response Team:**
- Incident Commander
- Security Analyst
- System Administrator
- Legal Counsel
- Communications Lead
- Data Protection Officer

**Response Workflow:**
```
1. Detection and Alert
   ↓
2. Initial Assessment (classify severity)
   ↓
3. Containment (isolate affected systems)
   ↓
4. Investigation (root cause analysis)
   ↓
5. Eradication (remove threat)
   ↓
6. Recovery (restore normal operations)
   ↓
7. Post-Incident Review (lessons learned)
   ↓
8. Documentation and Reporting
```

**Communication Plan:**
- Internal notification: immediate
- ICO notification: within 72 hours (if required)
- Affected individuals: without undue delay
- Media relations: coordinate with Communications Lead

### 12.3 Business Continuity

**Backup and Recovery:**
- RPO (Recovery Point Objective): 1 hour
- RTO (Recovery Time Objective): 4 hours
- Regular disaster recovery drills (quarterly)
- Documented recovery procedures
- Offsite backup storage

**Failover Procedures:**
- Automated failover for critical components
- Manual failover procedures documented
- Regular failover testing

---

## 13. Security Testing Strategy

### 13.1 Testing Types

**Static Application Security Testing (SAST):**
- Automated code scanning in CI/CD pipeline
- Tools: SonarQube, Checkmarx, Veracode
- Frequency: Every code commit
- Blocker issues prevent deployment

**Dynamic Application Security Testing (DAST):**
- Automated vulnerability scanning
- Tools: OWASP ZAP, Burp Suite, Acunetix
- Frequency: Weekly on staging, monthly on production
- Tests: SQL injection, XSS, CSRF, authentication bypass

**Penetration Testing:**
- Annual third-party penetration test
- Scope: Full application, infrastructure, APIs
- CREST or TIGER certified testers
- Remediation timeline: Critical (7 days), High (30 days)

**Software Composition Analysis (SCA):**
- Dependency vulnerability scanning
- Tools: Snyk, WhiteSource, Black Duck
- Frequency: Daily automated scans
- Automatic alerts for critical vulnerabilities

### 13.2 Security Testing Checklist

**Pre-Deployment Testing:**
- [ ] OWASP Top 10 vulnerabilities tested
- [ ] Authentication and authorization testing
- [ ] Input validation and sanitization verified
- [ ] Encryption implementation validated
- [ ] Security headers configured correctly
- [ ] Rate limiting functional
- [ ] CSRF protection verified
- [ ] Session management secure
- [ ] Error handling doesn't leak information
- [ ] Audit logging functional
- [ ] GDPR features tested (access, erasure, portability)
- [ ] API security controls validated
- [ ] File upload security tested
- [ ] Compliance checklist completed

### 13.3 Vulnerability Management

**Process:**
1. Identification (automated scanning, manual testing)
2. Assessment (severity, exploitability, impact)
3. Prioritization (CVSS scoring + business context)
4. Remediation (patch, configuration change, compensating control)
5. Verification (retest to confirm fix)
6. Documentation (tracking and reporting)

**SLA for Remediation:**
- Critical: 7 days
- High: 30 days
- Medium: 90 days
- Low: Next release cycle

---

## 14. Secure Development Lifecycle

### 14.1 Security Requirements Phase

- Threat modeling during design
- Security requirements documented
- Privacy impact assessment
- Compliance requirements identified

### 14.2 Development Phase

**Secure Coding Standards:**
- OWASP Secure Coding Practices
- Language-specific guidelines (OWASP for Node.js, Python, etc.)
- Mandatory code review for security-sensitive code
- Pair programming for critical components

**Developer Training:**
- Annual secure coding training
- OWASP Top 10 awareness
- Privacy and compliance training
- Incident response awareness

### 14.3 CI/CD Security

**Pipeline Security Controls:**
- Automated SAST scanning
- Dependency vulnerability checking
- Container image scanning
- Infrastructure-as-Code security scanning
- Secrets detection (prevent credential commits)
- Automated security testing gates

**Deployment Controls:**
- Immutable infrastructure
- Blue-green deployments
- Automated rollback capability
- Change approval workflow
- Deployment audit logging

---

## 15. Third-Party Risk Management

### 15.1 Vendor Assessment

**Security Questionnaire:**
- ISO 27001 certification
- SOC 2 Type II report
- GDPR compliance attestation
- Incident response capability
- Data processing agreement
- Subprocessor list
- Geographic data storage locations
- Business continuity plans

### 15.2 Data Processing Agreements

**Required Clauses:**
- Purpose limitation
- Confidentiality obligations
- Security measures
- Sub-processing restrictions
- Data subject rights support
- Breach notification (within 24 hours)
- Audit rights
- Data return/deletion on termination

### 15.3 Monitoring and Review

- Annual vendor security reviews
- Continuous monitoring of vendor incidents
- Service level agreement (SLA) compliance tracking
- Regular penetration testing of integrations

---

## 16. PCI-DSS Considerations

### 16.1 Scope Assessment

**Payment Data Handling:**
If the webform collects payment card information (e.g., for advisory fees):

**Option A: Recommended - No Card Data Storage**
- Use payment gateway with hosted payment page (Stripe, PayPal)
- Redirect to PCI-compliant third party
- Receive only transaction reference/token
- Significantly reduces PCI-DSS scope

**Option B: If Card Data Collection Required**
- Full PCI-DSS Level 1 compliance required
- Quarterly Approved Scanning Vendor (ASV) scans
- Annual Report on Compliance (ROC)
- Qualified Security Assessor (QSA) engagement

### 16.2 PCI-DSS Requirements (if applicable)

**Key Controls:**
- Never store CVV/CVC codes
- Truncate or mask PAN (show last 4 digits only)
- Strong cryptography for cardholder data (AES-256)
- Restrict access to cardholder data
- Regularly test security systems
- Maintain information security policy

**Recommendation:** Avoid storing payment card data by using tokenization services from PCI-compliant payment processors.

---

## 17. Mobile Security (Future Consideration)

### 17.1 Mobile Application Security

If native mobile apps are developed:

**Controls:**
- Certificate pinning
- Biometric authentication
- Local data encryption (device keystore)
- Jailbreak/root detection
- Code obfuscation
- Secure storage (iOS Keychain, Android Keystore)
- Mobile application management (MAM) support

---

## 18. Compliance Checklist

### 18.1 UK GDPR Compliance

- [x] Privacy notice displayed prominently
- [x] Lawful basis for processing identified (consent/contract)
- [x] Consent mechanism implemented (granular, withdrawable)
- [x] Data minimization applied
- [x] Purpose limitation enforced
- [x] Storage limitation implemented (retention policy)
- [x] Right of access portal implemented
- [x] Right to erasure workflow implemented
- [x] Right to rectification available
- [x] Right to data portability implemented
- [x] Right to object mechanism available