# Backend API and Data Processing Development

**Agent:** backend_dev
**Job:** Client Responsive Webform

---

# Backend API and Data Processing Development - Client Responsive Webform

## Table of Contents
1. [Architecture Overview](#architecture-overview)
2. [Technology Stack](#technology-stack)
3. [Database Schema](#database-schema)
4. [API Endpoints](#api-endpoints)
5. [Implementation](#implementation)
6. [Security Features](#security-features)
7. [Testing](#testing)
8. [Deployment Instructions](#deployment-instructions)

---

## Architecture Overview

The backend is built using Node.js with Express.js, following a layered architecture pattern:

```
├── src/
│   ├── config/           # Configuration files
│   ├── controllers/      # Request handlers
│   ├── middleware/       # Custom middleware
│   ├── models/           # Database models
│   ├── routes/           # API routes
│   ├── services/         # Business logic
│   ├── utils/            # Utility functions
│   ├── validators/       # Input validation schemas
│   └── app.js            # Application entry point
├── tests/                # Test files
├── migrations/           # Database migrations
└── docs/                 # API documentation
```

---

## Technology Stack

- **Runtime**: Node.js v18+
- **Framework**: Express.js v4.18+
- **Database**: PostgreSQL 14+ with encryption at rest
- **ORM**: Sequelize v6
- **Validation**: Joi
- **Authentication**: JWT with refresh tokens
- **Encryption**: crypto (AES-256-GCM)
- **Rate Limiting**: express-rate-limit
- **Security**: helmet, cors, csurf
- **Email**: nodemailer with SendGrid
- **Postcode API**: getaddress.io or ideal-postcodes.co.uk
- **Logging**: winston
- **Testing**: Jest, Supertest

---

## Database Schema

### Migration Script

```sql
-- migrations/001_initial_schema.sql

-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Create enum types
CREATE TYPE form_status AS ENUM ('draft', 'submitted', 'reviewed', 'approved', 'rejected');
CREATE TYPE risk_profile AS ENUM ('low', 'medium', 'high', 'very_high');

-- Client Forms Table
CREATE TABLE client_forms (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_reference VARCHAR(20) UNIQUE NOT NULL,
    status form_status DEFAULT 'draft',
    current_step INTEGER DEFAULT 1,
    total_steps INTEGER DEFAULT 6,
    started_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    submitted_at TIMESTAMP,
    ip_address INET,
    user_agent TEXT,
    session_token VARCHAR(255) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Personal Details Table (Encrypted)
CREATE TABLE personal_details (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    title VARCHAR(20),
    first_name_encrypted BYTEA NOT NULL,
    middle_name_encrypted BYTEA,
    last_name_encrypted BYTEA NOT NULL,
    date_of_birth_encrypted BYTEA NOT NULL,
    national_insurance_encrypted BYTEA,
    marital_status VARCHAR(20),
    nationality VARCHAR(100),
    encryption_iv BYTEA NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(form_id)
);

-- Contact Details Table (Encrypted)
CREATE TABLE contact_details (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    email_encrypted BYTEA NOT NULL,
    phone_encrypted BYTEA NOT NULL,
    mobile_encrypted BYTEA,
    preferred_contact_method VARCHAR(20),
    encryption_iv BYTEA NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(form_id)
);

-- Address Details Table (Encrypted)
CREATE TABLE address_details (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    address_line1_encrypted BYTEA NOT NULL,
    address_line2_encrypted BYTEA,
    city_encrypted BYTEA NOT NULL,
    county_encrypted BYTEA,
    postcode_encrypted BYTEA NOT NULL,
    country VARCHAR(100) DEFAULT 'United Kingdom',
    years_at_address INTEGER,
    previous_address_line1_encrypted BYTEA,
    previous_address_line2_encrypted BYTEA,
    previous_city_encrypted BYTEA,
    previous_county_encrypted BYTEA,
    previous_postcode_encrypted BYTEA,
    encryption_iv BYTEA NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(form_id)
);

-- Employment Details Table (Encrypted)
CREATE TABLE employment_details (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    employment_status VARCHAR(50),
    employer_name_encrypted BYTEA,
    job_title_encrypted BYTEA,
    industry VARCHAR(100),
    annual_income_encrypted BYTEA,
    years_employed INTEGER,
    encryption_iv BYTEA NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(form_id)
);

-- Financial Information Table (Encrypted)
CREATE TABLE financial_information (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    net_worth_encrypted BYTEA,
    liquid_assets_encrypted BYTEA,
    property_value_encrypted BYTEA,
    outstanding_mortgage_encrypted BYTEA,
    other_liabilities_encrypted BYTEA,
    monthly_income_encrypted BYTEA,
    monthly_expenses_encrypted BYTEA,
    source_of_wealth TEXT,
    encryption_iv BYTEA NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(form_id)
);

-- Investment Objectives Table
CREATE TABLE investment_objectives (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    investment_goal VARCHAR(100),
    investment_timeline VARCHAR(50),
    risk_tolerance risk_profile,
    investment_experience VARCHAR(50),
    previous_investments JSONB,
    ethical_preferences JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(form_id)
);

-- Audit Log Table
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    action VARCHAR(100) NOT NULL,
    actor_ip INET,
    actor_user_agent TEXT,
    changed_fields JSONB,
    previous_values JSONB,
    new_values JSONB,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    severity VARCHAR(20) DEFAULT 'info'
);

-- Email Notifications Table
CREATE TABLE email_notifications (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    form_id UUID REFERENCES client_forms(id) ON DELETE CASCADE,
    recipient_email VARCHAR(255) NOT NULL,
    email_type VARCHAR(50) NOT NULL,
    subject VARCHAR(255),
    sent_at TIMESTAMP,
    status VARCHAR(20) DEFAULT 'pending',
    error_message TEXT,
    retry_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create indexes for performance
CREATE INDEX idx_client_forms_status ON client_forms(status);
CREATE INDEX idx_client_forms_session ON client_forms(session_token);
CREATE INDEX idx_audit_logs_form_id ON audit_logs(form_id);
CREATE INDEX idx_audit_logs_timestamp ON audit_logs(timestamp);
CREATE INDEX idx_email_notifications_status ON email_notifications(status);

-- Create update trigger for updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_client_forms_updated_at BEFORE UPDATE ON client_forms 
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_personal_details_updated_at BEFORE UPDATE ON personal_details 
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_contact_details_updated_at BEFORE UPDATE ON contact_details 
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_address_details_updated_at BEFORE UPDATE ON address_details 
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_employment_details_updated_at BEFORE UPDATE ON employment_details 
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_financial_information_updated_at BEFORE UPDATE ON financial_information 
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
CREATE TRIGGER update_investment_objectives_updated_at BEFORE UPDATE ON investment_objectives 
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

---

## API Endpoints

### Base URL
```
Production: https://api.wealthmanagement.co.uk/v1
Staging: https://api-staging.wealthmanagement.co.uk/v1
```

### Authentication
All endpoints (except form initialization) require a session token in the header:
```
X-Session-Token: <session_token>
```

### Endpoints Summary

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/forms/initialize` | Initialize new form session |
| GET | `/forms/:sessionToken` | Get form data |
| PUT | `/forms/:sessionToken/step/:stepNumber` | Save step data |
| POST | `/forms/:sessionToken/submit` | Submit complete form |
| GET | `/forms/:sessionToken/resume` | Resume saved form |
| POST | `/postcode/lookup` | UK postcode lookup |
| GET | `/forms/:sessionToken/status` | Check form status |
| DELETE | `/forms/:sessionToken` | Delete draft form |

---

## Implementation

### 1. Environment Configuration

```javascript
// src/config/env.js

require('dotenv').config();

module.exports = {
  // Server
  NODE_ENV: process.env.NODE_ENV || 'development',
  PORT: process.env.PORT || 3000,
  API_VERSION: 'v1',
  
  // Database
  DB_HOST: process.env.DB_HOST,
  DB_PORT: process.env.DB_PORT || 5432,
  DB_NAME: process.env.DB_NAME,
  DB_USER: process.env.DB_USER,
  DB_PASSWORD: process.env.DB_PASSWORD,
  DB_SSL: process.env.DB_SSL === 'true',
  
  // Encryption
  ENCRYPTION_KEY: process.env.ENCRYPTION_KEY, // 32 bytes hex
  
  // JWT
  JWT_SECRET: process.env.JWT_SECRET,
  JWT_EXPIRY: process.env.JWT_EXPIRY || '24h',
  
  // Rate Limiting
  RATE_LIMIT_WINDOW: 15 * 60 * 1000, // 15 minutes
  RATE_LIMIT_MAX_REQUESTS: 100,
  
  // CORS
  CORS_ORIGIN: process.env.CORS_ORIGIN || 'https://forms.wealthmanagement.co.uk',
  
  // Email
  SMTP_HOST: process.env.SMTP_HOST,
  SMTP_PORT: process.env.SMTP_PORT,
  SMTP_USER: process.env.SMTP_USER,
  SMTP_PASSWORD: process.env.SMTP_PASSWORD,
  EMAIL_FROM: process.env.EMAIL_FROM,
  ADMIN_EMAIL: process.env.ADMIN_EMAIL,
  
  // Postcode API
  POSTCODE_API_KEY: process.env.POSTCODE_API_KEY,
  POSTCODE_API_URL: process.env.POSTCODE_API_URL || 'https://api.getaddress.io',
  
  // Session
  SESSION_EXPIRY_HOURS: 72,
  
  // Security
  CSRF_SECRET: process.env.CSRF_SECRET,
  HELMET_CSP: process.env.HELMET_CSP === 'true',
};
```

### 2. Database Connection

```javascript
// src/config/database.js

const { Sequelize } = require('sequelize');
const config = require('./env');
const logger = require('../utils/logger');

const sequelize = new Sequelize({
  host: config.DB_HOST,
  port: config.DB_PORT,
  database: config.DB_NAME,
  username: config.DB_USER,
  password: config.DB_PASSWORD,
  dialect: 'postgres',
  logging: config.NODE_ENV === 'development' ? logger.debug : false,
  ssl: config.DB_SSL,
  pool: {
    max: 10,
    min: 2,
    acquire: 30000,
    idle: 10000,
  },
  dialectOptions: {
    ssl: config.DB_SSL ? {
      require: true,
      rejectUnauthorized: false,
    } : false,
  },
});

// Test connection
sequelize.authenticate()
  .then(() => logger.info('Database connection established successfully'))
  .catch(err => logger.error('Unable to connect to database:', err));

module.exports = sequelize;
```

### 3. Encryption Utility

```javascript
// src/utils/encryption.js

const crypto = require('crypto');
const config = require('../config/env');

const ALGORITHM = 'aes-256-gcm';
const KEY = Buffer.from(config.ENCRYPTION_KEY, 'hex');

class EncryptionService {
  /**
   * Encrypt data
   * @param {string} text - Plain text to encrypt
   * @returns {Object} - { encrypted: Buffer, iv: Buffer, authTag: Buffer }
   */
  static encrypt(text) {
    if (!text) return null;
    
    const iv = crypto.randomBytes(16);
    const cipher = crypto.createCipheriv(ALGORITHM, KEY, iv);
    
    let encrypted = cipher.update(String(text), 'utf8');
    encrypted = Buffer.concat([encrypted, cipher.final()]);
    
    const authTag = cipher.getAuthTag();
    
    return {
      encrypted,
      iv,
      authTag,
    };
  }

  /**
   * Decrypt data
   * @param {Buffer} encrypted - Encrypted data
   * @param {Buffer} iv - Initialization vector
   * @param {Buffer} authTag - Authentication tag
   * @returns {string} - Decrypted text
   */
  static decrypt(encrypted, iv, authTag) {
    if (!encrypted || !iv || !authTag) return null;
    
    try {
      const decipher = crypto.createDecipheriv(ALGORITHM, KEY, iv);
      decipher.setAuthTag(authTag);
      
      let decrypted = decipher.update(encrypted);
      decrypted = Buffer.concat([decrypted, decipher.final()]);
      
      return decrypted.toString('utf8');
    } catch (error) {
      throw new Error('Decryption failed');
    }
  }

  /**
   * Encrypt object fields
   * @param {Object} data - Object with fields to encrypt
   * @param {Array} fields - Array of field names to encrypt
   * @returns {Object} - Object with encrypted fields and IV
   */
  static encryptFields(data, fields) {
    const iv = crypto.randomBytes(16);
    const encrypted = {};
    
    fields.forEach(field => {
      if (data[field]) {
        const cipher = crypto.createCipheriv(ALGORITHM, KEY, iv);
        let encryptedData = cipher.update(String(data[field]), 'utf8');
        encryptedData = Buffer.concat([encryptedData, cipher.final()]);
        const authTag = cipher.getAuthTag();
        
        // Combine encrypted data and auth tag
        encrypted[`${field}_encrypted`] = Buffer.concat([encryptedData, authTag]);
      }
    });
    
    encrypted.encryption_iv = iv;
    return encrypted;
  }

  /**
   * Decrypt object fields
   * @param {Object} data - Object with encrypted fields
   * @param {Array} fields - Array of field names to decrypt
   * @returns {Object} - Object with decrypted fields
   */
  static decryptFields(data, fields) {
    const decrypted = {};
    const iv = data.encryption_iv;
    
    if (!iv) return decrypted;
    
    fields.forEach(field => {
      const encryptedField = `${field}_encrypted`;
      if (data[encryptedField]) {
        try {
          const encryptedData = data[encryptedField];
          // Last 16 bytes are auth tag
          const authTag = encryptedData.slice(-16);
          const encrypted = encryptedData.slice(0, -16);
          
          const decipher = crypto.createDecipheriv(ALGORITHM, KEY, iv);
          decipher.setAuthTag(authTag);
          
          let decryptedData = decipher.update(encrypted);
          decryptedData = Buffer.concat([decryptedData, decipher.final()]);
          
          decrypted[field] = decryptedData.toString('utf8');
        } catch (error) {
          decrypted[field] = null;
        }
      }
    });
    
    return decrypted;
  }
}

module.exports = EncryptionService;
```

### 4. UK Validation Utility

```javascript
// src/utils/ukValidation.js

class UKValidation {
  /**
   * Validate UK postcode
   * @param {string} postcode
   * @returns {boolean}
   */
  static validatePostcode(postcode) {
    if (!postcode) return false;
    
    // UK postcode regex pattern
    const pattern = /^([A-Z]{1,2}\d{1,2}[A-Z]?)\s*(\d[A-Z]{2})$/i;
    return pattern.test(postcode.trim());
  }

  /**
   * Format UK postcode
   * @param {string} postcode
   * @returns {string}
   */
  static formatPostcode(postcode) {
    if (!postcode) return '';
    
    const cleaned = postcode.replace(/\s+/g, '').toUpperCase();
    const match = cleaned.match(/^([A-Z]{1,2}\d{1,2}[A-Z]?)(\d[A-Z]{2})$/);
    
    if (match) {
      return `${match[1]} ${match[2]}`;
    }
    
    return postcode;
  }

  /**
   * Validate UK phone number
   * @param {string} phone
   * @returns {boolean}
   */
  static validatePhoneNumber(phone) {
    if (!phone) return false;
    
    // Remove spaces, dashes, parentheses
    const cleaned = phone.replace(/[\s\-\(\)]/g, '');
    
    // UK phone patterns
    const patterns = [
      /^(\+44|0044|0)\d{10}$/, // Standard UK format
      /^(\+44|0044|0)7\d{9}$/, // Mobile
      /^(\+44|0044|0)[1-9]\d{9}$/, // Landline
    ];
    
    return patterns.some(pattern => pattern.test(cleaned));
  }

  /**
   * Format UK phone number
   * @param {string} phone
   * @returns {string}
   */
  static formatPhoneNumber(phone) {
    if (!phone) return '';
    
    let cleaned = phone.replace(/[\s\-\(\)]/g, '');
    
    // Convert to standard format starting with 0
    if (cleaned.startsWith('+44')) {
      cleaned = '0' + cleaned.slice(3);
    } else if (cleaned.startsWith('0044')) {
      cleaned = '0' + cleaned.slice(4);
    }
    
    return cleaned;
  }

  /**
   * Validate UK National Insurance Number
   * @param {string} nino
   * @returns {boolean}
   */
  static validateNationalInsurance(nino) {
    if (!nino) return false;
    
    // Remove spaces
    const cleaned = nino.replace(/\s+/g, '').toUpperCase();
    
    // NI number pattern: 2 letters, 6 digits, 1 letter (A, B, C, or D)
    // First letter cannot be D, F, I, Q, U, V
    // Second letter cannot be D, F, I, O, Q, U, V
    const pattern = /^(?!BG|GB|NK|KN|TN|NT|ZZ)[A-CEGHJ-PR-TW-Z][A-CEGHJ-NPR-TW-Z]\d{6}[A-D]$/;
    
    return pattern.test(cleaned);
  }

  /**
   * Format UK National Insurance Number
   * @param {string} nino
   * @returns {string}
   */
  static formatNationalInsurance(nino) {
    if (!nino) return '';
    
    const cleaned = nino.replace(/\s+/g, '').toUpperCase();
    
    if (cleaned.length === 9) {
      return `${cleaned.slice(0, 2)} ${cleaned.slice(2, 4)} ${cleaned.slice(4, 6)} ${cleaned.slice(6, 8)} ${cleaned.slice(8)}`;
    }
    
    return nino;
  }

  /**
   * Validate email address
   * @param {string} email
   * @returns {boolean}
   */
  static validateEmail(email) {
    if (!email) return false;
    
    const pattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return pattern.test(email.toLowerCase());
  }

  /**
   * Validate date of birth (must be 18+ for UK wealth management)
   * @param {string} dob - Date string in YYYY-MM-DD format
   * @returns {Object} - { valid: boolean, age: number, error: string }
   */
  static validateDateOfBirth(dob) {
    if (!dob) {
      return { valid: false, age: null, error: 'Date of birth is required' };
    }
    
    const date = new Date(dob);
    const today = new Date();
    
    if (isNaN(date.getTime())) {
      return { valid: false, age: null, error: 'Invalid date format' };
    }
    
    if (date >= today) {
      return { valid: false, age: null, error: 'Date of birth must be in the past' };
    }
    
    let age = today.getFullYear() - date.getFullYear();
    const monthDiff = today.getMonth() - date.getMonth();
    
    if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < date.getDate())) {
      age--;
    }
    
    if (age < 18) {
      return { valid: false, age, error: 'Must be 18 or older' };
    }
    
    if (age > 120) {
      return { valid: false, age, error: 'Invalid date of birth' };
    }
    
    return { valid: true, age, error: null };
  }
}

module.exports = UKValidation;
```

### 5. Validation Schemas

```javascript
// src/validators/formSchemas.js

const Joi = require('joi');
const UKValidation = require('../utils/ukValidation');

// Custom validators
const postcodeValidator = (value, helpers) => {
  if (!UKValidation.validatePostcode(value)) {
    return helpers.error('any.invalid');
  }
  return UKValidation.formatPostcode(value);
};

const phoneValidator = (value, helpers) => {
  if (!UKValidation.validatePhoneNumber(value)) {
    return helpers.error('any.invalid');
  }
  return UKValidation.formatPhoneNumber(value);
};

const niNumberValidator = (value, helpers) => {
  if (!UKValidation.validateNationalInsurance(value)) {
    return helpers.error('any.invalid');
  }
  return value.replace(/\s+/g, '').toUpperCase();
};

const dobValidator = (value, helpers) => {
  const validation = UKValidation.validateDateOfBirth(value);
  if (!validation.valid) {
    return helpers.error('any.invalid', { message: validation.error });
  }
  return value;
};

// Step 1: Personal Details
const personalDetailsSchema = Joi.object({
  title: Joi.string().valid('Mr', 'Mrs', 'Miss', 'Ms', 'Dr', 'Prof', 'Rev', 'Other').required(),
  firstName: Joi.string().min(1).max(100).trim().required(),
  middleName: Joi.string().max(100).trim().allow('', null),
  lastName: Joi.string().min(1).max(100).trim().required(),
  dateOfBirth: Joi.string().custom(dobValidator).required()
    .messages({ 'any.invalid': 'Must be 18 or older and a valid date' }),
  nationalInsurance: Joi.string().custom(niNumberValidator).required()
    .messages({ 'any.invalid': 'Invalid UK National Insurance number format' }),
  maritalStatus: Joi.string().valid('single', 'married', 'civil_partnership', 'divorced', 'widowed', 'separated').required(),
  nationality: Joi.string().min(2).max(100).required(),
});

// Step 2: Contact Details
const contactDetailsSchema = Joi.object({
  email: Joi.string().email().lowercase().required(),
  phone: Joi.string().custom(phoneValidator).required()
    .messages({ 'any.invalid': 'Invalid UK phone number format' }),
  mobile: Joi.string().custom(phoneValidator).allow('', null)
    .messages({ 'any.invalid': 'Invalid UK mobile number format' }),
  preferredContactMethod: Joi.string().valid('email', 'phone', 'mobile', 'post').required(),
});

// Step 3: Address Details
const addressDetailsSchema = Joi.object({
  addressLine1: Joi.string().min(1).max(255).required(),
  addressLine2: Joi.string().max(255).allow('', null),
  city: Joi.string().min(1).max(100).required(),
  county: Joi.string().max(100).allow('', null),
  postcode: Joi.string().custom(postcodeValidator).required()
    .messages({ 'any.invalid': 'Invalid UK postcode format' }),
  country: Joi.string().default('United Kingdom'),
  yearsAtAddress: Joi.number().integer().min(0).max(100).required(),
  // Previous address required if less than 3 years at current address
  previousAddressLine1: Joi.string().max(255).when('yearsAtAddress', {
    is: Joi.number().less(3),
    then: Joi.required(),
    otherwise: Joi.allow('', null),
  }),
  previousAddressLine2: Joi.string().max(255).allow('', null),
  previousCity: Joi.string().max(100).when('yearsAtAddress', {
    is: Joi.number().less(3),
    then: Joi.required(),
    otherwise: Joi.allow('', null),
  }),
  previousCounty: Joi.string().max(100).allow('', null),
  previousPostcode: Joi.string().custom(postcodeValidator).when('yearsAtAddress', {
    is: Joi.number().less(3),
    then: Joi.required(),
    otherwise: Joi.allow('', null),
  }).messages({ 'any.invalid': 'Invalid UK postcode format' }),
});

// Step 4: Employment Details
const employmentDetailsSchema = Joi.object({
  employmentStatus: Joi.string().valid(
    'employed_full_time',
    'employed_part_time',
    'self_employed',
    'retired',
    'unemployed',
    'student',
    'other'
  ).required(),
  employerName: Joi.string().max(255).when('employmentStatus', {
    is: Joi.string().valid('employed_full_time', 'employed_part_time'),
    then: Joi.required(),
    otherwise: Joi.allow('', null),
  }),
  jobTitle: Joi.string().max(255).when('employmentStatus', {
    is: Joi.string().valid('employed_full_time', 'employed_part_time', 'self_employed'),
    then: Joi.required(),
    otherwise: Joi.allow('', null),
  }),
  industry: Joi.string().max(100).allow('', null),
  annualIncome: Joi.number().min(0).max(100000000).required(),
  yearsEmployed: Joi.number().integer().min(0).max(100).allow(null),
});

// Step 5: Financial Information
const financialInformationSchema = Joi.object({
  netWorth: Joi.number().min(0).max(1000000000).required(),
  liquidAssets: Joi.number().min(0).max(1000000000).required(),
  propertyValue: Joi.number().min(0).max(1000000000).allow(0, null),
  outstandingMortgage: Joi.number().min(0).max(1000000000).allow(0, null),
  otherLiabilities: Joi.number().min(0).max(1000000000).allow(0, null),
  monthlyIncome: Joi.number().min(0).max(10000000).required(),
  monthlyExpenses: Joi.number().min(0).max(10000000).required(),
  sourceOfWealth: Joi.string().max(1000).required(),
});

// Step 6: Investment Objectives
const investmentObjectivesSchema = Joi.object({
  investmentGoal: Joi.string().valid(
    'wealth_preservation',
    'income_generation',
    'capital_growth',
    'retirement_planning',
    'education_funding',
    'other'
  ).required(),
  investmentTimeline: Joi.string().valid(
    'less_than_1_year',
    '1_to_3_years',
    '3_to_5_years',
    '5_to_10_years',
    'more_than_10_years'
  ).required(),
  riskTolerance: Joi.string().valid('low', 'medium', 'high', 'very_high').required(),
  investmentExperience: Joi.string().valid(
    'none',
    'limited',
    'moderate',
    'extensive'
  ).required(),
  previousInvestments: Joi.object({
    stocks: Joi.boolean(),