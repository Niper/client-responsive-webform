# Security Testing and Compliance Validation

**Agent:** security_analyst
**Job:** Client Responsive Webform

---

# Security Testing and Compliance Validation Report

## Client Responsive Webform - UK Wealth Management Firm

**Report Date:** 2024
**Classification:** Confidential
**Version:** 1.0

---

## Executive Summary

This comprehensive security testing and compliance validation report addresses the Client Responsive Webform for a UK wealth management firm. The assessment encompasses penetration testing, encryption verification, authentication/authorization testing, GDPR compliance, FCA regulatory requirements, and privacy mechanism validation.

**Key Findings Summary:**
- Critical security controls required before production deployment
- GDPR and FCA compliance framework defined
- 12 high-priority security tests identified
- Comprehensive remediation roadmap provided

---

## 1. Comprehensive Security Risk Assessment

### 1.1 Asset Classification

| Asset Type | Sensitivity Level | Regulatory Impact |
|------------|------------------|-------------------|
| Personal Identity Information (PII) | Critical | GDPR, DPA 2018 |
| Financial Data | Critical | FCA, GDPR |
| Fact-find Information | High | FCA COBS, GDPR |
| Contact Details | High | GDPR |
| Session Tokens | Critical | Security |
| Audit Logs | High | FCA, GDPR |

### 1.2 Threat Model

#### Attack Vectors & Mitigations

**1. Application Layer Attacks**

| Threat | Likelihood | Impact | Mitigation Priority |
|--------|-----------|--------|---------------------|
| SQL Injection | High | Critical | P0 |
| Cross-Site Scripting (XSS) | High | High | P0 |
| CSRF Attacks | Medium | High | P0 |
| XML/JSON Injection | Medium | High | P1 |
| Server-Side Request Forgery | Low | Medium | P2 |

**2. Authentication & Session Management**

| Threat | Likelihood | Impact | Mitigation Priority |
|--------|-----------|--------|---------------------|
| Credential Stuffing | High | Critical | P0 |
| Session Hijacking | Medium | Critical | P0 |
| Brute Force Attacks | High | High | P0 |
| Session Fixation | Low | High | P1 |
| Password Reset Exploitation | Medium | High | P1 |

**3. Data Protection Threats**

| Threat | Likelihood | Impact | Mitigation Priority |
|--------|-----------|--------|---------------------|
| Data Breach via Storage | Medium | Critical | P0 |
| Man-in-the-Middle | Medium | Critical | P0 |
| Insecure Data Transmission | Low | Critical | P0 |
| Unauthorized Data Access | Medium | Critical | P0 |

---

## 2. Security Testing Methodology

### 2.1 Testing Scope

**In-Scope:**
- Web application (all form steps)
- API endpoints
- Database layer
- Authentication mechanisms
- Data storage and encryption
- UK-specific validation logic
- Privacy and consent workflows

**Out-of-Scope:**
- Network infrastructure (unless directly impacting application)
- Third-party service providers (separate assessment required)
- Physical security

### 2.2 Testing Environment

**Requirements:**
- Isolated testing environment mirroring production
- Test data compliant with GDPR (synthetic/anonymized)
- Separate test database
- Comprehensive logging enabled
- Version control snapshot for rollback

---

## 3. Penetration Testing Protocol

### 3.1 SQL Injection Testing

#### Test Cases:

**TC-SQL-01: Input Field SQL Injection**
```
Test Inputs:
- Name field: ' OR '1'='1
- Email field: admin'--
- Address field: '; DROP TABLE clients;--
- Phone: 1' UNION SELECT * FROM users--

Expected Result: All inputs sanitized/rejected
Validation Method: 
- Input validation rejects malicious patterns
- Parameterized queries prevent execution
- Database logs show no unauthorized queries
- Error messages don't reveal database structure
```

**TC-SQL-02: Blind SQL Injection**
```
Test Inputs:
- Time-based: 1' AND SLEEP(5)--
- Boolean-based: 1' AND '1'='1
- Error-based: 1' AND CONVERT(int, (SELECT @@version))--

Expected Result: No time delays, no conditional responses
Validation Method: Response time analysis, behavior consistency
```

