# FCA Compliance and Legal Requirements Analysis

**Agent:** legal_compliance_analyst
**Job:** Client Responsive Webform

---

# FCA Compliance and Legal Requirements Analysis
## Client Onboarding Webform for UK Wealth Management

**Document Version:** 1.0  
**Date:** 2024  
**Classification:** Compliance Requirements Specification

---

## EXECUTIVE SUMMARY

This document provides a comprehensive legal and regulatory compliance framework for a client onboarding webform for UK wealth management firms. The analysis covers FCA regulations, UK GDPR requirements, anti-money laundering obligations, and industry best practices to ensure full regulatory compliance.

---

## 1. REGULATORY FRAMEWORK OVERVIEW

### 1.1 Applicable Regulations

1. **FCA Handbook Requirements:**
   - COBS (Conduct of Business Sourcebook) - Client categorization and suitability
   - SYSC (Senior Management Arrangements, Systems and Controls)
   - PRIN (Principles for Businesses)
   - CASS (Client Assets Sourcebook)

2. **Data Protection:**
   - UK GDPR (General Data Protection Regulation)
   - Data Protection Act 2018
   - Privacy and Electronic Communications Regulations (PECR) 2003

3. **Financial Crime Prevention:**
   - Money Laundering, Terrorist Financing and Transfer of Funds Regulations 2017 (MLR 2017)
   - Proceeds of Crime Act 2002
   - FCA Financial Crime Guide

4. **Consumer Protection:**
   - Consumer Rights Act 2015
   - Financial Services and Markets Act 2000

---

## 2. FCA CLIENT ONBOARDING REQUIREMENTS

### 2.1 Know Your Client (KYC) Obligations

#### 2.1.1 Client Categorization (COBS 3)
**Purpose:** Determine appropriate level of regulatory protection

**Mandatory Requirements:**
- Categorize client as Retail, Professional, or Eligible Counterparty
- Provide written notification of categorization
- Inform clients of their right to request different categorization

**Form Implementation:**
- Auto-categorization logic based on responses
- Display categorization notice before form submission
- Store categorization decision with justification

#### 2.1.2 Client Identification Data (AML Compliance)

**Mandatory Personal Information Fields:**

| Field Name | Data Type | Validation Rules | Retention Period |
|------------|-----------|------------------|------------------|
| Full Legal Name | Text | Match ID document | 5 years post-relationship |
| Previous Names | Text | Optional but recommended | 5 years post-relationship |
| Date of Birth | Date | Age ≥ 18 years | 5 years post-relationship |
| Nationality | Dropdown | ISO country codes | 5 years post-relationship |
| National Insurance Number | Alphanumeric | Format: AA 99 99 99 A | 5 years post-relationship |
| Place of Birth | Text | Mandatory for AML | 5 years post-relationship |

**Mandatory Address Information:**

| Field Name | Data Type | Validation Rules | Retention Period |
|------------|-----------|------------------|------------------|
| Current Residential Address | Multi-line | UK postcode validation | 5 years post-relationship |
| Time at Current Address | Number | Months/Years | 5 years post-relationship |
| Previous Address (if <3 years) | Multi-line | Required if current <3 years | 5 years post-relationship |
| Correspondence Address | Multi-line | Optional, if different | 5 years post-relationship |

**Mandatory Contact Information:**

| Field Name | Data Type | Validation Rules | Retention Period |
|------------|-----------|------------------|------------------|
| Primary Phone Number | Tel | UK format validation | 5 years post-relationship |
| Mobile Number | Tel | SMS verification recommended | 5 years post-relationship |
| Email Address | Email | RFC 5322 validation + verification | 5 years post-relationship |
| Preferred Contact Method | Radio/Dropdown | Store preference | Duration of relationship |

### 2.2 Fact-Find Requirements (COBS 9 - Suitability)

#### 2.2.1 Financial Situation Assessment

**Mandatory Financial Information:**

| Category | Fields Required | Compliance Basis | Validation |
|----------|----------------|------------------|------------|
| **Income Details** | Employment status, Annual income (gross), Other income sources | COBS 9.2.1R | Income ranges acceptable |
| **Assets** | Property value, Savings/investments value, Pension values | COBS 9.2.1R | Estimated values acceptable |
| **Liabilities** | Mortgage outstanding, Other debts, Monthly commitments | COBS 9.2.1R | Ranges acceptable |
| **Regular Expenditure** | Monthly living expenses, Discretionary spending | COBS 9.2.2R | For disposable income calc |

