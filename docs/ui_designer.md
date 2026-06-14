# Design Multi-Step Form UI/UX and Wireframes

**Agent:** ui_designer
**Job:** Client Responsive Webform

---

# Client Responsive Webform - UI/UX Design Documentation

## Table of Contents
1. Executive Summary
2. User Flow Diagrams
3. Wireframes (Desktop, Tablet, Mobile)
4. Design System Specifications
5. Accessibility & Compliance Guidelines
6. Validation & Error Handling Patterns
7. Technical Design Notes

---

## 1. Executive Summary

This document provides comprehensive UI/UX design specifications for a multi-step client onboarding webform tailored for UK wealth management firms. The design prioritizes:

- **Regulatory Compliance**: FCA requirements and data protection (GDPR)
- **User Experience**: Reduced cognitive load through progressive disclosure
- **Accessibility**: WCAG 2.1 AA compliance
- **Responsive Design**: Optimized for desktop (1920px), tablet (768px), and mobile (375px)
- **Trust & Professionalism**: Visual design appropriate for high-net-worth clientele

---

## 2. User Flow Diagrams

### 2.1 Primary User Journey

```
Entry Point (Landing/Email Link)
         ↓
    Welcome Screen
    (Overview + Privacy Notice)
         ↓
    Step 1: Personal Details
    - Title, Full Name
    - Date of Birth
    - National Insurance Number
    - Nationality/Residency
         ↓
    Step 2: Contact & Address
    - Primary Address (with postcode lookup)
    - Previous Address (if <3 years)
    - Phone Numbers
    - Email Address
    - Preferred Contact Method
         ↓
    Step 3: Financial Information
    - Employment Status
    - Occupation/Industry
    - Annual Income Range
    - Source of Wealth
    - Net Worth Range
    - Existing Assets
         ↓
    Step 4: Investment Objectives & Risk Profile
    - Investment Goals
    - Time Horizon
    - Risk Tolerance Questionnaire
    - Investment Experience
    - Attitude to Loss
         ↓
    Step 5: Fact Find
    - Current Financial Arrangements
    - Existing Pensions/ISAs
    - Protection Needs
    - Estate Planning
    - Dependents
         ↓
    Step 6: Consents & Review
    - Data Summary (editable)
    - Terms & Conditions
    - Privacy Policy Consent
    - Marketing Preferences
    - FCA Disclosures
    - Electronic Signature
         ↓
    Confirmation Screen
    (Next Steps + Reference Number)
```

### 2.2 Alternative Flows

- **Save & Resume**: Users can save progress at any step and receive secure link
- **Back Navigation**: Users can navigate backward without data loss
- **Validation Failures**: Inline errors with guidance, preventing progression
- **Session Timeout**: Auto-save + warning before timeout (20 min warning, 30 min timeout)

---

## 3. Wireframes

### 3.1 Welcome Screen

