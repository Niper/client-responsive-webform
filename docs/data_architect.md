# Design Data Storage and Integration Architecture

**Agent:** data_architect
**Job:** Client Responsive Webform

---

# Client Responsive Webform - Data Storage and Integration Architecture

## Document Version Control
- **Version:** 1.0
- **Date:** 2024
- **Classification:** Confidential
- **Compliance Framework:** FCA SYSC 3.2, GDPR, UK Data Protection Act 2018

---

## Executive Summary

This document outlines the comprehensive data architecture for the Client Responsive Webform system for a UK wealth management firm. The architecture ensures FCA compliance, data security, scalability, and seamless integration with existing wealth management systems while maintaining data integrity and supporting regulatory reporting requirements.

---

## 1. Database Schema Design

### 1.1 Logical Data Model

#### Core Entities

```sql
-- ============================================================================
-- SCHEMA: client_onboarding
-- PURPOSE: Secure storage of client data with audit trail and compliance
-- ============================================================================

-- 1. CLIENT_PROFILE (Core Identity)
CREATE TABLE client_profile (
    client_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_reference VARCHAR(20) UNIQUE NOT NULL, -- Business-friendly ID
    title VARCHAR(10),
    first_name VARCHAR(100) NOT NULL,
    middle_names VARCHAR(200),
    surname VARCHAR(100) NOT NULL,
    preferred_name VARCHAR(100),
    date_of_birth DATE NOT NULL,
    place_of_birth VARCHAR(100),
    nationality VARCHAR(3), -- ISO 3166-1 alpha-3
    national_insurance_number VARCHAR(9), -- Encrypted
    marital_status VARCHAR(20),
    number_of_dependents INTEGER,
    
    -- Compliance & Risk
    pep_status BOOLEAN DEFAULT FALSE, -- Politically Exposed Person
    pep_details TEXT, -- Encrypted if TRUE
    sanctions_screening_status VARCHAR(20) DEFAULT 'PENDING',
    sanctions_screening_date TIMESTAMP,
    risk_rating VARCHAR(20), -- LOW, MEDIUM, HIGH
    
    -- Metadata
    onboarding_status VARCHAR(30) DEFAULT 'DRAFT', 
    -- DRAFT, SUBMITTED, UNDER_REVIEW, APPROVED, REJECTED, ACTIVE
    form_submission_date TIMESTAMP,
    approval_date TIMESTAMP,
    approved_by UUID REFERENCES system_users(user_id),
    
    -- Technical
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_by UUID REFERENCES system_users(user_id),
    updated_by UUID REFERENCES system_users(user_id),
    version INTEGER DEFAULT 1,
    is_deleted BOOLEAN DEFAULT FALSE,
    deleted_at TIMESTAMP,
    
    -- Constraints
    CONSTRAINT chk_dob_valid CHECK (date_of_birth <= CURRENT_DATE - INTERVAL '18 years'),
    CONSTRAINT chk_status_valid CHECK (onboarding_status IN 
        ('DRAFT', 'SUBMITTED', 'UNDER_REVIEW', 'APPROVED', 'REJECTED', 'ACTIVE'))
);

-- 2. CONTACT_INFORMATION
CREATE TABLE contact_information (
    contact_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id) ON DELETE CASCADE,
    contact_type VARCHAR(20) NOT NULL, -- PRIMARY, SECONDARY, CORRESPONDENCE
    
    -- Address Details
    address_line_1 VARCHAR(200),
    address_line_2 VARCHAR(200),
    address_line_3 VARCHAR(200),
    town_city VARCHAR(100),
    county VARCHAR(100),
    postcode VARCHAR(10),
    country VARCHAR(3) DEFAULT 'GBR', -- ISO 3166-1 alpha-3
    
    -- Address Validation
    address_verified BOOLEAN DEFAULT FALSE,
    address_verification_date TIMESTAMP,
    proof_of_address_document_id UUID REFERENCES documents(document_id),
    
    -- Contact Details
    primary_phone VARCHAR(20), -- Encrypted
    secondary_phone VARCHAR(20), -- Encrypted
    mobile_phone VARCHAR(20), -- Encrypted
    email_address VARCHAR(255), -- Encrypted
    email_verified BOOLEAN DEFAULT FALSE,
    email_verification_token VARCHAR(255),
    email_verified_at TIMESTAMP,
    
    -- Preferences
    preferred_contact_method VARCHAR(20), -- EMAIL, PHONE, POST
    marketing_consent BOOLEAN DEFAULT FALSE,
    marketing_consent_date TIMESTAMP,
    
    -- Metadata
    is_current BOOLEAN DEFAULT TRUE,
    effective_from DATE NOT NULL DEFAULT CURRENT_DATE,
    effective_to DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT chk_contact_type_valid CHECK (contact_type IN ('PRIMARY', 'SECONDARY', 'CORRESPONDENCE')),
    CONSTRAINT chk_contact_method_valid CHECK (preferred_contact_method IN ('EMAIL', 'PHONE', 'POST', 'SMS'))
);

-- 3. EMPLOYMENT_INFORMATION
CREATE TABLE employment_information (
    employment_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id) ON DELETE CASCADE,
    
    employment_status VARCHAR(30) NOT NULL, 
    -- EMPLOYED, SELF_EMPLOYED, RETIRED, UNEMPLOYED, STUDENT, OTHER
    employer_name VARCHAR(200),
    job_title VARCHAR(100),
    industry_sector VARCHAR(100),
    occupation VARCHAR(100),
    
    -- Employment Address
    employer_address_line_1 VARCHAR(200),
    employer_address_line_2 VARCHAR(200),
    employer_town_city VARCHAR(100),
    employer_postcode VARCHAR(10),
    employer_country VARCHAR(3) DEFAULT 'GBR',
    
    employment_start_date DATE,
    employment_end_date DATE,
    
    -- Income Information (Encrypted)
    annual_income_gross DECIMAL(15,2), -- Encrypted
    annual_income_net DECIMAL(15,2), -- Encrypted
    income_currency VARCHAR(3) DEFAULT 'GBP',
    
    is_current BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT chk_employment_status_valid CHECK (employment_status IN 
        ('EMPLOYED', 'SELF_EMPLOYED', 'RETIRED', 'UNEMPLOYED', 'STUDENT', 'OTHER'))
);

-- 4. FINANCIAL_PROFILE (Fact Find)
CREATE TABLE financial_profile (
    financial_profile_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id) ON DELETE CASCADE,
    
    -- Wealth & Assets (All Encrypted)
    total_net_worth DECIMAL(15,2),
    liquid_assets DECIMAL(15,2),
    property_value DECIMAL(15,2),
    pension_value DECIMAL(15,2),
    investment_portfolio_value DECIMAL(15,2),
    other_assets DECIMAL(15,2),
    
    -- Liabilities (Encrypted)
    mortgage_outstanding DECIMAL(15,2),
    loans_outstanding DECIMAL(15,2),
    credit_card_debt DECIMAL(15,2),
    other_liabilities DECIMAL(15,2),
    
    -- Income Sources
    salary_income DECIMAL(15,2),
    dividend_income DECIMAL(15,2),
    rental_income DECIMAL(15,2),
    pension_income DECIMAL(15,2),
    other_income DECIMAL(15,2),
    
    -- Regular Expenditure
    monthly_expenditure DECIMAL(15,2),
    
    -- Investment Experience
    investment_experience_level VARCHAR(20), 
    -- NONE, LIMITED, MODERATE, EXTENSIVE, PROFESSIONAL
    years_investing INTEGER,
    previous_investments TEXT, -- JSON array of investment types
    
    -- Risk Profile
    risk_tolerance VARCHAR(20), -- CONSERVATIVE, MODERATE, BALANCED, GROWTH, AGGRESSIVE
    risk_capacity VARCHAR(20),
    investment_objectives TEXT, -- JSON array
    investment_time_horizon INTEGER, -- Years
    
    -- Tax Status
    tax_residency VARCHAR(3) DEFAULT 'GBR',
    uk_taxpayer BOOLEAN DEFAULT TRUE,
    tax_identification_number VARCHAR(50), -- Encrypted
    
    currency VARCHAR(3) DEFAULT 'GBP',
    as_of_date DATE NOT NULL DEFAULT CURRENT_DATE,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT chk_risk_tolerance_valid CHECK (risk_tolerance IN 
        ('CONSERVATIVE', 'MODERATE', 'BALANCED', 'GROWTH', 'AGGRESSIVE')),
    CONSTRAINT chk_investment_exp_valid CHECK (investment_experience_level IN 
        ('NONE', 'LIMITED', 'MODERATE', 'EXTENSIVE', 'PROFESSIONAL'))
);

-- 5. INVESTMENT_OBJECTIVES
CREATE TABLE investment_objectives (
    objective_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id) ON DELETE CASCADE,
    
    objective_type VARCHAR(50) NOT NULL,
    -- RETIREMENT_PLANNING, WEALTH_PRESERVATION, INCOME_GENERATION, 
    -- CAPITAL_GROWTH, EDUCATION_FUNDING, TAX_EFFICIENCY, OTHER
    objective_description TEXT,
    target_amount DECIMAL(15,2), -- Encrypted
    target_date DATE,
    priority_rank INTEGER,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 6. BENEFICIARIES
CREATE TABLE beneficiaries (
    beneficiary_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id) ON DELETE CASCADE,
    
    relationship VARCHAR(50) NOT NULL, 
    -- SPOUSE, CHILD, PARENT, SIBLING, TRUST, CHARITY, OTHER
    title VARCHAR(10),
    first_name VARCHAR(100) NOT NULL,
    surname VARCHAR(100) NOT NULL,
    date_of_birth DATE,
    
    allocation_percentage DECIMAL(5,2),
    
    -- Contact Information
    address_line_1 VARCHAR(200),
    town_city VARCHAR(100),
    postcode VARCHAR(10),
    country VARCHAR(3),
    phone VARCHAR(20), -- Encrypted
    email VARCHAR(255), -- Encrypted
    
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT chk_allocation_valid CHECK (allocation_percentage >= 0 AND allocation_percentage <= 100)
);

-- 7. DOCUMENTS
CREATE TABLE documents (
    document_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id) ON DELETE CASCADE,
    
    document_type VARCHAR(50) NOT NULL,
    -- ID_PROOF, PROOF_OF_ADDRESS, BANK_STATEMENT, TAX_RETURN, 
    -- SIGNATURE, W8_BEN, OTHER
    document_name VARCHAR(255) NOT NULL,
    file_size_bytes BIGINT,
    mime_type VARCHAR(100),
    
    -- Storage
    storage_location VARCHAR(500) NOT NULL, -- Encrypted path/URL
    storage_provider VARCHAR(50), -- S3, AZURE_BLOB, etc.
    encryption_key_id VARCHAR(100), -- Reference to KMS key
    
    -- Verification
    verification_status VARCHAR(20) DEFAULT 'PENDING',
    -- PENDING, VERIFIED, REJECTED, EXPIRED
    verified_by UUID REFERENCES system_users(user_id),
    verified_at TIMESTAMP,
    rejection_reason TEXT,
    
    -- Compliance
    document_expiry_date DATE,
    retention_until DATE NOT NULL, -- FCA retention period
    
    -- Metadata
    uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    uploaded_by UUID,
    checksum_sha256 VARCHAR(64),
    virus_scan_status VARCHAR(20),
    virus_scan_date TIMESTAMP,
    
    is_deleted BOOLEAN DEFAULT FALSE,
    deleted_at TIMESTAMP,
    
    CONSTRAINT chk_verification_status_valid CHECK (verification_status IN 
        ('PENDING', 'VERIFIED', 'REJECTED', 'EXPIRED'))
);

-- 8. CONSENT_RECORDS
CREATE TABLE consent_records (
    consent_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id) ON DELETE CASCADE,
    
    consent_type VARCHAR(50) NOT NULL,
    -- DATA_PROCESSING, MARKETING, THIRD_PARTY_SHARING, CREDIT_CHECK, 
    -- AUTOMATED_DECISION_MAKING, TERMS_CONDITIONS
    consent_given BOOLEAN NOT NULL,
    consent_version VARCHAR(20) NOT NULL, -- Version of T&C/Privacy Policy
    
    consent_text TEXT NOT NULL, -- Full text shown to client
    consent_timestamp TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    
    -- IP and Device Information for audit
    ip_address INET,
    user_agent TEXT,
    
    -- Withdrawal
    withdrawn BOOLEAN DEFAULT FALSE,
    withdrawn_at TIMESTAMP,
    withdrawal_reason TEXT,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 9. FORM_SUBMISSIONS
CREATE TABLE form_submissions (
    submission_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID REFERENCES client_profile(client_id),
    
    session_id UUID NOT NULL,
    form_version VARCHAR(20) NOT NULL,
    
    -- Submission Progress
    current_step INTEGER DEFAULT 1,
    total_steps INTEGER DEFAULT 5,
    completion_percentage DECIMAL(5,2),
    
    -- Submission Data (Encrypted JSON)
    form_data JSONB, -- Encrypted blob of all form data
    
    submission_status VARCHAR(30) DEFAULT 'IN_PROGRESS',
    -- IN_PROGRESS, COMPLETED, ABANDONED, SUBMITTED
    
    -- Tracking
    started_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP,
    
    -- Device/Browser Info
    ip_address INET,
    user_agent TEXT,
    referrer_url TEXT,
    
    -- Data Validation
    validation_errors JSONB,
    
    CONSTRAINT chk_submission_status_valid CHECK (submission_status IN 
        ('IN_PROGRESS', 'COMPLETED', 'ABANDONED', 'SUBMITTED'))
);

-- 10. AUDIT_LOG
CREATE TABLE audit_log (
    audit_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    
    -- What
    table_name VARCHAR(100) NOT NULL,
    record_id UUID NOT NULL,
    operation VARCHAR(20) NOT NULL, -- INSERT, UPDATE, DELETE, SELECT
    
    -- Who
    user_id UUID REFERENCES system_users(user_id),
    user_email VARCHAR(255),
    user_role VARCHAR(50),
    
    -- When
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    -- Where
    ip_address INET,
    session_id UUID,
    
    -- Changes
    old_values JSONB,
    new_values JSONB,
    changed_fields TEXT[],
    
    -- Context
    application VARCHAR(100),
    action_description TEXT,
    
    -- Compliance
    compliance_relevant BOOLEAN DEFAULT FALSE,
    retention_until DATE,
    
    CONSTRAINT chk_operation_valid CHECK (operation IN 
        ('INSERT', 'UPDATE', 'DELETE', 'SELECT', 'EXPORT'))
);

-- 11. DATA_QUALITY_CHECKS
CREATE TABLE data_quality_checks (
    check_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID REFERENCES client_profile(client_id),
    
    check_type VARCHAR(50) NOT NULL,
    -- COMPLETENESS, ACCURACY, CONSISTENCY, VALIDITY, DUPLICATE_CHECK
    check_status VARCHAR(20) NOT NULL, -- PASSED, FAILED, WARNING
    
    field_name VARCHAR(100),
    expected_value TEXT,
    actual_value TEXT,
    error_message TEXT,
    severity VARCHAR(20), -- LOW, MEDIUM, HIGH, CRITICAL
    
    checked_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    resolved BOOLEAN DEFAULT FALSE,
    resolved_at TIMESTAMP,
    resolved_by UUID REFERENCES system_users(user_id),
    
    CONSTRAINT chk_status_valid CHECK (check_status IN ('PASSED', 'FAILED', 'WARNING')),
    CONSTRAINT chk_severity_valid CHECK (severity IN ('LOW', 'MEDIUM', 'HIGH', 'CRITICAL'))
);

-- 12. SYSTEM_USERS (Reference table)
CREATE TABLE system_users (
    user_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    role VARCHAR(50) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 13. CRM_SYNC_STATUS
CREATE TABLE crm_sync_status (
    sync_id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES client_profile(client_id),
    
    target_system VARCHAR(50) NOT NULL, -- SALESFORCE, DYNAMICS, CUSTOM_CRM
    external_client_id VARCHAR(100),
    
    sync_status VARCHAR(30) NOT NULL,
    -- PENDING, IN_PROGRESS, SUCCESS, FAILED, RETRY
    sync_direction VARCHAR(20), -- OUTBOUND, INBOUND, BIDIRECTIONAL
    
    last_sync_attempt TIMESTAMP,
    last_successful_sync TIMESTAMP,
    sync_error_message TEXT,
    retry_count INTEGER DEFAULT 0,
    
    payload_sent JSONB,
    response_received JSONB,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT chk_sync_status_valid CHECK (sync_status IN 
        ('PENDING', 'IN_PROGRESS', 'SUCCESS', 'FAILED', 'RETRY'))
);
```

