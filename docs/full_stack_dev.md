# Full-Stack Form Application Development

**Agent:** full_stack_dev
**Job:** Client Responsive Webform

---

# Client Responsive Webform - Full-Stack Implementation

## Executive Summary

This document provides a complete implementation of a multi-step responsive web form for UK wealth management firms to collect client information in compliance with FCA regulations. The solution includes frontend UI, backend API, database schema, security features, and deployment instructions.

## Technology Stack

### Frontend
- **Framework**: React 18 with TypeScript
- **Styling**: Tailwind CSS + shadcn/ui components
- **Form Management**: React Hook Form + Zod validation
- **State Management**: React Context API
- **HTTP Client**: Axios with interceptors

### Backend
- **Runtime**: Node.js 18+
- **Framework**: Express.js with TypeScript
- **Validation**: Zod
- **Database**: PostgreSQL 14+
- **ORM**: Prisma
- **Security**: Helmet, express-rate-limit, csurf
- **Authentication**: JWT (optional for future enhancement)

## Database Schema

### Prisma Schema (`prisma/schema.prisma`)

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Client {
  id                    String   @id @default(uuid())
  formStatus            String   @default("draft") // draft, submitted, reviewed
  currentStep           Int      @default(1)
  
  // Personal Details
  title                 String?
  firstName             String
  middleName            String?
  lastName              String
  dateOfBirth           DateTime
  nationalInsuranceNumber String?
  nationality           String
  
  // Contact Information
  email                 String   @unique
  phoneNumber           String
  mobileNumber          String?
  
  // Address
  addressLine1          String
  addressLine2          String?
  city                  String
  county                String?
  postcode              String
  country               String   @default("United Kingdom")
  yearsAtAddress        Int?
  
  // Previous Address (if less than 3 years at current)
  prevAddressLine1      String?
  prevAddressLine2      String?
  prevCity              String?
  prevCounty            String?
  prevPostcode          String?
  prevCountry           String?
  
  // Employment Information
  employmentStatus      String
  occupation            String?
  employerName          String?
  employerAddress       String?
  annualIncome          Decimal? @db.Decimal(12, 2)
  
  // Fact Find - Financial Situation
  totalAssets           Decimal? @db.Decimal(12, 2)
  totalLiabilities      Decimal? @db.Decimal(12, 2)
  monthlyExpenditure    Decimal? @db.Decimal(12, 2)
  existingInvestments   Json?    // Array of investment objects
  
  // Fact Find - Investment Objectives
  investmentObjectives  String[] // growth, income, capital_preservation
  investmentTimeHorizon String?  // short_term, medium_term, long_term
  riskTolerance         String?  // low, medium, high
  investmentKnowledge   String?  // none, basic, good, extensive
  
  // Fact Find - Tax Status
  taxResidency          String
  taxIdentificationNumber String?
  isPEP                 Boolean  @default(false) // Politically Exposed Person
  pepDetails            String?
  
  // Marketing & Consent
  marketingConsent      Boolean  @default(false)
  termsAccepted         Boolean  @default(false)
  dataProcessingConsent Boolean  @default(false)
  
  // Metadata
  createdAt             DateTime @default(now())
  updatedAt             DateTime @updatedAt
  submittedAt           DateTime?
  ipAddress             String?
  userAgent             String?
  
  // Relations
  documents             Document[]
  auditLogs             AuditLog[]
  
  @@index([email])
  @@index([formStatus])
  @@index([createdAt])
}

model Document {
  id           String   @id @default(uuid())
  clientId     String
  client       Client   @relation(fields: [clientId], references: [id], onDelete: Cascade)
  
  documentType String   // id_proof, address_proof, bank_statement
  fileName     String
  fileSize     Int
  mimeType     String
  storagePath  String
  
  uploadedAt   DateTime @default(now())
  
  @@index([clientId])
}