**TC-SQL-03: Second-Order SQL Injection**
```
Test Scenario:
1. Submit form with payload: test'); DROP TABLE clients;--
2. Verify data storage
3. Trigger data retrieval/processing
4. Verify database integrity

Expected Result: Payload stored as string, never executed
```

#### SQL Injection Security Controls Checklist:

- [ ] Parameterized queries/prepared statements implemented
- [ ] ORM (Object-Relational Mapping) properly configured
- [ ] Input validation on all form fields
- [ ] Least privilege database accounts
- [ ] Stored procedures used where applicable
- [ ] Error messages sanitized (no SQL details exposed)
- [ ] Web Application Firewall (WAF) rules configured
- [ ] Database activity monitoring enabled

---

### 3.2 Cross-Site Scripting (XSS) Testing

#### Test Cases:

**TC-XSS-01: Reflected XSS**
```
Test Inputs:
- Name: <script>alert('XSS')</script>
- Address: <img src=x onerror=alert('XSS')>
- Email: test@test.com<script>alert(document.cookie)</script>
- URL Parameters: ?step=<script>alert('XSS')</script>

Expected Result: Scripts encoded/sanitized, not executed
Validation Method: 
- View page source for HTML encoding
- Browser console shows no script execution
- Content-Security-Policy headers active
```

**TC-XSS-02: Stored XSS**
```
Test Scenario:
1. Submit form with: <svg/onload=alert('Stored XSS')>
2. Admin views submitted data
3. Client dashboard displays data

Expected Result: Script encoded in storage and display
Validation Method: Check database, admin panel, client views
```

**TC-XSS-03: DOM-Based XSS**
```
Test JavaScript Manipulation:
- Client-side validation bypass
- Hash fragment injection: #<img src=x onerror=alert(1)>
- LocalStorage/SessionStorage manipulation

Expected Result: Client-side sanitization prevents execution
```

**TC-XSS-04: Advanced XSS Payloads**
```
Test Inputs:
- Event handlers: <body onload=alert('XSS')>
- JavaScript protocol: <a href="javascript:alert('XSS')">
- SVG vectors: <svg><script>alert('XSS')</script></svg>
- CSS injection: <style>@import'http://attacker.com/xss.css';</style>
- Polyglot: jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('XSS') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('XSS')//>\x3e
```

#### XSS Security Controls Checklist:

- [ ] Output encoding implemented (HTML, JavaScript, URL, CSS contexts)
- [ ] Content Security Policy (CSP) headers configured
- [ ] HTTPOnly and Secure flags on cookies
- [ ] Input validation whitelist approach
- [ ] DOM manipulation sanitization (DOMPurify or similar)
- [ ] Template engine auto-escaping enabled
- [ ] X-XSS-Protection header configured
- [ ] Regular expression validation for expected patterns

---

### 3.3 Cross-Site Request Forgery (CSRF) Testing

#### Test Cases:

**TC-CSRF-01: Form Submission CSRF**
```html
Test Attack Page:
<!DOCTYPE html>
<html>
<body>
<form action="https://wealthmanagement.co.uk/api/submit-client" method="POST" id="csrf">
  <input type="hidden" name="name" value="Attacker Name">
  <input type="hidden" name="email" value="attacker@evil.com">
  <input type="hidden" name="account_type" value="premium">
</form>
<script>document.getElementById('csrf').submit();</script>
</body>
</html>

Expected Result: Request rejected due to missing/invalid CSRF token
Validation Method: 
- 403 Forbidden or similar error
- No data saved to database
- Security event logged
```

**TC-CSRF-02: AJAX Request CSRF**
```javascript
Test Script:
fetch('https://wealthmanagement.co.uk/api/update-profile', {
  method: 'POST',
  credentials: 'include',
  body: JSON.stringify({email: 'attacker@evil.com'})
});

Expected Result: CORS policy blocks request, anti-CSRF token required
```

**TC-CSRF-03: Multi-Step Form CSRF**
```
Test Scenario:
1. Initiate form at step 1
2. Attempt to jump to step 3 via crafted request
3. Submit final step with forged data

Expected Result: Session state validation prevents skip/manipulation
```

#### CSRF Security Controls Checklist:

- [ ] Synchronizer tokens implemented on all state-changing operations
- [ ] SameSite cookie attribute configured (Strict/Lax)
- [ ] Double-submit cookie pattern for AJAX requests
- [ ] Origin/Referer header validation
- [ ] Custom request headers for API calls
- [ ] Token rotation per session/request
- [ ] CORS policy properly configured
- [ ] Re-authentication for sensitive operations

---

### 3.4 Additional Vulnerability Testing

#### TC-AUTH-01: Authentication Bypass
```
Test Scenarios:
1. Direct URL access to form steps without authentication
2. Parameter manipulation: ?user_id=1 to ?user_id=2
3. Role escalation: client to admin
4. JWT token manipulation (if used)
5. Session token prediction

Expected Result: All unauthorized access attempts blocked
```

#### TC-AUTHZ-01: Authorization Testing
```
Test Scenarios:
1. Access other clients' data via ID manipulation
2. Admin functions accessible to regular users
3. API endpoint authorization checks
4. File upload directory traversal: ../../etc/passwd

Expected Result: Proper role-based access control enforced
```

#### TC-ENC-01: Encryption Verification
```
Tests:
1. TLS version check (TLS 1.2 minimum, prefer 1.3)
2. Cipher suite analysis (no weak ciphers)
3. Certificate validation (proper chain, not expired)
4. HSTS header presence
5. SSL Labs test (A rating minimum)
6. Data at rest encryption verification
7. Key management security review

Tools: testssl.sh, SSL Labs, nmap with ssl-enum-ciphers
```

#### TC-INPUT-01: Input Validation Testing
```
UK-Specific Tests:
1. Postcode validation: 
   Valid: SW1A 1AA, EC1A 1BB, W1A 0AX
   Invalid: 12345, AAAAA, SW1A1AA (no space)
   
2. Phone number validation:
   Valid: +44 20 7946 0958, 07700 900123
   Invalid: 123, (555) 1234, 001-234-5678
   
3. National Insurance Number:
   Valid: QQ 12 34 56 C
   Invalid: AA 12 34 56 C (invalid prefix)
   
4. Sort Code validation: 12-34-56 format
5. Account Number: 8 digits
6. Email: RFC 5322 compliance
7. Name fields: Unicode support, XSS prevention
8. Address: Special characters handling
```

#### TC-SESS-01: Session Management Testing
```
Tests:
1. Session timeout enforcement (15 minutes inactivity)
2. Session termination on logout
3. Concurrent session handling
4. Session fixation prevention
5. Session token entropy analysis
6. Session cookie attributes (Secure, HTTPOnly, SameSite)
7. Re-authentication for sensitive operations
```

---

## 4. Data Encryption Verification

### 4.1 Encryption-in-Transit Checklist

- [ ] **TLS Configuration**
  - [ ] TLS 1.3 enabled (preferred)
  - [ ] TLS 1.2 enabled (minimum)
  - [ ] TLS 1.0/1.1 disabled
  - [ ] SSL v2/v3 disabled
  
- [ ] **Cipher Suites** (Recommended order)
  - [ ] TLS_AES_256_GCM_SHA384
  - [ ] TLS_CHACHA20_POLY1305_SHA256
  - [ ] TLS_AES_128_GCM_SHA256
  - [ ] Weak ciphers disabled (RC4, DES, 3DES, MD5)
  
- [ ] **Certificate Management**
  - [ ] Valid SSL/TLS certificate from trusted CA
  - [ ] Certificate expiry > 30 days
  - [ ] Wildcard certificate or SAN configured
  - [ ] Certificate pinning implemented (mobile apps)
  - [ ] OCSP stapling enabled
  
- [ ] **Security Headers**
  - [ ] Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  - [ ] HTTP to HTTPS redirect (301)
  - [ ] All resources loaded over HTTPS (mixed content check)

### 4.2 Encryption-at-Rest Checklist

- [ ] **Database Encryption**
  - [ ] Transparent Data Encryption (TDE) enabled
  - [ ] Column-level encryption for sensitive fields:
    - [ ] National Insurance Number
    - [ ] Bank account details
    - [ ] Date of birth
    - [ ] Financial information
  - [ ] Encryption key rotation schedule defined
  - [ ] Key management via HSM or cloud KMS
  
