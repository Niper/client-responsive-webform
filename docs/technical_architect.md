# Technical Architecture and Security Design

**Agent:** technical_architect
**Job:** Client Responsive Webform

---

# Technical Architecture and Security Design
## Client Responsive Webform - UK Wealth Management

**Document Version:** 1.0  
**Date:** 2024  
**Classification:** CONFIDENTIAL  
**Compliance Scope:** UK GDPR, FCA COBS, SYSC, Data Protection Act 2018

---

## 1. Executive Summary

This document outlines the technical architecture for a secure, compliant, multi-step web form application designed to capture client information for UK wealth management firms. The architecture prioritizes data security, regulatory compliance, scalability, and user experience while maintaining audit trails and supporting integration with enterprise systems.

**Key Design Principles:**
- Security by Design (OWASP Top 10 mitigation)
- Privacy by Design (UK GDPR Article 25)
- FCA Compliance (Senior Management Arrangements, Systems and Controls)
- Scalability and Performance
- Audit Trail and Non-repudiation
- Resilience and Business Continuity

---

## 2. System Architecture Overview

### 2.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER LAYER                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │   Desktop    │  │    Tablet    │  │    Mobile    │         │
│  │   Browser    │  │    Browser   │  │    Browser   │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
└─────────────────────────────────────────────────────────────────┘
                              │
                         HTTPS/TLS 1.3
                              │
