# Responsive Frontend Form Implementation

**Agent:** frontend_dev
**Job:** Client Responsive Webform

---

# Client Responsive Webform - Frontend Implementation

## Executive Summary

This document outlines the complete frontend implementation of a multi-step, accessible, and FCA-compliant web form for UK wealth management client onboarding. The solution uses React with TypeScript, leveraging modern best practices for form handling, validation, and state management.

## Technology Stack

- **Framework**: React 18 with TypeScript
- **Form Management**: React Hook Form
- **Validation**: Zod schema validation
- **State Management**: Zustand (for form persistence)
- **Styling**: Tailwind CSS with custom components
- **API Integration**: Axios with retry logic
- **Postcode Lookup**: Getaddress.io API integration
- **Testing**: Jest + React Testing Library
- **Accessibility**: ARIA attributes, keyboard navigation, screen reader support

## Project Structure

```
src/
├── components/
│   ├── form/
│   │   ├── FormStep.tsx
│   │   ├── ProgressIndicator.tsx
│   │   ├── FormNavigation.tsx
│   │   └── fields/
│   │       ├── TextInput.tsx
│   │       ├── PostcodeLookup.tsx
│   │       ├── PhoneInput.tsx
│   │       ├── EmailInput.tsx
│   │       ├── DateInput.tsx
│   │       ├── SelectInput.tsx
│   │       └── RadioGroup.tsx
│   ├── layout/
│   │   ├── FormLayout.tsx
│   │   └── ErrorBoundary.tsx
│   └── ui/
│       ├── Button.tsx
│       ├── LoadingSpinner.tsx
│       └── ErrorMessage.tsx
├── hooks/
│   ├── useFormPersistence.ts
│   ├── usePostcodeLookup.ts
│   └── useFormNavigation.ts
├── schemas/
│   ├── personalDetails.schema.ts
│   ├── contactDetails.schema.ts
│   ├── financialDetails.schema.ts
│   └── factFind.schema.ts
├── store/
│   └── formStore.ts
├── services/
│   ├── api.service.ts
│   └── postcode.service.ts
├── types/
│   └── form.types.ts
├── utils/
│   ├── validation.utils.ts
│   └── formatting.utils.ts
└── App.tsx
```

## Core Implementation

### 1. Type Definitions

```typescript
// src/types/form.types.ts

export interface PersonalDetails {
  title: string;
  firstName: string;
  middleName?: string;
  lastName: string;
  dateOfBirth: string;
  nationalInsurance?: string;
  nationality: string;
  maritalStatus: string;
}

export interface ContactDetails {
  addressLine1: string;
  addressLine2?: string;
  city: string;
  county?: string;
  postcode: string;
  country: string;
  phoneNumber: string;
  mobileNumber?: string;
  emailAddress: string;
  preferredContactMethod: 'email' | 'phone' | 'post';
}

export interface FinancialDetails {
  employmentStatus: string;
  occupation?: string;
  employer?: string;
  annualIncome: string;
  sourceOfWealth: string[];
  investmentExperience: string;
  riskTolerance: string;
}

export interface FactFindDetails {
  investmentObjectives: string[];
  investmentTimeline: string;
  existingInvestments?: string;
  pensionArrangements?: string;
  dependents: number;
  hasWill: boolean;
  hasPowerOfAttorney: boolean;
}

export interface ClientFormData {
  personalDetails: PersonalDetails;
  contactDetails: ContactDetails;
  financialDetails: FinancialDetails;
  factFind: FactFindDetails;
}

export type FormStep = 'personal' | 'contact' | 'financial' | 'factFind' | 'review';

export interface FormState {
  currentStep: FormStep;
  completedSteps: FormStep[];
  formData: Partial<ClientFormData>;
  lastSaved: string | null;
}
```

### 2. Validation Schemas

