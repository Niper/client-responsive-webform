# FCA Compliance and Regulatory Requirements Analysis

**Agent:** legal_compliance_analyst
**Job:** Client Responsive Webform

---

# FCA Compliance and Regulatory Requirements Analysis
## Client Onboarding Web Form for UK Wealth Management

**Document Version:** 1.0  
**Date:** 2024  
**Classification:** Compliance Requirements Document  
**Applicable Regulations:** FCA Handbook, UK GDPR, Data Protection Act 2018, Money Laundering Regulations 2017

---

## EXECUTIVE SUMMARY

This document provides comprehensive compliance requirements for developing a client onboarding web form for UK wealth management firms. The analysis covers FCA regulatory obligations, UK GDPR requirements, anti-money laundering (AML) provisions, and data protection standards necessary for legal and compliant client data collection.

---

## 1. REGULATORY FRAMEWORK OVERVIEW

### 1.1 Applicable Regulations
- **FCA Handbook** - COBS (Conduct of Business Sourcebook)
- **UK GDPR** (General Data Protection Regulation)
- **Data Protection Act 2018**
- **Money Laundering, Terrorist Financing and Transfer of Funds Regulations 2017**
- **Senior Managers & Certification Regime (SM&CR)**
- **Consumer Duty** (effective July 2023)

### 1.2 Key Regulatory Principles
- Fair treatment of customers (TCF - Treating Customers Fairly)
- Know Your Customer (KYC)
- Suitability assessment
- Client classification and categorization
- Data minimization and purpose limitation
- Transparency and informed consent

---

## 2. MANDATORY DATA FIELDS

### 2.1 Personal Identification Information

#### **Required Fields:**
| Field Name | Validation Rules | FCA/Legal Basis | Retention Period |
|------------|------------------|-----------------|------------------|
| Full Legal Name | Min 2 chars, alphabetic + spaces/hyphens | COBS 9.2, MLR 2017 | 6 years post-relationship |
| Previous Names | Optional but recommended | MLR 2017 (AML) | 6 years post-relationship |
| Date of Birth | Format: DD/MM/YYYY, Age ≥18 | COBS 9.2, MLR 2017 | 6 years post-relationship |
| Nationality | Dropdown/multi-select | MLR 2017, CRS/FATCA | 6 years post-relationship |
| National Insurance Number | Format: XX123456X | AML verification | 6 years post-relationship |
| Passport/ID Number | Alphanumeric, conditional required | MLR 2017 | 6 years post-relationship |
| Country of Tax Residence | Multi-select allowed | FATCA/CRS compliance | 6 years post-relationship |

#### **Contact Information:**
| Field Name | Validation Rules | FCA/Legal Basis | Retention Period |
|------------|------------------|-----------------|------------------|
| Current Residential Address | Full address with postcode | COBS 9.2, MLR 2017 | 6 years post-relationship |
| Time at Address | Months/Years | MLR 2017 (AML) | 6 years post-relationship |
| Previous Address (if <3 years) | Conditional required | MLR 2017 | 6 years post-relationship |
| Email Address | RFC 5322 compliant | COBS 2.1 (communications) | 6 years post-relationship |
| Mobile Phone | UK format +44 validation | COBS 2.1 | 6 years post-relationship |
| Alternative Contact Number | Optional | COBS 2.1 | 6 years post-relationship |
| Preferred Contact Method | Radio buttons required | Consumer Duty | Duration of relationship |

### 2.2 Employment and Financial Status

#### **Required Fields:**
| Field Name | Validation Rules | FCA/Legal Basis | Retention Period |
|------------|------------------|-----------------|------------------|
| Employment Status | Dropdown: Employed/Self-employed/Retired/Unemployed/Student | COBS 9.2 (suitability) | 6 years post-relationship |
| Occupation/Job Title | Text field, required if employed | MLR 2017, COBS 9.2 | 6 years post-relationship |
| Employer Name | Text field, conditional | MLR 2017 | 6 years post-relationship |
| Industry Sector | Dropdown | PEP screening, MLR 2017 | 6 years post-relationship |
| Annual Income Range | Dropdown with ranges | COBS 9.2 (suitability) | 6 years post-relationship |
| Source of Wealth | Checkboxes/Text | MLR 2017 (AML) | 6 years post-relationship |
| Net Worth Range | Dropdown with ranges | COBS 9.2 (client classification) | 6 years post-relationship |

