# Documentation and Training Materials

**Agent:** documentation_writer
**Job:** Client Responsive Webform

---

# Client Responsive Webform - Complete Documentation Package

## Document Version Control
- **Version:** 1.0
- **Last Updated:** 2024
- **Document Owner:** Development Team
- **Review Cycle:** Quarterly

---

# TABLE OF CONTENTS

1. [Technical Documentation](#technical-documentation)
2. [User Guide for Wealth Management Staff](#user-guide)
3. [Administrator Guide](#administrator-guide)
4. [Privacy Notice Templates](#privacy-notice-templates)
5. [Compliance Documentation](#compliance-documentation)
6. [Training Materials](#training-materials)
7. [Troubleshooting Guide](#troubleshooting-guide)
8. [FAQ](#faq)

---

# TECHNICAL DOCUMENTATION

## 1. System Architecture

### 1.1 Overview
The Client Responsive Webform is a multi-tier web application designed to securely collect and manage client information for UK wealth management firms.

### 1.2 Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     CLIENT LAYER                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Desktop    │  │    Tablet    │  │    Mobile    │      │
│  │   Browser    │  │    Browser   │  │    Browser   │      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘      │
└─────────┼──────────────────┼──────────────────┼─────────────┘
          │                  │                  │
          └──────────────────┼──────────────────┘
                             │
                    ┌────────▼────────┐
                    │   HTTPS/TLS 1.3 │
                    └────────┬────────┘
          ┌─────────────────────────────────────┐
          │        PRESENTATION LAYER           │
          │  ┌────────────────────────────┐    │
          │  │  React.js Frontend         │    │
          │  │  - Multi-step Form         │    │
          │  │  - Validation Logic        │    │
          │  │  - Progress Tracking       │    │
          │  └────────────────────────────┘    │
          └─────────────────┬───────────────────┘
                            │
                   ┌────────▼────────┐
                   │   REST API      │
                   └────────┬────────┘
          ┌─────────────────────────────────────┐
          │      APPLICATION LAYER              │
          │  ┌────────────────────────────┐    │
          │  │  Node.js/Express Backend   │    │
          │  │  - Authentication          │    │
          │  │  - Business Logic          │    │
          │  │  - Data Validation         │    │
          │  │  - Audit Logging           │    │
          │  └────────────────────────────┘    │
          └─────────────────┬───────────────────┘
                            │
          ┌─────────────────────────────────────┐
          │         DATA LAYER                  │
          │  ┌────────────┐  ┌──────────────┐  │
          │  │ PostgreSQL │  │ File Storage │  │
          │  │  Database  │  │   (S3/Azure) │  │
          │  └────────────┘  └──────────────┘  │
          └─────────────────────────────────────┘
                            │
          ┌─────────────────────────────────────┐
          │      SECURITY & COMPLIANCE          │
          │  - Encryption at Rest (AES-256)    │
          │  - Encryption in Transit (TLS 1.3) │
          │  - Audit Trail System              │
          │  - Data Retention Manager          │
          └─────────────────────────────────────┘
```

### 1.3 Technology Stack

**Frontend:**
- React.js 18.x
- TypeScript 5.x
- Formik for form management
- Yup for validation
- TailwindCSS for responsive design
- Axios for API communication

**Backend:**
- Node.js 18.x LTS
- Express.js 4.x
- TypeScript 5.x
- JWT for authentication
- Bcrypt for password hashing
- Winston for logging

**Database:**
- PostgreSQL 15.x
- Redis for session management

**Infrastructure:**
- Docker containers
- Nginx reverse proxy
- AWS/Azure cloud hosting
- CloudFlare CDN

---

## 2. Database Schema

### 2.1 Entity Relationship Diagram

```
┌─────────────────────┐
│     clients         │
├─────────────────────┤
│ PK client_id        │
│    uuid             │
│    submission_date  │
│    status           │
│    assigned_to      │
│    created_at       │
│    updated_at       │
└──────┬──────────────┘
       │
       │ 1:1
       │
┌──────▼──────────────┐
│ personal_details    │
├─────────────────────┤
│ PK detail_id        │
│ FK client_id        │
│    title            │
│    first_name       │
│    middle_names     │
│    last_name        │
│    date_of_birth    │
│    ni_number        │
│    marital_status   │
└─────────────────────┘

┌─────────────────────┐
│  contact_info       │
├─────────────────────┤
│ PK contact_id       │
│ FK client_id        │
│    email            │
│    phone_primary    │
│    phone_secondary  │
│    preferred_method │
└──────┬──────────────┘
       │
       │ 1:M
       │
┌──────▼──────────────┐
│    addresses        │
├─────────────────────┤
│ PK address_id       │
│ FK contact_id       │
│    address_type     │
│    line_1           │
│    line_2           │
│    city             │
│    county           │
│    postcode         │
│    country          │
│    from_date        │
│    to_date          │
└─────────────────────┘

┌─────────────────────┐
│   financial_info    │
├─────────────────────┤
│ PK financial_id     │
│ FK client_id        │
│    employment_status│
│    occupation       │
│    employer_name    │
│    annual_income    │
│    income_source    │
│    assets_value     │
│    liabilities_value│
└─────────────────────┘

┌─────────────────────┐
│   fact_find         │
├─────────────────────┤
│ PK factfind_id      │
│ FK client_id        │
│    investment_exp   │
│    risk_appetite    │
│    investment_goals │
│    time_horizon     │
│    existing_plans   │
│    health_status    │
│    dependents       │
└─────────────────────┘

┌─────────────────────┐
│   audit_trail       │
├─────────────────────┤
│ PK audit_id         │
│ FK client_id        │
│    user_id          │
│    action_type      │
│    action_detail    │
│    ip_address       │
│    timestamp        │
│    data_before      │
│    data_after       │
└─────────────────────┘

┌─────────────────────┐
│   documents         │
├─────────────────────┤
│ PK document_id      │
│ FK client_id        │
│    document_type    │
│    file_name        │
│    file_path        │
│    file_size        │
│    uploaded_by      │
│    upload_date      │
│    virus_scanned    │
└─────────────────────┘

┌─────────────────────┐
│   users             │
├─────────────────────┤
│ PK user_id          │
│    username         │
│    email            │
│    password_hash    │
│    role             │
│    last_login       │
│    is_active        │
│    created_at       │
└─────────────────────┘
```

### 2.2 Schema SQL

```sql
-- Create database
CREATE DATABASE wealth_client_forms;

-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Users table
CREATE TABLE users (
    user_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    username VARCHAR(100) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL CHECK (role IN ('admin', 'advisor', 'viewer')),
    is_active BOOLEAN DEFAULT true,
    last_login TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Clients table
CREATE TABLE clients (
    client_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    uuid VARCHAR(36) UNIQUE NOT NULL,
    submission_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(50) DEFAULT 'pending' CHECK (status IN ('pending', 'in_review', 'approved', 'rejected', 'archived')),
    assigned_to UUID REFERENCES users(user_id),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Personal details table
CREATE TABLE personal_details (
    detail_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    client_id UUID UNIQUE REFERENCES clients(client_id) ON DELETE CASCADE,
    title VARCHAR(20),
    first_name VARCHAR(100) NOT NULL,
    middle_names VARCHAR(200),
    last_name VARCHAR(100) NOT NULL,
    date_of_birth DATE NOT NULL,
    ni_number VARCHAR(13),
    marital_status VARCHAR(50),
    nationality VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Contact information table
CREATE TABLE contact_info (
    contact_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    client_id UUID UNIQUE REFERENCES clients(client_id) ON DELETE CASCADE,
    email VARCHAR(255) NOT NULL,
    phone_primary VARCHAR(20) NOT NULL,
    phone_secondary VARCHAR(20),
    preferred_contact_method VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Addresses table
CREATE TABLE addresses (
    address_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    contact_id UUID REFERENCES contact_info(contact_id) ON DELETE CASCADE,
    address_type VARCHAR(50) CHECK (address_type IN ('current', 'previous', 'correspondence')),
    line_1 VARCHAR(255) NOT NULL,
    line_2 VARCHAR(255),
    city VARCHAR(100) NOT NULL,
    county VARCHAR(100),
    postcode VARCHAR(10) NOT NULL,
    country VARCHAR(100) DEFAULT 'United Kingdom',
    from_date DATE,
    to_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Financial information table
CREATE TABLE financial_info (
    financial_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    client_id UUID UNIQUE REFERENCES clients(client_id) ON DELETE CASCADE,
    employment_status VARCHAR(50),
    occupation VARCHAR(200),
    employer_name VARCHAR(200),
    annual_income DECIMAL(15, 2),
    income_source VARCHAR(500),
    assets_value DECIMAL(15, 2),
    liabilities_value DECIMAL(15, 2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Fact find table
CREATE TABLE fact_find (
    factfind_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    client_id UUID UNIQUE REFERENCES clients(client_id) ON DELETE CASCADE,
    investment_experience VARCHAR(50),
    risk_appetite VARCHAR(50),
    investment_goals TEXT,
    time_horizon VARCHAR(50),
    existing_plans TEXT,
    health_status VARCHAR(200),
    dependents INTEGER,
    additional_notes TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Audit trail table
CREATE TABLE audit_trail (
    audit_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    client_id UUID REFERENCES clients(client_id),
    user_id UUID REFERENCES users(user_id),
    action_type VARCHAR(100) NOT NULL,
    action_detail TEXT,
    ip_address INET,
    user_agent TEXT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    data_before JSONB,
    data_after JSONB
);

-- Documents table
CREATE TABLE documents (
    document_id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    client_id UUID REFERENCES clients(client_id) ON DELETE CASCADE,
    document_type VARCHAR(100),
    file_name VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    file_size INTEGER,
    mime_type VARCHAR(100),
    uploaded_by UUID REFERENCES users(user_id),
    upload_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    virus_scanned BOOLEAN DEFAULT false,
    scan_result VARCHAR(50)
);

-- Create indexes for performance
CREATE INDEX idx_clients_status ON clients(status);
CREATE INDEX idx_clients_assigned ON clients(assigned_to);
CREATE INDEX idx_clients_submission ON clients(submission_date);
CREATE INDEX idx_audit_client ON audit_trail(client_id);
CREATE INDEX idx_audit_timestamp ON audit_trail(timestamp);
CREATE INDEX idx_documents_client ON documents(client_id);

-- Create updated_at trigger function
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

-- Apply triggers
CREATE TRIGGER update_clients_updated_at BEFORE UPDATE ON clients
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_personal_details_updated_at BEFORE UPDATE ON personal_details
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_contact_info_updated_at BEFORE UPDATE ON contact_info
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_financial_info_updated_at BEFORE UPDATE ON financial_info
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER update_fact_find_updated_at BEFORE UPDATE ON fact_find
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

---

## 3. API Documentation

### 3.1 API Overview

**Base URL:** `https://api.yourfirm.com/v1`

**Authentication:** Bearer Token (JWT)

**Content Type:** `application/json`

**Rate Limiting:** 100 requests per minute per IP

### 3.2 Authentication Endpoints

#### POST /auth/login
Authenticate user and receive JWT token.

**Request:**
```json
{
  "email": "advisor@firm.com",
  "password": "SecurePassword123!"
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600,
    "user": {
      "id": "uuid",
      "email": "advisor@firm.com",
      "role": "advisor",
      "username": "John Smith"
    }
  }
}
```

#### POST /auth/refresh
Refresh expired JWT token.

**Request:**
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### POST /auth/logout
Invalidate current session.

### 3.3 Client Form Endpoints

#### POST /forms/submit
Submit a new client form.

**Headers:**
```
Content-Type: application/json
X-CSRF-Token: csrf-token-value
```

**Request:**
```json
{
  "personalDetails": {
    "title": "Mr",
    "firstName": "John",
    "middleNames": "David",
    "lastName": "Smith",
    "dateOfBirth": "1980-05-15",
    "niNumber": "AB123456C",
    "maritalStatus": "married",
    "nationality": "British"
  },
  "contactInfo": {
    "email": "john.smith@email.com",
    "phonePrimary": "07700900123",
    "phoneSecondary": "02012345678",
    "preferredContactMethod": "email"
  },
  "addresses": [
    {
      "addressType": "current",
      "line1": "123 High Street",
      "line2": "Flat 4",
      "city": "London",
      "county": "Greater London",
      "postcode": "SW1A 1AA",
      "country": "United Kingdom",
      "fromDate": "2020-01-01"
    }
  ],
  "financialInfo": {
    "employmentStatus": "employed",
    "occupation": "Software Engineer",
    "employerName": "Tech Corp Ltd",
    "annualIncome": 75000,
    "incomeSource": "Employment, Savings Interest",
    "assetsValue": 150000,
    "liabilitiesValue": 200000
  },
  "factFind": {
    "investmentExperience": "moderate",
    "riskAppetite": "balanced",
    "investmentGoals": "Retirement planning, wealth growth",
    "timeHorizon": "10-15 years",
    "existingPlans": "Company pension, ISA",
    "healthStatus": "Good",
    "dependents": 2,
    "additionalNotes": "Interested in ethical investments"
  },
  "consent": {
    "dataProcessing": true,
    "marketing": false,
    "creditCheck": true,
    "timestamp": "2024-01-15T10:30:00Z",
    "ipAddress": "192.168.1.1"
  }
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "clientId": "uuid-here",
    "referenceNumber": "WM-2024-001234",
    "submissionDate": "2024-01-15T10:30:00Z",
    "status": "pending"
  },
  "message": "Form submitted successfully"
}
```

#### GET /forms/:clientId
Retrieve a specific client form (authenticated users only).

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "clientId": "uuid",
    "status": "in_review",
    "submissionDate": "2024-01-15T10:30:00Z",
    "assignedTo": "advisor-uuid",
    "personalDetails": { },
    "contactInfo": { },
    "addresses": [ ],
    "financialInfo": { },
    "factFind": { }
  }
}
```

#### GET /forms
List all client forms with filtering and pagination.

**Query Parameters:**
- `status` - Filter by status (pending, in_review, approved, rejected)
- `assignedTo` - Filter by assigned advisor
- `fromDate` - Filter submissions from date
- `toDate` - Filter submissions to date
- `page` - Page number (default: 1)
- `limit` - Results per page (default: 20, max: 100)
- `sortBy` - Sort field (submissionDate, status)
- `sortOrder` - asc or desc

**Example:** `/forms?status=pending&page=1&limit=20&sortBy=submissionDate&sortOrder=desc`

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "forms": [
      {
        "clientId": "uuid",
        "referenceNumber": "WM-2024-001234",
        "clientName": "John Smith",
        "submissionDate": "2024-01-15T10:30:00Z",
        "status": "pending",
        "assignedTo": null
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalRecords": 94,
      "limit": 20
    }
  }
}
```

#### PATCH /forms/:clientId/status
Update form status.

**Request:**
```json
{
  "status": "approved",
  "notes": "All documentation verified and approved"
}
```

#### PATCH /forms/:clientId/assign
Assign form to advisor.

**Request:**
```json
{
  "advisorId": "uuid",
  "notes": "Assigning to specialist advisor"
}
```

### 3.4 Document Endpoints

#### POST /forms/:clientId/documents
Upload supporting documents.

**Headers:**
```
Content-Type: multipart/form-data
Authorization: Bearer {token}
```

**Request:**
```
Form Data:
- file: [binary]
- documentType: "proof_of_identity"
- description: "Passport copy"
```

**Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "documentId": "uuid",
    "fileName": "passport.pdf",
    "documentType": "proof_of_identity",
    "uploadDate": "2024-01-15T11:00:00Z",
    "virusScanned": true,
    "scanResult": "clean"
  }
}
```

#### GET /forms/:clientId/documents
List all documents for a client.

#### DELETE /documents/:documentId
Delete a document (admin only).

### 3.5 Audit Trail Endpoints

#### GET /audit/:clientId
Retrieve audit trail for a specific client.

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "auditRecords": [
      {
        "auditId": "uuid",
        "userId": "uuid",
        "userName": "Jane Advisor",
        "actionType": "status_change",
        "actionDetail": "Changed status from pending to in_review",
        "timestamp": "2024-01-15T12:00:00Z",
        "ipAddress": "192.168.1.10"
      }
    ]
  }
}
```

### 3.6 Error Responses

**400 Bad Request:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      {
        "field": "personalDetails.email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

**401 Unauthorized:**
```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication required"
  }
}
```

**403 Forbidden:**
```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "Insufficient permissions"
  }
}
```

**404 Not Found:**
```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "Resource not found"
  }
}
```

**429 Too Many Requests:**
```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Too many requests. Please try again later.",
    "retryAfter": 60
  }
}
```

**500 Internal Server Error:**
```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "An internal error occurred",
    "requestId": "uuid"
  }
}
```

---

## 4. Deployment Guide

### 4.1 Prerequisites

**System Requirements:**
- Ubuntu 20.04 LTS or later / RHEL 8+
- 4GB RAM minimum (8GB recommended)
- 50GB disk space
- Node.js 18.x LTS
- PostgreSQL 15.x
- Redis 7.x
- Docker 24.x (optional)
- SSL certificate (Let's Encrypt or commercial)

**Required Access:**
- SSH access to server
- Database administrator credentials
- Cloud storage account (AWS S3 or Azure Blob)
- Email service credentials (SendGrid, AWS SES, etc.)

### 4.2 Installation Steps

#### Step 1: Server Setup

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install Node.js
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs

# Install PostgreSQL
sudo apt install -y postgresql postgresql-contrib

# Install Redis
sudo apt install -y redis-server

# Install Nginx
sudo apt install -y nginx

# Install certbot for SSL
sudo apt install -y certbot python3-certbot-nginx
```

#### Step 2: Database Setup

```bash
# Switch to postgres user
sudo -u postgres psql

# Create database and user
CREATE DATABASE wealth_client_forms;
CREATE USER app_user WITH ENCRYPTED PASSWORD 'secure_password_here';
GRANT ALL PRIVILEGES ON DATABASE wealth_client_forms TO app_user;
\q

# Run schema creation
psql -U app_user -d wealth_client_forms -f schema.sql
```

#### Step 3: Application Deployment

```bash
# Create application directory
sudo mkdir -p /var/www/client-form
cd /var/www/client-form

# Clone repository (replace with your repo)
git clone https://github.com/yourfirm/client-form.git .

# Install backend dependencies
cd backend
npm install --production

# Install frontend dependencies
cd ../frontend
npm install
npm run build

# Copy environment configuration
cp .env.example .env
```

#### Step 4: Environment Configuration

Create `/var/www/client-form/backend/.env`:

```env
# Application
NODE_ENV=production
PORT=3000
APP_URL=https://forms.yourfirm.com
API_URL=https://api.yourfirm.com

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=wealth_client_forms
DB_USER=app_user
DB_PASSWORD=secure_password_here
DB_SSL=true

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=redis_password_here

# JWT
JWT_SECRET=your-super-secret-jwt-key-change-this
JWT_EXPIRY=3600
REFRESH_TOKEN_SECRET=your-refresh-token-secret
REFRESH_TOKEN_EXPIRY=604800

# Encryption
ENCRYPTION_KEY=your-32-character-encryption-key

# Email
EMAIL_PROVIDER=sendgrid
EMAIL_FROM=noreply@yourfirm.com
SENDGRID_API_KEY=your-sendgrid-api-key

# File Storage
STORAGE_PROVIDER=aws-s3
AWS_ACCESS_KEY_ID=your-access-key
AWS_SECRET_ACCESS_KEY=your-secret-key
AWS_REGION=eu-west-2
AWS_BUCKET=client-forms-documents

# Security
CORS_ORIGIN=https://forms.yourfirm.com
RATE_LIMIT_WINDOW=60000
RATE_LIMIT_MAX=100
SESSION_SECRET=your-session-secret

# Logging
LOG_LEVEL=info
LOG_FILE=/var/log/client-form/app.log

# Compliance
DATA_RETENTION_DAYS=2555
AUDIT_LOG_RETENTION_DAYS=3650
```

#### Step 5: Nginx Configuration

Create `/etc/nginx/sites-available/client-form`:

```nginx
# Rate limiting
limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
limit_req_zone $binary_remote_addr zone=form_limit:10m rate=5r/s;

# Frontend
server {
    listen 80;
    listen [::]:80;
    server_name forms.yourfirm.com;
    
    # Redirect to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name forms.yourfirm.com;

    # SSL Configuration
    ssl_certificate /etc/letsencrypt/live/forms.yourfirm.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/forms.yourfirm.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # Security Headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';" always;

    root /var/www/client-form/frontend/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api {
        limit_req zone=api_limit burst=20 nodelay;
        
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
        
        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }

    # Logging
    access_log /var/log/nginx/client-form-access.log;
    error_log /var/log/nginx/client-form-error.log;
}
```

Enable site:
```bash
sudo ln -s /etc/nginx/sites-available/client-form /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

#### Step 6: SSL Certificate

```bash
sudo certbot --nginx -d