### 1.2 Indexing Strategy

```sql
-- ============================================================================
-- INDEXING STRATEGY
-- PURPOSE: Optimize query performance for common access patterns
-- ============================================================================

-- CLIENT_PROFILE Indexes
CREATE INDEX idx_client_profile_reference ON client_profile(client_reference);
CREATE INDEX idx_client_profile_status ON client_profile(onboarding_status);
CREATE INDEX idx_client_profile_submission_date ON client_profile(form_submission_date);
CREATE INDEX idx_client_profile_risk_rating ON client_profile(risk_rating);
CREATE INDEX idx_client_profile_created_at ON client_profile(created_at DESC);
CREATE INDEX idx_client_profile_surname ON client_profile(surname);
CREATE INDEX idx_client_profile_active ON client_profile(onboarding_status) 
    WHERE is_deleted = FALSE;

-- CONTACT_INFORMATION Indexes
CREATE INDEX idx_contact_client_id ON contact_information(client_id);
CREATE INDEX idx_contact_type ON contact_information(client_id, contact_type) 
    WHERE is_current = TRUE;
CREATE INDEX idx_contact_postcode ON contact_information(postcode);
CREATE INDEX idx_contact_email_verified ON contact_information(email_verified);

-- EMPLOYMENT_INFORMATION Indexes
CREATE INDEX idx_employment_client_id ON employment_information(client_id);
CREATE INDEX idx_employment_current ON employment_information(client_id) 
    WHERE is_current = TRUE;

-- FINANCIAL_PROFILE Indexes
CREATE INDEX idx_financial_client_id ON financial_profile(client_id);
CREATE INDEX idx_financial_as_of_date ON financial_profile(as_of_date DESC);
CREATE INDEX idx_financial_risk_tolerance ON financial_profile(risk_tolerance);

-- DOCUMENTS Indexes
CREATE INDEX idx_documents_client_id ON documents(client_id);
CREATE INDEX idx_documents_type ON documents(document_type);
CREATE INDEX idx_documents_verification_status ON documents(verification_status);
CREATE INDEX idx_documents_expiry ON documents(document_expiry_date) 
    WHERE document_expiry_date IS NOT NULL;
CREATE INDEX idx_documents_retention ON documents(retention_until);
CREATE INDEX idx_documents_active ON documents(client_id, document_type) 
    WHERE is_deleted = FALSE;

-- AUDIT_LOG Indexes
CREATE INDEX idx_audit_table_record ON audit_log(table_name, record_id);
CREATE INDEX idx_audit_user ON audit_log(user_id);
CREATE INDEX idx_audit_timestamp ON audit_log(timestamp DESC);
CREATE INDEX idx_audit_compliance ON audit_log(timestamp DESC) 
    WHERE compliance_relevant = TRUE;

-- FORM_SUBMISSIONS Indexes
CREATE INDEX idx_form_client_id ON form_submissions(client_id);
CREATE INDEX idx_form_session_id ON form_submissions(session_id);
CREATE INDEX idx_form_status ON form_submissions(submission_status);
CREATE INDEX idx_form_started_at ON form_submissions(started_at DESC);

-- CRM_SYNC_STATUS Indexes
CREATE INDEX idx_sync_client_id ON crm_sync_status(client_id);
CREATE INDEX idx_sync_status ON crm_sync_status(sync_status);
CREATE INDEX idx_sync_external_id ON crm_sync_status(target_system, external_client_id);
CREATE INDEX idx_sync_retry ON crm_sync_status(sync_status, retry_count) 
    WHERE sync_status = 'FAILED';

-- CONSENT_RECORDS Indexes
CREATE INDEX idx_consent_client_id ON consent_records(client_id);
CREATE INDEX idx_consent_type ON consent_records(consent_type);
CREATE INDEX idx_consent_timestamp ON consent_records(consent_timestamp DESC);

-- DATA_QUALITY_CHECKS Indexes
CREATE INDEX idx_dq_client_id ON data_quality_checks(client_id);
CREATE INDEX idx_dq_status ON data_quality_checks(check_status);
CREATE INDEX idx_dq_unresolved ON data_quality_checks(severity, checked_at DESC) 
    WHERE resolved = FALSE;
```