### 2.3 Client Classification Fields

**Regulatory Requirement:** COBS 3.5 - Classification of clients

| Field Name | Validation Rules | Purpose | Retention Period |
|------------|------------------|---------|------------------|
| Investment Experience | Years of experience | Professional client assessment | 6 years post-relationship |
| Qualification Status | Financial qualifications held | Professional client criteria | 6 years post-relationship |
| Portfolio Size | Value ranges | Client categorization | 6 years post-relationship |
| Transaction Frequency | Historical frequency | Appropriateness assessment | 6 years post-relationship |
| Professional Status Declaration | Yes/No with criteria | COBS 3.5.3R | 6 years post-relationship |

**Default Classification:** Retail Client (highest protection level)

### 2.4 Politically Exposed Person (PEP) Screening

**Mandatory Fields:**
- PEP Status Declaration (Yes/No)
- If Yes: Position/Role held
- If Yes: Country of position
- Family Member PEP Status
- Close Associate PEP Status

**Legal Basis:** MLR 2017, Regulation 35  
**Retention:** 6 years post-relationship

### 2.5 Fact-Find Information

#### **Financial Objectives (COBS 9.2 - Suitability):**
| Category | Required Fields | Validation |
|----------|----------------|------------|
| Investment Objectives | Multi-select: Capital Growth/Income/Capital Preservation/Inheritance Planning | Minimum 1 required |
| Investment Timeframe | Dropdown: <1 year/1-3 years/3-5 years/5-10 years/10+ years | Required |
| Risk Capacity | Calculated from financial position | Auto-calculated |
| Risk Tolerance | Questionnaire score (1-10 scale) | Required, minimum 5 questions |
| Attitude to Loss | Numeric/Percentage tolerance | Required |
| Investment Knowledge | Scale assessment | COBS 10A (appropriateness) |

#### **Assets and Liabilities:**
| Field Name | Required | Validation |
|------------|----------|------------|
| Cash Savings | Yes | Numeric, ≥0 |
| Investments (existing) | Yes | Numeric, ≥0 |
| Property Value | Yes | Numeric, ≥0 |
| Pension Values | Yes | Numeric, ≥0 |
| Other Assets | Optional | Numeric, ≥0 |
| Mortgage Balance | Yes | Numeric, ≥0 |
| Other Debts | Yes | Numeric, ≥0 |
| Monthly Expenditure | Yes | Numeric, >0 |

#### **Dependents and Circumstances:**
- Number of Dependents (Required)
- Marital Status (Required)
- Health Considerations affecting investment (Optional but recommended)
- Expected Significant Life Changes (Optional)

### 2.6 Anti-Money Laundering (AML) Requirements

**Source of Funds Declaration:**
- Primary source of investment funds (Required)
- Secondary sources (if applicable)
- Expected account activity level (Required)
- Purpose of account opening (Required)

**Legal Basis:** MLR 2017, Regulations 27-28  
**Validation:** Free text with character minimum (50 chars)

---

## 3. CONSENT REQUIREMENTS

### 3.1 Mandatory Consents

#### **Data Processing Consent (UK GDPR Article 6 & 9):**

**Primary Legal Basis:** Legitimate Interest + Contractual Necessity

**Required Consent Text:**
```
☐ I consent to [Firm Name] collecting, processing, and storing my personal 
information for the purposes of:
  - Providing wealth management and financial advisory services
  - Conducting suitability assessments and ongoing reviews
  - Meeting regulatory obligations under FCA rules
  - Preventing fraud and money laundering

I understand that:
  - This consent is required to provide the requested services
  - My data will be processed in accordance with UK GDPR and the firm's 
    Privacy Policy
  - I have the right to withdraw consent at any time, which may affect 
    the firm's ability to provide services
  - The firm will retain my data for 6 years after the relationship ends 
    as required by FCA regulations

Date: [Auto-populated]
```

**Mandatory:** Yes (Checkbox + Timestamp)  
**Withdrawal Process:** Must be provided

#### **Special Category Data Consent (UK GDPR Article 9):**

```
☐ I consent to [Firm Name] processing special category data (including 
health information, if provided) where this is relevant to assessing 
my financial needs and suitability of advice.

This is optional - I can choose not to provide this information, though 
it may limit the comprehensiveness of advice.
```