**Implementation Notes:**
- Allow range selections for privacy (e.g., £50k-£75k)
- Mark fields as "Prefer not to say" where appropriate
- Explain why information is required (suitability assessment)

#### 2.2.2 Investment Knowledge and Experience

**Mandatory Assessment Fields:**

```
Investment Experience:
□ Investment products previously held (multi-select):
  - Savings accounts
  - Stocks and shares ISAs
  - Individual stocks/bonds
  - Investment funds
  - Pensions
  - Alternative investments
  - Derivatives/structured products
  
□ Years of investment experience: [Dropdown: 0, <1, 1-3, 3-5, 5-10, 10+]

□ Frequency of investments: [Never / Rarely / Occasionally / Regularly]

□ Professional qualifications in finance: [Yes/No + Details]

□ Relevant professional experience: [Yes/No + Details]
```

**Validation Rule:** Minimum knowledge assessment required before offering complex products (COBS 10 - Appropriateness)

#### 2.2.3 Investment Objectives and Risk Tolerance

**Mandatory Objective Fields:**

| Field | Type | Options | Compliance Requirement |
|-------|------|---------|----------------------|
| Investment Time Horizon | Radio | <1yr, 1-3yr, 3-5yr, 5-10yr, 10yr+ | COBS 9.2.2R(1) |
| Primary Investment Objective | Multi-select | Capital preservation, Income, Growth, Balanced | COBS 9.2.2R(2) |
| Risk Tolerance | Scale (1-10) | With explanatory text for each level | COBS 9.2.2R(3) |
| Capacity for Loss | Dropdown | With scenario descriptions | COBS 9.2.2R(3) |
| Liquidity Needs | Text/Radio | Anticipated withdrawals | COBS 9.2.2R |

**Risk Assessment Questions (Mandatory):**

1. "How would you react if your investment fell by 10% in value?"
2. "Can you afford to lose any of the money you're investing?"
3. "How important is it to access your money at short notice?"
4. "What is more important: protecting your capital or achieving growth?"

**Validation:** Risk profile must be calculated and displayed before submission

#### 2.2.4 Tax Status Information

**Mandatory Tax Fields:**

| Field | Requirement | Notes |
|-------|-------------|-------|
| UK Tax Resident | Yes/No | Mandatory - affects reporting |
| Tax Residence Countries | Multi-select | If non-UK or dual |
| Tax Identification Numbers | Text fields | For all relevant jurisdictions |
| US Person Status | Yes/No | FATCA compliance |
| ISA Eligibility | Auto-calculated | Based on tax residence |

---

## 3. UK GDPR AND DATA PROTECTION REQUIREMENTS

### 3.1 Legal Basis for Processing

**Primary Legal Bases:**

1. **Contractual Necessity** (Article 6(1)(b))
   - Processing necessary to enter into contract for wealth management services
   - Applies to: Personal details, financial information, contact data

2. **Legal Obligation** (Article 6(1)(c))
   - AML/KYC checks required by MLR 2017
   - FCA regulatory reporting
   - Applies to: Identity verification, source of funds, beneficial ownership

3. **Legitimate Interests** (Article 6(1)(f))
   - Fraud prevention
   - Risk assessment
   - Requires: Legitimate Interest Assessment (LIA) documentation

4. **Consent** (Article 6(1)(a))
   - Marketing communications
   - Non-essential cookies/tracking
   - Sharing data with third parties for non-regulatory purposes

### 3.2 Consent Mechanisms - Technical Requirements

#### 3.2.1 Mandatory Consent Checkboxes

**Structure Required:**