model AuditLog {
  id        String   @id @default(uuid())
  clientId  String
  client    Client   @relation(fields: [clientId], references: [id], onDelete: Cascade)
  
  action    String   // created, updated, submitted, viewed
  step      Int?
  fieldName String?
  oldValue  String?
  newValue  String?
  ipAddress String?
  userAgent String?
  
  createdAt DateTime @default(now())
  
  @@index([clientId])
  @@index([createdAt])
}
```

## Backend Implementation

### Project Structure

```
backend/
├── src/
│   ├── config/
│   │   ├── database.ts
│   │   └── security.ts
│   ├── controllers/
│   │   ├── clientController.ts
│   │   └── documentController.ts
│   ├── middleware/
│   │   ├── errorHandler.ts
│   │   ├── validation.ts
│   │   ├── rateLimiter.ts
│   │   └── csrf.ts
│   ├── routes/
│   │   ├── clientRoutes.ts
│   │   └── documentRoutes.ts
│   ├── services/
│   │   ├── clientService.ts
│   │   ├── documentService.ts
│   │   └── auditService.ts
│   ├── validators/
│   │   └── clientValidators.ts
│   ├── types/
│   │   └── index.ts
│   └── app.ts
├── prisma/
│   ├── schema.prisma
│   └── migrations/
├── .env.example
├── package.json
└── tsconfig.json
```

### Core Backend Files

#### `src/app.ts`

```typescript
import express, { Express } from 'express';
import cors from 'cors';
import helmet from 'helmet';
import compression from 'compression';
import cookieParser from 'cookie-parser';
import clientRoutes from './routes/clientRoutes';
import documentRoutes from './routes/documentRoutes';
import { errorHandler } from './middleware/errorHandler';
import { rateLimiter } from './middleware/rateLimiter';

const app: Express = express();

// Security middleware
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https:"],
    },
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  }
}));

// CORS configuration
app.use(cors({
  origin: process.env.FRONTEND_URL || 'http://localhost:3000',
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-CSRF-Token']
}));

// Body parsing middleware
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));
app.use(cookieParser());
app.use(compression());

// Rate limiting
app.use('/api/', rateLimiter);

// Health check
app.get('/health', (req, res) => {
  res.status(200).json({ status: 'ok', timestamp: new Date().toISOString() });
});

// API routes
app.use('/api/clients', clientRoutes);
app.use('/api/documents', documentRoutes);

// Error handling
app.use(errorHandler);

export default app;
```

#### `src/validators/clientValidators.ts`

```typescript
import { z } from 'zod';

// UK postcode validation
const postcodeRegex = /^[A-Z]{1,2}\d{1,2}[A-Z]?\s?\d[A-Z]{2}$/i;

// UK phone number validation
const phoneRegex = /^(\+44\s?|0)(\d{10}|\d{4}\s?\d{6}|\d{3}\s?\d{3}\s?\d{4})$/;

// National Insurance Number validation
const ninoRegex = /^[A-Z]{2}\d{6}[A-Z]$/;

export const personalDetailsSchema = z.object({
  title: z.enum(['Mr', 'Mrs', 'Miss', 'Ms', 'Dr', 'Prof', 'Other']).optional(),
  firstName: z.string().min(1, 'First name is required').max(50),
  middleName: z.string().max(50).optional(),
  lastName: z.string().min(1, 'Last name is required').max(50),
  dateOfBirth: z.string().refine((date) => {
    const dob = new Date(date);
    const age = (Date.now() - dob.getTime()) / (365.25 * 24 * 60 * 60 * 1000);
    return age >= 18 && age <= 120;
  }, 'Must be between 18 and 120 years old'),
  nationalInsuranceNumber: z.string().regex(ninoRegex, 'Invalid NI number format').optional(),
  nationality: z.string().min(1, 'Nationality is required'),
});

export const contactDetailsSchema = z.object({
  email: z.string().email('Invalid email address'),
  phoneNumber: z.string().regex(phoneRegex, 'Invalid UK phone number'),
  mobileNumber: z.string().regex(phoneRegex, 'Invalid UK mobile number').optional(),
});