**Mandatory:** No, but must be presented if collecting health data  
**Explicit Consent Required:** Yes

### 3.2 Optional Marketing Consents

**Requirement:** Must be separate from service provision consents (ICO guidance)

```
☐ I consent to receive marketing communications about products and services 
that may be of interest to me via:
  ☐ Email
  ☐ SMS
  ☐ Post
  ☐ Telephone

I understand I can withdraw this consent at any time.
```

**Mandatory:** No  
**Granular Options:** Yes (per channel)  
**Pre-ticked:** Not permitted

### 3.3 Third-Party Data Sharing Consent

```
☐ I consent to [Firm Name] sharing my information with:
  - Third-party service providers (for platform access, custody services)
  - Professional advisors (accountants, solicitors) where relevant
  - Regulatory authorities as required by law

A full list of data processors is available in our Privacy Policy.
```

**Mandatory:** Yes for service provision  
**Link to Privacy Policy:** Required

### 3.4 Electronic Communications Consent

```
☐ I consent to receive contractual documentation, including suitability 
reports, valuations, and regulatory communications via electronic means.

I understand I can request paper copies at any time.
```

**Mandatory:** No, but recommended  
**Legal Basis:** COBS 2.1.9R - Client's consent required

### 3.5 Automated Decision-Making Notice

If using automated risk profiling or robo-advice elements:

```
☐ I acknowledge that [Firm Name] may use automated processing to:
  - Assess my risk profile
  - Generate preliminary investment recommendations
  - Monitor portfolio performance

I understand that:
  - I have the right to human intervention in decision-making
  - I can request an explanation of automated decisions
  - Final investment decisions require human advisor review
```

**Mandatory:** If automated processing used (UK GDPR Article 22)

---

## 4. DATA RETENTION POLICIES

### 4.1 Retention Periods by Data Type

| Data Category | Retention Period | Legal Basis | Destruction Method |
|---------------|------------------|-------------|-------------------|
| Client identification data | 6 years from end of relationship | FCA Handbook, SYSC 9.1 | Secure deletion/shredding |
| Fact-find information | 6 years from end of relationship | COBS 9.2 | Secure deletion |
| Suitability assessments | Indefinite (recommended 15 years) | COBS 9.4.7R | Secure archival |
| Communications records | 6 years from date | COBS 11.8 | Secure deletion |
| Complaints records | 6 years from complaint resolution | DISP 1.9 | Secure archival |
| AML documentation | 6 years from end of relationship | MLR 2017, Reg 40 | Secure deletion |
| Marketing consent records | Until withdrawal + 3 years | ICO guidance | Secure deletion |
| Consent audit trail | Duration of processing + 3 years | UK GDPR accountability | Secure archival |

### 4.2 Retention Schedule Implementation

**Technical Requirements:**
- Automated retention scheduling system
- Flagging for review at retention expiry
- Secure deletion protocols (data wiping standards)
- Audit logging of all deletions
- Backup retention alignment

**Process Requirements:**
- Annual review of retention schedule
- Data Protection Officer oversight
- Client notification before destruction (optional, recommended)
- Exception handling for ongoing legal matters

---

## 5. RIGHT TO BE FORGOTTEN (ERASURE) PROVISIONS

### 5.1 UK GDPR Article 17 Compliance

**Client Rights:**
Clients may request erasure of their personal data under certain circumstances.

**Exemptions Applicable to Wealth Management:**

1. **Regulatory Obligation** (Article 17(3)(b))
   - Cannot erase data required for 6-year FCA retention
   - AML records must be retained per MLR 2017
   - Cannot erase if prevents compliance with legal obligations

2. **Legal Claims** (Article 17(3)(e))
   - Data required for establishment, exercise, or defense of legal claims
   - Complaints under investigation
   - Ongoing litigation

3. **Public Interest/Official Authority** (Article 17(3)(b))
   - Regulatory investigations
   - FCA information requests

### 5.2 Erasure Request Handling Process

**Timeline:** Response within 1 month (extendable to 3 months for complex requests)

