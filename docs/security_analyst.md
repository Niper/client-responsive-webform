# Data Security and Compliance Testing

**Agent:** security_analyst
**Job:** Client Responsive Webform

---

# Security Testing and Compliance Validation Report
## Client Responsive Webform - UK Wealth Management Firm

**Document Version:** 1.0  
**Date:** 2024  
**Classification:** CONFIDENTIAL  
**Prepared by:** Security Analyst Agent

---

## Executive Summary

This comprehensive security testing and compliance validation report covers the Client Responsive Webform application for a UK wealth management firm. The assessment includes penetration testing, vulnerability assessment, security code review, and regulatory compliance validation against FCA requirements and UK GDPR.

**Key Findings Summary:**
- Security Risk Level: To be determined post-testing
- Compliance Status: To be validated
- Critical Issues: TBD
- High Priority Items: TBD
- Recommendations: Detailed below

---

## Table of Contents

1. [Security Testing Methodology](#1-security-testing-methodology)
2. [OWASP Top 10 Assessment](#2-owasp-top-10-assessment)
3. [Penetration Testing Results](#3-penetration-testing-results)
4. [Vulnerability Assessment](#4-vulnerability-assessment)
5. [Security Code Review](#5-security-code-review)
6. [Encryption & Data Protection Controls](#6-encryption--data-protection-controls)
7. [Authentication & Authorization Testing](#7-authentication--authorization-testing)
8. [UK GDPR Compliance Validation](#8-uk-gdpr-compliance-validation)
9. [FCA Regulatory Compliance](#9-fca-regulatory-compliance)
10. [Session Management & Timeout Testing](#10-session-management--timeout-testing)
11. [Backup & Recovery Procedures](#11-backup--recovery-procedures)
12. [Compliance Checklist](#12-compliance-checklist)
13. [Remediation Roadmap](#13-remediation-roadmap)
14. [Appendices](#14-appendices)

---

## 1. Security Testing Methodology

### 1.1 Testing Approach

**Testing Framework:**
- OWASP Testing Guide v4.2
- NIST SP 800-115 Technical Guide to Information Security Testing
- CREST Penetration Testing Methodology
- FCA Cyber Security Guidelines

**Testing Phases:**
1. **Reconnaissance & Information Gathering**
2. **Vulnerability Identification**
3. **Exploitation Attempts (Controlled)**
4. **Post-Exploitation Analysis**
5. **Compliance Validation**
6. **Reporting & Remediation**

### 1.2 Testing Scope

**In-Scope Components:**
- Multi-step web form interface (all steps)
- Backend API endpoints
- Database layer
- Authentication mechanisms
- Session management
- Data encryption (in-transit and at-rest)
- File upload functionality (if applicable)
- Error handling and logging
- Third-party integrations
- Administrative interfaces

**Out-of-Scope:**
- Infrastructure penetration testing (network layer)
- Social engineering attacks
- Physical security
- DoS/DDoS testing (production environment)

### 1.3 Testing Tools

**Automated Tools:**
- OWASP ZAP (vulnerability scanning)
- Burp Suite Professional (web application security)
- Nmap (port scanning and service detection)
- SQLMap (SQL injection testing)
- Nikto (web server scanner)
- SSLyze (SSL/TLS configuration analysis)
- Dependency-Check (vulnerable component analysis)

**Manual Testing Tools:**
- Postman/Insomnia (API testing)
- Browser Developer Tools
- cURL (request manipulation)
- Custom Python scripts for specific tests

---

## 2. OWASP Top 10 Assessment

### 2.1 A01:2021 - Broken Access Control

**Test Cases:**
- [ ] Horizontal privilege escalation (accessing other clients' data)
- [ ] Vertical privilege escalation (accessing admin functions)
- [ ] Direct object reference manipulation (changing form IDs in URLs)
- [ ] Missing function-level access control
- [ ] CORS misconfiguration allowing unauthorized access
- [ ] Forced browsing to restricted pages

**Testing Procedures:**
```
TEST-001: Direct Object Reference
1. Submit form with client ID = 1001
2. Intercept response and note client reference
3. Create new session, modify request to access client 1001
4. EXPECTED: Access denied with 403 error
5. ACTUAL: [TO BE COMPLETED]

TEST-002: Multi-step Form Navigation
1. Access step 5 of form without completing steps 1-4
2. EXPECTED: Redirect to step 1 or session validation error
3. ACTUAL: [TO BE COMPLETED]
```

**Required Controls:**
- ✓ Server-side authorization checks on every request
- ✓ Session-based validation of form progression
- ✓ Resource-level access control (per-client data isolation)
- ✓ Anti-CSRF tokens on all state-changing operations
- ✓ Deny-by-default access control policy

---

### 2.2 A02:2021 - Cryptographic Failures

**Test Cases:**
- [ ] TLS configuration (version, cipher suites)
- [ ] Certificate validation
- [ ] Sensitive data in transit encryption
- [ ] Sensitive data at rest encryption
- [ ] Password storage mechanism
- [ ] Encryption key management
- [ ] Backup encryption

**Testing Procedures:**
```
TEST-003: TLS Configuration
Tool: SSLyze
Command: sslyze --regular [domain]
Verify:
- TLS 1.2+ only (TLS 1.3 preferred)
- Strong cipher suites (ECDHE, AES-GCM)
- No SSL v2/v3, TLS 1.0/1.1
- Perfect Forward Secrecy enabled
- Valid certificate chain
- HSTS header present

TEST-004: Data at Rest Encryption
1. Review database encryption configuration
2. Verify AES-256 or equivalent for PII/financial data
3. Check encryption key storage (HSM/KMS preferred)
4. Validate field-level encryption for sensitive data:
   - National Insurance numbers
   - Bank account details
   - Investment portfolio information
   - Income/wealth data
```

**Required Standards:**
- TLS 1.2 minimum (TLS 1.3 recommended)
- AES-256 for data at rest
- bcrypt/Argon2 for password hashing (min. 12 rounds)
- Separate encryption keys per data classification
- Key rotation policy (annually minimum)

---

### 2.3 A03:2021 - Injection

**Test Cases:**
- [ ] SQL Injection (all input fields)
- [ ] NoSQL Injection (if applicable)
- [ ] OS Command Injection
- [ ] LDAP Injection
- [ ] XPath Injection
- [ ] Template Injection
- [ ] Server-Side Request Forgery (SSRF)

**Critical Fields for Testing:**
```
High-Risk Input Fields:
1. Name fields (First name, Last name, Middle name)
2. Address fields (Street, City, Postcode)
3. Email address
4. Phone number
5. National Insurance number
6. Search/filter functionality
7. File upload fields
8. Any custom query parameters
```

**Testing Procedures:**
```
TEST-005: SQL Injection - Basic
Payloads:
- ' OR '1'='1
- 1' ORDER BY 1--
- 1' UNION SELECT NULL--
- '; DROP TABLE clients--
Test on: All text inputs, search fields, hidden parameters

TEST-006: SQL Injection - Advanced
- Time-based blind injection
- Boolean-based blind injection
- Error-based injection
Tool: SQLMap with --risk=3 --level=5

TEST-007: OS Command Injection
Payloads (if file upload exists):
- filename.pdf; ls -la
- filename.pdf && whoami
- filename.pdf | cat /etc/passwd
```

**Required Controls:**
- ✓ Parameterized queries/prepared statements (100% coverage)
- ✓ Input validation (whitelist approach)
- ✓ ORM usage with proper escaping
- ✓ Least privilege database accounts
- ✓ Web Application Firewall (WAF) with injection rules

---

### 2.4 A04:2021 - Insecure Design

**Test Cases:**
- [ ] Business logic flaws
- [ ] Rate limiting on form submission
- [ ] Account enumeration protection
- [ ] Secure form progression logic
- [ ] Data retention policies implementation
- [ ] Consent management workflow

**Testing Procedures:**
```
TEST-008: Business Logic - Form Submission
1. Submit same form data multiple times
2. EXPECTED: Duplicate detection or rate limiting
3. Test rapid form submissions (>10/minute)
4. EXPECTED: Rate limiting after threshold

TEST-009: Account Enumeration
1. Enter existing email in form
2. Enter non-existing email in form
3. EXPECTED: Generic response (no difference in timing/message)

TEST-010: Data Minimization
1. Review all collected fields
2. Verify each field has documented business justification
3. Check for excessive data collection
4. Validate data retention periods are enforced
```

**Required Design Patterns:**
- Secure by default configuration
- Principle of least privilege
- Defense in depth
- Zero trust architecture
- Privacy by design (GDPR requirement)

---

### 2.5 A05:2021 - Security Misconfiguration

**Test Cases:**
- [ ] Default credentials
- [ ] Directory listing enabled
- [ ] Unnecessary features enabled
- [ ] Error handling reveals sensitive info
- [ ] Security headers missing
- [ ] Outdated software versions
- [ ] Unnecessary ports/services exposed

**Testing Procedures:**
```
TEST-011: HTTP Security Headers
Required Headers:
✓ Strict-Transport-Security: max-age=31536000; includeSubDomains
✓ Content-Security-Policy: default-src 'self'
✓ X-Frame-Options: DENY
✓ X-Content-Type-Options: nosniff
✓ Referrer-Policy: strict-origin-when-cross-origin
✓ Permissions-Policy: geolocation=(), microphone=()

TEST-012: Error Handling
1. Trigger various errors (invalid input, server errors)
2. EXPECTED: Generic error messages only
3. NOT ACCEPTABLE: Stack traces, database errors, file paths

TEST-013: Information Disclosure
Check for exposure of:
- Software versions in headers
- Internal IP addresses
- Database schema information
- Development/debug endpoints
- .git, .env, config files
```

**Security Configuration Checklist:**
```
Web Server:
□ Remove default pages and error pages
□ Disable directory browsing
□ Configure custom error pages
□ Remove server version headers
□ Disable unnecessary HTTP methods (TRACE, OPTIONS)

Application:
□ Disable debug mode in production
□ Remove development endpoints
□ Configure secure session cookies
□ Set appropriate CORS policies
□ Implement rate limiting

Database:
□ Change default passwords
□ Remove default/test databases
□ Configure IP whitelisting
□ Enable audit logging
□ Encrypt connections
```

---

### 2.6 A06:2021 - Vulnerable and Outdated Components

**Test Cases:**
- [ ] Frontend library vulnerabilities (React, Vue, jQuery, etc.)
- [ ] Backend framework vulnerabilities
- [ ] Third-party package vulnerabilities
- [ ] Outdated SSL/TLS libraries
- [ ] Vulnerable npm/pip packages

**Testing Procedures:**
```
TEST-014: Dependency Scanning
Tools: 
- npm audit (for Node.js)
- pip-audit (for Python)
- OWASP Dependency-Check
- Snyk

Process:
1. Scan all dependencies
2. Identify CVEs with CVSS > 7.0 (HIGH/CRITICAL)
3. Check for available patches
4. Document remediation timeline

TEST-015: Version Detection
1. Identify all frameworks and libraries
2. Compare against latest stable versions
3. Check EOL status
4. Verify security patch compliance
```

**Dependency Management Requirements:**
- Automated vulnerability scanning in CI/CD
- Monthly dependency updates
- Security patch deployment within 14 days (critical), 30 days (high)
- Software Bill of Materials (SBOM) maintained
- No use of EOL software

---

### 2.7 A07:2021 - Identification and Authentication Failures

**Test Cases:**
- [ ] Weak password requirements
- [ ] Credential stuffing protection
- [ ] Brute force protection
- [ ] Session fixation
- [ ] Weak session ID generation
- [ ] Missing multi-factor authentication (where required)
- [ ] Insecure password recovery

**Testing Procedures:**
```
TEST-016: Authentication Strength (Admin/Staff Access)
Password Requirements:
✓ Minimum 12 characters
✓ Complexity (upper, lower, number, special char)
✓ No common passwords (check against top 10k list)
✓ Password history (prevent reuse of last 12)
✓ Account lockout after 5 failed attempts
✓ MFA required for privileged access

TEST-017: Session Management
1. Obtain valid session token
2. Analyze token entropy and predictability
3. Test session fixation attack
4. Verify session invalidation on logout
5. Check session timeout implementation
6. Verify secure and httpOnly cookie flags

TEST-018: Brute Force Protection
1. Attempt multiple failed logins
2. EXPECTED: Account lockout or rate limiting
3. Verify CAPTCHA after N attempts
4. Check for timing attack vulnerabilities
```

**Authentication Requirements for Wealth Management:**
- Multi-factor authentication for staff/admin access
- Single sign-on (SSO) integration capability
- Strong session management
- Audit logging of authentication events
- Password complexity enforcement
- Account lockout policies
- Secure password reset mechanism

---

### 2.8 A08:2021 - Software and Data Integrity Failures

**Test Cases:**
- [ ] Insecure deserialization
- [ ] Unsigned code/updates
- [ ] Compromised third-party resources (CDN)
- [ ] Insecure CI/CD pipeline
- [ ] Lack of integrity verification

**Testing Procedures:**
```
TEST-019: Third-Party Resource Integrity
1. Identify all external resources (CDN, libraries)
2. Verify Subresource Integrity (SRI) hashes present
3. Example:
   <script src="https://cdn.example.com/lib.js" 
           integrity="sha384-..." 
           crossorigin="anonymous"></script>

TEST-020: Data Integrity
1. Test form data tampering in transit
2. Verify digital signatures on critical data
3. Check audit log integrity (append-only, tamper-evident)
4. Validate backup integrity checks

TEST-021: Deserialization Testing
1. Identify serialization points (JSON, XML, binary)
2. Attempt object injection attacks
3. Test with modified serialized data
4. Verify input validation before deserialization
```

**Required Controls:**
- Subresource Integrity (SRI) for all external resources
- Code signing for deployments
- Immutable audit logs
- Digital signatures for critical data
- Input validation before deserialization

---

### 2.9 A09:2021 - Security Logging and Monitoring Failures

**Test Cases:**
- [ ] Authentication events logged
- [ ] Authorization failures logged
- [ ] Input validation failures logged
- [ ] Log tampering protection
- [ ] Log retention compliance
- [ ] Real-time alerting for suspicious activity
- [ ] PII in logs

**Testing Procedures:**
```
TEST-022: Security Event Logging
Events that MUST be logged:
✓ User authentication (success/failure)
✓ Form submission (with client reference, not full data)
✓ Access to sensitive data
✓ Permission changes
✓ Session creation/destruction
✓ Input validation failures
✓ Rate limit violations
✓ Administrative actions

Log Format Requirements:
- Timestamp (UTC, ISO 8601)
- Event type
- User identifier
- Source IP address
- User agent
- Result (success/failure)
- Session ID

TEST-023: Log Integrity
1. Attempt to modify log files
2. EXPECTED: Prevention or tamper detection
3. Verify log centralization
4. Check write-once storage

TEST-024: PII in Logs
1. Review log samples
2. Verify NO logging of:
   - Passwords
   - National Insurance numbers
   - Bank account details
   - Full credit card numbers
   - Session tokens
3. Only log sanitized/masked data
```

**UK GDPR Logging Requirements:**
- Log all access to personal data
- Retain logs for 6 years (FCA requirement)
- Implement log monitoring and alerting
- Protect logs as personal data themselves
- Support data subject access requests (DSAR) using logs

---

### 2.10 A10:2021 - Server-Side Request Forgery (SSRF)

**Test Cases:**
- [ ] SSRF via URL input fields
- [ ] SSRF via file upload (XML, PDF)
- [ ] DNS rebinding attacks
- [ ] Access to internal services
- [ ] Cloud metadata access (AWS, Azure, GCP)

**Testing Procedures:**
```
TEST-025: SSRF Detection
Test Scenarios (if URL input exists):
1. Attempt to access internal IP ranges:
   - http://127.0.0.1
   - http://169.254.169.254 (AWS metadata)
   - http://10.0.0.0/8
   - http://192.168.0.0/16
   - http://172.16.0.0/12

2. Attempt DNS rebinding
3. Test for blind SSRF (out-of-band detection)

TEST-026: File Upload SSRF
If file upload exists:
1. Upload XML with external entity
2. Upload SVG with embedded URLs
3. Upload HTML with meta refresh
```

**Mitigation Requirements:**
- Input validation with whitelist of allowed domains
- Network segmentation (application isolated from internal services)
- Disable unnecessary URL schemes (file://, gopher://, etc.)
- Sanitize file uploads

---

## 3. Penetration Testing Results

### 3.1 External Penetration Testing

**Scope:** Public-facing web application

**Test Cases:**

#### 3.1.1 Reconnaissance
```
TEST-027: Information Gathering
Tools: Shodan, Google Dorking, DNS enumeration

Checks:
□ Publicly exposed services
□ Subdomain enumeration
□ Email addresses in public repositories
□ Sensitive data in search engines
□ DNS records analysis (SPF, DMARC, DKIM)
□ SSL certificate transparency logs
□ Historical data (Wayback Machine)

Expected Findings: None
Actual Findings: [TO BE COMPLETED]
```

#### 3.1.2 Network Layer Testing
```
TEST-028: Port Scanning
Tool: Nmap
Command: nmap -sV -sC -A [target]

Expected Open Ports:
- 443 (HTTPS) - ALLOWED
- 80 (HTTP - redirect to HTTPS) - ALLOWED

Unexpected Ports (security issue if found):
- 22 (SSH)
- 3306 (MySQL)
- 5432 (PostgreSQL)
- 27017 (MongoDB)
- 8080 (Alternative HTTP)

Finding: [TO BE COMPLETED]
```

#### 3.1.3 Web Application Attacks
```
TEST-029: Automated Vulnerability Scan
Tool: OWASP ZAP - Active Scan
Configuration:
- Attack strength: Medium (production)
- Threshold: Low
- Scan Policy: OWASP Top 10

TEST-030: Manual Exploitation Attempts
Focus Areas:
1. Authentication bypass
2. Session hijacking
3. SQL injection exploitation
4. XSS payload execution
5. File upload vulnerabilities
6. CSRF attack chains
7. Business logic exploitation

Results: [TO BE COMPLETED]
```

### 3.2 Internal Security Testing

**Scope:** Application behavior, API security, data handling

#### 3.2.1 API Security Testing
```
TEST-031: API Endpoint Discovery
1. Map all API endpoints
2. Test authentication requirements
3. Verify authorization enforcement
4. Check rate limiting
5. Test input validation

Endpoints to Test:
- POST /api/client/create
- POST /api/client/update
- GET /api/client/{id}
- POST /api/factfind/submit
- POST /api/document/upload
- GET /api/admin/*
- POST /api/consent/record

TEST-032: API Authentication & Authorization
For each endpoint:
1. Access without authentication
2. Access with invalid token
3. Access with expired token
4. Access with other user's token
5. Verify proper 401/403 responses
```

#### 3.2.2 Input Validation Testing
```
TEST-033: Field-Level Validation

Personal Details Validation:
Field: First Name
- Max length: 50 chars
- Allowed: Letters, spaces, hyphens, apostrophes
- XSS payload: <script>alert('xss')</script>
- SQL injection: ' OR '1'='1
- Expected: Sanitized or rejected

Field: Email
- Format validation: RFC 5322 compliant
- Max length: 254 chars
- Test: invalid@, @invalid.com, script@<script>
- Expected: Proper format validation

Field: Phone Number
- Format: UK format (+44 or 0)
- Validation: Numbers, spaces, hyphens only
- Test: +44 (0)20 7946 0958, (123)456-7890
- Expected: Accept valid UK formats only

Field: Postcode
- Format: UK postcode validation
- Test: SW1A 1AA (valid), AAAA BBBB (invalid)
- Expected: UK postcode format enforced

Field: National Insurance Number
- Format: XX 12 34 56 X
- Validation: Proper NI number format
- Test: QQ123456C (invalid prefix)
- Expected: Validation per HMRC rules

Field: Bank Account Number
- Format: 8 digits
- Validation: Numeric only
- Test: 12345678 (valid), ABC12345 (invalid)
- Modulus checking if implemented

Field: Sort Code
- Format: 12-34-56
- Validation: 6 digits, proper format
- Test: Various UK bank sort codes

TEST-034: File Upload Validation (if applicable)
Allowed: PDF, DOCX, XLSX, JPG, PNG
Max size: 10MB

Tests:
1. Upload .exe file (renamed to .pdf)
   Expected: MIME type validation rejects
2. Upload file >10MB
   Expected: Size limit enforced
3. Upload file with malicious content
   Expected: Virus scanning (if implemented)
4. Upload file with script in metadata
   Expected: Metadata sanitization
5. Null byte injection: file.pdf%00.exe
   Expected: Rejection
```

### 3.3 Client-Side Security Testing

```
TEST-035: Client-Side Validation Bypass
1. Disable JavaScript
2. Attempt form submission with invalid data
3. EXPECTED: Server-side validation catches all issues
4. Use browser dev tools to modify validation rules
5. EXPECTED: Server-side validation enforces all rules

TEST-036: DOM-Based XSS
1. Test all JavaScript that handles user input
2. Check for dangerous functions:
   - eval()
   - innerHTML
   - document.write()
3. Test URL parameters reflected in page
4. Verify Content Security Policy prevents inline scripts

TEST-037: Sensitive Data Exposure (Client-Side)
Check for:
□ API keys in JavaScript
□ Passwords in page source
□ Personal data in localStorage/sessionStorage
□ Sensitive data in console logs
□ Comments with sensitive information
```

---

## 4. Vulnerability Assessment

### 4.1 Automated Vulnerability Scanning

**Tools Used:**
- OWASP ZAP
- Burp Suite Professional
- Nessus/Qualys (infrastructure)
- npm audit / Snyk (dependencies)

**Scan Configuration:**
```
OWASP ZAP Configuration:
- Mode: Active Scan
- Context: Authenticated session
- Attack Strength: Medium
- Threshold: Low
- Scan Policy: All (with custom rules for financial sector)

Scan Coverage:
□ All form steps (1-N)
□ Success/error pages
□ Authentication flows
□ Administrative interfaces
□ API endpoints
□ Static resources
```

### 4.2 Vulnerability Classification

**Severity Rating Matrix:**
```
CRITICAL (CVSS 9.0-10.0):
- Immediate remediation required (24-48 hours)
- Examples: SQL injection allowing data exfiltration, 
             Authentication bypass,
             Unencrypted financial data transmission

HIGH (CVSS 7.0-8.9):
- Remediation within 7 days
- Examples: Stored XSS, 
             Privilege escalation,
             Sensitive data exposure

MEDIUM (CVSS 4.0-6.9):
- Remediation within 30 days
- Examples: Reflected XSS,
             Information disclosure,
             Missing security headers

LOW (CVSS 0.1-3.9):
- Remediation within 90 days
- Examples: Verbose error messages,
             Directory listing enabled,
             Missing best practices
```

### 4.3 Expected Vulnerability Categories

**Based on wealth management webform context:**

| Category | Risk Level | Testing Priority |
|----------|-----------|------------------|
| PII Data Exposure | CRITICAL | 1 |
| SQL Injection | CRITICAL | 1 |
| Authentication Weaknesses | HIGH | 1 |
| XSS (Stored) | HIGH | 2 |
| CSRF | HIGH | 2 |
| Access Control Issues | HIGH | 2 |
| Encryption Failures | HIGH | 1 |
| Session Management | MEDIUM | 3 |
| Input Validation | MEDIUM | 2 |
| Security Misconfiguration | MEDIUM | 3 |

---

## 5. Security Code Review

### 5.1 Code Review Methodology

**Review Focus Areas:**

#### 5.1.1 Secure Coding Practices
```
REVIEW-001: Input Validation
Location: All form input handlers
Check for:
✓ Server-side validation (never trust client)
✓ Whitelist approach where possible
✓ Proper regex for specific formats (email, phone, NI number)
✓ Length restrictions enforced
✓ Type checking
✓ Encoding validation
✓ File upload restrictions

REVIEW-002: Output Encoding
Location: All data display points
Check for:
✓ Context-aware encoding (HTML, JavaScript, URL, CSS)
✓ No use of dangerous functions (innerHTML with user input)
✓ Template auto-escaping enabled
✓ JSON encoding for API responses
✓ Proper Content-Type headers

REVIEW-003: Database Interactions
Location: Data access layer
Check for:
✓ 100% parameterized queries
✓ No string concatenation for SQL
✓ ORM usage (if applicable)
✓ Least privilege database accounts
✓ Connection string security (no hardcoded passwords)
✓ Prepared statements for all queries

Example - INSECURE:
query = "SELECT * FROM clients WHERE email = '" + userInput + "'"

Example - SECURE:
query = "SELECT * FROM clients WHERE email = ?"
execute(query, [userInput])
```

#### 5.1.2 Authentication & Session Management
```
REVIEW-004: Authentication Implementation
Check for:
✓ Password hashing (bcrypt/Argon2, not MD5/SHA1)
✓ Salt per password (automatic with bcrypt)
✓ Secure session ID generation (cryptographically random)
✓ Session fixation prevention
✓ Session invalidation on logout
✓ Concurrent session handling
✓ Password reset token security

REVIEW-005: Session Configuration
Required Settings:
✓ session.cookie.secure = true (HTTPS only)
✓ session.cookie.httpOnly = true (no JavaScript access)
✓ session.cookie.sameSite = 'strict' or 'lax'
✓ session.timeout = 15 minutes (for financial applications)
✓ session.regenerate on privilege changes
```

#### 5.1.3 Access Control
```
REVIEW-006: Authorization Checks
Location: Every protected resource
Check for:
✓ Authorization check at beginning of function
✓ No reliance on client-side checks only
✓ Resource-level access control
✓ Role-based or attribute-based access control
✓ Deny by default
✓ Centralized authorization logic

Pattern to Find:
if (userRole === 'admin') { // Potential issue
  // Should check permissions, not role
}

Preferred Pattern:
if (hasPermission(user, 'client.data.read', clientId)) {
  // Granular permission check
}
```

#### 5.1.4 Cryptography
```
REVIEW-007: Cryptographic Implementation
Check for:
✓ No custom crypto algorithms
✓ Use of established libraries (NaCl, libsodium, crypto standards)
✓ Appropriate algorithm selection:
  - AES-256-GCM for encryption
  - SHA-256 or SHA-3 for hashing
  - RSA-2048+ or ECC for key exchange
✓ Secure random number generation
✓ Proper IV/nonce handling
✓ No hardcoded keys or passwords

RED FLAGS:
❌ MD5 or SHA1 for security purposes
❌ DES, 3DES, RC4
❌ ECB mode
❌ Hardcoded encryption keys
❌ Random() instead of SecureRandom()
```

#### 5.1.5 Error Handling & Logging
```
REVIEW-008: Error Handling
Check for:
✓ Generic error messages to users
✓ Detailed errors logged (not displayed)
✓ No stack traces in production
✓ No SQL error messages exposed
✓ Try-catch blocks around critical operations
✓ Proper exception handling

REVIEW-009: Logging Implementation
Check for:
✓ Security events logged (see OWASP top 10 section)
✓ No logging of sensitive data:
  ❌ Passwords
  ❌ Session tokens
  ❌ Credit card numbers
  ❌ National Insurance numbers
  ❌ Bank account details
✓ Structured logging format
✓ Log integrity protection
✓ Appropriate log levels
```

### 5.2 Technology-Specific Checks

#### For React/Vue/Angular Frontend:
```
REVIEW-010: Frontend Security
✓ No sensitive data in component state
✓ Proper use of dangerouslySetInnerHTML (avoid if possible)
✓ XSS protection via framework escaping
✓ No eval() or Function() with user input
✓ Dependency vulnerabilities checked (npm audit)
✓ Source maps disabled in production
```

#### For Node.js/Express Backend:
```
REVIEW-011: Node.js Security
✓ Helmet.js middleware configured
✓ Express-validator for input validation
✓ CORS properly configured
✓ Rate limiting implemented
✓ No use of vulnerable packages
✓ Environment variables for secrets (.env)
✓ PM2 or equivalent for production
```

#### For Python/Django/Flask Backend:
```
REVIEW-012: Python Security
✓ Django middleware enabled (CSRF, clickjacking, XSS)
✓ ORM usage (no raw SQL with user input)
✓ Secret key security
✓ Debug mode disabled in production
✓ ALLOWED_HOSTS configured
✓ Secure cookie settings
```

### 5.3 Third-Party Dependencies

```
REVIEW-013: Dependency Security
Process:
1. Generate complete dependency list
2. Run vulnerability scanner
3. Identify all HIGH/CRITICAL vulnerabilities
4. Check for available patches
5. Document remediation plan

Commands:
npm audit --production
pip-audit
bundle audit (Ruby)
dotnet list package --vulnerable

Required Actions:
□ All CRITICAL vulnerabilities patched
□ HIGH vulnerabilities remediated or documented risk acceptance
□ Dependency update policy established
□ Automated scanning in CI/CD pipeline
```

---

## 6. Encryption & Data Protection Controls

### 6.1 Data Classification

**For UK Wealth Management Client Data:**

| Data Type | Classification | Encryption Requirement | Retention |
|-----------|---------------|----------------------|-----------|
| National Insurance Number | HIGHLY SENSITIVE | At rest + in transit | 6 years post-relationship |
| Bank Account Details | HIGHLY SENSITIVE | At rest + in transit | 6 years post-relationship |
| Income/Wealth Information | SENSITIVE | At rest + in transit | 6 years post-relationship |
| Investment Details | SENSITIVE | At rest + in transit | 