```html
<!-- Essential Processing Notice (Non-optional) -->
<div class="gdpr-notice mandatory">
  <p><strong>Essential Data Processing</strong></p>
  <p>We will process your personal information to:
    • Provide wealth management services
    • Comply with legal and regulatory obligations (AML, FCA reporting)
    • Manage our business relationship with you
  </p>
  <p>This processing is necessary for us to provide our services and comply 
     with legal requirements. You cannot opt out of this processing if you 
     wish to proceed with our services.</p>
  <p><a href="/privacy-policy" target="_blank">Read our full Privacy Policy</a></p>
</div>

<!-- Optional Marketing Consent (Granular) -->
<div class="gdpr-consent optional">
  <label>
    <input type="checkbox" name="consent_email_marketing" value="yes">
    I consent to receiving marketing communications by email about products 
    and services that may be of interest to me.
  </label>
  
  <label>
    <input type="checkbox" name="consent_phone_marketing" value="yes">
    I consent to being contacted by phone for marketing purposes.
  </label>
  
  <label>
    <input type="checkbox" name="consent_post_marketing" value="yes">
    I consent to receiving marketing materials by post.
  </label>
  
  <p class="consent-note">You can withdraw your consent at any time by 
     contacting us or using the unsubscribe link in our communications.</p>
</div>

<!-- Third Party Data Sharing (If Applicable) -->
<div class="gdpr-consent optional">
  <label>
    <input type="checkbox" name="consent_partner_sharing" value="yes">
    I consent to my information being shared with trusted partners for 
    [specific purpose]. <a href="/partner-list">View partners</a>
  </label>
</div>
```

**Compliance Requirements:**
- Pre-ticked boxes NOT permitted for optional consent
- Clear separation between mandatory and optional processing
- Granular consent options (separate checkboxes for each channel)
- Easy withdrawal mechanism documented
- Consent records stored with timestamp and IP address

#### 3.2.2 Privacy Notice Requirements

**Mandatory Disclosures (Article 13):**

Must be provided BEFORE data collection begins:

1. **Identity and Contact Details**
   - Data controller name and registered address
   - Data Protection Officer contact details
   - FCA registration number

2. **Processing Information**
   - Categories of personal data collected
   - Purposes of processing
   - Legal basis for each purpose
   - Retention periods
   - Automated decision-making (if any)

3. **Data Subject Rights**
   - Right to access (Subject Access Request)
   - Right to rectification
   - Right to erasure ("right to be forgotten")
   - Right to restrict processing
   - Right to data portability
   - Right to object
   - Right to withdraw consent
   - Right to lodge complaint with ICO

4. **Data Sharing**
   - Categories of recipients
   - International transfers (if any) with safeguards
   - Service providers (with examples)

**Implementation:**
- First page of webform must display privacy notice
- "Layered approach" acceptable (summary + full policy link)
- Must be accessible, clear, and in plain English
- Require acknowledgment before proceeding

### 3.3 Data Subject Rights - Form Implications

**Right to Access:**
- Form must facilitate data export in structured format (JSON/CSV)
- Provide copy of all submitted data within 1 month

**Right to Rectification:**
- Enable clients to update information post-submission
- Audit trail of changes required

**Right to Erasure:**
- Note: Limited by legal retention obligations
- After retention period, automated deletion process required
- Exception: Cannot delete if legal obligation to retain (AML = 5 years)

**Right to Data Portability:**
- Provide machine-readable export of data
- Structured format (JSON recommended)

---

## 4. ANTI-MONEY LAUNDERING (AML) COMPLIANCE

### 4.1 Customer Due Diligence (CDD) Requirements - MLR 2017

#### 4.1.1 Standard CDD - Mandatory Fields

**Identity Verification:**

| Requirement | Form Implementation | Documentation |
|-------------|---------------------|---------------|
| Full legal name | Text input (mandatory) | Match to ID document |
| Date of birth | Date picker (mandatory) | Match to ID document |
| Residential address | Address lookup + manual | Verify via utility bill/bank statement |
| Identification number | NI Number/Passport (mandatory) | Government-issued ID |

**Document Upload Requirements:**

```
Mandatory Document Uploads:
1. Proof of Identity (one of):
   - UK Passport
   - UK Driving License (photocard)
   - National ID card (EEA)
   - Biometric Residence Permit

2. Proof of Address (dated within 3 months, one of):
   - Utility bill
   - Bank/credit card statement
   - Council tax bill
   - HMRC correspondence
   - Mortgage statement

File Requirements:
- Formats: PDF, JPG, PNG
- Max size: 5MB per file
- Clear, color, full document visible
- Unaltered and valid
```