**Workflow for Web Form:**
```
1. Request Receipt
   ↓
2. Identity Verification (prevent fraudulent requests)
   ↓
3. Legal Exemption Assessment
   ↓
4. Partial vs. Full Erasure Determination
   ↓
5. Client Communication (what can/cannot be deleted and why)
   ↓
6. Execute Erasure (if applicable)
   ↓
7. Third-Party Notification (if data shared)
   ↓
8. Confirmation to Client
   ↓
9. Audit Log Entry
```

**Technical Requirements:**
- Erasure request function in web form
- Identity verification mechanism
- Automated exemption flagging
- Audit trail of all erasure activities
- Third-party data processor notification system

### 5.3 Restricted Processing Alternative

When full erasure is not possible:

**Offer Option:**
- Restriction of processing (UK GDPR Article 18)
- Data retention in archive-only state
- No active processing except for storage
- Available for legal claims only

**Communication Template Required:**
```
"While we cannot fully delete your data due to [regulatory obligation], 
we can restrict its processing. This means:
- Your data will be securely stored but not actively used
- It will only be accessed if required by law or regulatory authority
- It will be deleted at the end of the mandatory retention period
- You will be notified before any resumption of processing"
```

---

## 6. UK GDPR COMPLIANCE MEASURES

### 6.1 Lawful Basis for Processing

**Primary Bases for Wealth Management Web Form:**

| Processing Activity | Lawful Basis | GDPR Article |
|---------------------|--------------|--------------|
| Basic client onboarding | Contractual necessity | Article 6(1)(b) |
| AML/KYC checks | Legal obligation | Article 6(1)(c) |
| Suitability assessment | Legitimate interest + Contract | Article 6(1)(b)(f) |
| Marketing | Consent | Article 6(1)(a) |
| Health data (if collected) | Explicit consent | Article 9(2)(a) |
| Regulatory reporting | Legal obligation | Article 6(1)(c) |

**Documentation Required:**
- Legitimate Interest Assessment (LIA) for Article 6(1)(f) processing
- Record of Processing Activities (ROPA)
- Data Protection Impact Assessment (see Section 7)

### 6.2 Data Minimization Requirements

**Principle:** Only collect data necessary for specified purposes (Article 5(1)(c))

**Implementation:**
- All fields must have documented necessity justification
- Optional fields clearly marked
- Progressive disclosure (multi-step form)
- Conditional logic (only show relevant fields)
- Regular review of collected fields (annual minimum)

**Examples:**
- ✓ **Necessary:** National Insurance Number (AML requirement)
- ✗ **Unnecessary:** Dietary preferences (unless relevant to hospitality events - requires separate consent)
- ? **Conditional:** Number of children (necessary for estate planning, not for general investment)

### 6.3 Privacy by Design and Default

**Technical Measures:**
- Default to minimum data collection
- Privacy-preserving form design
- Encryption in transit (TLS 1.3 minimum)
- Encryption at rest (AES-256 minimum)
- Access controls and authentication
- Session timeout (15 minutes recommended)
- Progressive form saving (encrypted)

**Organizational Measures:**
- Privacy review in development lifecycle
- Data Protection Officer consultation
- Privacy impact assessment
- Regular privacy audits
- Staff training on data protection

### 6.4 Transparency Requirements

**Privacy Information to Provide (Article 13):**

Must be provided BEFORE data collection:

1. **Identity of Controller:** Firm name, registration details
2. **Data Protection Officer Contact:** Email and address
3. **Purposes of Processing:** Specific purposes for each data category
4. **Lawful Basis:** Which GDPR article applies
5. **Recipients:** Who will receive the data
6. **International Transfers:** If data leaves UK (requires additional safeguards)
7. **Retention Periods:** How long data will be kept
8. **Individual Rights:** Full list of rights under UK GDPR
9. **Right to Withdraw Consent:** How and effect of withdrawal
10. **Right to Complain:** ICO contact details
11. **Contractual Requirement:** Whether provision of data is mandatory
12. **Automated Decision-Making:** If used, logic and consequences

**Implementation:**
- Privacy Notice available before form completion
- Link on every page of form
- Just-in-time privacy notices (contextual pop-ups)
- Layered approach (short notice + full policy)
- Clear, plain language (no legal jargon)

### 6.5 Individual Rights Mechanisms

**Rights to Facilitate:**