- [ ] **File Storage Encryption**
  - [ ] Uploaded documents encrypted
  - [ ] Encryption algorithm: AES-256 minimum
  - [ ] Encrypted backups
  - [ ] Secure key storage (not in code/config)
  
- [ ] **Application-Level Encryption**
  - [ ] Passwords hashed with bcrypt/Argon2 (cost factor ≥ 12)
  - [ ] No reversible encryption for passwords
  - [ ] API keys/secrets stored in secure vault
  - [ ] Environment variables encrypted

### 4.3 Encryption Testing Protocol

**Test Case: ENC-VERIFY-01**
```bash
# TLS Configuration Test
nmap --script ssl-enum-ciphers -p 443 wealthmanagement.co.uk

# Expected: Only strong ciphers, TLS 1.2+, A rating

# Certificate Test
echo | openssl s_client -connect wealthmanagement.co.uk:443 -servername wealthmanagement.co.uk 2>/dev/null | openssl x509 -noout -dates -subject

# Expected: Valid dates, correct subject
```

**Test Case: ENC-VERIFY-02**
```sql
-- Database Encryption Verification
-- Verify TDE status
SELECT name, is_encrypted FROM sys.databases WHERE name = 'ClientDB';

-- Verify column encryption
SELECT * FROM sys.column_encryption_keys;

-- Expected: Encryption enabled, keys present
```

**Test Case: ENC-VERIFY-03**
```python
# Password Hashing Verification
import bcrypt

# Test password storage
password = "TestPassword123!"
hashed = hash_password(password)

# Verify:
# 1. Hash is not reversible
# 2. Same password produces different hashes (salt)
# 3. Hash starts with $2a$, $2b$, or $2y$ (bcrypt)
# 4. Cost factor ≥ 12 (e.g., $2b$12$...)
```

---

## 5. Authentication and Authorization Testing

### 5.1 Authentication Testing Protocol

#### Test Cases:

**TC-AUTH-02: Password Policy Enforcement**
```
Test Inputs:
1. Weak password: "password123"
2. Short password: "Ab1!"
3. No uppercase: "password123!"
4. No numbers: "Password!"
5. Common password: "Password123!"
6. Valid password: "W3@lthM@n@g3m3nt2024!"

Expected Result:
- Minimum 12 characters
- Uppercase, lowercase, number, special character required
- Common password dictionary check
- No user information in password (name, email)
- Password strength meter displayed
```

**TC-AUTH-03: Multi-Factor Authentication (MFA)**
```
Tests:
1. MFA enrollment process
2. TOTP code validation (time-based one-time password)
3. Backup codes generation and usage
4. MFA bypass attempt (should fail)
5. Rate limiting on MFA attempts
6. Recovery process if MFA device lost

Expected Result: MFA enforced for admin, optional/recommended for clients
```

**TC-AUTH-04: Account Lockout**
```
Test Scenario:
1. Attempt login with wrong password 5 times
2. Verify account locked
3. Wait for lockout period or admin unlock
4. Verify login restored

Expected Result:
- Lockout after 5 failed attempts
- Lockout duration: 15 minutes minimum
- Email notification sent to account owner
- CAPTCHA required after 3 attempts
- Admin notification for repeated lockouts
```

**TC-AUTH-05: Password Reset Security**
```
Test Scenarios:
1. Request password reset with valid email
2. Verify reset link sent (check token entropy)
3. Verify link expiration (max 1 hour)
4. Verify one-time use only
5. Test reset link reuse (should fail)
6. Test account enumeration via reset form
7. Verify old password invalidated after reset

Expected Result: Secure reset process, no information disclosure
```

### 5.2 Authorization Testing Protocol

**TC-AUTHZ-02: Role-Based Access Control**