export const addressSchema = z.object({
  addressLine1: z.string().min(1, 'Address line 1 is required').max(100),
  addressLine2: z.string().max(100).optional(),
  city: z.string().min(1, 'City is required').max(50),
  county: z.string().max(50).optional(),
  postcode: z.string().regex(postcodeRegex, 'Invalid UK postcode'),
  country: z.string().default('United Kingdom'),
  yearsAtAddress: z.number().min(0).max(100).optional(),
});

export const previousAddressSchema = z.object({
  prevAddressLine1: z.string().max(100).optional(),
  prevAddressLine2: z.string().max(100).optional(),
  prevCity: z.string().max(50).optional(),
  prevCounty: z.string().max(50).optional(),
  prevPostcode: z.string().regex(postcodeRegex, 'Invalid UK postcode').optional(),
  prevCountry: z.string().optional(),
}).optional();

export const employmentSchema = z.object({
  employmentStatus: z.enum([
    'employed',
    'self_employed',
    'unemployed',
    'retired',
    'student',
    'homemaker'
  ]),
  occupation: z.string().max(100).optional(),
  employerName: z.string().max(100).optional(),
  employerAddress: z.string().max(200).optional(),
  annualIncome: z.number().min(0).optional(),
});

export const financialSituationSchema = z.object({
  totalAssets: z.number().min(0).optional(),
  totalLiabilities: z.number().min(0).optional(),
  monthlyExpenditure: z.number().min(0).optional(),
  existingInvestments: z.array(z.object({
    type: z.string(),
    provider: z.string(),
    value: z.number(),
    description: z.string().optional(),
  })).optional(),
});

export const investmentObjectivesSchema = z.object({
  investmentObjectives: z.array(z.enum(['growth', 'income', 'capital_preservation', 'tax_efficiency'])),
  investmentTimeHorizon: z.enum(['short_term', 'medium_term', 'long_term']).optional(),
  riskTolerance: z.enum(['low', 'medium', 'high']).optional(),
  investmentKnowledge: z.enum(['none', 'basic', 'good', 'extensive']).optional(),
});

export const taxStatusSchema = z.object({
  taxResidency: z.string().min(1, 'Tax residency is required'),
  taxIdentificationNumber: z.string().optional(),
  isPEP: z.boolean().default(false),
  pepDetails: z.string().max(500).optional(),
});

export const consentSchema = z.object({
  marketingConsent: z.boolean().default(false),
  termsAccepted: z.boolean().refine(val => val === true, 'You must accept the terms and conditions'),
  dataProcessingConsent: z.boolean().refine(val => val === true, 'You must consent to data processing'),
});

export const createClientSchema = z.object({
  step: z.number().min(1).max(7),
  data: z.object({
    ...personalDetailsSchema.shape,
    ...contactDetailsSchema.shape,
    ...addressSchema.shape,
    ...previousAddressSchema.shape,
    ...employmentSchema.shape,
    ...financialSituationSchema.shape,
    ...investmentObjectivesSchema.shape,
    ...taxStatusSchema.shape,
    ...consentSchema.shape,
  }).partial(),
});

export const updateClientSchema = createClientSchema;
```

#### `src/services/clientService.ts`

```typescript
import { PrismaClient, Client, Prisma } from '@prisma/client';
import { auditService } from './auditService';

const prisma = new PrismaClient();

export class ClientService {
  async createClient(data: Partial<Client>, metadata: { ipAddress?: string; userAgent?: string }) {
    try {
      const client = await prisma.client.create({
        data: {
          ...data,
          currentStep: 1,
          formStatus: 'draft',
          ipAddress: metadata.ipAddress,
          userAgent: metadata.userAgent,
        } as Prisma.ClientCreateInput,
      });

      await auditService.log({
        clientId: client.id,
        action: 'created',
        step: 1,
        ipAddress: metadata.ipAddress,
        userAgent: metadata.userAgent,
      });

      return client;
    } catch (error) {
      throw new Error('Failed to create client record');
    }
  }