#### Desktop (1920px)
```
┌─────────────────────────────────────────────────────────────┐
│  [Company Logo]                    [Save & Exit] [Help: ?]  │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│              Welcome to [Firm Name]                           │
│              New Client Registration                          │
│                                                               │
│   This secure form will take approximately 15-20 minutes     │
│   to complete. We'll collect information to help us           │
│   understand your financial needs and ensure regulatory       │
│   compliance.                                                 │
│                                                               │
│   ┌──────────────────────────────────────────────┐          │
│   │  What you'll need:                            │          │
│   │  ✓ National Insurance Number                 │          │
│   │  ✓ Current and previous address details      │          │
│   │  ✓ Employment and income information         │          │
│   │  ✓ Details of existing financial arrangements│          │
│   └──────────────────────────────────────────────┘          │
│                                                               │
│   ┌──────────────────────────────────────────────┐          │
│   │  🔒 Your data is secure                       │          │
│   │  Your information is encrypted and handled in │          │
│   │  accordance with GDPR and FCA regulations.    │          │
│   │  [Read our Privacy Notice]                    │          │
│   └──────────────────────────────────────────────┘          │
│                                                               │
│   ☐ I have read and understood the Privacy Notice           │
│                                                               │
│              [Get Started →]                                  │
│                                                               │
│   Already started? [Resume your application]                 │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 Multi-Step Form Template (All Steps)

#### Desktop Layout (1920px)
```
┌─────────────────────────────────────────────────────────────┐
│  [Company Logo]              Step 2 of 6: Contact & Address │
│                              [💾 Auto-saved 2 min ago]       │
├─────────────────────────────────────────────────────────────┤
│  Progress Bar:                                               │
│  [████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░] 33%           │
│  Personal | Contact | Financial | Investment | Fact Find | Review
│    ✓         ●          ○           ○           ○          ○   │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                       │   │
│  │  Contact Information & Address                       │   │
│  │  ________________________________________________     │   │
│  │                                                       │   │
│  │  Primary Residential Address *                       │   │
│  │  ┌────────────────────────────────────────────┐     │   │
│  │  │ Postcode              [Find Address ▼]     │     │   │
│  │  └────────────────────────────────────────────┘     │   │
│  │                                                       │   │
│  │  ┌────────────────────────────────────────────┐     │   │
│  │  │ Street Address                              │     │   │
│  │  └────────────────────────────────────────────┘     │   │
│  │                                                       │   │
│  │  ┌──────────────────────┐ ┌───────────────────┐    │   │
│  │  │ City/Town             │ │ County            │    │   │
│  │  └──────────────────────┘ └───────────────────┘    │   │
│  │                                                       │   │
│  │  How long have you lived at this address? *          │   │
│  │  ┌────┐ Years  ┌────┐ Months                        │   │
│  │  │    │        │    │                                │   │
│  │  └────┘        └────┘                                │   │
│  │                                                       │   │
│  │  [Conditional: Shows if <3 years]                    │   │
│  │  ┌────────────────────────────────────────────┐     │   │
│  │  │ Previous Address Details                    │     │   │
│  │  │ [Same fields as above]                      │     │   │
│  │  └────────────────────────────────────────────┘     │   │
│  │                                                       │   │
│  │  Contact Numbers *                                    │   │
│  │  ┌────────────────────────────────────────────┐     │   │
│  │  │ Mobile: +44 |                              │     │   │
│  │  └────────────────────────────────────────────┘     │   │
│  │                                                       │   │
│  │  ┌────────────────────────────────────────────┐     │   │
│  │  │ Home (optional):                            │     │   │
│  │  └────────────────────────────────────────────┘     │   │
│  │                                                       │   │
│  │  Email Address *                                      │   │
│  │  ┌────────────────────────────────────────────┐     │   │
│  │  │ email@example.com                           │     │   │
│  │  └────────────────────────────────────────────┘     │   │
│  │  ℹ️ We'll send your confirmation to this address    │   │
│  │                                                       │   │
│  │  Preferred Contact Method *                           │   │
│  │  ◉ Email    ○ Mobile    ○ Post    ○ Phone           │   │
│  │                                                       │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                               │
│  [← Previous: Personal Details]    [Next: Financial Info →] │
│                                                               │
│  [Save & Exit]                                               │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

#### Tablet Layout (768px)
```
┌──────────────────────────────────────┐
│  [☰] [Logo]      Step 2/6  [💾] [?] │
├──────────────────────────────────────┤
│  [████████░░░░░░░░░░░░░░░░] 33%    │
│  Personal > Contact > ...            │
├──────────────────────────────────────┤
│                                      │
│  Contact Information & Address       │
│  ________________________________    │
│                                      │
│  Primary Residential Address *       │
│  ┌────────────────────────────┐     │
│  │ Postcode   [Find Address]  │     │
│  └────────────────────────────┘     │
│                                      │
│  ┌────────────────────────────┐     │
│  │ Street Address              │     │
│  └────────────────────────────┘     │
│                                      │
│  ┌────────────────────────────┐     │
│  │ City/Town                   │     │
│  └────────────────────────────┘     │
│                                      │
│  ┌────────────────────────────┐     │
│  │ County                      │     │
│  └────────────────────────────┘     │
│                                      │
│  [Form continues...]                 │
│                                      │
│  [← Previous]      [Next →]         │
│  [Save & Exit]                       │
│                                      │
└──────────────────────────────────────┘
```