### 1.3 Encryption Strategy

```sql
-- ============================================================================
-- ENCRYPTION STRATEGY
-- PURPOSE: Column-level encryption for sensitive data
-- ============================================================================

-- Install pgcrypto extension
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Encryption wrapper functions
CREATE OR REPLACE FUNCTION encrypt_sensitive_data(plaintext TEXT)
RETURNS BYTEA AS $$
BEGIN
    -- Uses application-managed encryption key stored in AWS KMS/Azure Key Vault
    RETURN pgp_sym_encrypt(plaintext, current_setting('app.encryption_key'));
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE OR REPLACE FUNCTION decrypt_sensitive_data(ciphertext BYTEA)
RETURNS TEXT AS $$
BEGIN
    RETURN pgp_sym_decrypt(ciphertext, current_setting('app.encryption_key'));
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Apply encryption to sensitive columns (modify table structure)
-- Note: In production, these should be BYTEA columns with application-level encryption

-- Fields requiring encryption:
-- client_profile: national_insurance_number, pep_details
-- contact_information: primary_phone, secondary_phone, mobile_phone, email_address
-- employment_information: annual_income_gross, annual_income_net
-- financial_profile: ALL monetary fields, tax_identification_number
-- beneficiaries: phone, email
-- documents: storage_location
```