**Validation Rules:**
- Name on documents must match form input
- Address on proof must match residential address
- DOB must match across documents
- Documents must be in date/not expired

#### 4.1.2 Enhanced Due Diligence (EDD) Triggers

**Automatic EDD Required If:**

1. **High-risk client indicators:**
   - Non-UK resident investing significant sums
   - Politically Exposed Person (PEP) status
   - Investment >€15,000 from high-risk jurisdiction
   - Source of wealth unclear/unusual

2. **PEP Screening - Mandatory Questions:**

```
PEP Status Assessment:

□ Are you, or have you been in the last 12 months, a Politically Exposed Person (PEP)?
  [Yes/No]

□ Are any of your immediate family members or known close associates PEPs?
  [Yes/No]

PEP Definition (display):
A person entrusted with a prominent public function, including:
- Heads of state, government ministers, senior politicians
- Senior government, judicial, or military officials
- Senior executives of state-owned enterprises
- Important political party officials

If YES to either:
  → Additional fields appear:
    - Position/role held: [Text]
    - Organization: [Text]
    - Dates held: [Date range]
    - Country: [Dropdown]
    - Relationship (if family/associate): [Dropdown]
```

**Additional EDD Fields:**

| Field | Requirement | Purpose |
|-------|-------------|---------|
| Source of Wealth | Mandatory text (if EDD) | Understand origin of funds |
| Source of Funds for Investment | Mandatory dropdown + text | Specific to this investment |
| Occupation Details | Enhanced detail required | Risk assessment |
| Business Interests | Declare all directorships | Conflict/sanctions check |
| Expected Account Activity | Transaction volume/value | Baseline for monitoring |

#### 4.1.3 Source of Funds/Wealth Declaration

**Mandatory for Investments >£10,000:**

```
Source of Funds for this Investment (select all that apply):

□ Salary/Employment Income
  → Employer name: [Text]
  → Occupation: [Text]
  
□ Business Profits
  → Business name: [Text]
  → Nature of business: [Text]
  → Your role: [Text]
  
□ Sale of Property
  → Property address: [Text]
  → Sale date: [Date]
  → Sale value: [Currency]
  
□ Inheritance
  → Relationship to deceased: [Text]
  → Approximate date: [Date]
  
□ Gift
  → Relationship to donor: [Text]
  → Donor name: [Text]
  
□ Investment Returns
  → Type of investment: [Text]
  
□ Sale of Business
  → Business name and details: [Text]
  
□ Other
  → Please specify: [Text area - mandatory]

Supporting Documentation (if source >£50,000):
[File upload] - e.g., payslips, business accounts, completion statement, probate
```

**Validation Rules:**
- At least one source must be selected
- If "Other", text explanation mandatory
- For amounts >£50,000, supporting documents required
- Internal review flag if source appears unusual

### 4.2 Sanctions and PEP Screening

**Technical Implementation Requirements:**

1. **Automated Screening:**
   - Integrate with sanctions list API (HMT, OFSI, UN, EU)
   - Screen against PEP databases
   - Run check on form submission
   - Re-check periodically during relationship

2. **Screening Data Points:**
   - Full name + variations
   - Date of birth
   - Nationality
   - Residential address
   - Any business associations disclosed

3. **Match Handling:**
   - Potential match = halt onboarding pending review
   - Record screening results with timestamp
   - MLRO review required for any matches
   - Cannot proceed until cleared

**Form Implementation:**
```
[Background process - not visible to client]
On submission:
  → Screen against sanctions lists
  → Screen against PEP databases
  → Screen against adverse media
  → Generate risk score
  
If match/high risk:
  → Flag for MLRO review
  → Do not auto-approve
  → Send holding message to client
  → Trigger manual compliance workflow
```

### 4.3 Ongoing Monitoring Requirements

**Data Collection for Monitoring:**

| Field | Purpose | Frequency |
|-------|---------|-----------|
| Expected Investment Amount | Establish baseline | Initial + annual review |
| Expected Transaction Frequency | Establish pattern | Initial + annual review |
| Expected Source of Future Funds | Monitor consistency | Initial + annual review |
| Any anticipated changes | Forward-looking | Annual review |

