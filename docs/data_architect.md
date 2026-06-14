# Data Architecture and Security Design

**Agent:** data_architect
**Job:** Client Responsive Webform

---

# Data Architecture and Security Design Document
## Client Responsive Webform - UK Wealth Management Firm

**Version:** 1.0  
**Date:** 2024  
**Classification:** CONFIDENTIAL

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Data Model Design](#2-data-model-design)
3. [Database Schema Specification](#3-database-schema-specification)
4. [Security Architecture](#4-security-architecture)
5. [API Specifications](#5-api-specifications)
6. [GDPR Compliance Framework](#6-gdpr-compliance-framework)
7. [UK-Specific Requirements](#7-uk-specific-requirements)
8. [Data Quality and Validation](#8-data-quality-and-validation)
9. [Infrastructure and Deployment](#9-infrastructure-and-deployment)
10. [Appendices](#10-appendices)

---

## 1. Executive Summary

This document outlines the comprehensive data architecture for a client onboarding webform tailored for UK wealth management firms. The design prioritizes FCA compliance, GDPR adherence, and robust security measures while maintaining scalability and performance.

### Key Design Principles

- **Security-First**: Multi-layer encryption, field-level security for PII
- **Compliance-Ready**: FCA and GDPR requirements embedded in architecture
- **Audit-Complete**: Full audit trail for regulatory reporting
- **Scalable**: Designed to handle 100,000+ clients with sub-second response times
- **Privacy-Preserving**: Data minimization and purpose limitation built-in

---

## 2. Data Model Design

### 2.1 Entity Relationship Diagram

```
┌─────────────────────┐
│   Client            │
├─────────────────────┤
│ PK client_id        │
│    client_uuid      │
│    onboarding_stage │
│    created_at       │
│    updated_at       │
└──────────┬──────────┘
           │
           │ 1:1
           │
┌──────────┴──────────────────────┐
│   PersonalDetails               │
├─────────────────────────────────┤
│ PK personal_details_id          │
│ FK client_id                    │
│    title                        │
│    first_name (encrypted)       │
│    middle_names (encrypted)     │
│    surname (encrypted)          │
│    preferred_name               │
│    date_of_birth (encrypted)    │
│    national_insurance (encrypted)│
│    nationality                  │
│    marital_status               │
│    number_of_dependents         │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   ContactInformation            │
├─────────────────────────────────┤
│ PK contact_id                   │
│ FK client_id                    │
│    email_primary (encrypted)    │
│    email_secondary (encrypted)  │
│    phone_mobile (encrypted)     │
│    phone_home (encrypted)       │
│    phone_work (encrypted)       │
│    preferred_contact_method     │
│    preferred_contact_time       │
│    marketing_consent            │
│    marketing_consent_date       │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   Address                       │
├─────────────────────────────────┤
│ PK address_id                   │
│ FK client_id                    │
│    address_type                 │
│    building_name                │
│    building_number              │
│    street_name (encrypted)      │
│    locality                     │
│    town_city (encrypted)        │
│    county                       │
│    postcode (encrypted)         │
│    country                      │
│    uprn                         │
│    from_date                    │
│    to_date                      │
│    is_current                   │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   EmploymentDetails             │
├─────────────────────────────────┤
│ PK employment_id                │
│ FK client_id                    │
│    employment_status            │
│    employer_name (encrypted)    │
│    job_title                    │
│    industry_sector              │
│    annual_income (encrypted)    │
│    employment_start_date        │
│    employment_end_date          │
│    is_current                   │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   FinancialProfile              │
├─────────────────────────────────┤
│ PK financial_profile_id         │
│ FK client_id                    │
│    estimated_net_worth (encrypted)│
│    liquid_assets (encrypted)    │
│    annual_income (encrypted)    │
│    monthly_expenditure (encrypted)│
│    source_of_wealth             │
│    source_of_funds              │
│    tax_residency                │
│    tax_identification (encrypted)│
│    politically_exposed          │
│    pep_details                  │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   FactFind                      │
├─────────────────────────────────┤
│ PK fact_find_id                 │
│ FK client_id                    │
│    investment_objectives        │
│    risk_tolerance               │
│    investment_horizon           │
│    investment_experience        │
│    capacity_for_loss            │
│    ethical_preferences          │
│    existing_investments         │
│    pension_details              │
│    protection_needs             │
│    estate_planning_needs        │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   ConsentManagement             │
├─────────────────────────────────┤
│ PK consent_id                   │
│ FK client_id                    │
│    consent_type                 │
│    consent_given                │
│    consent_date                 │
│    consent_version              │
│    consent_withdrawn_date       │
│    purpose                      │
│    legal_basis                  │
│    ip_address                   │
│    user_agent                   │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   DocumentMetadata              │
├─────────────────────────────────┤
│ PK document_id                  │
│ FK client_id                    │
│    document_type                │
│    document_name                │
│    storage_path (encrypted)     │
│    mime_type                    │
│    file_size                    │
│    checksum                     │
│    encryption_key_id            │
│    uploaded_at                  │
│    verified_at                  │
│    expiry_date                  │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   AuditLog                      │
├─────────────────────────────────┤
│ PK audit_id                     │
│ FK client_id                    │
│    table_name                   │
│    record_id                    │
│    action_type                  │
│    field_name                   │
│    old_value_hash               │
│    new_value_hash               │
│    changed_by                   │
│    changed_at                   │
│    ip_address                   │
│    user_agent                   │
│    session_id                   │
│    reason                       │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   DataRetentionLog              │
├─────────────────────────────────┤
│ PK retention_id                 │
│ FK client_id                    │
│    data_category                │
│    retention_period_months      │
│    legal_basis                  │
│    retention_start_date         │
│    scheduled_deletion_date      │
│    deletion_executed_date       │
│    deletion_method              │
│    verification_hash            │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│   EncryptionKeyManagement       │
├─────────────────────────────────┤
│ PK key_id                       │
│    key_version                  │
│    algorithm                    │
│    key_encrypted_key            │
│    created_at                   │
│    rotated_at                   │
│    expires_at                   │
│    status                       │
│    key_purpose                  │
└─────────────────────────────────┘
```

### 2.2 Data Model Rationale

**Normalization Strategy**: 3NF (Third Normal Form) with selective denormalization for read performance

**Key Design Decisions**:

1. **Separate Entities**: Personal, contact, and financial data separated for:
   - Granular access control
   - Different encryption strategies
   - Independent retention policies

2. **Address History**: Multi-address support with temporal tracking for AML compliance

3. **Consent Management**: Separate entity for GDPR Article 7 compliance and audit

4. **Audit Log**: Immutable append-only structure for complete traceability

---

## 3. Database Schema Specification

### 3.1 PostgreSQL Schema Definition

```sql
-- Enable required extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pgcrypto";
CREATE EXTENSION IF NOT EXISTS "pg_trgm"; -- For fuzzy text search

-- Create custom types
CREATE TYPE onboarding_stage_enum AS ENUM (
    'not_started', 'personal_details', 'contact_info', 'address', 
    'employment', 'financial_profile', 'fact_find', 'documents', 
    'review', 'submitted', 'approved', 'rejected'
);

CREATE TYPE address_type_enum AS ENUM (
    'residential', 'correspondence', 'previous', 'business'
);

CREATE TYPE employment_status_enum AS ENUM (
    'employed', 'self_employed', 'retired', 'unemployed', 
    'student', 'homemaker', 'other'
);

CREATE TYPE consent_type_enum AS ENUM (
    'data_processing', 'marketing', 'third_party_sharing', 
    'profiling', 'automated_decisions', 'terms_and_conditions'
);

CREATE TYPE audit_action_enum AS ENUM (
    'INSERT', 'UPDATE', 'DELETE', 'SELECT', 'EXPORT', 'PRINT'
);

-- Main Client Table
CREATE TABLE clients (
    client_id BIGSERIAL PRIMARY KEY,
    client_uuid UUID DEFAULT uuid_generate_v4() UNIQUE NOT NULL,
    onboarding_stage onboarding_stage_enum DEFAULT 'not_started' NOT NULL,
    relationship_manager_id BIGINT,
    application_source VARCHAR(100), -- 'web_form', 'mobile_app', 'advisor_portal'
    is_active BOOLEAN DEFAULT true,
    is_deleted BOOLEAN DEFAULT false,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP WITH TIME ZONE,
    CONSTRAINT chk_deleted_at CHECK (is_deleted = false OR deleted_at IS NOT NULL)
);

CREATE INDEX idx_clients_uuid ON clients(client_uuid);
CREATE INDEX idx_clients_stage ON clients(onboarding_stage) WHERE is_active = true;
CREATE INDEX idx_clients_created ON clients(created_at);

-- Personal Details Table (Encrypted Fields)
CREATE TABLE personal_details (
    personal_details_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    title VARCHAR(20),
    first_name_encrypted BYTEA NOT NULL,
    first_name_hash VARCHAR(64) NOT NULL, -- For searching without decryption
    middle_names_encrypted BYTEA,
    surname_encrypted BYTEA NOT NULL,
    surname_hash VARCHAR(64) NOT NULL,
    preferred_name VARCHAR(100),
    date_of_birth_encrypted BYTEA NOT NULL,
    dob_hash VARCHAR(64) NOT NULL,
    age_band VARCHAR(20), -- For analytics without exposing DOB
    national_insurance_encrypted BYTEA,
    ni_hash VARCHAR(64),
    nationality VARCHAR(100),
    country_of_birth VARCHAR(100),
    marital_status VARCHAR(50),
    number_of_dependents SMALLINT,
    gender VARCHAR(50), -- Optional, for analytics only if provided
    encryption_key_id BIGINT NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(client_id)
);

CREATE INDEX idx_personal_first_name_hash ON personal_details(first_name_hash);
CREATE INDEX idx_personal_surname_hash ON personal_details(surname_hash);
CREATE INDEX idx_personal_ni_hash ON personal_details(ni_hash) WHERE ni_hash IS NOT NULL;

-- Contact Information Table
CREATE TABLE contact_information (
    contact_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    email_primary_encrypted BYTEA NOT NULL,
    email_primary_hash VARCHAR(64) NOT NULL,
    email_secondary_encrypted BYTEA,
    email_secondary_hash VARCHAR(64),
    phone_mobile_encrypted BYTEA,
    phone_mobile_hash VARCHAR(64),
    phone_home_encrypted BYTEA,
    phone_home_hash VARCHAR(64),
    phone_work_encrypted BYTEA,
    phone_work_hash VARCHAR(64),
    phone_extension VARCHAR(20),
    preferred_contact_method VARCHAR(20), -- 'email', 'phone', 'post'
    preferred_contact_time VARCHAR(50),
    marketing_consent BOOLEAN DEFAULT false,
    marketing_consent_date TIMESTAMP WITH TIME ZONE,
    email_verified BOOLEAN DEFAULT false,
    email_verified_at TIMESTAMP WITH TIME ZONE,
    phone_verified BOOLEAN DEFAULT false,
    phone_verified_at TIMESTAMP WITH TIME ZONE,
    encryption_key_id BIGINT NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(client_id),
    UNIQUE(email_primary_hash)
);

CREATE INDEX idx_contact_email_hash ON contact_information(email_primary_hash);
CREATE INDEX idx_contact_phone_hash ON contact_information(phone_mobile_hash) WHERE phone_mobile_hash IS NOT NULL;

-- Address Table (Supporting Multiple Addresses)
CREATE TABLE addresses (
    address_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    address_type address_type_enum NOT NULL,
    building_name VARCHAR(200),
    building_number VARCHAR(50),
    sub_building VARCHAR(100),
    street_name_encrypted BYTEA,
    street_name_hash VARCHAR(64),
    locality VARCHAR(100),
    town_city_encrypted BYTEA,
    town_city_hash VARCHAR(64),
    county VARCHAR(100),
    postcode_encrypted BYTEA NOT NULL,
    postcode_hash VARCHAR(64) NOT NULL,
    postcode_area VARCHAR(4), -- First part of postcode for analytics
    country VARCHAR(100) DEFAULT 'United Kingdom',
    uprn VARCHAR(50), -- Unique Property Reference Number
    from_date DATE NOT NULL,
    to_date DATE,
    is_current BOOLEAN DEFAULT true,
    address_verified BOOLEAN DEFAULT false,
    verified_at TIMESTAMP WITH TIME ZONE,
    verification_method VARCHAR(50), -- 'manual', 'postcode_lookup', 'document'
    encryption_key_id BIGINT NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_address_dates CHECK (to_date IS NULL OR to_date >= from_date),
    CONSTRAINT chk_current_address CHECK (is_current = false OR to_date IS NULL)
);

CREATE INDEX idx_addresses_client ON addresses(client_id);
CREATE INDEX idx_addresses_current ON addresses(client_id, is_current) WHERE is_current = true;
CREATE INDEX idx_addresses_postcode_hash ON addresses(postcode_hash);
CREATE INDEX idx_addresses_uprn ON addresses(uprn) WHERE uprn IS NOT NULL;

-- Employment Details Table
CREATE TABLE employment_details (
    employment_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    employment_status employment_status_enum NOT NULL,
    employer_name_encrypted BYTEA,
    employer_name_hash VARCHAR(64),
    job_title VARCHAR(200),
    industry_sector VARCHAR(100),
    occupation_code VARCHAR(20), -- Standard Occupational Classification
    annual_income_encrypted BYTEA,
    annual_income_band VARCHAR(50), -- For analytics
    employment_start_date DATE,
    employment_end_date DATE,
    is_current BOOLEAN DEFAULT true,
    encryption_key_id BIGINT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_employment_dates CHECK (employment_end_date IS NULL OR employment_end_date >= employment_start_date)
);

CREATE INDEX idx_employment_client ON employment_details(client_id);
CREATE INDEX idx_employment_current ON employment_details(client_id, is_current) WHERE is_current = true;

-- Financial Profile Table
CREATE TABLE financial_profiles (
    financial_profile_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    estimated_net_worth_encrypted BYTEA,
    net_worth_band VARCHAR(50),
    liquid_assets_encrypted BYTEA,
    liquid_assets_band VARCHAR(50),
    annual_income_encrypted BYTEA,
    annual_income_band VARCHAR(50),
    monthly_expenditure_encrypted BYTEA,
    expenditure_band VARCHAR(50),
    source_of_wealth TEXT[],
    source_of_funds TEXT[],
    tax_residency VARCHAR(100)[],
    tax_identification_encrypted BYTEA,
    tin_hash VARCHAR(64),
    politically_exposed_person BOOLEAN DEFAULT false,
    pep_relationship VARCHAR(100), -- If related to PEP
    pep_details JSONB,
    sanctions_checked BOOLEAN DEFAULT false,
    sanctions_check_date TIMESTAMP WITH TIME ZONE,
    sanctions_result VARCHAR(20),
    encryption_key_id BIGINT NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(client_id)
);

CREATE INDEX idx_financial_client ON financial_profiles(client_id);
CREATE INDEX idx_financial_pep ON financial_profiles(politically_exposed_person) WHERE politically_exposed_person = true;

-- Fact Find Table
CREATE TABLE fact_finds (
    fact_find_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    investment_objectives TEXT[],
    primary_objective VARCHAR(100),
    risk_tolerance VARCHAR(50), -- 'conservative', 'moderate', 'balanced', 'growth', 'aggressive'
    risk_score NUMERIC(5,2),
    investment_horizon_years SMALLINT,
    capacity_for_loss VARCHAR(50),
    investment_knowledge VARCHAR(50),
    investment_experience JSONB, -- {asset_class: years}
    ethical_preferences TEXT[],
    exclusions TEXT[],
    existing_investments JSONB,
    existing_pensions JSONB,
    protection_cover JSONB,
    estate_planning_completed BOOLEAN,
    will_in_place BOOLEAN,
    lasting_power_attorney BOOLEAN,
    notes TEXT,
    completed_at TIMESTAMP WITH TIME ZONE,
    reviewed_by BIGINT,
    reviewed_at TIMESTAMP WITH TIME ZONE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(client_id)
);

CREATE INDEX idx_factfind_client ON fact_finds(client_id);
CREATE INDEX idx_factfind_risk ON fact_finds(risk_tolerance);

-- Consent Management Table
CREATE TABLE consent_management (
    consent_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    consent_type consent_type_enum NOT NULL,
    consent_given BOOLEAN NOT NULL,
    consent_date TIMESTAMP WITH TIME ZONE NOT NULL,
    consent_version VARCHAR(20) NOT NULL,
    consent_withdrawn_date TIMESTAMP WITH TIME ZONE,
    purpose TEXT NOT NULL,
    legal_basis VARCHAR(100) NOT NULL, -- 'consent', 'contract', 'legal_obligation', etc.
    ip_address INET,
    user_agent TEXT,
    session_id VARCHAR(255),
    parent_consent_id BIGINT REFERENCES consent_management(consent_id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_consent_client ON consent_management(client_id);
CREATE INDEX idx_consent_type ON consent_management(client_id, consent_type);
CREATE INDEX idx_consent_active ON consent_management(client_id, consent_type) 
    WHERE consent_given = true AND consent_withdrawn_date IS NULL;

-- Document Metadata Table
CREATE TABLE document_metadata (
    document_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    document_type VARCHAR(100) NOT NULL, -- 'proof_of_id', 'proof_of_address', 'bank_statement', etc.
    document_name VARCHAR(255) NOT NULL,
    storage_path_encrypted BYTEA NOT NULL,
    storage_provider VARCHAR(50), -- 's3', 'azure_blob', 'local'
    mime_type VARCHAR(100),
    file_size_bytes BIGINT,
    checksum_sha256 VARCHAR(64),
    encryption_algorithm VARCHAR(50),
    encryption_key_id BIGINT NOT NULL,
    uploaded_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    uploaded_by VARCHAR(255),
    verified_at TIMESTAMP WITH TIME ZONE,
    verified_by BIGINT,
    expiry_date DATE,
    retention_until DATE NOT NULL,
    is_deleted BOOLEAN DEFAULT false,
    deleted_at TIMESTAMP WITH TIME ZONE,
    virus_scan_status VARCHAR(20),
    virus_scan_date TIMESTAMP WITH TIME ZONE,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_documents_client ON document_metadata(client_id);
CREATE INDEX idx_documents_type ON document_metadata(client_id, document_type);
CREATE INDEX idx_documents_expiry ON document_metadata(expiry_date) WHERE expiry_date IS NOT NULL;

-- Audit Log Table (Immutable)
CREATE TABLE audit_log (
    audit_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT,
    table_name VARCHAR(100) NOT NULL,
    record_id BIGINT NOT NULL,
    action_type audit_action_enum NOT NULL,
    field_name VARCHAR(100),
    old_value_hash VARCHAR(64),
    new_value_hash VARCHAR(64),
    changed_by VARCHAR(255) NOT NULL,
    changed_by_type VARCHAR(50), -- 'client', 'advisor', 'system', 'admin'
    changed_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP NOT NULL,
    ip_address INET,
    user_agent TEXT,
    session_id VARCHAR(255),
    request_id VARCHAR(100),
    reason TEXT,
    compliance_flag BOOLEAN DEFAULT false,
    CONSTRAINT no_audit_updates CHECK (changed_at = created_at),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_client ON audit_log(client_id, changed_at DESC);
CREATE INDEX idx_audit_table_record ON audit_log(table_name, record_id, changed_at DESC);
CREATE INDEX idx_audit_timestamp ON audit_log(changed_at DESC);
CREATE INDEX idx_audit_user ON audit_log(changed_by, changed_at DESC);

-- Data Retention Log Table
CREATE TABLE data_retention_log (
    retention_id BIGSERIAL PRIMARY KEY,
    client_id BIGINT NOT NULL REFERENCES clients(client_id),
    data_category VARCHAR(100) NOT NULL,
    retention_period_months SMALLINT NOT NULL,
    legal_basis VARCHAR(200) NOT NULL,
    retention_start_date DATE NOT NULL,
    scheduled_deletion_date DATE NOT NULL,
    deletion_executed_date TIMESTAMP WITH TIME ZONE,
    deletion_method VARCHAR(50), -- 'hard_delete', 'anonymize', 'archive'
    verification_hash VARCHAR(64),
    deleted_by VARCHAR(255),
    notes TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_retention_client ON data_retention_log(client_id);
CREATE INDEX idx_retention_scheduled ON data_retention_log(scheduled_deletion_date) 
    WHERE deletion_executed_date IS NULL;

-- Encryption Key Management Table
CREATE TABLE encryption_key_management (
    key_id BIGSERIAL PRIMARY KEY,
    key_version VARCHAR(50) NOT NULL,
    algorithm VARCHAR(50) NOT NULL, -- 'AES-256-GCM', 'RSA-4096'
    key_encrypted_key BYTEA NOT NULL, -- KEK encrypted DEK
    master_key_id VARCHAR(255) NOT NULL, -- Reference to HSM/KMS
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    rotated_at TIMESTAMP WITH TIME ZONE,
    expires_at TIMESTAMP WITH TIME ZONE,
    status VARCHAR(20) DEFAULT 'active', -- 'active', 'rotated', 'expired', 'revoked'
    key_purpose VARCHAR(100), -- 'data_encryption', 'document_encryption'
    CONSTRAINT chk_key_status CHECK (status IN ('active', 'rotated', 'expired', 'revoked'))
);

CREATE INDEX idx_encryption_status ON encryption_key_management(status, expires_at);

-- Session Management for Form Progress
CREATE TABLE form_sessions (
    session_id VARCHAR(255) PRIMARY KEY,
    client_uuid UUID NOT NULL REFERENCES clients(client_uuid),
    session_data JSONB, -- Encrypted session data
    current_step onboarding_stage_enum,
    last_activity TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP WITH TIME ZONE NOT NULL,
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_session_client ON form_sessions(client_uuid);
CREATE INDEX idx_session_expiry ON form_sessions(expires_at);

-- Validation Rules Table
CREATE TABLE validation_rules (
    rule_id BIGSERIAL PRIMARY KEY,
    field_name VARCHAR(100) NOT NULL,
    rule_type VARCHAR(50) NOT NULL, -- 'regex', 'range', 'lookup', 'custom'
    rule_definition JSONB NOT NULL,
    error_message TEXT NOT NULL,
    is_active BOOLEAN DEFAULT true,
    priority SMALLINT DEFAULT 100,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_validation_field ON validation_rules(field_name) WHERE is_active = true;
```

### 3.2 Indexing Strategy

**Primary Indexes**:
- Primary keys on all tables (automatic B-tree indexes)
- Unique constraints on client_uuid, email hashes, NI hashes

**Search Optimization**:
- Hash indexes for encrypted field lookups (first_name_hash, surname_hash)
- Composite indexes for common query patterns (client_id + is_current)
- GIN indexes on JSONB fields for fact find data

**Performance Indexes**:
- Partial indexes for active records only
- Covering indexes for frequently accessed columns
- Descending indexes on timestamp fields for recent record retrieval

**Index Maintenance**:
```sql
-- Weekly index maintenance job
REINDEX INDEX CONCURRENTLY idx_clients_stage;
ANALYZE clients;
VACUUM ANALYZE audit_log;
```

### 3.3 Database Constraints and Rules

**Data Integrity Constraints**:

```sql
-- Trigger to maintain updated_at timestamp
CREATE OR REPLACE FUNCTION update_modified_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Apply to all relevant tables
CREATE TRIGGER update_clients_modtime BEFORE UPDATE ON clients
    FOR EACH ROW EXECUTE FUNCTION update_modified_column();

-- Prevent direct updates to audit_log
CREATE OR REPLACE FUNCTION prevent_audit_modification()
RETURNS TRIGGER AS $$
BEGIN
    RAISE EXCEPTION 'Audit log records are immutable';
END;
$$ language 'plpgsql';

CREATE TRIGGER no_audit_updates BEFORE UPDATE ON audit_log
    FOR EACH ROW EXECUTE FUNCTION prevent_audit_modification();

-- Automatic audit trail trigger
CREATE OR REPLACE FUNCTION create_audit_trail()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO audit_log (
        client_id, table_name, record_id, action_type,
        changed_by, changed_by_type, session_id
    ) VALUES (
        COALESCE(NEW.client_id, OLD.client_id),
        TG_TABLE_NAME,
        COALESCE(NEW.client_id, OLD.client_id),
        TG_OP::audit_action_enum,
        current_user,
        'system',
        current_setting('app.session_id', true)
    );
    RETURN NULL;
END;
$$ language 'plpgsql';

-- Apply to sensitive tables
CREATE TRIGGER audit_personal_details AFTER INSERT OR UPDATE OR DELETE ON personal_details
    FOR EACH ROW EXECUTE FUNCTION create_audit_trail();
```

---

## 4. Security Architecture

### 4.1 Encryption Strategy

#### 4.1.1 Encryption at Rest

**Multi-Layer Encryption Approach**:

```
┌─────────────────────────────────────────┐
│  Layer 1: Disk Encryption (LUKS/BitLocker)│
├─────────────────────────────────────────┤
│  Layer 2: Database TDE (Transparent Data │
│           Encryption)                    │
├─────────────────────────────────────────┤
│  Layer 3: Column-Level Encryption        │
│           (Application-Managed)          │
├─────────────────────────────────────────┤
│  Layer 4: Document Encryption            │
│           (Pre-upload Client-Side)       │
└─────────────────────────────────────────┘
```

**Implementation Details**:

1. **Envelope Encryption Pattern**:
   - Master Key: Stored in AWS KMS / Azure Key Vault / Google Cloud KMS
   - Data Encryption Keys (DEK): Generated per client, encrypted with Master Key
   - Field Encryption: AES-256-GCM with unique DEK per client

```python
# Pseudo-code for encryption implementation
class FieldEncryption:
    def __init__(self, kms_client, master_key_id):
        self.kms = kms_client
        self.master_key_id =