```
User Roles:
1. Client (Standard)
2. Client (High Net Worth)
3. Financial Advisor
4. Compliance Officer
5. System Administrator

Test Matrix:

| Action | Client | Client HNW | Advisor | Compliance | Admin |
|--------|--------|------------|---------|------------|-------|
| Submit own form | ✓ | ✓ | ✗ | ✗ | ✗ |
| View own data | ✓ | ✓ | ✗ | ✗ | ✓ |
| Edit own data | ✓ | ✓ | ✗ | ✗ | ✓ |
| View client forms | ✗ | ✗ | ✓ (assigned) | ✓ (all) | ✓ |
| Export data | ✗ | ✗ | ✓ (assigned) | ✓ (all) | ✓ |
| Delete records | ✗ | ✗ | ✗ | ✗ | ✓ |
| Access audit logs | ✗ | ✗ | ✗ | ✓ | ✓ |
| Manage users | ✗ | ✗ | ✗ | ✗ | ✓ |

Test Method: Attempt each action as each role, verify proper access control
```

**TC-AUTHZ-03: Horizontal Access Control**
```
Test Scenario:
1. Login as Client A (ID: 1001)
2. Access Client A's form: /api/client/1001/form
3. Attempt to access Client B's form: /api/client/1002/form
4. Attempt parameter manipulation: /api/client/1002/form?user=1001
5. Test API endpoints with different client IDs

Expected Result: Access denied for other clients' data
```

**TC-AUTHZ-04: Vertical Privilege Escalation**
```
Test Scenarios:
1. Client attempts to access: /admin/dashboard
2. Client attempts to call: POST /api/admin/delete-user
3. Advisor attempts compliance-only functions
4. Role modification via parameter tampering
5. JWT token role claim manipulation (if applicable)

Expected Result: All escalation attempts blocked, logged
```

### 5.3 Session Management Security

**Session Configuration Requirements:**

```json
{
  "session": {
    "timeout_minutes": 15,
    "absolute_timeout_minutes": 480,
    "renewal_threshold_minutes": 5,
    "cookie_config": {
      "httpOnly": true,
      "secure": true,
      "sameSite": "Strict",
      "domain": ".wealthmanagement.co.uk",
      "path": "/",
      "maxAge": 900
    },
    "token_length_bytes": 32,
    "concurrent_sessions_allowed": 1,
    "ip_binding": true,
    "user_agent_binding": true
  }
}
```

**Test Case: SESS-02**
```
Session Security Tests:
1. Verify session expires after 15 minutes inactivity
2. Verify absolute timeout after 8 hours
3. Verify session renewed on activity (before timeout)
4. Verify logout destroys server-side session
5. Verify no session in URL parameters
6. Verify session token randomness (entropy test)
7. Verify new session on login (prevent fixation)
8. Verify concurrent session handling (logout other sessions)
```

---

## 6. GDPR Compliance Verification

### 6.1 GDPR Principles Assessment

#### Principle 1: Lawfulness, Fairness, and Transparency

**Checklist:**
- [ ] **Lawful Basis Documented**
  - [ ] Contract (wealth management services)
  - [ ] Consent (marketing communications)
  - [ ] Legal obligation (FCA requirements)
  - [ ] Legitimate interests (fraud prevention)
  
- [ ] **Transparency Requirements**
  - [ ] Privacy Notice accessible before data collection
  - [ ] Privacy Notice written in plain English
  - [ ] Contact details for Data Protection Officer (DPO)
  - [ ] Information about data transfers (if applicable)
  - [ ] Retention periods specified
  - [ ] Right to withdraw consent explained

**Test Case: GDPR-TRANS-01**
```
Verification Steps:
1. Access form without accepting privacy policy
2. Verify privacy notice link present and functional
3. Review privacy notice for GDPR completeness:
   - Data controller identity
   - Purpose of processing
   - Legal basis for each purpose
   - Recipients of data
   - Retention period
   - Data subject rights
   - Right to lodge complaint with ICO
   - Automated decision-making disclosure
4. Verify understandable language (readability score)

Expected Result: Complete, accessible privacy information
```

#### Principle 2: Purpose Limitation

**Checklist:**
- [ ] Data collection limited to specified purposes
- [ ] No secondary use without consent
- [ ] Purpose clearly stated in privacy notice
- [ ] Form fields justified by purpose
- [ ] Optional fields clearly marked