┌─────────────────────────────────────────────────────────────────┐
│                    CDN & WAF LAYER                               │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Cloudflare/AWS CloudFront + WAF                          │  │
│  │  - DDoS Protection  - Rate Limiting  - Bot Detection     │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────────┐
│                   APPLICATION LAYER                              │
│  ┌────────────────────────────────────────────────────────┐    │
│  │              Frontend Application                       │    │
│  │  React 18+ (TypeScript) + Next.js 14                   │    │
│  │  - React Hook Form + Zod Validation                    │    │
│  │  - Progressive Enhancement                              │    │
│  │  - Accessibility (WCAG 2.1 AA)                         │    │
│  └────────────────────────────────────────────────────────┘    │
│                              │                                   │
│                        REST/GraphQL API                          │
│                              │                                   │
│  ┌────────────────────────────────────────────────────────┐    │
│  │         API Gateway (AWS API Gateway/Kong)             │    │
│  │  - Authentication  - Rate Limiting  - Request Logging  │    │
│  └────────────────────────────────────────────────────────┘    │
│                              │                                   │
│  ┌────────────────────────────────────────────────────────┐    │
│  │           Backend API Services                          │    │
│  │  Node.js/NestJS OR Python/FastAPI                      │    │
│  │  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐ │    │
│  │  │ Form Service │  │ Auth Service │  │ Audit Svc   │ │    │
│  │  └──────────────┘  └──────────────┘  └─────────────┘ │    │
│  │  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐ │    │
│  │  │Validation Svc│  │Encrypt Svc   │  │Integration  │ │    │
│  │  └──────────────┘  └──────────────┘  └─────────────┘ │    │
│  └────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────────┐
│                      DATA LAYER                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │  PostgreSQL  │  │    Redis     │  │  AWS KMS /   │         │
│  │  (Encrypted) │  │  (Sessions)  │  │ Azure KeyVault│        │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
│  ┌──────────────┐  ┌──────────────┐                            │
│  │  Document    │  │   Audit Log  │                            │
│  │  Storage(S3) │  │  (Immutable) │                            │
│  └──────────────┘  └──────────────┘                            │
└─────────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────────┐
│                   INTEGRATION LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │  CRM System  │  │   AML/KYC    │  │   Document   │         │
│  │  (Salesforce)│  │   Screening  │  │  Management  │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
└─────────────────────────────────────────────────────────────────┘
```

### 2.2 Architecture Patterns

- **Frontend**: Single Page Application (SPA) with Server-Side Rendering (SSR)
- **Backend**: Microservices architecture with API Gateway pattern
- **Data**: Event Sourcing for audit trail, CQRS for read/write optimization
- **Security**: Zero Trust Architecture, Defense in Depth
- **Integration**: Event-Driven Architecture with message queues

---

## 3. Technology Stack Recommendations

### 3.1 Frontend Stack

| Component | Technology | Justification |
|-----------|-----------|---------------|
| **Framework** | React 18+ with TypeScript | Industry standard, strong ecosystem, type safety, concurrent features |
| **Meta Framework** | Next.js 14 (App Router) | SSR/SSG for performance, built-in security headers, API routes |
| **Form Management** | React Hook Form | Performance optimized, reduces re-renders, excellent validation |
| **Validation** | Zod | Type-safe schema validation, runtime type checking |
| **State Management** | Zustand + React Query | Lightweight, server state management, caching |
| **UI Components** | Tailwind CSS + shadcn/ui | Customizable, accessible components (WCAG 2.1 AA) |
| **Testing** | Jest + React Testing Library + Playwright | Unit, integration, and E2E testing |

**Alternative Stack (Enterprise):** Angular 17+ with RxJS for organizations with existing Angular expertise.

### 3.2 Backend Stack

**Primary Recommendation: Node.js/NestJS**

| Component | Technology | Justification |
|-----------|-----------|---------------|
| **Runtime** | Node.js 20 LTS | Performance, non-blocking I/O, shared language with frontend |
| **Framework** | NestJS | Enterprise-grade, TypeScript-native, modular architecture |
| **API Protocol** | REST + GraphQL (optional) | REST for simplicity, GraphQL for flexible data fetching |
| **Validation** | class-validator + class-transformer | Decorator-based validation, DTO transformation |
| **ORM** | Prisma | Type-safe queries, migrations, excellent DX |
| **Authentication** | Passport.js + JWT | Extensible strategy pattern, industry standard |
| **Testing** | Jest + Supertest | Comprehensive testing suite |

**Alternative Stack:** Python/FastAPI for organizations with Python expertise (excellent async support, automatic OpenAPI docs).

### 3.3 Database & Storage

| Component | Technology | Justification |
|-----------|-----------|---------------|
| **Primary Database** | PostgreSQL 15+ | ACID compliance, JSON support, strong encryption, mature |
| **Encryption** | pgcrypto + AWS RDS encryption | Column-level and at-rest encryption |
| **Cache/Session** | Redis 7+ with encryption | High performance, session management, rate limiting |
| **Document Storage** | AWS S3 / Azure Blob Storage | Scalable, encrypted, versioning, lifecycle policies |
| **Audit Logs** | PostgreSQL + AWS CloudWatch | Immutable logs, tamper-evident, long-term retention |
| **Secrets Management** | AWS Secrets Manager / Azure Key Vault | Automatic rotation, audit logging, encryption |

### 3.4 Infrastructure & DevOps

| Component | Technology | Justification |
|-----------|-----------|---------------|
| **Cloud Provider** | AWS or Azure | FCA-compliant, UK data residency, comprehensive services |
| **Hosting** | AWS ECS/EKS or Azure AKS | Container orchestration, auto-scaling, high availability |
| **CDN + WAF** | Cloudflare or AWS CloudFront | DDoS protection, WAF rules, edge caching |
| **CI/CD** | GitHub Actions / GitLab CI | Automated testing, security scanning, deployment |
| **Monitoring** | Datadog / New Relic | APM, logs, metrics, alerts, compliance reporting |
| **IaC** | Terraform | Multi-cloud support, version control, reproducibility |

---

## 4. Database Design

### 4.1 Entity Relationship Diagram

```
┌─────────────────────────┐
│      FormSession        │
│─────────────────────────│
│ id: UUID (PK)          │
│ session_token: TEXT    │
│ user_agent: TEXT       │
│ ip_address_hash: TEXT  │
│ current_step: INT      │
│ expires_at: TIMESTAMP  │
│ created_at: TIMESTAMP  │
│ updated_at: TIMESTAMP  │
└─────────────────────────┘
            │ 1
            │
            │ *
┌─────────────────────────┐
│      ClientData         │
│─────────────────────────│
│ id: UUID (PK)          │
│ session_id: UUID (FK)  │
│ data_category: ENUM    │
│ encrypted_data: BYTEA  │
│ encryption_key_id: TEXT│
│ checksum: TEXT         │
│ created_at: TIMESTAMP  │
│ updated_at: TIMESTAMP  │
│ retention_until: DATE  │
└─────────────────────────┘
            │
            │