| Right | Implementation in Web Form | Response Time |
|-------|---------------------------|---------------|
| Right to Access (Article 15) | Download my data function | 1 month |
| Right to Rectification (Article 16) | Edit profile function, update requests | 1 month |
| Right to Erasure (Article 17) | Deletion request function | 1 month |
| Right to Restrict Processing (Article 18) | Restriction request mechanism | 1 month |
| Right to Data Portability (Article 20) | Structured data export (JSON/CSV) | 1 month |
| Right to Object (Article 21) | Objection to processing form | Immediate for marketing |

**Technical Requirements:**
- Self-service portal for exercising rights
- Secure identity verification
- Automated data export functionality
- Audit trail of all rights requests
- Workflow for manual review where needed

### 6.6 Data Security Measures (Article 32)

**Required Security Controls:**

**Technical:**
- Multi-factor authentication (MFA)
- Role-based access control (RBAC)
- Encryption: TLS 1.3+ (transit), AES-256 (rest)
- Secure session management
- Input validation and sanitization (prevent injection attacks)
- Regular vulnerability scanning
- Penetration testing (annual minimum)
- Web Application Firewall (WAF)
- DDoS protection
- Secure backup with encryption
- Pseudonymization where possible
- Database encryption

**Organizational:**
- Access logging and monitoring
- Incident response plan
- Breach notification procedures (72 hours to ICO)
- Staff security training
- Vendor security assessments
- Data processing agreements with third parties
- Regular security audits
- Disaster recovery plan
- Business continuity plan

### 6.7 International Data Transfers

**If using non-UK hosting or processors:**

**Requirement:** Adequate safeguards for international transfers (Chapter V)

**Options:**
1. **Adequacy Decision:** EU/EEA countries have adequacy
2. **Standard Contractual Clauses (SCCs):** Use UK International Data Transfer Agreement
3. **Binding Corporate Rules:** For intra-group transfers

**Implementation:**
- Identify all international data flows
- Map data processor locations
- Implement appropriate transfer mechanisms
- Document in privacy notice
- Regular review of adequacy decisions

**Recommendation:** Use UK or EU-based hosting for simplicity

---

## 7. DATA PROTECTION IMPACT ASSESSMENT (DPIA)

### 7.1 DPIA Requirement Triggers

**UK GDPR Article 35 - DPIA Required When:**
- ✓ Systematic and extensive profiling (risk profiling qualifies)
- ✓ Large-scale processing of special category data (if collecting health data)
- ✓ Systematic monitoring (ongoing client monitoring)
- ? New technology use (depends on implementation)

**Recommendation:** **DPIA IS REQUIRED** for this web form project

### 7.2 DPIA Process

**Stage 1: Necessity and Proportionality Assessment**
- Justify data collection
- Consider alternatives
- Demonstrate benefits outweigh risks

**Stage 2: Risk Identification**
| Risk Category | Specific Risks | Likelihood | Impact | Mitigation |
|---------------|----------------|------------|---------|------------|
| Unauthorized Access | Data breach via web vulnerabilities | Medium | High | WAF, encryption, MFA, penetration testing |
| Data Loss | Server failure, deletion error | Low | High | Encrypted backups, RAID, disaster recovery |
| Identity Theft | Stolen credentials, session hijacking | Medium | High | MFA, session timeout, secure cookies |
| Regulatory Non-compliance | Missing mandatory fields, retention errors | Medium | High | Compliance validation, audit trails |
| Third-party Breach | Processor security failure | Low | High | Vendor assessments, DPA requirements |
| Insider Threat | Staff unauthorized access | Low | Medium | RBAC, access logging, background checks |

**Stage 3: Risk Mitigation Measures**
- Document all security controls
- Assign responsibility for implementation
- Set timelines for deployment
- Define residual risk acceptance criteria

**Stage 4: DPO and Stakeholder Consultation**
- Data Protection Officer review
- Legal counsel review
- Information security team input
- Business stakeholder approval

**Stage 5: Approval and Sign-off**
- Senior management approval
- Document decision-making rationale
- Define review triggers and schedule

**Stage 6: Ongoing Review**
- Annual review minimum
- Review on significant system changes
- Review on new data processing activities
- Review on security incidents

### 7.3 DPIA Documentation Requirements

**Must Include:**
- Description of processing operations
- Assessment of necessity and proportionality
- Assessment of risks to individual rights
- Measures to address risks
- Safeguards and security measures
- DPO opinion
- Approval signatures