  async updateClient(
    id: string,
    data: Partial<Client>,
    step: number,
    metadata: { ipAddress?: string; userAgent?: string }
  ) {
    try {
      const existingClient = await prisma.client.findUnique({ where: { id } });
      
      if (!existingClient) {
        throw new Error('Client not found');
      }

      const client = await prisma.client.update({
        where: { id },
        data: {
          ...data,
          currentStep: Math.max(step, existingClient.currentStep),
          updatedAt: new Date(),
        } as Prisma.ClientUpdateInput,
      });

      await auditService.log({
        clientId: client.id,
        action: 'updated',
        step,
        ipAddress: metadata.ipAddress,
        userAgent: metadata.userAgent,
      });

      return client;
    } catch (error) {
      throw new Error('Failed to update client record');
    }
  }

  async getClient(id: string) {
    try {
      const client = await prisma.client.findUnique({
        where: { id },
        include: {
          documents: true,
        },
      });

      if (!client) {
        throw new Error('Client not found');
      }

      return client;
    } catch (error) {
      throw new Error('Failed to retrieve client record');
    }
  }

  async submitClient(id: string, metadata: { ipAddress?: string; userAgent?: string }) {
    try {
      const client = await prisma.client.update({
        where: { id },
        data: {
          formStatus: 'submitted',
          submittedAt: new Date(),
        },
      });

      await auditService.log({
        clientId: client.id,
        action: 'submitted',
        ipAddress: metadata.ipAddress,
        userAgent: metadata.userAgent,
      });

      return client;
    } catch (error) {
      throw new Error('Failed to submit client record');
    }
  }

  async getClientByEmail(email: string) {
    return prisma.client.findUnique({
      where: { email },
    });
  }

  async deleteClient(id: string) {
    try {
      await prisma.client.delete({
        where: { id },
      });
    } catch (error) {
      throw new Error('Failed to delete client record');
    }
  }
}

export const clientService = new ClientService();
```

#### `src/services/auditService.ts`

```typescript
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

interface AuditLogData {
  clientId: string;
  action: string;
  step?: number;
  fieldName?: string;
  oldValue?: string;
  newValue?: string;
  ipAddress?: string;
  userAgent?: string;
}

export class AuditService {
  async log(data: AuditLogData) {
    try {
      await prisma.auditLog.create({
        data: {
          ...data,
          createdAt: new Date(),
        },
      });
    } catch (error) {
      console.error('Failed to create audit log:', error);
      // Don't throw error - audit logging should not break main functionality
    }
  }

  async getClientLogs(clientId: string) {
    return prisma.auditLog.findMany({
      where: { clientId },
      orderBy: { createdAt: 'desc' },
    });
  }
}

export const auditService = new AuditService();
```

#### `src/controllers/clientController.ts`

```typescript
import { Request, Response, NextFunction } from 'express';
import { clientService } from '../services/clientService';
import { createClientSchema, updateClientSchema } from '../validators/clientValidators';
import { z } from 'zod';

export class ClientController {
  async createClient(req: Request, res: Response, next: NextFunction) {
    try {
      const validatedData = createClientSchema.parse(req.body);
      
      // Check if email already exists
      if (validatedData.data.email) {
        const existing = await clientService.getClientByEmail(validatedData.data.email);
        if (existing) {
          return res.status(400).json({
            success: false,
            message: 'A client with this email already exists',
          });
        }
      }

      const metadata = {
        ipAddress: req.ip,
        userAgent: req.get('user-agent'),
      };

      const client = await clientService.createClient(validatedData.data, metadata);

      res.status(201).json({
        success: true,
        data: {
          id: client.id,
          currentStep: client.currentStep,
          formStatus: client.formStatus,
        },
      });
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({
          success: false,
          message: 'Validation error',
          errors: error.errors,
        });
      }
      next(error);
    }
  }

  async updateClient(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      const validatedData = updateClientSchema.parse(req.body);

      const metadata = {
        ipAddress: req.ip,
        userAgent: req.get('user-agent'),
      };

      const client = await clientService.updateClient(
        id,
        validatedData.data,
        validatedData.step,
        metadata
      );

      res.status(200).json({
        success: true,
        data: {
          id: client.id,
          currentStep: client.currentStep,
          formStatus: client.formStatus,
        },
      });
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({
          success: false,
          message: 'Validation error',
          errors: error.errors,
        });
      }
      next(error);
    }
  }

  async getClient(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      const client = await clientService.getClient(id);

      res.status(200).json({
        success: true,
        data: client,
      });
    } catch (error) {
      next(error);
    }
  }

  async submitClient(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;

      const metadata = {
        ipAddress: req.ip,
        userAgent: req.get('user-agent'),
      };

      const client = await clientService.submitClient(id, metadata);

      res.status(200).json({
        success: true,
        message: 'Form submitted successfully',
        data: {
          id: client.id,
          formStatus: client.formStatus,
          submittedAt: client.submittedAt,
        },
      });
    } catch (error) {
      next(error);
    }
  }

  async autoSave(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      const { step, data } = req.body;

      const metadata = {
        ipAddress: req.ip,
        userAgent: req.get('user-agent'),
      };

      await clientService.updateClient(id, data, step, metadata);

      res.status(200).json({
        success: true,
        message: 'Progress saved',
      });
    } catch (error) {
      next(error);
    }
  }
}