┌─────────────────────────┐       ┌─────────────────────────┐
│    PersonalDetails      │       │     FactFindData        │
│─────────────────────────│       │─────────────────────────│
│ id: UUID (PK)          │       │ id: UUID (PK)          │
│ client_data_id: FK     │       │ client_data_id: FK     │
│ title: TEXT (enc)      │       │ employment_status: TEXT│
│ first_name: TEXT (enc) │       │ income_range: TEXT     │
│ last_name: TEXT (enc)  │       │ assets: TEXT (enc)     │
│ dob: DATE (enc)        │       │ objectives: TEXT       │
│ national_ins: TEXT(enc)│       │ risk_profile: ENUM     │
│ address_json: JSONB    │       │ created_at: TIMESTAMP  │
│ phone: TEXT (enc)      │       │ updated_at: TIMESTAMP  │
│ email: TEXT (enc)      │       └─────────────────────────┘
│ created_at: TIMESTAMP  │
│ updated_at: TIMESTAMP  │
└─────────────────────────┘

┌─────────────────────────┐       ┌─────────────────────────┐
│      AuditLog           │       │     DocumentMetadata    │
│─────────────────────────│       │─────────────────────────│
│ id: UUID (PK)          │       │ id: UUID (PK)          │
│ session_id: UUID (FK)  │       │ session_id: UUID (FK)  │
│ event_type: TEXT       │       │ document_type: TEXT    │
│ user_id: UUID          │       │ storage_path: TEXT (enc)│
│ ip_address_hash: TEXT  │       │ file_hash: TEXT        │
│ action: TEXT           │       │ encryption_key_id: TEXT│
│ entity_type: TEXT      │       │ mime_type: TEXT        │
│ entity_id: UUID        │       │ file_size: BIGINT      │
│ changes: JSONB         │       │ virus_scan_status: ENUM│
│ timestamp: TIMESTAMP   │       │ uploaded_at: TIMESTAMP │
│ (IMMUTABLE)            │       │ retention_until: DATE  │
└─────────────────────────┘       └─────────────────────────┘

┌─────────────────────────┐       ┌─────────────────────────┐
│      Consent            │       │   DataProcessingLog     │
│─────────────────────────│       │─────────────────────────│
│ id: UUID (PK)          │       │ id: UUID (PK)          │
│ session_id: UUID (FK)  │       │ session_id: UUID (FK)  │
│ consent_type: ENUM     │       │ processing_type: TEXT  │
│ granted: BOOLEAN       │       │ legal_basis: TEXT      │
│ consent_text: TEXT     │       │ purpose: TEXT          │
│ version: TEXT          │       │ data_categories: TEXT[]│
│ ip_address_hash: TEXT  │       │ timestamp: TIMESTAMP   │
│ timestamp: TIMESTAMP   │       └─────────────────────────┘
│ withdrawn_at: TIMESTAMP│
└─────────────────────────┘
```

### 4.2 Data Encryption Strategy

**Encryption at Rest:**
- Database-level: AWS RDS encryption with KMS
- Column-level: pgcrypto for PII fields (name, address, phone, email, NI number)
- Document storage: S3 server-side encryption with KMS

**Encryption in Transit:**
- TLS 1.3 for all communications
- Certificate pinning for API calls
- Encrypted database connections

**Key Management:**
- AWS KMS with automatic key rotation
- Separate keys for different data classifications
- Hardware Security Module (HSM) backing
- Key usage audit logging

### 4.3 Data Retention and Deletion

```sql
-- Pseudonymization function for GDPR Article 17
CREATE OR REPLACE FUNCTION pseudonymize_client_data(client_id UUID)
RETURNS VOID AS $$
BEGIN
    -- Anonymize personal details
    UPDATE PersonalDetails
    SET 
        first_name = 'REDACTED',
        last_name = 'REDACTED',
        email = 'redacted@pseudonymized.local',
        phone = 'REDACTED',
        national_ins = 'REDACTED',
        address_json = '{"pseudonymized": true}'::jsonb
    WHERE client_data_id = client_id;
    
    -- Log the action
    INSERT INTO AuditLog (event_type, action, entity_id)
    VALUES ('GDPR_DELETION', 'PSEUDONYMIZE', client_id);