**Test Case: GDPR-PURP-01**
```
Data Minimization Review:
1. Review each form field
2. Document business justification
3. Identify fields that could be optional
4. Remove unjustified mandatory fields

Example Assessment:
- Name: Required (contract, identification)
- Email: Required (contract communication)
- Phone: Required (FCA contact requirements)
- Date of Birth: Required (age verification, suitability)
- National Insurance: Required (tax reporting)
- Marital Status: Required (fact-find)
- Children: Required (estate planning)
- Social Media: NOT REQUIRED (remove or make optional)
```

#### Principle 3: Data Minimization

**Checklist:**
- [ ] Only essential data collected
- [ ] No "nice to have" fields required
- [ ] Progressive disclosure in multi-step form
- [ ] Conditional fields (only show when relevant)
- [ ] Drop-downs instead of free text where possible

#### Principle 4: Accuracy

**Checklist:**
- [ ] Email verification (double opt-in)
- [ ] Phone verification (SMS code)
- [ ] Address validation (postcode lookup API)
- [ ] Update mechanism for clients to correct data
- [ ] Regular data quality reviews scheduled
- [ ] Duplicate detection

**Test Case: GDPR-ACC-01**
```
Verification Tests:
1. Submit form with incorrect email (typo)
2. Verify email verification sent
3. Test correction workflow
4. Verify client can update own information
5. Test data validation prevents incorrect formats

Expected Result: Mechanisms ensure data accuracy
```

#### Principle 5: Storage Limitation

**Checklist:**
- [ ] **Retention Policy Documented**
  - [ ] Active clients: Duration of relationship + 7 years (FCA requirement)
  - [ ] Prospects (no contract): 2 years
  - [ ] Marketing consent: Until withdrawn + 30 days
  - [ ] Audit logs: 7 years
  
- [ ] **Automated Deletion Process**
  - [ ] Scheduled job identifies expired data
  - [ ] Review process before deletion
  - [ ] Secure deletion (overwrite, not just marked deleted)
  - [ ] Deletion log maintained

**Test Case: GDPR-RET-01**
```sql
-- Retention Verification Query
SELECT 
  client_id,
  created_date,
  last_active_date,
  status,
  DATEDIFF(day, last_active_date, GETDATE()) as days_inactive,
  CASE 
    WHEN status = 'prospect' AND DATEDIFF(year, created_date, GETDATE()) > 2 
      THEN 'DELETE'
    WHEN status = 'closed' AND DATEDIFF(year, last_active_date, GETDATE()) > 7 
      THEN 'DELETE'
    ELSE 'RETAIN'
  END as retention_action
FROM clients
WHERE status IN ('prospect', 'closed');

-- Verify automated deletion process exists and runs
```

#### Principle 6: Integrity and Confidentiality

**Checklist:**
- [ ] Encryption in transit (TLS 1.2+)
- [ ] Encryption at rest (AES-256)
- [ ] Access controls (RBAC)
- [ ] Audit logging
- [ ] Regular security testing
- [ ] Incident response plan
- [ ] Data breach notification procedure (72-hour ICO notification)
- [ ] Employee training on data protection

### 6.2 Data Subject Rights Implementation

#### Right of Access (Subject Access Request - SAR)

**Requirements:**
- [ ] Process to receive SAR (email, portal, postal)
- [ ] Identity verification procedure
- [ ] Response within 1 month (extendable to 3 months)
- [ ] Free of charge (unless excessive/repetitive)
- [ ] Provide copy of data in structured format
- [ ] Include supplementary information (purposes, recipients, retention)

**Test Case: GDPR-SAR-01**
```
SAR Testing Procedure:
1. Submit SAR as test client
2. Verify identity verification requested
3. Verify response timeline tracked
4. Verify data export includes:
   - All personal data held
   - Source of data
   - Processing purposes
   - Categories of recipients
   - Retention period
   - Right to rectification/erasure
   - Right to lodge complaint
5. Verify format is machine-readable (JSON/CSV)
6. Verify no data omitted

Expected Result: Complete data provided within 1 month
```