export const clientController = new ClientController();
```

#### `src/routes/clientRoutes.ts`

```typescript
import { Router } from 'express';
import { clientController } from '../controllers/clientController';
import { csrfProtection } from '../middleware/csrf';

const router = Router();

// Apply CSRF protection to all routes
router.use(csrfProtection);

router.post('/', clientController.createClient.bind(clientController));
router.get('/:id', clientController.getClient.bind(clientController));
router.put('/:id', clientController.updateClient.bind(clientController));
router.post('/:id/submit', clientController.submitClient.bind(clientController));
router.post('/:id/autosave', clientController.autoSave.bind(clientController));

export default router;
```

#### `src/middleware/rateLimiter.ts`

```typescript
import rateLimit from 'express-rate-limit';

export const rateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP, please try again later.',
  standardHeaders: true,
  legacyHeaders: false,
});

export const strictRateLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 5, // Limit each IP to 5 submissions per hour
  message: 'Too many form submissions, please try again later.',
});
```

#### `src/middleware/csrf.ts`

```typescript
import csrf from 'csurf';
import { Request, Response, NextFunction } from 'express';

export const csrfProtection = csrf({
  cookie: {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'strict',
  },
});

export const csrfErrorHandler = (err: any, req: Request, res: Response, next: NextFunction) => {
  if (err.code !== 'EBADCSRFTOKEN') return next(err);
  
  res.status(403).json({
    success: false,
    message: 'Invalid CSRF token',
  });
};
```

#### `src/middleware/errorHandler.ts`

```typescript
import { Request, Response, NextFunction } from 'express';

export const errorHandler = (
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  console.error('Error:', err);

  const statusCode = res.statusCode !== 200 ? res.statusCode : 500;

  res.status(statusCode).json({
    success: false,
    message: err.message || 'Internal server error',
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  });
};
```

#### `.env.example`

```env
# Database
DATABASE_URL="postgresql://user:password@localhost:5432/wealth_management"

# Server
PORT=5000
NODE_ENV=development

# Frontend
FRONTEND_URL=http://localhost:3000

# Security
SESSION_SECRET=your-super-secret-session-key-change-in-production
CSRF_SECRET=your-csrf-secret-key-change-in-production