END;
$$ LANGUAGE plpgsql;

-- Automatic retention policy
CREATE TABLE data_retention_policy (
    data_category TEXT PRIMARY KEY,
    retention_days INTEGER NOT NULL,
    legal_basis TEXT NOT NULL
);

-- FCA requires 7 years for most financial advice records
INSERT INTO data_retention_policy VALUES
    ('CLIENT_PERSONAL_DATA', 2555, 'FCA COBS 9.4'),
    ('FACT_FIND', 2555, 'FCA COBS 9.4'),
    ('CONSENT_RECORDS', 2555, 'GDPR Article 7(1)'),
    ('AUDIT_LOGS', 3650, 'FCA SYSC 9');
```

---

## 5. Security Architecture

### 5.1 OWASP Top 10 Mitigation Strategy

| Vulnerability | Mitigation Strategy |
|---------------|---------------------|
| **A01: Broken Access Control** | - Implement role-based access control (RBAC)<br>- Session validation on every request<br>- Deny by default principle<br>- API rate limiting per session |
| **A02: Cryptographic Failures** | - TLS 1.3 enforced<br>- Strong cipher suites only<br>- HSTS headers<br>- Encryption at rest (AES-256)<br>- Secure key management (KMS) |
| **A03: Injection** | - Parameterized queries (Prisma ORM)<br>- Input validation (Zod schemas)<br>- Output encoding<br>- CSP headers<br>- NoSQL injection prevention |
| **A04: Insecure Design** | - Threat modeling<br>- Security requirements<br>- Secure SDLC<br>- Design review process |
| **A05: Security Misconfiguration** | - Secure defaults<br>- Automated security scanning<br>- Minimal attack surface<br>- Security headers (Helmet.js) |
| **A06: Vulnerable Components** | - Automated dependency scanning (Snyk/Dependabot)<br>- Regular updates<br>- SBOM generation<br>- Vulnerability alerts |
| **A07: Auth Failures** | - Multi-factor authentication<br>- Secure session management<br>- Rate limiting<br>- Account lockout<br>- Password complexity requirements |
| **A08: Software/Data Integrity** | - Code signing<br>- SRI for CDN resources<br>- Checksum verification<br>- Immutable audit logs |
| **A09: Logging Failures** | - Comprehensive audit logging<br>- Log integrity protection<br>- Centralized log management<br>- Anomaly detection |
| **A10: SSRF** | - URL validation<br>- Network segmentation<br>- Disable unnecessary protocols<br>- Allowlist approach |

### 5.2 Authentication and Authorization

**Authentication Flow:**

```
┌──────────┐                                    ┌──────────┐
│  Client  │                                    │ Auth API │
└────┬─────┘                                    └────┬─────┘
     │                                                │
     │  1. Request Form Access                        │
     │───────────────────────────────────────────────>│
     │                                                │
     │  2. Generate Session Token + CSRF Token        │
     │<───────────────────────────────────────────────│
     │     (HTTP-Only Cookie + Response Header)       │
     │                                                │
     │  3. Submit Form Data (with CSRF + Session)     │
     │───────────────────────────────────────────────>│
     │                                                │
     │  4. Validate Session, CSRF, Rate Limit         │
     │                 ┌───────────┐                  │
     │                 │   Redis   │                  │
     │                 └─────┬─────┘                  │
     │                       │                        │
     │  5. Process Data (if valid)                    │
     │<───────────────────────────────────────────────│
     │                                                │
```

**Security Controls:**

1. **Session Management:**
   - Secure, HTTP-only, SameSite=Strict cookies
   - Random session tokens (cryptographically secure)
   - 30-minute idle timeout
   - Absolute timeout: 2 hours
   - Session invalidation on completion

2. **CSRF Protection:**
   - Double-submit cookie pattern
   - Synchronizer token pattern
   - Origin header validation

3. **Rate Limiting:**
   - 5 requests per minute per IP
   - 10 form submissions per hour per session
   - Exponential backoff on failures

4. **API Authentication (for internal services):**
   - OAuth 2.0 with JWT tokens
   - mTLS for service-to-service communication
   - API keys with rotation policy

### 5.3 Input Validation and Sanitization

**Validation Layers:**

```typescript
// Layer 1: Client-Side Validation (UX, not security)
import { z } from 'zod';