```typescript
// src/schemas/personalDetails.schema.ts

import { z } from 'zod';

export const personalDetailsSchema = z.object({
  title: z.enum(['Mr', 'Mrs', 'Miss', 'Ms', 'Dr', 'Other'], {
    errorMap: () => ({ message: 'Please select a title' }),
  }),
  firstName: z
    .string()
    .min(1, 'First name is required')
    .max(50, 'First name must be less than 50 characters')
    .regex(/^[a-zA-Z\s'-]+$/, 'First name contains invalid characters'),
  middleName: z
    .string()
    .max(50, 'Middle name must be less than 50 characters')
    .regex(/^[a-zA-Z\s'-]*$/, 'Middle name contains invalid characters')
    .optional(),
  lastName: z
    .string()
    .min(1, 'Last name is required')
    .max(50, 'Last name must be less than 50 characters')
    .regex(/^[a-zA-Z\s'-]+$/, 'Last name contains invalid characters'),
  dateOfBirth: z
    .string()
    .min(1, 'Date of birth is required')
    .refine(
      (date) => {
        const dob = new Date(date);
        const age = new Date().getFullYear() - dob.getFullYear();
        return age >= 18 && age <= 120;
      },
      { message: 'Client must be between 18 and 120 years old' }
    ),
  nationalInsurance: z
    .string()
    .regex(/^[A-Z]{2}[0-9]{6}[A-D]?$/i, 'Invalid National Insurance number format')
    .optional()
    .or(z.literal('')),
  nationality: z.string().min(1, 'Nationality is required'),
  maritalStatus: z.enum(['single', 'married', 'divorced', 'widowed', 'civil_partnership'], {
    errorMap: () => ({ message: 'Please select marital status' }),
  }),
});

export type PersonalDetailsFormData = z.infer<typeof personalDetailsSchema>;
```

```typescript
// src/schemas/contactDetails.schema.ts

import { z } from 'zod';

const UK_POSTCODE_REGEX = /^[A-Z]{1,2}[0-9][A-Z0-9]? ?[0-9][A-Z]{2}$/i;
const UK_PHONE_REGEX = /^(\+44\s?7\d{3}|\(?07\d{3}\)?)\s?\d{3}\s?\d{3}$/;

export const contactDetailsSchema = z.object({
  addressLine1: z.string().min(1, 'Address line 1 is required').max(100),
  addressLine2: z.string().max(100).optional(),
  city: z.string().min(1, 'City is required').max(50),
  county: z.string().max(50).optional(),
  postcode: z
    .string()
    .min(1, 'Postcode is required')
    .regex(UK_POSTCODE_REGEX, 'Invalid UK postcode format'),
  country: z.string().default('United Kingdom'),
  phoneNumber: z
    .string()
    .min(1, 'Phone number is required')
    .regex(UK_PHONE_REGEX, 'Invalid UK phone number format'),
  mobileNumber: z
    .string()
    .regex(UK_PHONE_REGEX, 'Invalid UK mobile number format')
    .optional()
    .or(z.literal('')),
  emailAddress: z
    .string()
    .min(1, 'Email address is required')
    .email('Invalid email address format')
    .toLowerCase(),
  preferredContactMethod: z.enum(['email', 'phone', 'post']),
});

export type ContactDetailsFormData = z.infer<typeof contactDetailsSchema>;
```

```typescript
// src/schemas/financialDetails.schema.ts

import { z } from 'zod';

export const financialDetailsSchema = z.object({
  employmentStatus: z.enum([
    'employed',
    'self_employed',
    'unemployed',
    'retired',
    'student',
    'other',
  ]),
  occupation: z.string().max(100).optional(),
  employer: z.string().max(100).optional(),
  annualIncome: z.enum([
    'under_25k',
    '25k_50k',
    '50k_100k',
    '100k_250k',
    '250k_500k',
    'over_500k',
  ]),
  sourceOfWealth: z
    .array(z.string())
    .min(1, 'Please select at least one source of wealth'),
  investmentExperience: z.enum(['none', 'limited', 'moderate', 'extensive']),
  riskTolerance: z.enum(['low', 'medium', 'high']),
});

export type FinancialDetailsFormData = z.infer<typeof financialDetailsSchema>;
```

### 3. Form Store (State Management)