# File Upload
MAX_FILE_SIZE=10485760
UPLOAD_DIR=./uploads
```

## Frontend Implementation

### Project Structure

```
frontend/
├── src/
│   ├── components/
│   │   ├── form/
│   │   │   ├── FormProgress.tsx
│   │   │   ├── FormNavigation.tsx
│   │   │   ├── FormField.tsx
│   │   │   └── AutoSave.tsx
│   │   ├── steps/
│   │   │   ├── PersonalDetails.tsx
│   │   │   ├── ContactDetails.tsx
│   │   │   ├── AddressDetails.tsx
│   │   │   ├── EmploymentDetails.tsx
│   │   │   ├── FinancialSituation.tsx
│   │   │   ├── InvestmentObjectives.tsx
│   │   │   └── ReviewSubmit.tsx
│   │   ├── ui/
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── select.tsx
│   │   │   ├── checkbox.tsx
│   │   │   ├── radio.tsx
│   │   │   └── card.tsx
│   │   └── layout/
│   │       ├── Header.tsx
│   │       └── Footer.tsx
│   ├── context/
│   │   └── FormContext.tsx
│   ├── hooks/
│   │   ├── useFormData.ts
│   │   ├── useAutoSave.ts
│   │   └── useFormValidation.ts
│   ├── services/
│   │   └── api.ts
│   ├── types/
│   │   └── form.types.ts
│   ├── utils/
│   │   ├── validation.ts
│   │   └── formatters.ts
│   ├── App.tsx
│   └── main.tsx
├── public/
├── package.json
└── tsconfig.json
```

### Core Frontend Files

#### `src/types/form.types.ts`

```typescript
export interface PersonalDetails {
  title?: string;
  firstName: string;
  middleName?: string;
  lastName: string;
  dateOfBirth: string;
  nationalInsuranceNumber?: string;
  nationality: string;
}

export interface ContactDetails {
  email: string;
  phoneNumber: string;
  mobileNumber?: string;
}

export interface AddressDetails {
  addressLine1: string;
  addressLine2?: string;
  city: string;
  county?: string;
  postcode: string;
  country: string;
  yearsAtAddress?: number;
}

export interface PreviousAddress {
  prevAddressLine1?: string;
  prevAddressLine2?: string;
  prevCity?: string;
  prevCounty?: string;
  prevPostcode?: string;
  prevCountry?: string;
}

export interface EmploymentDetails {
  employmentStatus: string;
  occupation?: string;
  employerName?: string;
  employerAddress?: string;
  annualIncome?: number;
}

export interface Investment {
  type: string;
  provider: string;
  value: number;
  description?: string;
}

export interface FinancialSituation {
  totalAssets?: number;
  totalLiabilities?: number;
  monthlyExpenditure?: number;
  existingInvestments?: Investment[];
}

export interface InvestmentObjectives {
  investmentObjectives: string[];
  investmentTimeHorizon?: string;
  riskTolerance?: string;
  investmentKnowledge?: string;
}

export interface TaxStatus {
  taxResidency: string;
  taxIdentificationNumber?: string;
  isPEP: boolean;
  pepDetails?: string;
}

export interface Consent {
  marketingConsent: boolean;
  termsAccepted: boolean;
  dataProcessingConsent: boolean;
}

export interface FormData extends
  PersonalDetails,
  ContactDetails,
  AddressDetails,
  PreviousAddress,
  EmploymentDetails,
  FinancialSituation,
  InvestmentObjectives,
  TaxStatus,
  Consent {
  id?: string;
  currentStep: number;
  formStatus: 'draft' | 'submitted' | 'reviewed';
}

export interface FormStep {
  id: number;
  title: string;
  description: string;
  fields: string[];
}
```

#### `src/context/FormContext.tsx`

```typescript
import React, { createContext, useContext, useState, useEffect, ReactNode } from 'react';
import { FormData } from '../types/form.types';
import { apiService } from '../services/api';

interface FormContextType {
  formData: Partial<FormData>;
  currentStep: number;
  updateFormData: (data: Partial<FormData>) => void;
  setCurrentStep: (step: number) => void;
  saveProgress: () => Promise<void>;
  submitForm: () => Promise<void>;
  loading: boolean;
  error: string | null;
}

const FormContext = createContext<FormContextType | undefined>(undefined);

export const FormProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [formData, setFormData] = useState<Partial<FormData>>({
    currentStep: 1,
    formStatus: 'draft',
    country: 'United Kingdom',
    isPEP: false,
    marketingConsent: false,
    termsAccepted: false,
    dataProcessingConsent: false,
  });
  const [currentStep, setCurrentStep] = useState(1);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);