---

## 2. Entity Relationship Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         CLIENT_PROFILE (Core)                           │
│  - client_id (PK)                                                       │
│  - client_reference (UK)                                                │
│  - Personal identifiers                                                 │
│  - PEP status                                                           │
│  - Risk rating                                                          │
│  - Onboarding status                                                    │
└────────────┬────────────────────────────────────────────────────────────┘
             │
             ├──────────────┬──────────────┬──────────────┬──────────────┐
             │              │              │              │              │
             ▼              ▼              ▼              ▼              ▼
     ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
     │   CONTACT_   │ │ EMPLOYMENT_  │ │  FINANCIAL_  │ │ INVESTMENT_  │ │ BENEFICIARIES│
     │ INFORMATION  │ │ INFORMATION  │ │   PROFILE    │ │  OBJECTIVES  │ │              │
     │              │ │              │ │              │ │              │ │              │
     │ - Multiple   │ │ - Current &  │ │ - Net worth  │ │ - Goals      │ │ - Multiple   │
     │   addresses  │ │   historical │ │ - Income     │ │ - Targets    │ │   records    │
     │ - Phones     │ │ - Income     │ │ - Risk prof. │ │ - Timeline   │ │ - Allocation │
     │ - Email      │ │              │ │ - Tax status │ │              │ │              │
     └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘
             │
             ├──────────────┬──────────────┬──────────────┬──────────────┐
             │              │              │              │              │
             ▼              ▼              ▼              ▼              ▼
     ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
     │  DOCUMENTS   │ │   CONSENT_   │ │    FORM_     │ │     AUDIT_   │ │  CRM_SYNC_   │
     │              │ │   RECORDS    │ │ SUBMISSIONS  │ │      LOG     │ │    STATUS    │
     │ - KYC docs   │ │              │ │              │ │              │ │              │
     │ - Proof      │ │ - GDPR       │ │ - Progress   │ │ - All        │ │ - External   │
     │ - Retention  │ │ - Marketing  │ │ - Validation │ │   changes    │ │   system ID  │
     │              │ │ - Version    │ │              │ │ - Compliance │ │ - Sync state │
     └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘ └──────────────┘