#### Mobile Layout (375px)
```
┌────────────────────────┐
│ [☰] [Logo]    [💾] [?]│
├────────────────────────┤
│ Step 2 of 6            │
│ [████░░░░░░░░] 33%    │
├────────────────────────┤
│                        │
│ Contact & Address      │
│ ___________________    │
│                        │
│ Postcode *             │
│ ┌──────────────────┐  │
│ │                  │  │
│ └──────────────────┘  │
│ [Find Address ▼]      │
│                        │
│ Street Address *       │
│ ┌──────────────────┐  │
│ │                  │  │
│ └──────────────────┘  │
│                        │
│ City/Town *            │
│ ┌──────────────────┐  │
│ │                  │  │
│ └──────────────────┘  │
│                        │
│ [Continues...]         │
│                        │
│ [← Previous]           │
│ [Next: Financial →]    │
│                        │
│ [Save & Exit]          │
│                        │
└────────────────────────┘
```

### 3.3 Step-by-Step Screen Details

#### Step 1: Personal Details
**Fields:**
- Title (dropdown: Mr, Mrs, Ms, Miss, Dr, Prof, Other)
- First Name(s) *
- Middle Name(s)
- Surname *
- Preferred Name (if different)
- Date of Birth * (day/month/year dropdowns)
- National Insurance Number * (format: AB 12 34 56 C)
- Gender (Male, Female, Other, Prefer not to say)
- Marital Status (dropdown)
- Nationality * (searchable dropdown)
- UK Tax Resident? (Yes/No radio)
- Country of Tax Residence (if different)

**Validation:**
- Age must be 18+
- NI Number format validation
- Future dates not allowed for DOB

---

#### Step 2: Contact & Address
**Fields:**
- Primary Address (with UK postcode lookup integration)
- Duration at address (years/months)
- Previous address (conditional: if <3 years at current)
- Mobile phone * (UK format validation)
- Home phone (optional)
- Email address * (with confirmation)
- Preferred contact method (radio buttons)
- Best time to contact (dropdown)

**Features:**
- Postcode lookup API integration (e.g., Royal Mail PAF)
- Manual address entry option
- Email verification field
- Phone number formatting

---

#### Step 3: Financial Information
**Fields:**
- Employment Status * (dropdown: Employed, Self-employed, Retired, Unemployed, Student, Other)
- Occupation/Industry *
- Employer Name (if applicable)
- Annual Income Range * (dropdown: <£25k, £25k-£50k, £50k-£100k, £100k-£250k, £250k-£500k, £500k+)
- Source of Wealth * (checkboxes: Employment, Inheritance, Business Sale, Investments, Property, Gift, Other)
- Estimated Net Worth * (similar ranges)
- Liquid Assets Range *
- Do you expect significant changes to income in next 12 months? (Yes/No + details)

**Design Notes:**
- Ranges instead of exact figures for privacy/comfort
- Conditional fields based on employment status
- Clear FCA-required source of wealth options

---

#### Step 4: Investment Objectives & Risk Profile
**Fields:**

**Investment Goals** (checkboxes - multiple selection):
- Capital Growth
- Income Generation
- Preservation of Capital
- Retirement Planning
- Estate Planning
- Education Funding
- Other (specify)

**Investment Time Horizon** *
- Less than 3 years
- 3-5 years
- 5-10 years
- 10+ years

**Risk Tolerance Questionnaire** (required for FCA compliance):
Series of 8-10 scenario-based questions with scaled responses (1-5 or A-E)

Example questions:
1. "If your investment portfolio fell 20% in value, you would..."
   - Sell everything immediately
   - Sell some holdings
   - Hold steady
   - Buy more
   - Invest significantly more

2. "Which statement best describes your investment experience?"
   - No experience
   - Limited experience
   - Some experience
   - Experienced investor
   - Very experienced/professional

**Risk Profile Output** (calculated):
- Visual indicator showing calculated risk profile
- Conservative / Balanced / Growth / Aggressive
- Editable if client disagrees with assessment