const PersonalDetailsSchema = z.object({
  title: z.enum(['Mr', 'Mrs', 'Ms', 'Miss', 'Dr', 'Other']),
  firstName: z.string()
    .min(1, 'First name required')
    .max(50, 'First name too long')
    .regex(/^[a-zA-Z\s\-']+$/, 'Invalid characters'),
  lastName: z.string()
    .min(1, 'Last name required')
    .max(50, 'Last name too long')
    .regex(/^[a-zA-Z\s\-']+$/, 'Invalid characters'),
  email: z.string()
    .email('Invalid email format')
    .max(255)
    .toLowerCase(),
  phone: z.string()
    .regex(/^(\+44|0)[0-9]{10}$/, 'Invalid UK phone number'),
  postcode: z.string()
    .regex(/^[A-Z]{1,2}[0-9R][0-9A-Z]?\s?[0-9][A-Z]{2}$/i, 
           'Invalid UK postcode'),
  nationalInsurance: z.string()
    .regex(/^[A-Z]{2}[0-9]{6}[A-Z]$/i, 'Invalid NI number')
    .optional(),
});

// Layer 2: Server-Side Validation (security boundary)
import { IsEmail, IsNotEmpty, Matches, MaxLength } from 'class-validator';
import { sanitize } from 'class-sanitizer';

export class PersonalDetailsDto {
  @IsNotEmpty()
  @MaxLength(50)
  @Matches(/^[a-zA-Z\s\-']+$/)
  @sanitize()
  firstName: string;

  @IsEmail()
  @MaxLength(255)
  @sanitize()
  email: string;
  
  // ... additional fields
}

// Layer 3: Database Constraints
CREATE TABLE personal_details (
    first_name VARCHAR(50) NOT NULL CHECK (first_name ~ '^[a-zA-Z\s\-'']+$'),
    email VARCHAR(255) NOT NULL CHECK (email ~* '^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$'),
    -- ... constraints
);
```

### 5.4 Content Security Policy

```typescript
// Security Headers Configuration
const securityHeaders = {
  'Content-Security-Policy': [
    "default-src 'self'",
    "script-src 'self' 'unsafe-inline' 'unsafe-eval' https://cdn.jsdelivr.net",
    "style-src 'self' 'unsafe-inline' https://fonts.googleapis.com",
    "font-src 'self' https://fonts.gstatic.com",
    "img-src 'self' data: https:",
    "connect-src 'self' https://api.yourplatform.co.uk",
    "frame-ancestors 'none'",
    "base-uri 'self'",
    "form-action 'self'",
  ].join('; '),
  'X-Frame-Options': 'DENY',
  'X-Content-Type-Options': 'nosniff',
  'X-XSS-Protection': '1; mode=block',
  'Referrer-Policy': 'strict-origin-when-cross-origin',
  'Permissions-Policy': 'geolocation=(), microphone=(), camera=()',
  'Strict-Transport-Security': 'max-age=63072000; includeSubDomains; preload',
};
```

---

## 6. API Design and Contracts

### 6.1 RESTful API Structure

**Base URL:** `https://api.yourplatform.co.uk/v1`

**Endpoints:**

```yaml
openapi: 3.0.3
info:
  title: Client Onboarding API
  version: 1.0.0
  description: API for collecting client information for wealth management

servers:
  - url: https://api.yourplatform.co.uk/v1
    description: Production
  - url: https://api-staging.yourplatform.co.uk/v1
    description: Staging

paths:
  /sessions:
    post:
      summary: Initialize form session
      tags: [Session]
      security: []
      responses:
        '201':
          description: Session created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Session'
          headers:
            Set-Cookie:
              schema:
                type: string
                example: sessionToken=abc123; HttpOnly; Secure; SameSite=Strict
            X-CSRF-Token:
              schema:
                type: string
  
  /sessions/{sessionId}/personal-details:
    post:
      summary: Submit personal details (Step 1)
      tags: [Form Data]
      security:
        - sessionAuth: []
        - csrfToken: []
      parameters:
        - in: path
          name: sessionId
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PersonalDetails'
      responses:
        '200':
          description: Data saved successfully
        '400':
          description: Validation error
        '429':
          description: Rate limit exceeded
  
  /sessions/{sessionId}/fact-find:
    post:
      summary: Submit fact find information (Step 2)
      tags: [Form Data]
      security:
        - sessionAuth: []
        - csrfToken: []
      # ... similar structure
  
  /sessions/{sessionId}/documents:
    post:
      summary: Upload supporting documents
      tags: [Documents]
      security:
        - sessionAuth: []
        - csrfToken: []
      requestBody:
        required: true
        content:
          multipart/form-data:
            schema:
              type: object
              properties:
                file:
                  type: string
                  format: binary
                documentType:
                  type: string
                  enum: [ID_PROOF, ADDRESS_PROOF, OTHER]
      responses:
        '201':
          description: Document uploaded
        '413':
          description: File too large (max 10MB)
  
  /sessions/{sessionId}/submit:
    post:
      summary: Final submission and processing
      tags: [Submission]
      security:
        - sessionAuth: []
        - csrfToken: []
      responses:
        '200':
          description: Submission successful
          content:
            application/json:
              schema:
                type: object
                properties:
                  submissionId:
                    type: string
                    format: uuid
                  status:
                    type: string
                    enum: [PENDING_REVIEW, PROCESSING]
                  referenceNumber:
                    type: string

components:
  securitySchemes:
    sessionAuth:
      type: apiKey
      in: cookie
      name: sessionToken
    csrfToken:
      type: apiKey
      in: header
      name: X-CSRF-Token
  
  schemas:
    Session:
      type: object
      properties:
        sessionId:
          type: string
          format: uuid
        expiresAt:
          type: string
          format: date-time
        currentStep:
          type: integer
          minimum: 1
          maximum: 5
    
    PersonalDetails:
      type: object
      required:
        - title
        - firstName
        - lastName
        - dateOfBirth
        - email
        - phone
        - address
      properties:
        title:
          type: string
          enum: [Mr, Mrs, Ms, Miss, Dr, Other]
        firstName:
          type: string
          minLength: 1
          maxLength: 50
          pattern: '^[a-zA-Z\s\-'']+$'
        lastName:
          type: string
          minLength: 1
          maxLength: 50
          pattern: '^[a-zA-Z\s\-'']+$'
        dateOfBirth:
          type: string
          format: date
        email:
          type: string
          format: email
          maxLength: 255
        phone:
          type: string
          pattern: '^(\+44|0)[0-9]{10}$'
        address:
          $ref: '#/components/schemas/UKAddress'
        nationalInsurance:
          type: string
          pattern: '^[A-Z]{2}[0-9]{6}[A-Z]$'
    
    UKAddress:
      type: object
      required:
        - addressLine1
        - city
        - postcode
      properties:
        addressLine1:
          type: string
          maxLength: 100
        addressLine2:
          type: string
          maxLength: 100
        city:
          type: string
          maxLength: 50
        county:
          type: string
          maxLength: 50
        postcode:
          type: string
          pattern: '^[A-Z]{1,2}[0-9R][0-9A-Z]?\s?[0-9][A-Z]{2}$'
    
    FactFind:
      type: object
      properties:
        employmentStatus:
          type: string
          enum: [EMPLOYED, SELF_EMPLOYED, RETIRED, UNEMPLOYED, OTHER]
        annualIncome:
          type: string
          enum: ['0-25000', '25001-50000', '50001-100000', '100001-250000', '250000+']
        liquidAssets:
          type: string
          enum: ['0-10000', '10001-50000', '50001-100000', '100001-500000', '500000+']
        investmentObjectives:
          type: array
          items:
            type: string
            enum: [GROWTH, INCOME, CAPITAL_PRESERVATION, RETIREMENT, EDUCATION]
        riskTolerance:
          type: string
          enum: [VERY_LOW, LOW, MODERATE, HIGH, VERY_HIGH]
        investmentHorizon:
          type: string
          enum: ['<5_YEARS', '5-10_YEARS', '10-20_YEARS', '>20_YEARS']
```

### 6.2 Data Models

**TypeScript/Prisma Schema:**

```prisma
// schema.prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

enum DataCategory {
  PERSONAL_DETAILS
  