```typescript
// src/store/formStore.ts

import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import { FormState, FormStep, ClientFormData } from '../types/form.types';

interface FormStore extends FormState {
  updateFormData: (step: keyof ClientFormData, data: any) => void;
  setCurrentStep: (step: FormStep) => void;
  markStepComplete: (step: FormStep) => void;
  resetForm: () => void;
  saveProgress: () => void;
}

const FORM_STEPS: FormStep[] = ['personal', 'contact', 'financial', 'factFind', 'review'];

export const useFormStore = create<FormStore>()(
  persist(
    (set, get) => ({
      currentStep: 'personal',
      completedSteps: [],
      formData: {},
      lastSaved: null,

      updateFormData: (step, data) => {
        set((state) => ({
          formData: {
            ...state.formData,
            [step]: data,
          },
        }));
        get().saveProgress();
      },

      setCurrentStep: (step) => {
        set({ currentStep: step });
      },

      markStepComplete: (step) => {
        set((state) => {
          const completedSteps = state.completedSteps.includes(step)
            ? state.completedSteps
            : [...state.completedSteps, step];
          return { completedSteps };
        });
      },

      resetForm: () => {
        set({
          currentStep: 'personal',
          completedSteps: [],
          formData: {},
          lastSaved: null,
        });
        localStorage.removeItem('client-form-storage');
      },

      saveProgress: () => {
        set({ lastSaved: new Date().toISOString() });
      },
    }),
    {
      name: 'client-form-storage',
      version: 1,
    }
  )
);
```

### 4. Postcode Lookup Service

```typescript
// src/services/postcode.service.ts

import axios from 'axios';

interface Address {
  line1: string;
  line2: string;
  city: string;
  county: string;
}

export class PostcodeService {
  private apiKey: string;
  private baseUrl = 'https://api.getaddress.io';

  constructor(apiKey: string) {
    this.apiKey = apiKey;
  }

  async lookup(postcode: string): Promise<Address[]> {
    try {
      const cleanPostcode = postcode.replace(/\s/g, '');
      const response = await axios.get(
        `${this.baseUrl}/find/${cleanPostcode}`,
        {
          params: { 'api-key': this.apiKey },
          timeout: 5000,
        }
      );

      if (response.data.addresses) {
        return response.data.addresses.map((addr: string[]) => ({
          line1: addr[0] || '',
          line2: addr[1] || '',
          city: addr[5] || '',
          county: addr[6] || '',
        }));
      }

      return [];
    } catch (error) {
      if (axios.isAxiosError(error)) {
        if (error.response?.status === 404) {
          throw new Error('Postcode not found');
        }
        throw new Error('Unable to lookup postcode. Please enter manually.');
      }
      throw error;
    }
  }
}

export const postcodeService = new PostcodeService(
  process.env.REACT_APP_POSTCODE_API_KEY || ''
);
```

### 5. Reusable Form Components

```typescript
// src/components/form/fields/TextInput.tsx

import React from 'react';
import { UseFormRegister, FieldError } from 'react-hook-form';

interface TextInputProps {
  name: string;
  label: string;
  register: UseFormRegister<any>;
  error?: FieldError;
  required?: boolean;
  placeholder?: string;
  type?: 'text' | 'email' | 'tel' | 'date';
  autoComplete?: string;
  hint?: string;
  maxLength?: number;
}

export const TextInput: React.FC<TextInputProps> = ({
  name,
  label,
  register,
  error,
  required = false,
  placeholder,
  type = 'text',
  autoComplete,
  hint,
  maxLength,
}) => {
  const inputId = `input-${name}`;
  const errorId = `error-${name}`;
  const hintId = `hint-${name}`;

  return (
    <div className="form-group mb-6">
      <label
        htmlFor={inputId}
        className="block text-sm font-medium text-gray-700 mb-2"
      >
        {label}
        {required && <span className="text-red-600 ml-1" aria-label="required">*</span>}
      </label>
      
      {hint && (
        <p id={hintId} className="text-sm text-gray-600 mb-2">
          {hint}
        </p>
      )}

      <input
        id={inputId}
        type={type}
        {...register(name)}
        className={`
          w-full px-4 py-2 border rounded-md shadow-sm
          focus:ring-2 focus:ring-blue-500 focus:border-blue-500
          ${error ? 'border-red-500' : 'border-gray-300'}
          disabled:bg-gray-100 disabled:cursor-not-allowed
        `}
        placeholder={placeholder}
        autoComplete={autoComplete}
        maxLength={maxLength}
        aria-invalid={error ? 'true' : 'false'}
        aria-describedby={`${error ? errorId : ''} ${hint ? hintId : ''}`.trim()}
      />

      {error && (
        <p
          id={errorId}
          className="mt-2 text-sm text-red-600"
          role="alert"
        >
          {error.message}
        </p>
      )}
    </div>
  );
};
```