**Deliverable:** Completed DPIA document before web form goes live

---

## 8. FIELD VALIDATION RULES

### 8.1 Data Quality Standards

**Purpose:** Ensure data accuracy for regulatory compliance (UK GDPR Article 5(1)(d))

### 8.2 Validation Rules by Field Type

#### **Name Fields:**
```
Full Legal Name:
- Minimum: 2 characters
- Maximum: 100 characters
- Allowed: Letters, spaces, hyphens, apostrophes, accented characters
- Pattern: ^[A-Za-zÀ-ÿ\s'-]{2,100}$
- Required: Yes
- Error message: "Please enter your full legal name as it appears on official documents"
```

#### **Date of Birth:**
```
- Format: DD/MM/YYYY
- Validation: Valid date
- Age check: ≥18 years (wealth management restriction)
- Maximum age: 120 years (data quality check)
- Required: Yes
- Error message: "You must be 18 or over to use our services"
```

#### **National Insurance Number:**
```
- Format: XX123456X
- Pattern: ^[A-CEGHJ-PR-TW-Z]{1}[A-CEGHJ-NPR-TW-Z]{1}[0-9]{6}[A-D]{1}$
- Required: Yes (AML requirement)
- Error message: "Please enter a valid National Insurance number (e.g., QQ123456C)"
```

#### **Email Address:**
```
- Format: RFC 5322 compliant
- Pattern: ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$
- Maximum: 254 characters
- Required: Yes
- Verification: Email confirmation link sent
- Error message: "Please enter a valid email address"
```

#### **UK Phone Number:**
```
- Format: +44 or 0 prefix
- Pattern: ^(\+44\s?|0)(\d{10}|\d{4}\s?\d{6}|\d{3}\s?\d{3}\s?\d{4})$
- Allow spaces and hyphens
- Strip to numeric for storage
- Required: Yes
- Error message: "Please enter a valid UK phone number"
```

#### **UK Postcode:**
```
- Format: UK postcode standard
- Pattern: ^[A-Z]{1,2}\d[A-Z\d]?\s?\d[A-Z]{2}$
- Case insensitive input, store uppercase
- Required: Yes
- Validation: Check against PAF (optional but recommended)
- Error message: "Please enter a valid UK postcode"
```

#### **Financial Values:**
```
Income/Asset Fields:
- Type: Numeric
- Minimum: 0
- Maximum: 999,999,999,999
- Decimal places: 2
- Format: Currency (£)
- Allow: Numbers, comma thousands separators
- Strip: £, commas before storage
- Required: Yes for mandatory fields
- Error message: "Please enter a valid amount"
```

#### **Risk Tolerance Score:**
```
- Type: Integer
- Minimum: 1
- Maximum: 10
- Required: Yes
- Dependent validation: Must align with questionnaire responses
- Error message: "Please complete the risk questionnaire"
```

### 8.3 Cross-field Validation Rules

```
Address History:
IF time_at_current_address < 3 years
THEN previous_address = REQUIRED
```

```
Employment Details:
IF employment_status IN ['Employed', 'Self-employed']
THEN occupation AND employer_name = REQUIRED
```

```
PEP Disclosure:
IF pep_status = 'Yes'
THEN pep_position AND pep_country = REQUIRED
```

```
Investment Experience:
IF professional_client_request = 'Yes'
THEN investment_qualifications OR large_portfolio_evidence = REQUIRED
```

```
Consent Dependencies:
IF special_category_data_provided = TRUE
THEN explicit_consent_special_data = REQUIRED
```

### 8.4 Real-time Validation Requirements

**User Experience Standards:**
- Validate on field blur (after user leaves field)
- Display inline error messages
- Use clear, helpful error text
- Highlight invalid fields visually
- Prevent form submission if validation errors exist
- Provide validation summary at top of form
- Retain valid data if user navigates back

**Accessibility:**
- ARIA labels for error messages
- Screen reader announcements for errors
- Keyboard navigation support
- WCAG 2.1 AA compliance minimum

---

## 9. COMPLIANCE POLICIES AND PROCEDURES

### 9.1 Client Onboarding Policy

**Purpose:** Ensure compliant client data collection and classification

**Policy Statements:**