**Audit Trail Requirements:**
- Log all form access (date, time, IP, user agent)
- Log all data modifications with timestamp and user
- Log all document uploads
- Log all screening results
- Log all risk assessments and decisions
- Retain logs for 5 years minimum

---

## 5. MANDATORY DISCLOSURES AND WARNINGS

### 5.1 FCA-Required Warnings

#### 5.1.1 Risk Warnings (COBS 2.1.1R)

**Display Requirements:**
- Must be clear, fair, and not misleading
- Appropriate prominence
- Cannot be hidden in T&Cs

**Standard Risk Warning Text:**

```
┌─────────────────────────────────────────────────────────┐
│  ⚠️  INVESTMENT RISK WARNING                            │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  The value of investments can go down as well as up     │
│  and you may get back less than you invested.           │
│                                                          │
│  Past performance is not a reliable indicator of        │
│  future results.                                         │
│                                                          │
│  The tax treatment depends on your individual           │
│  circumstances and may be subject to change.            │
│                                                          │
│  [For non-advised services:]                            │
│  If you are in any doubt about the suitability of       │
│  an investment, you should seek independent financial   │
│  advice.                                                 │
│                                                          │
└─────────────────────────────────────────────────────────┘

☐ I have read and understood the investment risk warning
```

**Placement:** Must appear before final submission and require acknowledgment

#### 5.1.2 Service Description and Limitations

**Mandatory Disclosure:**

```
Service Type Declaration:

This service provides [select applicable]:
□ Independent advice
□ Restricted advice  
□ Execution-only service (no advice)

[If restricted:]
We only offer products from [specify: limited panel/single provider/own products]

[If execution-only:]
We will not advise you on the suitability of products. You will make your own 
investment decisions. This means you will not benefit from the protection of 
the FCA rules on assessing suitability.

You have the right to request a different service if available.
```

#### 5.1.3 Costs and Charges Disclosure (COBS 2.2A)

**Pre-contract Information:**

```
Costs and Charges

Before we provide our services, you will receive:
• A detailed breakdown of all costs and charges
• Information about how costs impact returns over time
• Details of any commissions or incentives we receive

Initial Indication of Costs [if known]:
- Initial advice fee: [Amount/percentage]
- Ongoing management fee: [Amount/percentage]
- Product charges: [Range or TBC]
- Transaction costs: [Range or TBC]

Full costs disclosure will be provided before you commit to any investment.

☐ I understand I will receive detailed costs information before proceeding
```

**Implementation:** 
- Cannot be bypassed
- Must be presented in tabular format where specific
- Ex-ante costs disclosure required before contract

### 5.2 FSCS Protection Notice

**Mandatory Display:**

```
┌─────────────────────────────────────────────────────────┐
│  Financial Services Compensation Scheme (FSCS)          │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  We are covered by the Financial Services Compensation  │
│  Scheme (FSCS).                                          │
│                                                          │
│  You may be entitled to compensation from the scheme    │
│  if we cannot meet our obligations. This depends on     │
│  the type of business and circumstances of the claim.   │
│                                                          │
│  Most types of investment business are covered up to    │
│  £85,000.                                                │
│                                                          │
│  Further information about the compensation scheme      │
│  is available from the FSCS:                            │
│  www.fscs.org.uk or call 0800 678 1100                  │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

**Placement:** Must appear in client agreement section

### 5.3 Complaints Procedure

**Mandatory Information:**

```
How to Make a Complaint

If you are unhappy with our service, please contact:

[Compliance Officer Name]
[Company Name]
[Address]
Email: complaints@[company].co.uk
Phone: [Number]

We will acknowledge your complaint within 5 business days and aim to 
resolve it within 8 weeks.

If you are not satisfied with our response, or we have not resolved your 
complaint within 8 weeks, you may refer your complaint to:

Financial Ombudsman Service
Exchange Tower
London E14 9SR
Phone: 0800 023 4567
Email: complaint.info@financial-ombudsman.org.uk
Website: www.financial-ombudsman.org.uk
```

**Implementation:** Include link in footer and confirmation email

---

## 6. DATA RETENTION AND DELETION POLICIES

### 6.1 Regulatory Retention Requirements

**Minimum Retention Periods:**

| Data Type | Retention Period | Legal Basis | Deletion Policy |
|-----------|------------------|-------------|-----------------|
| AML/KYC Records | 5 years from end of relationship | MLR 2017 Reg 40 | Auto-delete after 5y + 1 month |
| Client Identity Documents | 5 years from end of relationship | MLR 2017 | Secure deletion after period |
| Fact-find Information | 5 years from advice given | FCA COBS/SYSC | Archive after 1y, delete after 5y |
| Suitability Reports | Indefinitely (recommend 10y min) | FCA COBS 9.4 | Review at 10y intervals |
| Consent Records | Duration of processing + 3 years | UK GDPR accountability | Delete after processing ends + 3y |
| Marketing Consents | Until withdrawn + 3 years | UK GDPR/PECR | Delete on withdrawal + 3y |
| Client Communications | 5 years minimum | FCA SYSC | Delete after 6 years |
| Transaction Records | 5 years minimum | FCA CASS/COBS | Delete after 6 years |
| Complaints Records | 5 years from complaint closure | FCA DISP | Delete after 5 years |
| Audio Recordings (if applicable) | 5 years | MiFID II | Secure deletion after 5y |

### 6.2 Data Lifecycle Management

**Technical Implementation:**

```
Data States and Transitions:

1. ACTIVE (0-12 months from collection)
   - Full accessibility
   - Regular processing permitted
   - Stored in primary database
   - Encrypted at rest

2. ARCHIVED (12 months - retention period)
   - Restricted access (compliance/legal only)
   - Move to archive storage
   - Compressed/encrypted
   - Read-only

3. DELETION-PENDING (End of retention period)
   - 30-day grace period
   - Automated review for exceptions
   - Legal hold check
   - Notification to DPO

4. DELETED (After retention + grace)
   - Secure deletion (3-pass overwrite minimum)
   - Delete from backups
   - Certificate of deletion generated
   - Logged in deletion register
```

**Exception Handling:**
- Legal hold: Suspend deletion if litigation/investigation
- Subject access request in progress: Retain until complete
- Active complaint: Retain until complaint + 5 years
- Regulatory investigation: Retain until closure + 2 years

### 6.3 Right to Erasure - Limitations

**When Right to Erasure DOES NOT Apply:**

1. **Legal Obligation** (Article 17(3)(b))
   - Cannot delete AML records for 5 years (legal requirement)
   - Cannot delete regulatory reporting data during retention period

2. **Legal Claims** (Article 17(3)(e))
   - Data needed to establish, exercise, or defend legal claims

3. **Public Interest** (Article 17(3)(e))
   - FCA regulatory oversight
   - Financial crime prevention

**Client Communication:**
```
If client requests deletion during retention period:

"We have received your request to delete your personal data. While we 
respect your right to erasure under UK GDPR, we are legally required to 
retain certain information for [X] years under:

- Money Laundering Regulations 2017 (5 years)
- FCA regulatory requirements (5 years minimum)

We will:
1. Delete all data not subject to legal retention requirements
2. Restrict processing of retained data to legal compliance only
3. Remove your data from all marketing lists immediately
4. Delete the remaining data automatically on [date]

We have marked your account for deletion on [date]. You will receive 
confirmation when deletion is complete."
```

---

## 7. FORM FIELD REQUIREMENTS - COMPREHENSIVE SPECIFICATION

### 7.1 Multi-Step Form Structure

**Recommended Step Sequence:**

```
Step 1: Welcome and Privacy Notice
├─ Display privacy notice (summary + full link)
├─ Essential processing explanation
├─ Cookie consent
└─ Acknowledgment required to proceed

Step 2: Personal Details
├─ Full legal name
├─ Date of birth
├─ Contact details
├─ Address (current + historical if <3 years)
├─ National Insurance Number
└─ Nationality/Tax residence

Step 3: Identity Verification
├─ Document uploads (ID + proof of address)
├─ PEP declaration
├─ Sanctions screening (background)
└─ Verification status

Step 4: Employment and Financial Situation
├─ Employment status
├─ Occupation details
├─ Income assessment
├─ Assets and liabilities
└─ Regular expenditure