```typescript
// src/components/form/fields/PostcodeLookup.tsx

import React, { useState } from 'react';
import { UseFormSetValue, UseFormRegister, FieldError } from 'react-hook-form';
import { postcodeService } from '../../../services/postcode.service';
import { LoadingSpinner } from '../../ui/LoadingSpinner';

interface PostcodeLookupProps {
  register: UseFormRegister<any>;
  setValue: UseFormSetValue<any>;
  errors: {
    postcode?: FieldError;
    addressLine1?: FieldError;
    city?: FieldError;
  };
}

export const PostcodeLookup: React.FC<PostcodeLookupProps> = ({
  register,
  setValue,
  errors,
}) => {
  const [isLookingUp, setIsLookingUp] = useState(false);
  const [addresses, setAddresses] = useState<any[]>([]);
  const [lookupError, setLookupError] = useState<string | null>(null);
  const [showManual, setShowManual] = useState(false);

  const handlePostcodeLookup = async (postcode: string) => {
    if (!postcode || postcode.length < 5) return;

    setIsLookingUp(true);
    setLookupError(null);

    try {
      const results = await postcodeService.lookup(postcode);
      setAddresses(results);
      if (results.length === 0) {
        setLookupError('No addresses found for this postcode');
        setShowManual(true);
      }
    } catch (error) {
      setLookupError((error as Error).message);
      setShowManual(true);
    } finally {
      setIsLookingUp(false);
    }
  };

  const handleAddressSelect = (address: any) => {
    setValue('addressLine1', address.line1, { shouldValidate: true });
    setValue('addressLine2', address.line2, { shouldValidate: true });
    setValue('city', address.city, { shouldValidate: true });
    setValue('county', address.county, { shouldValidate: true });
    setAddresses([]);
  };

  return (
    <div className="space-y-4">
      <div className="form-group">
        <label htmlFor="postcode" className="block text-sm font-medium text-gray-700 mb-2">
          Postcode <span className="text-red-600" aria-label="required">*</span>
        </label>
        <div className="flex gap-2">
          <input
            id="postcode"
            type="text"
            {...register('postcode')}
            className={`
              flex-1 px-4 py-2 border rounded-md shadow-sm
              focus:ring-2 focus:ring-blue-500 focus:border-blue-500
              ${errors.postcode ? 'border-red-500' : 'border-gray-300'}
            `}
            placeholder="SW1A 1AA"
            autoComplete="postal-code"
            aria-invalid={errors.postcode ? 'true' : 'false'}
            aria-describedby={errors.postcode ? 'error-postcode' : undefined}
          />
          <button
            type="button"
            onClick={(e) => {
              const input = document.getElementById('postcode') as HTMLInputElement;
              handlePostcodeLookup(input.value);
            }}
            disabled={isLookingUp}
            className="px-4 py-2 bg-blue-600 text-white rounded-md hover:bg-blue-700 
                     disabled:bg-gray-400 disabled:cursor-not-allowed flex items-center"
            aria-label="Find address by postcode"
          >
            {isLookingUp ? (
              <>
                <LoadingSpinner size="sm" />
                <span className="ml-2">Looking up...</span>
              </>
            ) : (
              'Find Address'
            )}
          </button>
        </div>
        {errors.postcode && (
          <p id="error-postcode" className="mt-2 text-sm text-red-600" role="alert">
            {errors.postcode.message}
          </p>
        )}
      </div>

      {lookupError && (
        <div className="p-3 bg-yellow-50 border border-yellow-200 rounded-md" role="alert">
          <p className="text-sm text-yellow-800">{lookupError}</p>
        </div>
      )}

      {addresses.length > 0 && (
        <div className="border border-gray-300 rounded-md max-h-60 overflow-y-auto">
          <ul role="listbox" aria-label="Address options">
            {addresses.map((address, index) => (
              <li key={index}>
                <button
                  type="button"
                  onClick={() => handleAddressSelect(address)}
                  className="w-full text-left px-4 py-3 hover:bg-blue-50 border-b 
                           border-gray-200 last:border-0 focus:bg-blue-100"
                  role="option"
                  aria-selected="false"
                >
                  <div className="text-sm">
                    <div className="font-medium">{address.line1}</div>
                    {address.line2 && <div className="text-gray-600">{address.line2}</div>}
                    <div className="text-gray-600">{address.city}, {address.county}</div>
                  </div>
                </button>
              </li>
            ))}
          </ul>
        </div>
      )}

      {(showManual || addresses.length === 0) && (
        <>
          <div className="form-group">
            <label htmlFor="addressLine1" className="block text-sm font-medium text-gray-700 mb-2">
              Address Line 1 <span className="text-red-600" aria-label="required">*</span>
            </label>
            <input
              id="addressLine1"
              type="text"
              {...register('addressLine1')}
              className={`
                w-full px-4 py-2 border rounded-md shadow-sm
                focus:ring-2 focus:ring-blue-500 focus:border-blue-500
                ${errors.addressLine1 ? 'border-red-500' : 'border-gray-300'}
              `}
              autoComplete="address-line1"
              aria-invalid={errors.addressLine1 ? 'true' : 'false'}
            />
            {errors.addressLine1 && (
              <p className="mt-2 text-sm text-red-600" role="alert">
                {errors.addressLine1.message}
              </p>
            )}
          </div>

          <div className="form-group">
            <label htmlFor="addressLine2" className="block text-sm font-medium text-gray-700 mb-2">
              Address Line 2 (Optional)
            </label>
            <input
              id="addressLine2"
              type="text"
              {...register('addressLine2')}
              className="w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm
                       focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
              autoComplete="address-line2"
            />
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div className="form-group">
              <label htmlFor="city" className="block text-sm font-medium text-gray-700 mb-2">
                Town/City <span className="text-red-600" aria-label="required">*</span>
              </label>
              <input
                id="city"
                type="text"
                {...register('city')}
                className={`
                  w-full px-4 py-2 border rounded-md shadow-sm
                  focus:ring-2 focus:ring-blue-500 focus:border-blue-500
                  ${errors.city ? 'border-red-500' : 'border-gray-300'}
                `}
                autoComplete="address-level2"
                aria-invalid={errors.city ? 'true' : 'false'}
              />
              {errors.city && (
                <p className="mt-2 text-sm text-red-600" role="alert">
                  {errors.city.message}
                </p>
              )}
            </div>

            <div className="form-group">
              <label htmlFor="county" className="block text-sm font-medium text-gray-700 mb-2">
                County (Optional)
              </label>
              <input
                id="county"
                type="text"
                {...register('county')}
                className="w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm
                         focus:ring-2 focus:ring-blue-500 focus:border-blue-500"
                autoComplete="address-level1"
              />
            </div>
          </div>
        </>
      )}

      {!showManual && addresses.length === 0 && (
        <button
          type="button"
          onClick={() => setShowManual(true)}
          className="text-sm text-blue-600 hover:text-blue-800 underline"
        >
          Enter address manually
        </button>
      )}
    </div>
  );
};
```