**Design Pattern:**
```
┌─────────────────────────────────────┐
│ Question 3 of 10                     │
│                                      │
│ How would you react if your          │
│ portfolio value decreased by 15%     │
│ in a single month?                   │
│                                      │
│ ○ Very concerned - I would sell      │
│ ○ Concerned - I would review         │
│ ○ Neutral - I would monitor          │
│ ○ Comfortable - expected volatility  │
│ ○ Opportunity - I would invest more  │
│                                      │
│        [← Previous]  [Next →]        │
│                   3 of 10            │
└─────────────────────────────────────┘
```

---

#### Step 5: Fact Find
**Sections:**

**A. Current Financial Arrangements**
- Do you have existing financial advisor? (Yes/No)
- Current pension arrangements (checkboxes: Workplace, Personal, SIPP, Final Salary, None)
- Current ISA holdings (Cash ISA, Stocks & Shares ISA, Lifetime ISA, None)
- Other investments (GIA, Offshore bonds, VCT/EIS, BTL property, None)

**B. Protection & Insurance**
- Life insurance coverage amount
- Critical illness cover
- Income protection
- Private medical insurance

**C. Estate Planning**
- Do you have a Will? (Yes/No/Prefer not to say)
- Lasting Power of Attorney in place?
- Any trusts in place?

**D. Dependents**
- Number of dependents
- Ages of dependents
- Financial obligations (education fees, care costs, etc.)

**Design Notes:**
- Accordion/collapsible sections to reduce overwhelming appearance
- "Prefer not to say" option for sensitive questions
- Tooltips explaining technical terms (SIPP, VCT, EIS, etc.)

---

#### Step 6: Consents & Review

**Section A: Review Your Information**
```
┌─────────────────────────────────────────────┐
│  Review Your Information                     │
│  ________________________________________    │
│                                              │
│  ▼ Personal Details                [Edit]   │
│    Name: Mr. John Alexander Smith           │
│    DOB: 15/03/1975                          │
│    NI Number: AB 12 34 56 C                 │
│                                              │
│  ▼ Contact & Address               [Edit]   │
│    123 High Street, London, W1A 1AA         │
│    Mobile: +44 7700 900000                  │
│    Email: john.smith@email.com              │
│                                              │
│  ▼ Financial Information           [Edit]   │
│    [Summarized information...]              │
│                                              │
│  [Continue sections...]                      │
└─────────────────────────────────────────────┘
```

**Section B: Declarations & Consents**

**Required Checkboxes:**
- ☐ I confirm that the information provided is accurate and complete to the best of my knowledge *
- ☐ I understand that providing false information may affect my ability to claim on insurance or investments *
- ☐ I have read and agree to the Terms & Conditions * [View full T&Cs]
- ☐ I have read and understood the Privacy Notice * [View Privacy Notice]
- ☐ I consent to my data being processed for the purposes of wealth management advice and FCA compliance *
- ☐ I understand this is not a contract for services and further documentation will be required *

**Optional Consents:**
- ☐ I consent to receive marketing communications about products and services
- ☐ I consent to receive market updates and investment insights
- ☐ I consent to my data being shared with partner firms (with full disclosure of partners)

**FCA Disclosures:**
- Clear display of regulatory status
- FSCS protection information
- Complaints procedure information
- Links to FCA register

**Electronic Signature:**
```
┌─────────────────────────────────────┐
│ Electronic Signature *               │
│                                      │
│ ┌─────────────────────────────┐     │
│ │ Type your full name         │     │
│ │ John Alexander Smith        │     │
│ └─────────────────────────────┘     │
│                                      │
│ Date: 15/01/2025 (auto-populated)   │
│                                      │
│ By typing your name above, you      │
│ electronically sign this document.  │
└─────────────────────────────────────┘
```

**Submit Button:**
Large, prominent, disabled until all required fields completed
```
[Submit Application →]
```

---

#### Confirmation Screen

```
┌─────────────────────────────────────────┐
│           ✓ Application Submitted        │
│                                          │
│  Thank you, Mr. Smith                   │
│                                          │
│  Your application has been received.    │
│                                          │
│  ┌────────────────────────────────┐    │
│  │ Reference Number:              │    │
│  │ WM-2025-001234                 │    │
│  │ [Copy]                         │    │
│  └────────────────────────────────┘    │
│                                          │
│  📧 A confirmation email has been        │
│     sent to john.smith@email.com        │
│                                          │
│  📄 [Download PDF Copy]                 │
│                                          │
│  Next Steps:                             │
│  1. You'll receive a call within 48hrs  │
│  2. We'll arrange an initial consultation│
│  3. Your dedicated advisor will review  │
│     your information                     │
│                                          │
│  Questions?                              │
│  Call: 020 1234 5678                    │
│  Email: clientservices@firm.co.uk       │
│                                          │
│  [Return to Homepage]                    │
└─────────────────────────────────────────┘
```