1. **Client Classification (COBS 3.5)**
   - All new clients default to "Retail Client" classification
   - Professional client election requires explicit request and verification
   - Classification must be confirmed in writing
   - Annual review of client classification required

2. **Suitability Assessment (COBS 9.2)**
   - Fact-find must be completed before providing advice
   - Information must be adequate for suitability determination
   - Gaps in information must be documented and addressed
   - Suitability report must be provided before transaction

3. **Know Your Client (AML)**
   - Identity verification required before account activation
   - Source of wealth documentation required for large investments (£50,000+)
   - Enhanced due diligence for PEPs
   - Ongoing monitoring of client activity

4. **Data Quality**
   - All mandatory fields must be completed
   - Validation errors must be resolved before submission
   - Client confirmation of accuracy required
   - Update requests processed within 5 business days

### 9.2 Data Protection and Privacy Policy

**Policy Elements:**

1. **Data Collection Principles**
   - Collect only necessary data
   - Obtain informed consent before collection
   - Provide privacy information before collection
   - Use secure collection methods (HTTPS, encryption)

2. **Data Storage and Security**
   - Encrypt data at rest and in transit
   - Implement access controls based on need-to-know
   - Log all data access and modifications
   - Regular security assessments and updates

3. **Data Sharing**
   - Share data only with consent or legal obligation
   - Data Processing Agreements with all third parties
   - Document all data sharing arrangements
   - Notify clients of data processors

4. **Data Subject Rights**
   - Process rights requests within legal timelines
   - Verify identity before fulfilling requests
   - Document all rights requests and responses
   - Provide reasons for refusal if applicable

5. **Data Breach Response**
   - Detect and contain breaches immediately
   - Assess breach severity and risk to individuals
   - Notify ICO within 72 hours if high risk
   - Notify affected individuals without undue delay
   - Document all breaches regardless of reporting requirement

### 9.3 Record Keeping and Retention Policy

**Policy Statements:**

1. **Retention Schedule Compliance**
   - Adhere to 6-year minimum retention for client records (FCA)
   - Indefinite retention of suitability reports (recommended)
   - Automated retention enforcement
   - Annual review of retention schedule

2. **Record Quality**
   - Maintain complete and accurate records
   - Records must be readily accessible
   - Format must enable retrieval and analysis
   - Backup and disaster recovery procedures

3. **Secure Destruction**
   - Destruction only after retention period expires
   - Secure deletion methods (data wiping standards)
   - Certificate of destruction for physical records
   - Audit trail of all destruction activities

### 9.4 Access Control and Authorization Policy

**Policy Elements:**

1. **User Access Levels**
   - Role-Based Access Control (RBAC)
   - Principle of least privilege
   - Segregation of duties
   - Regular access reviews (quarterly)

**Access Levels for Web Form System:**
| Role | Access Rights | Data Visibility |
|------|--------------|-----------------|
| Client | Own data only | Full personal data |
| Adviser | Assigned clients | Full client data |
| Compliance Officer | All clients (read-only) | Full data + audit logs |
| System Administrator | System configuration | No client data unless justified |
| Data Protection Officer | All data | Full data + processing records |

2. **Authentication Requirements**
   - Multi-factor authentication for staff
   - Strong password policy (12+ chars, complexity)
   - Password expiry (90 days)
   - Account lockout after failed attempts
   - Session timeout (15 minutes inactivity)

3. **Monitoring and Logging**
   - Log all data access and modifications
   - Alert on unusual access patterns
   - Regular log reviews
   - Retain logs for 6 years

### 9.5 Third-Party Data Processor Management

**Policy Requirements:**

1. **Vendor Selection**
   - Security assessment before engagement
   - Privacy and security certifications (ISO 27001, Cyber Essentials Plus)
   - Financial stability assessment
   - References and track record review

2. **Contractual Requirements**
   - Data Processing Agreement (DPA) required
   - UK GDPR Article 28 compliance
   - Security obligations specified
   - Audit rights included
   - Sub-processor approval requirements
   - Data breach notification obligations (24 hours)
   - Deletion/return of data on termination

3. **Ongoing Management**
   - Annual security reassessments
   - Performance monitoring
   - Incident reporting and management
   - Regular communication and updates

### 9.6 Training and Awareness Policy

**Requirements:**

1. **Staff Training**
   -