```typescript
// src/components/form/ProgressIndicator.tsx

import React from 'react';
import { FormStep } from '../../types/form.types';

interface ProgressIndicatorProps {
  currentStep: FormStep;
  completedSteps: FormStep[];
}

const STEPS = [
  { key: 'personal', label: 'Personal Details', number: 1 },
  { key: 'contact', label: 'Contact Details', number: 2 },
  { key: 'financial', label: 'Financial Information', number: 3 },
  { key: 'factFind', label: 'Fact Find', number: 4 },
  { key: 'review', label: 'Review & Submit', number: 5 },
];

export const ProgressIndicator: React.FC<ProgressIndicatorProps> = ({
  currentStep,
  completedSteps,
}) => {
  const currentStepIndex = STEPS.findIndex((step) => step.key === currentStep);

  return (
    <nav aria-label="Form progress" className="mb-8">
      <ol className="flex items-center justify-between md:justify-center md:space-x-8">
        {STEPS.map((step, index) => {
          const isCompleted = completedSteps.includes(step.key as FormStep);
          const isCurrent = step.key === currentStep;
          const isPast = index < currentStepIndex;

          return (
            <li key={step.key} className="flex items-center">
              <div className="flex flex-col items-center">
                <div
                  className={`
                    flex items-center justify-center w-10 h-10 rounded-full border-2
                    transition-colors duration-200
                    ${
                      isCurrent
                        ? 'border-blue-600 bg-blue-600 text-white'
                        : isCompleted || isPast
                        ? 'border-green-600 bg-green-600 text-white'
                        : 'border-gray-300 bg-white text-gray-500'
                    }
                  `}
                  aria-current={isCurrent ? 'step' : undefined}
                >
                  {isCompleted || isPast ? (
                    <svg
                      className="w-6 h-6"
                      fill="currentColor"
                      viewBox="0 0 20 20"
                      aria-hidden="true"
                    >
                      <path
                        fillRule="evenodd"
                        d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                        clipRule="evenodd"
                      />
                    </svg>
                  ) : (
                    <span className="font-semibold">{step.number}</span>
                  )}
                </div>
                <span
                  className={`
                    mt-2 text-xs md:text-sm font-medium text-center
                    ${isCurrent ? 'text-blue-600' : 'text-gray-500'}
                  `}
                >
                  {step.label}
                </span>
              </div>
              {index < STEPS.length - 1 && (
                <div
                  className={`
                    hidden md:block w-16 h-0.5 mx-4
                    ${isPast || isCompleted ? 'bg-green-600' : 'bg-gray-300'}
                  `}
                  aria-hidden="true"
                />
              )}
            </li>
          );
        })}
      </ol>
    </nav>
  );
};
```