**SAR Response Template:**
```json
{
  "data_subject": {
    "name": "John Smith",
    "email": "john.smith@example.com",
    "verification_method": "Email link + security questions",
    "request_date": "2024-01-15",
    "response_date": "2024-02-10"
  },
  "personal_data": {
    "identity": {
      "name": "...",
      "date_of_birth": "...",
      "ni_number": "...",
      "source": "Client form submission",
      "purpose": "Contract performance",
      "retention": "7 years after relationship ends"
    },
    "contact": {...},
    "financial": {...},
    "marketing_preferences": {...}
  },
  "processing_activities": [...],
  "third_party_disclosures": [...],
  "your_rights": {...}
}
```

#### Right to Rectification

**Requirements:**
- [ ] Mechanism for clients to update own data
- [ ] Process to request corrections
- [ ] Response within 1 month
- [ ] Third parties notified of corrections
- [ ] Audit trail of changes

**Test Case: GDPR-RECT-01**
```
Rectification Testing:
1. Login to client portal
2. Navigate to "My Information"
3. Update email address
4. Verify change saved
5. Verify email to old and new addresses
6. Verify audit log entry created
7. Submit correction request (e.g., wrong DOB)
8. Verify staff review process
9. Verify correction applied
10. Verify notification sent

Expected Result: Corrections processed within 1 month
```

#### Right to Erasure ("Right to be Forgotten")

**Requirements:**
- [ ] Process to receive erasure requests
- [ ] Evaluation criteria (legal obligations vs. right to erasure)
- [ ] FCA retention requirements considered
- [ ] Response within 1 month
- [ ] Confirmation of erasure
- [ ] Third parties notified
- [ ] Suppression list to prevent re-contact

**Exemptions to Erasure:**
- Legal obligation to retain (FCA 7-year requirement)
- Exercise/defense of legal claims
- Public interest

**Test Case: GDPR-ERAS-01**
```
Erasure Testing Scenarios:

Scenario 1: Prospect (no contract, no FCA obligation)
1. Submit erasure request
2. Verify eligibility assessment
3. Verify deletion within 1 month
4. Verify confirmation sent
5. Verify data actually deleted (not just flagged)
6. Verify added to suppression list
7. Attempt to re-access data (should fail)

Expected Result: Data erased, cannot be recovered

Scenario 2: Former client (FCA retention applies)
1. Submit erasure request
2. Verify eligibility assessment
3. Verify refusal with legal basis explained
4. Verify FCA retention schedule documented
5. Verify deletion scheduled for end of retention period

Expected Result: Erasure refused with clear explanation

Scenario 3: Active client
1. Submit erasure request
2. Verify impact assessment (contract performance)
3. Verify alternative options offered (data restriction)
4. If proceeding: verify account closure process
5. Verify 7-year retention for closed accounts

Expected Result: Client informed of implications
```

**Deletion Verification:**
```sql
-- Test deletion completeness
-- After erasure, these queries should return no results or anonymized data

SELECT * FROM clients WHERE client_id = 'TEST123';
SELECT * FROM client_documents WHERE client_id = 'TEST123';
SELECT * FROM fact_find WHERE client_id = 'TEST123';
SELECT * FROM audit_log WHERE client_id = 'TEST123'; -- May be retained
SELECT * FROM backups WHERE client_id = 'TEST123'; -- Verify deletion from backups

-- Verify suppression list entry
SELECT * FROM suppression_list WHERE email = 'test@example.com';
```

#### Right to Data Portability

**Requirements:**
- [ ] Structured, commonly used, machine-readable format
- [ ] Direct transmission to another controller (if technically feasible)
- [ ] Response within 1 month
- [ ] Free of charge

**Test Case: GDPR-PORT-01**
```
Data Portability Testing:
1. Submit data portability request
2. Verify format options offered (JSON, CSV, XML)
3. Verify export includes:
   - All data provided by client
   - Data generated by processing (e.g., risk profile)
4. Verify schema documentation included
5. Verify data completeness
6. Verify file is machine-readable
7. Test import into another system

Expected Result: Complete data in portable format
```

**Data Export Format Example:**
```json
{
  "export_metadata": {
    "format_version": "1.0",
    "export_date": "2024-01-15T10:30:00Z",
    "data_controller": "XYZ Wealth Management Ltd",
    "schema": "https://wealthmanagement.co.uk/schemas/client-data-v1.json"
  },
  "client_data": {
    "personal_information":