---

## 4. Design System Specifications

### 4.1 Color Palette

**Primary Colors:**
- Primary Brand: `#003B5C` (Deep Blue) - Conveys trust, professionalism
- Primary Accent: `#C5A572` (Champagne Gold) - Premium feel
- Success: `#2E7D32` (Forest Green)
- Error: `#C62828` (Deep Red)
- Warning: `#F57C00` (Amber)
- Info: `#0277BD` (Blue)

**Neutral Colors:**
- Text Primary: `#1A1A1A` (Near Black)
- Text Secondary: `#666666` (Medium Grey)
- Border: `#D4D4D4` (Light Grey)
- Background: `#FFFFFF` (White)
- Background Alt: `#F8F9FA` (Off-white)
- Disabled: `#E0E0E0`

**Usage:**
- All text must meet WCAG AA contrast ratios (4.5:1 for normal text, 3:1 for large text)
- Error states: Red border + red text + error icon
- Success states: Green border + green text + checkmark icon

### 4.2 Typography

**Font Family:**
- Primary: `'Inter', 'Helvetica Neue', Arial, sans-serif`
- Monospace (for reference numbers): `'Roboto Mono', monospace`

**Font Sizes & Weights:**
```
H1 (Page Titles): 32px / 2rem, font-weight: 600, line-height: 1.2
H2 (Section Titles): 24px / 1.5rem, font-weight: 600, line-height: 1.3
H3 (Subsections): 20px / 1.25rem, font-weight: 500, line-height: 1.4
Body Text: 16px / 1rem, font-weight: 400, line-height: 1.6
Small Text: 14px / 0.875rem, font-weight: 400, line-height: 1.5
Label Text: 14px / 0.875rem, font-weight: 500, line-height: 1.4
Helper Text: 13px / 0.8125rem, font-weight: 400, line-height: 1.4
```

**Responsive Typography:**
- Mobile: Reduce H1 to 24px, H2 to 20px
- Maintain minimum 16px for body text (prevents zoom on iOS)

### 4.3 Spacing System

**Base Unit: 8px**
```
xs: 4px   (0.25rem)
sm: 8px   (0.5rem)
md: 16px  (1rem)
lg: 24px  (1.5rem)
xl: 32px  (2rem)
2xl: 48px (3rem)
3xl: 64px (4rem)
```

**Component Spacing:**
- Form field vertical spacing: 24px (lg)
- Section spacing: 48px (2xl)
- Container padding: 24px (desktop), 16px (mobile)
- Button padding: 12px 24px (vertical horizontal)

### 4.4 Component Library

#### Input Fields

**Text Input - Default State:**
```css
.input-field {
  width: 100%;
  height: 48px;
  padding: 12px 16px;
  border: 1px solid #D4D4D4;
  border-radius: 4px;
  font-size: 16px;
  color: #1A1A1A;
  background: #FFFFFF;
  transition: all 0.2s ease;
}
```

**States:**
- Focus: `border-color: #003B5C; box-shadow: 0 0 0 3px rgba(0, 59, 92, 0.1);`
- Error: `border-color: #C62828; background: #FFEBEE;`
- Success: `border-color: #2E7D32; background: #F1F8F4;`
- Disabled: `background: #F5F5F5; color: #999; cursor: not-allowed;`

**Input Label Structure:**
```html
<div class="form-group">
  <label for="email" class="form-label">
    Email Address 
    <span class="required">*</span>
    <span class="tooltip-trigger">ⓘ</span>
  </label>
  <input 
    type="email" 
    id="email" 
    class="input-field"
    aria-required="true"
    aria-describedby="email-help email-error"
  />
  <p id="email-help" class="helper-text">
    We'll send your confirmation here
  </p>
  <p id="email-error" class="error-text" hidden>
    Please enter a valid email address
  </p>
</div>
```