### 6. Form Step Components

```typescript
// src/components/form/steps/PersonalDetailsStep.tsx

import React from 'react';
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { personalDetailsSchema, PersonalDetailsFormData } from '../../../schemas/personalDetails.schema';
import { TextInput } from '../fields/TextInput';
import { SelectInput } from '../fields/SelectInput';
import { useFormStore } from '../../../store/formStore';

interface PersonalDetailsStepProps {
  onNext: () => void;
}

export const PersonalDetailsStep: React.FC<PersonalDetailsStepProps> = ({ onNext }) => {
  const { formData, updateFormData, markStepComplete } = useFormStore();

  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<PersonalDetailsFormData>({
    resolver: zodResolver(personalDetailsSchema),
    defaultValues: formData.personalDetails || {},
  });

  const onSubmit = (data: PersonalDetailsFormData) => {
    updateFormData('personalDetails', data);
    markStepComplete('personal');
    onNext();
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="max-w-3xl mx-auto">
      <div className="bg-white p-6 md:p-8 rounded-lg shadow-sm">
        <h2 className="text-2xl font-bold text-gray-900 mb-6">
          Personal Details
        </h2>

        <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
          <SelectInput
            name="title"
            label="Title"
            register={register}
            error={errors.title}
            required
            options={[
              { value: '', label: 'Please select' },
              { value: 'Mr', label: 'Mr' },
              { value: 'Mrs', label: 'Mrs' },
              { value: 'Miss', label: 'Miss' },
              { value: 'Ms', label: 'Ms' },
              { value: 'Dr', label: 'Dr' },
              { value: 'Other', label: 'Other' },
            ]}
          />

          <div className="md:col-span-2">
            <TextInput
              name="firstName"
              label="First Name"
              register={register}
              error={errors.firstName}
              required
              autoComplete="given-name"
              maxLength={50}
            />
          </div>
        </div>

        <TextInput
          name="middleName"
          label="Middle Name"
          register={register}
          error={errors.middleName}
          autoComplete="additional-name"
          maxLength={50}
        />