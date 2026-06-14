# Develop Full-Stack Web Form Application

**Agent:** full_stack_dev
**Job:** Client Responsive Webform

---

# Client Responsive Webform - Full-Stack Implementation

## Executive Summary

This document provides a comprehensive implementation of a multi-step, responsive web form for UK wealth management firms to collect client information in compliance with FCA regulations. The solution includes a React frontend with TypeScript, Node.js/Express backend, PostgreSQL database, and Redis for session management.

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Database Schema](#database-schema)
3. [Backend Implementation](#backend-implementation)
4. [Frontend Implementation](#frontend-implementation)
5. [API Documentation](#api-documentation)
6. [Security & Compliance](#security-compliance)
7. [Testing Strategy](#testing-strategy)
8. [Deployment Instructions](#deployment-instructions)
9. [Performance Considerations](#performance-considerations)

---

## Architecture Overview

### Technology Stack

**Frontend:**
- React 18 with TypeScript
- React Hook Form for form management
- Zod for schema validation
- Tailwind CSS for responsive design
- Axios for API communication
- React Router for navigation

**Backend:**
- Node.js with Express
- TypeScript
- PostgreSQL for persistent storage
- Redis for session/auto-save management
- Joi for server-side validation
- Winston for logging
- Helmet for security headers

**Infrastructure:**
- Docker for containerization
- Nginx as reverse proxy
- Let's Encrypt for SSL/TLS

### System Architecture Diagram

```
┌─────────────┐     HTTPS      ┌──────────────┐
│   Browser   │ ◄──────────────► │    Nginx     │
└─────────────┘                 └──────┬───────┘
                                       │
                         ┌─────────────┼─────────────┐
                         │                           │
                    ┌────▼─────┐              ┌─────▼──────┐
                    │  React   │              │  Express   │
                    │  App     │              │  API       │
                    └──────────┘              └─────┬──────┘
                                                    │
                                       ┌────────────┼────────────┐
                                       │                         │
                                  ┌────▼─────┐           ┌──────▼─────┐
                                  │PostgreSQL│           │   Redis    │
                                  │ Database │           │   Cache    │
                                  └──────────┘           └────────────┘
```

---

## Database Schema

### PostgreSQL Schema

```sql
-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Enum types
CREATE TYPE submission_status AS ENUM ('draft', 'in_progress', 'completed', 'submitted', 'archived');
CREATE TYPE marital_status AS ENUM ('single', 'married', 'civil_partnership', 'divorced', 'widowed', 'separated');
CREATE TYPE employment_status AS ENUM ('employed', 'self_employed', 'retired', 'unemployed', 'student', 'other');
CREATE TYPE risk_tolerance AS ENUM ('low', 'medium', 'high', 'very_high');

-- Main client submissions table
CREATE TABLE client_submissions (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    session_id VARCHAR(255) UNIQUE NOT NULL,
    status submission_status DEFAULT 'draft',
    current_step INTEGER DEFAULT 1,
    total_steps INTEGER DEFAULT 6,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP WITH TIME ZONE,
    submitted_at TIMESTAMP WITH TIME ZONE,
    ip_address INET,
    user_agent TEXT,
    
    -- Audit fields
    version INTEGER DEFAULT 1,
    archived BOOLEAN DEFAULT FALSE,
    
    -- Indexes
    CONSTRAINT valid_step CHECK (current_step >= 1 AND current_step <= total_steps)
);

-- Personal details
CREATE TABLE personal_details (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    
    -- Name
    title VARCHAR(20),
    first_name VARCHAR(100) NOT NULL,
    middle_names VARCHAR(100),
    last_name VARCHAR(100) NOT NULL,
    preferred_name VARCHAR(100),
    
    -- Date of birth
    date_of_birth DATE NOT NULL,
    
    -- National Insurance
    national_insurance_number VARCHAR(13),
    
    -- Marital status
    marital_status marital_status,
    
    -- Nationality
    nationality VARCHAR(100),
    country_of_birth VARCHAR(100),
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT valid_dob CHECK (date_of_birth <= CURRENT_DATE AND date_of_birth >= '1900-01-01'),
    CONSTRAINT valid_ni_format CHECK (national_insurance_number ~ '^[A-Z]{2}[0-9]{6}[A-D]$' OR national_insurance_number IS NULL)
);

-- Contact information
CREATE TABLE contact_information (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    
    -- Email
    email VARCHAR(255) NOT NULL,
    email_verified BOOLEAN DEFAULT FALSE,
    email_verification_token VARCHAR(255),
    
    -- Phone numbers
    primary_phone VARCHAR(20) NOT NULL,
    primary_phone_verified BOOLEAN DEFAULT FALSE,
    secondary_phone VARCHAR(20),
    mobile_phone VARCHAR(20),
    
    -- Preferred contact method
    preferred_contact_method VARCHAR(20) DEFAULT 'email',
    preferred_contact_time VARCHAR(50),
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT valid_email CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z]{2,}$'),
    CONSTRAINT valid_contact_method CHECK (preferred_contact_method IN ('email', 'phone', 'post', 'no_preference'))
);

-- Address information
CREATE TABLE addresses (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    address_type VARCHAR(20) NOT NULL, -- 'current', 'previous', 'correspondence'
    
    -- Address fields
    address_line_1 VARCHAR(255) NOT NULL,
    address_line_2 VARCHAR(255),
    address_line_3 VARCHAR(255),
    town_city VARCHAR(100) NOT NULL,
    county VARCHAR(100),
    postcode VARCHAR(10) NOT NULL,
    country VARCHAR(100) DEFAULT 'United Kingdom',
    
    -- Residency information
    move_in_date DATE,
    residential_status VARCHAR(50), -- 'owner', 'tenant', 'living_with_family'
    years_at_address INTEGER,
    
    is_primary BOOLEAN DEFAULT FALSE,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT valid_uk_postcode CHECK (
        country != 'United Kingdom' OR 
        postcode ~* '^[A-Z]{1,2}[0-9]{1,2}[A-Z]?\s?[0-9][A-Z]{2}$'
    ),
    CONSTRAINT valid_address_type CHECK (address_type IN ('current', 'previous', 'correspondence'))
);

-- Employment information
CREATE TABLE employment_information (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    
    employment_status employment_status NOT NULL,
    
    -- Employment details
    employer_name VARCHAR(255),
    job_title VARCHAR(100),
    industry VARCHAR(100),
    occupation VARCHAR(100),
    
    -- Employment dates
    employment_start_date DATE,
    employment_end_date DATE,
    
    -- Income
    annual_income DECIMAL(15, 2),
    other_income DECIMAL(15, 2),
    income_source VARCHAR(255),
    
    is_current BOOLEAN DEFAULT TRUE,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT valid_income CHECK (annual_income >= 0),
    CONSTRAINT valid_employment_dates CHECK (employment_end_date IS NULL OR employment_end_date >= employment_start_date)
);

-- Financial information
CREATE TABLE financial_information (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    
    -- Assets
    total_assets DECIMAL(15, 2),
    property_value DECIMAL(15, 2),
    savings_investments DECIMAL(15, 2),
    pension_value DECIMAL(15, 2),
    other_assets DECIMAL(15, 2),
    other_assets_description TEXT,
    
    -- Liabilities
    total_liabilities DECIMAL(15, 2),
    mortgage_outstanding DECIMAL(15, 2),
    loans_outstanding DECIMAL(15, 2),
    credit_card_debt DECIMAL(15, 2),
    other_liabilities DECIMAL(15, 2),
    other_liabilities_description TEXT,
    
    -- Net worth (calculated)
    net_worth DECIMAL(15, 2) GENERATED ALWAYS AS (
        COALESCE(total_assets, 0) - COALESCE(total_liabilities, 0)
    ) STORED,
    
    -- Monthly finances
    monthly_income DECIMAL(15, 2),
    monthly_expenses DECIMAL(15, 2),
    monthly_disposable_income DECIMAL(15, 2) GENERATED ALWAYS AS (
        COALESCE(monthly_income, 0) - COALESCE(monthly_expenses, 0)
    ) STORED,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT valid_amounts CHECK (
        total_assets >= 0 AND 
        total_liabilities >= 0 AND 
        monthly_income >= 0 AND 
        monthly_expenses >= 0
    )
);

-- Fact find / Investment objectives
CREATE TABLE fact_find (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    
    -- Investment objectives
    investment_objectives TEXT[],
    investment_time_horizon INTEGER, -- in years
    risk_tolerance risk_tolerance,
    
    -- Investment experience
    investment_experience_years INTEGER,
    investment_knowledge_level VARCHAR(20), -- 'basic', 'intermediate', 'advanced', 'professional'
    previous_investments TEXT[],
    
    -- Financial goals
    financial_goals TEXT[],
    retirement_age INTEGER,
    retirement_income_target DECIMAL(15, 2),
    
    -- Dependents
    number_of_dependents INTEGER DEFAULT 0,
    dependents_ages INTEGER[],
    
    -- Other considerations
    ethical_investment_preferences TEXT,
    tax_considerations TEXT,
    estate_planning_needs TEXT,
    
    -- Additional notes
    additional_information TEXT,
    special_requirements TEXT,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT valid_time_horizon CHECK (investment_time_horizon >= 0 AND investment_time_horizon <= 100),
    CONSTRAINT valid_retirement_age CHECK (retirement_age >= 50 AND retirement_age <= 100),
    CONSTRAINT valid_knowledge_level CHECK (
        investment_knowledge_level IN ('basic', 'intermediate', 'advanced', 'professional')
    )
);

-- Consent and declarations
CREATE TABLE consents (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    
    -- Data protection
    data_processing_consent BOOLEAN NOT NULL DEFAULT FALSE,
    marketing_consent BOOLEAN DEFAULT FALSE,
    third_party_sharing_consent BOOLEAN DEFAULT FALSE,
    
    -- Terms and conditions
    terms_accepted BOOLEAN NOT NULL DEFAULT FALSE,
    terms_accepted_at TIMESTAMP WITH TIME ZONE,
    terms_version VARCHAR(20),
    
    -- Declarations
    information_accuracy_declaration BOOLEAN NOT NULL DEFAULT FALSE,
    fca_declaration BOOLEAN NOT NULL DEFAULT FALSE,
    
    -- Electronic signature
    electronic_signature VARCHAR(255),
    signature_date TIMESTAMP WITH TIME ZONE,
    ip_address_at_signature INET,
    
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT all_required_consents CHECK (
        data_processing_consent = TRUE AND 
        terms_accepted = TRUE AND 
        information_accuracy_declaration = TRUE AND 
        fca_declaration = TRUE
    )
);

-- Audit log
CREATE TABLE audit_log (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    submission_id UUID REFERENCES client_submissions(id) ON DELETE CASCADE,
    action VARCHAR(50) NOT NULL,
    table_name VARCHAR(100),
    record_id UUID,
    old_values JSONB,
    new_values JSONB,
    changed_by VARCHAR(255),
    ip_address INET,
    user_agent TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    
    CONSTRAINT valid_action CHECK (
        action IN ('create', 'update', 'delete', 'submit', 'archive', 'restore')
    )
);

-- Indexes for performance
CREATE INDEX idx_submissions_session ON client_submissions(session_id);
CREATE INDEX idx_submissions_status ON client_submissions(status);
CREATE INDEX idx_submissions_created ON client_submissions(created_at);
CREATE INDEX idx_personal_details_submission ON personal_details(submission_id);
CREATE INDEX idx_contact_email ON contact_information(email);
CREATE INDEX idx_addresses_submission ON addresses(submission_id);
CREATE INDEX idx_addresses_postcode ON addresses(postcode);
CREATE INDEX idx_audit_log_submission ON audit_log(submission_id);
CREATE INDEX idx_audit_log_created ON audit_log(created_at);

-- Function to update updated_at timestamp
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Triggers for updated_at
CREATE TRIGGER update_client_submissions_updated_at BEFORE UPDATE ON client_submissions FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_personal_details_updated_at BEFORE UPDATE ON personal_details FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_contact_information_updated_at BEFORE UPDATE ON contact_information FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_addresses_updated_at BEFORE UPDATE ON addresses FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_employment_information_updated_at BEFORE UPDATE ON employment_information FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_financial_information_updated_at BEFORE UPDATE ON financial_information FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_fact_find_updated_at BEFORE UPDATE ON fact_find FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_consents_updated_at BEFORE UPDATE ON consents FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

---

## Backend Implementation

### Project Structure

```
backend/
├── src/
│   ├── config/
│   │   ├── database.ts
│   │   ├── redis.ts
│   │   └── environment.ts
│   ├── middleware/
│   │   ├── auth.ts
│   │   ├── validation.ts
│   │   ├── errorHandler.ts
│   │   ├── rateLimiter.ts
│   │   └── security.ts
│   ├── models/
│   │   ├── ClientSubmission.ts
│   │   ├── PersonalDetails.ts
│   │   ├── ContactInformation.ts
│   │   ├── Address.ts
│   │   ├── Employment.ts
│   │   ├── Financial.ts
│   │   ├── FactFind.ts
│   │   └── Consent.ts
│   ├── routes/
│   │   ├── index.ts
│   │   ├── submission.routes.ts
│   │   ├── autosave.routes.ts
│   │   └── validation.routes.ts
│   ├── services/
│   │   ├── submissionService.ts
│   │   ├── validationService.ts
│   │   ├── autosaveService.ts
│   │   ├── emailService.ts
│   │   └── auditService.ts
│   ├── validators/
│   │   ├── personalDetails.validator.ts
│   │   ├── contactInfo.validator.ts
│   │   ├── address.validator.ts
│   │   ├── employment.validator.ts
│   │   ├── financial.validator.ts
│   │   ├── factFind.validator.ts
│   │   └── consent.validator.ts
│   ├── utils/
│   │   ├── logger.ts
│   │   ├── postcodeValidator.ts
│   │   ├── niNumberValidator.ts
│   │   └── encryption.ts
│   ├── types/
│   │   └── index.ts
│   └── server.ts
├── tests/
├── package.json
├── tsconfig.json
└── .env.example
```

### Core Backend Files

#### `src/config/environment.ts`

```typescript
import dotenv from 'dotenv';

dotenv.config();

export const config = {
  node_env: process.env.NODE_ENV || 'development',
  port: parseInt(process.env.PORT || '5000', 10),
  
  // Database
  database: {
    host: process.env.DB_HOST || 'localhost',
    port: parseInt(process.env.DB_PORT || '5432', 10),
    name: process.env.DB_NAME || 'wealth_client_forms',
    user: process.env.DB_USER || 'postgres',
    password: process.env.DB_PASSWORD || '',
    ssl: process.env.DB_SSL === 'true',
    max_connections: parseInt(process.env.DB_MAX_CONNECTIONS || '20', 10),
  },
  
  // Redis
  redis: {
    host: process.env.REDIS_HOST || 'localhost',
    port: parseInt(process.env.REDIS_PORT || '6379', 10),
    password: process.env.REDIS_PASSWORD,
    ttl: parseInt(process.env.REDIS_TTL || '3600', 10), // 1 hour default
  },
  
  // Security
  security: {
    cors_origin: process.env.CORS_ORIGIN || 'http://localhost:3000',
    encryption_key: process.env.ENCRYPTION_KEY || '',
    session_secret: process.env.SESSION_SECRET || '',
    rate_limit_window: parseInt(process.env.RATE_LIMIT_WINDOW || '900000', 10), // 15 minutes
    rate_limit_max: parseInt(process.env.RATE_LIMIT_MAX || '100', 10),
  },
  
  // Email
  email: {
    smtp_host: process.env.SMTP_HOST,
    smtp_port: parseInt(process.env.SMTP_PORT || '587', 10),
    smtp_user: process.env.SMTP_USER,
    smtp_password: process.env.SMTP_PASSWORD,
    from_address: process.env.EMAIL_FROM || 'noreply@wealthfirm.co.uk',
  },
  
  // Application
  app: {
    autosave_interval: parseInt(process.env.AUTOSAVE_INTERVAL || '30000', 10), // 30 seconds
    session_timeout: parseInt(process.env.SESSION_TIMEOUT || '3600000', 10), // 1 hour
    max_file_size: parseInt(process.env.MAX_FILE_SIZE || '5242880', 10), // 5MB
  },
};

export default config;
```

#### `src/config/database.ts`

```typescript
import { Pool, PoolClient } from 'pg';
import { config } from './environment';
import logger from '../utils/logger';

const pool = new Pool({
  host: config.database.host,
  port: config.database.port,
  database: config.database.name,
  user: config.database.user,
  password: config.database.password,
  max: config.database.max_connections,
  ssl: config.database.ssl ? { rejectUnauthorized: false } : false,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

pool.on('error', (err) => {
  logger.error('Unexpected error on idle client', err);
  process.exit(-1);
});

pool.on('connect', () => {
  logger.info('Database connection established');
});

export const query = async (text: string, params?: any[]) => {
  const start = Date.now();
  try {
    const res = await pool.query(text, params);
    const duration = Date.now() - start;
    logger.debug('Executed query', { text, duration, rows: res.rowCount });
    return res;
  } catch (error) {
    logger.error('Database query error', { text, error });
    throw error;
  }
};

export const getClient = async (): Promise<PoolClient> => {
  const client = await pool.connect();
  return client;
};

export const transaction = async (callback: (client: PoolClient) => Promise<any>) => {
  const client = await pool.connect();
  try {
    await client.query('BEGIN');
    const result = await callback(client);
    await client.query('COMMIT');
    return result;
  } catch (error) {
    await client.query('ROLLBACK');
    throw error;
  } finally {
    client.release();
  }
};

export default pool;
```

#### `src/config/redis.ts`

```typescript
import Redis from 'ioredis';
import { config } from './environment';
import logger from '../utils/logger';

const redis = new Redis({
  host: config.redis.host,
  port: config.redis.port,
  password: config.redis.password,
  retryStrategy: (times) => {
    const delay = Math.min(times * 50, 2000);
    return delay;
  },
  maxRetriesPerRequest: 3,
});

redis.on('connect', () => {
  logger.info('Redis connection established');
});

redis.on('error', (err) => {
  logger.error('Redis connection error', err);
});

export const setCache = async (key: string, value: any, ttl?: number): Promise<void> => {
  const serialized = JSON.stringify(value);
  const expiry = ttl || config.redis.ttl;
  await redis.setex(key, expiry, serialized);
};

export const getCache = async <T>(key: string): Promise<T | null> => {
  const data = await redis.get(key);
  return data ? JSON.parse(data) : null;
};

export const deleteCache = async (key: string): Promise<void> => {
  await redis.del(key);
};

export const exists = async (key: string): Promise<boolean> => {
  const result = await redis.exists(key);
  return result === 1;
};

export default redis;
```

#### `src/types/index.ts`

```typescript
export enum SubmissionStatus {
  DRAFT = 'draft',
  IN_PROGRESS = 'in_progress',
  COMPLETED = 'completed',
  SUBMITTED = 'submitted',
  ARCHIVED = 'archived',
}

export enum MaritalStatus {
  SINGLE = 'single',
  MARRIED = 'married',
  CIVIL_PARTNERSHIP = 'civil_partnership',
  DIVORCED = 'divorced',
  WIDOWED = 'widowed',
  SEPARATED = 'separated',
}

export enum EmploymentStatus {
  EMPLOYED = 'employed',
  SELF_EMPLOYED = 'self_employed',
  RETIRED = 'retired',
  UNEMPLOYED = 'unemployed',
  STUDENT = 'student',
  OTHER = 'other',
}

export enum RiskTolerance {
  LOW = 'low',
  MEDIUM = 'medium',
  HIGH = 'high',
  VERY_HIGH = 'very_high',
}

export interface PersonalDetailsData {
  title?: string;
  firstName: string;
  middleNames?: string;
  lastName: string;
  preferredName?: string;
  dateOfBirth: string;
  nationalInsuranceNumber?: string;
  maritalStatus?: MaritalStatus;
  nationality?: string;
  countryOfBirth?: string;
}

export interface ContactInformationData {
  email: string;
  primaryPhone: string;
  secondaryPhone?: string;
  mobilePhone?: string;
  preferredContactMethod?: string;
  preferredContactTime?: string;
}

export interface AddressData {
  addressType: 'current' | 'previous' | 'correspondence';
  addressLine1: string;
  addressLine2?: string;
  addressLine3?: string;
  townCity: string;
  county?: string;
  postcode: string;
  country?: string;
  moveInDate?: string;
  residentialStatus?: string;
  yearsAtAddress?: number;
  isPrimary?: boolean;
}

export interface EmploymentData {
  employmentStatus: EmploymentStatus;
  employerName?: string;
  jobTitle?: string;
  industry?: string;
  occupation?: string;
  employmentStartDate?: string;
  employmentEndDate?: string;
  annualIncome?: number;
  otherIncome?: number;
  incomeSource?: string;
  isCurrent?: boolean;
}

export interface FinancialData {
  totalAssets?: number;
  propertyValue?: number;
  savingsInvestments?: number;
  pensionValue?: number;
  otherAssets?: number;
  otherAssetsDescription?: string;
  totalLiabilities?: number;
  mortgageOutstanding?: number;
  loansOutstanding?: number;
  creditCardDebt?: number;
  otherLiabilities?: number;
  otherLiabilitiesDescription?: string;
  monthlyIncome?: number;
  monthlyExpenses?: number;
}

export interface FactFindData {
  investmentObjectives?: string[];
  investmentTimeHorizon?: number;
  riskTolerance?: RiskTolerance;
  investmentExperienceYears?: number;
  investmentKnowledgeLevel?: string;
  previousInvestments?: string[];
  financialGoals?: string[];
  retirementAge?: number;
  retirementIncomeTarget?: number;
  numberOfDependents?: number;
  dependentsAges?: number[];
  ethicalInvestmentPreferences?: string;
  taxConsiderations?: string;
  estatePlanningNeeds?: string;
  additionalInformation?: string;
  specialRequirements?: string;
}

export interface ConsentData {
  dataProcessingConsent: boolean;
  marketingConsent?: boolean;
  thirdPartySharingConsent?: boolean;
  termsAccepted: boolean;
  termsVersion?: string;
  informationAccuracyDeclaration: boolean;
  fcaDeclaration: boolean;
  electronicSignature?: string;
}

export interface FormSubmission {
  sessionId: string;
  currentStep: number;
  personalDetails?: PersonalDetailsData;
  contactInformation?: ContactInformationData;
  addresses?: AddressData[];
  employment?: EmploymentData[];
  financial?: FinancialData;
  factFind?: FactFindData;
  consents?: ConsentData;
}

export interface ApiResponse<T = any> {
  success: boolean;
  data?: T;
  error?: {
    code: string;
    message: string;
    details?: any;
  };
  meta?: {
    timestamp: string;
    requestId?: string;
  };
}
```

#### `src/utils/logger.ts`

```typescript
import winston from 'winston';
import { config } from '../config/environment';

const levels = {
  error: 0,
  warn: 1,
  info: 2,
  http: 3,
  debug: 4,
};

const level = () => {
  const env = config.node_env || 'development';
  const isDevelopment = env === 'development';
  return isDevelopment ? 'debug' : 'warn';
};

const colors = {
  error: 'red',
  warn: 'yellow',
  info: 'green',
  http: 'magenta',
  debug: 'white',
};

winston.addColors(colors);

const format = winston.format.combine(
  winston.format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss:ms' }),
  winston.format.colorize({ all: true }),
  winston.format.printf(
    (info) => `${info.timestamp} ${info.level}: ${info.message}`,
  ),
);

const transports = [
  new winston.transports.Console(),
  new winston.transports.File({
    filename: 'logs/error.log',
    level: 'error',
  }),
  new winston.transports.File({ filename: 'logs/all.log' }),
];

const logger = winston.createLogger({
  level: level(),
  levels,
  format,
  transports,
});

export default logger;
```

#### `src/utils/postcodeValidator.ts`

```typescript
import axios from 'axios';
import logger from './logger';

// UK Postcode regex pattern
const UK_POSTCODE_REGEX = /^[A-Z]{1,2}\d{1,2}[A-Z]?\s?\d[A-Z]{2}$/i;

export const validateUKPostcode = (postcode: string): boolean => {
  const cleanPostcode = postcode.replace(/\s/g, '').toUpperCase();
  return UK_POSTCODE_REGEX.test(cleanPostcode);
};

export const formatUKPostcode = (postcode: string): string => {
  const cleanPostcode = postcode.replace(/\s/g, '').toUpperCase();
  
  if (!validateUKPostcode(cleanPostcode)) {
    return postcode;
  }
  
  // Insert space before the last 3 characters
  const outward = cleanPostcode.slice(0, -3);
  const inward = cleanPostcode.slice(-3);
  
  return `${outward} ${inward}`;
};

export const lookupPostcode = async (postcode: string): Promise<any | null> => {
  try {
    const cleanPostcode = postcode.replace(/\s/g, '');
    const response = await axios.get(
      `https://api.postcodes.io/postcodes/${cleanPostcode}`
    );
    
    if (response.data && response.