#### Buttons

**Primary Button:**
```css
.btn-primary {
  background: #003B5C;
  color: #FFFFFF;
  border: none;
  border-radius: 4px;
  padding: 14px 32px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  min-height: 48px; /* Touch target size */
}

.btn-primary:hover {
  background: #00517A;
  box-shadow: 0 2px 8px rgba(0, 59, 92, 0.3);
}

.btn-primary:disabled {
  background: #E0E0E0;
  color: #999;
  cursor: not-allowed;
}
```

**Secondary Button:**
```css
.btn-secondary {
  background: transparent;
  color: #003B5C;
  border: 2px solid #003B5C;
  /* Other properties same as primary */
}
```

**Button Sizes:**
- Large (primary actions): 48px height, 16px font
- Medium (secondary): 40px height, 14px font
- Small (tertiary): 32px height, 14px font

#### Progress Indicator

**Linear Progress Bar:**
```html
<div class="progress-container">
  <div class="progress-bar" role="progressbar" 
       aria-valuenow="33" aria-valuemin="0" aria-valuemax="100">
    <div class="progress-fill" style="width: 33%"></div>
  </div>
  <p class="progress-text">Step 2 of 6 - 33% Complete</p>
</div>
```

**Step Indicator:**
```html
<ol class="step-indicator">
  <li class="step completed">
    <span class="step-number">✓</span>
    <span class="step-label">Personal</span>
  </li>
  <li class="step active">
    <span class="step-number">2</span>
    <span class="step-label">Contact</span>
  </li>
  <li class="step pending">
    <span class="step-number">3</span>
    <span class="step-label">Financial</span>
  </li>
  <!-- More steps -->
</ol>
```

#### Error & Validation Messages

**Inline Error:**
```html
<div class="error-message" role="alert">
  <svg class="error-icon">⚠</svg>
  <span>Please enter a valid National Insurance Number</span>
</div>
```

**Success Message:**
```html
<div class="success-message" role="status">
  <svg class="success-icon">✓</svg>
  <span>Information saved successfully</span>
</div>
```

**Global Error (top of form):**
```html
<div class="alert alert-error" role="alert">
  <h3>There are errors in your submission</h3>
  <ul>
    <li><a href="#email">Email address is required</a></li>
    <li><a href="#phone">Phone number format is invalid</a></li>
  </ul>
</div>
```

#### Auto-save Indicator

```html
<div class="autosave-status">
  <span class="saving" hidden>
    <span class="spinner"></span> Saving...
  </span>
  <span class="saved">
    ✓ Auto-saved 2 minutes ago
  </span>
  <span class="error" hidden>
    ⚠ Unable to save. Check your connection.
  </span>
</div>
```

### 4.5 Responsive Breakpoints

```css
/* Mobile First Approach */
/* Base styles: Mobile (320px - 767px) */

/* Tablet */
@media (min-width: 768px) {
  /* Tablet specific styles */
}

/* Desktop */
@media (min-width: 1024px) {
  /* Desktop specific styles */
}

/* Large Desktop */
@media (min-width: 1440px) {
  /* Large screen optimizations */
}
```

**Container Max-widths:**
- Mobile: 100% (with 16px padding)
- Tablet: 720px
- Desktop: 960px
- Large: 1140px

**Form Column Layouts:**
- Mobile: 1 column (stack all fields)
- Tablet: 2 columns for related fields (e.g., First Name | Surname)
- Desktop: Up to 3 columns for short fields (e.g., Day | Month | Year)

---

## 5. Accessibility & Compliance Guidelines

### 5.1 WCAG 2.1 AA Requirements

#### Perceivable
✓ **Text Alternatives (1.1.1)**
- All form icons have text alternatives
- Error icons include text descriptions
- Logo has alt text

✓ **Audio Description or Media Alternative (1.2.3)**
- If instructional videos added, captions required

✓ **Adaptable Content (1.3.x)**
- Semantic HTML5 (`<main>`, `<section>`, `<nav>`)
- Form labels properly associated with inputs
- Heading hierarchy (H1 → H2 → H3, no skipping)
-