Step 5: Investment Experience and Knowledge
├─ Previous investment products
├─ Years of experience
├─ Professional qualifications
├─ Knowledge assessment questions
└─ Appropriateness evaluation

Step 6: Investment Objectives and Risk
├─ Investment objectives
├─ Time horizon
├─ Risk tolerance questionnaire
├─ Capacity for loss
└─ Risk profile calculation

Step 7: Source of Funds
├─ Source of wealth
├─ Source of funds for this investment
├─ Expected investment amount
└─ Supporting documentation (if required)

Step 8: Service Selection and Agreements
├─ Service type confirmation
├─ Client categorization notice
├─ Terms of business
├─ Costs and charges acknowledgment
└─ Risk warnings

Step 9: Consents and Preferences
├─ Marketing consents (granular)
├─ Communication preferences
├─ Data sharing consents (if applicable)
└─ Third-party cookies

Step 10: Review and Submit
├─ Summary of all information
├─ Edit option for each section
├─ Final declarations
├─ Electronic signature
└─ Submit button
```

### 7.2 Mandatory vs. Optional Fields Matrix

| Field Category | Field Name | Mandatory? | Basis | Validation |
|----------------|------------|------------|-------|------------|
| **PERSONAL DETAILS** |
| | Title | Optional | Courtesy | Mr/Mrs/Miss/Ms/Mx/Dr/Other |
| | First Name | **Mandatory** | KYC/AML | Min 1 char, letters only |
| | Middle Name(s) | Optional | Complete record | Letters only |
| | Last Name | **Mandatory** | KYC/AML | Min 1 char, letters only |
| | Previous Names | Recommended | AML | If changed in last 5 years |
| | Preferred Name | Optional | Client service | Any |
| | Date of Birth | **Mandatory** | KYC/AML | Age ≥18, valid date |
| | Place of Birth | **Mandatory** | AML (EDD) | Country + City |
| | Gender | Optional | None | Male/Female/Other/Prefer not to say |
| | Marital Status | Recommended | Fact-find | Single/Married/Civil Partner/Divorced/Widowed |
| | National Insurance Number | **Mandatory** (UK residents) | Tax/ID | Format: AA999999A |
| | Passport Number | Conditional | AML verification | If primary ID |
| | Nationality | **Mandatory** | AML/Tax | ISO country code |
| **CONTACT DETAILS** |
| | Primary Email | **Mandatory** | Communication/ID | RFC 5322 + verification |
| | Secondary Email | Optional | Backup | RFC 5322 |
| | Mobile Number | **Mandatory** | Communication/2FA | UK format + SMS verify |
| | Home Phone | Optional | Alternative contact | UK format |
| | Work Phone | Optional | Alternative contact | UK format |
| | Preferred Contact Method | **Mandatory** | GDPR preference | Email/Phone/Post |
| | Preferred Contact Time | Optional | Service | Morning/Afternoon/Evening |
| **ADDRESS INFORMATION** |
| | Current Address Line 1 | **Mandatory** | KYC/AML | Not PO Box |
| | Current Address Line 2 | Optional | Complete address | Any |
| | Current Town/City | **Mandatory** | KYC/AML | Letters, spaces, hyphens |
| | Current County | Optional | Complete address | Dropdown |
| | Current Postcode | **Mandatory** | KYC/AML | UK postcode validation |
| | Time at Current Address | **Mandatory** | AML | Years + Months |
| | Previous Address (if <3y) | **Conditional** | AML requirement | Same format as current |
| | Correspondence Address | Optional | If different | Same format |
| **TAX INFORMATION** |
| | UK Tax Resident | **Mandatory** | Tax reporting | Yes/No |
| | Other Tax Residencies | Conditional | CRS/FATCA | If non-UK or dual |
| | Tax ID Numbers | Conditional | Tax reporting | For each jurisdiction |
| | US Person | **Mandatory** | FATCA | Yes/No + explanation if yes |
| **EMPLOYMENT** |
| | Employment Status | **Mandatory** | Fact-find/AML | Employed/Self-employed/Retired/Unemployed/Student/Other |
| | Employer Name | Conditional | Source of funds | If employed/self-employed |
| | Job Title/