```

**Relationship Cardinality:**
- Client Profile → Contact Information: 1:N (one client, multiple contact records)
- Client Profile → Employment: 1:N (current and historical)
- Client Profile → Financial Profile: 1:N (point-in-time snapshots)
- Client Profile → Documents: 1:N
- Client Profile → Consent Records: 1:N
- Client Profile → Beneficiaries: 1:N
- Client Profile → Investment Objectives: 1:N

---

## 3. Data Pipeline Architecture

### 3.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        DATA PIPELINE ARCHITECTURE                        │
└─────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐
│   WEB FORM UI    │
│  (React/Vue.js)  │
└────────┬─────────┘
         │ HTTPS/TLS 1.3
         │ JWT Token
         ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                        API GATEWAY / LOAD BALANCER                       │
│  - Rate limiting (100 req/min per IP)                                    │
│  - DDoS protection                                                       │
│  - Request validation                                                    │
│  - SSL termination                                                       │
└────────┬─────────────────────────────────────────────────────────────────┘
         │
         ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                      APPLICATION TIER (Microservices)                    │
│                                                                           │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐         │
│  │ Form Submission │  │   Validation    │  │   Document      │         │
│  │    Service      │  │    Service      │  │   Upload Svc    │         │
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘         │
│           │                     │                     │                  │
│           └─────────────────────┴─────────────────────┘                  │
│                                 │                                        │
└─────────────────────────────────┼────────────────────────────────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         │                        │                        │
         ▼                        ▼                        ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Message Queue  │    │   PostgreSQL    │    │  Object Storage │
│  (RabbitMQ/SQS) │    │   Primary DB    │    │  (S3/Azure)     │
│                 │    │                 │    │                 │
│ - Form events   │    │ - Client data   │    │ - Documents     │
│ - Sync jobs     │    │ - Encrypted PII │    │ - Encrypted     │
│ - Notifications │    │ - Audit logs    │    │ - Versioned     │
└────────┬────────┘    └────────┬────────┘    └─────────────────┘
         │                      │
         │                      │ Read Replica (Analytics)
         │                      ▼
         │             ┌─────────────────┐
         │             │   PostgreSQL    │
         │             │  Read Replica   │
         │             └─────────────────┘
         │
         ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                      INTEGRATION & TRANSFORMATION LAYER                  │
│                                                                           │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐         │
│  │   ETL Engine    │  │  Data Quality   │  │    Change Data  │         │
│  │   (Apache       │  │   Validator     │  │    Capture      │         │
│  │   NiFi/Airflow) │  │                 │  │    (Debezium)   │         │
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘         │
│           │                     │                     │                  │
│           └─────────────────────┴─────────────────────┘                  │
└───────────────────────────────────┬──────────────────────────────────────┘
                                    │
         ┌──────────────────────────┼──────────────────────────┐
         │                          │                          │
         ▼                          ▼                          