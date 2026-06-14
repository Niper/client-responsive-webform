# Define FCA Compliance Requirements and Data Specification

**Agent:** business_analyst
**Job:** Client Responsive Webform

---

# FCA Compliance Requirements and Data Specification
## Client Responsive Webform - Requirements Specification Document

**Document Version:** 1.0  
**Date:** 2024  
**Project:** Client Responsive Webform for UK Wealth Management  
**Business Analyst:** [Agent]  
**Status:** For Review

---

## 1. EXECUTIVE SUMMARY

This document provides a comprehensive requirements specification for a client onboarding webform designed for UK wealth management firms. The specification ensures full compliance with FCA regulations, GDPR, and Money Laundering Regulations 2017 (MLR 2017), while delivering an optimal user experience for client data collection.

**Key Objectives:**
- Ensure FCA regulatory compliance for client onboarding
- Implement robust AML/KYC procedures
- Maintain GDPR compliance and data protection standards
- Create a user-friendly, multi-step form experience
- Enable comprehensive fact-finding for suitability assessments

---

## 2. REGULATORY FRAMEWORK

### 2.1 Applicable Regulations

| Regulation | Reference | Key Requirements |
|------------|-----------|------------------|
| FCA Conduct of Business Sourcebook (COBS) | COBS 9 | Suitability assessment and reporting |
| FCA Senior Management Arrangements, Systems and Controls (SYSC) | SYSC 3 & 6 | Systems, controls, and record-keeping |
| Money Laundering Regulations 2017 | MLR 2017 | Customer due diligence (CDD) |
| General Data Protection Regulation | GDPR | Data processing, consent, and retention |
| Data Protection Act 2018 | DPA 2018 | UK-specific data protection |
| FCA Principles for Businesses | Principle 3 & 9 | Management and control; customer relationships |

### 2.2 Regulatory Compliance Mapping

**FCA COBS 9.2 - Assessing Suitability:**
- Must obtain necessary information about client's knowledge and experience
- Must obtain information about client's financial situation
- Must obtain information about client's investment objectives

**MLR 2017 Customer Due Diligence:**
- Identity verification requirements
- Source of wealth and funds verification
- Politically Exposed Person (PEP) screening
- Enhanced due diligence where applicable

**GDPR Requirements:**
- Explicit consent for data processing
- Right to be forgotten
- Data portability
- Privacy by design

---

## 3. STAKEHOLDER ANALYSIS

### 3.1 Primary Stakeholders

| Stakeholder | Role | Needs | Impact Level |
|-------------|------|-------|--------------|
| Prospective Clients | Form Users | Simple, clear, secure form; understanding of data usage | HIGH |
| Wealth Advisors | Data Consumers | Complete, accurate client information; efficient onboarding | HIGH |
| Compliance Officers | Regulators | Full regulatory compliance; audit trail; documentation | HIGH |
| IT/Security Team | System Owners | Secure data storage; system reliability; maintenance | MEDIUM |
| Senior Management | Decision Makers | Risk mitigation; business efficiency; ROI | MEDIUM |
| FCA | External Regulator | Regulatory compliance; consumer protection | HIGH |
| Legal Team | Advisors | Legal compliance; liability management | MEDIUM |

### 3.2 User Personas

**Persona 1: Sarah Thompson - Prospective Client**
- Age: 45, Senior Marketing Director
- Tech-savvy, values privacy
- Looking to invest £250,000
- Needs: Clear guidance, security reassurance, mobile-friendly

**Persona 2: James Mitchell - Wealth Advisor**
- Age: 38, Chartered Financial Planner
- Reviews 5-10 new client applications weekly
- Needs: Complete information, reduced back-and-forth, integration with CRM

**Persona 3: Patricia Chen - Compliance Officer**
- Age: 52, Head of Compliance
- Responsible for FCA adherence
- Needs: Audit trails, regulatory documentation, risk flagging

---

## 4. COMPREHENSIVE DATA SCHEMA

### 4.1 Section 1: Personal Details (Identity Verification)

#### 4.1.1 Basic Identity Information

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Title | Dropdown | Yes | Mr, Mrs, Ms, Miss, Dr, Prof, Rev, Other | MLR 2017 - CDD | - | Mr |
| First Name(s) | Text | Yes | Alpha + spaces, hyphens; min 1 char | MLR 2017 - CDD | 100 | John David |
| Middle Name(s) | Text | No | Alpha + spaces, hyphens | MLR 2017 - CDD | 100 | Michael |
| Surname | Text | Yes | Alpha + spaces, hyphens, apostrophes; min 1 char | MLR 2017 - CDD | 100 | Smith-Jones |
| Previous Names | Text | No | Alpha + spaces; comma-separated | MLR 2017 - CDD | 200 | Jones |
| Date of Birth | Date | Yes | Format: DD/MM/YYYY; Age 18-120; Past date | MLR 2017 - CDD | - | 15/03/1978 |
| Age Verification | Checkbox | Yes | Must confirm 18+ | FCA - Client categorization | - | ✓ |
| Place of Birth | Text | Yes | City, Country format | MLR 2017 - EDD | 100 | London, UK |
| Nationality | Dropdown | Yes | ISO country codes; multi-select | MLR 2017 - CDD | - | British |
| Second Nationality | Dropdown | No | ISO country codes | MLR 2017 - CDD | - | Irish |
| National Insurance Number | Text | Yes | Format: XX 12 34 56 X; unique validation | MLR 2017 - CDD | 13 | AB 12 34 56 C |
| Passport Number | Text | Conditional* | Alphanumeric; format validation | MLR 2017 - CDD | 20 | 123456789 |
| Passport Country of Issue | Dropdown | Conditional* | ISO country codes | MLR 2017 - CDD | - | United Kingdom |
| Driving License Number | Text | Conditional* | UK format validation | MLR 2017 - CDD | 20 | SMITH123456ABC78 |

*Conditional: At least one government-issued ID required beyond NI Number

**Business Rules:**
- BR-001: Minimum age must be 18 years
- BR-002: Date of birth must result in current age < 120 years
- BR-003: At least one form of photo ID (Passport or Driving License) required for AML compliance
- BR-004: NI Number must be validated against checksum algorithm
- BR-005: Duplicate NI Numbers must trigger system alert for fraud prevention

#### 4.1.2 Contact Information

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Address Line 1 | Text | Yes | Min 3 chars | MLR 2017 - CDD | 100 | 123 High Street |
| Address Line 2 | Text | No | - | MLR 2017 - CDD | 100 | Apartment 4B |
| Town/City | Text | Yes | Alpha + spaces | MLR 2017 - CDD | 50 | London |
| County | Text | No | Alpha + spaces | MLR 2017 - CDD | 50 | Greater London |
| Postcode | Text | Yes | UK postcode format validation | MLR 2017 - CDD | 10 | SW1A 1AA |
| Country | Dropdown | Yes | Default: United Kingdom | MLR 2017 - CDD | - | United Kingdom |
| Residential Status | Dropdown | Yes | Owner, Renting, Living with Family, Other | COBS 9 - Fact Find | - | Owner |
| Time at Address | Dropdown | Yes | <6 months, 6-12 months, 1-3 years, 3+ years | MLR 2017 - CDD | - | 3+ years |
| Previous Address | Text | Conditional** | Full address if < 3 years at current | MLR 2017 - CDD | 300 | - |
| Primary Phone | Tel | Yes | UK/International format; +44 validation | COBS 2.1 - Communications | 20 | +44 20 7123 4567 |
| Mobile Phone | Tel | Yes | UK mobile format preferred | COBS 2.1 - Communications | 20 | +44 7700 900123 |
| Email Address | Email | Yes | RFC 5322 validation; confirmation field | COBS 2.1 - Communications | 100 | john.smith@email.com |
| Email Confirmation | Email | Yes | Must match Email Address | COBS 2.1 - Communications | 100 | john.smith@email.com |
| Preferred Contact Method | Radio | Yes | Email, Phone, Post | COBS 2.1 - Client communications | - | Email |
| Preferred Contact Time | Dropdown | No | Morning, Afternoon, Evening, Anytime | COBS 2.1 - Client communications | - | Afternoon |

**Conditional: Previous address mandatory if time at current address < 3 years

**Business Rules:**
- BR-006: Postcode validation against Royal Mail PAF database
- BR-007: Address history for minimum 3 years required for AML compliance
- BR-008: Email must be unique in system
- BR-009: At least one valid phone number required
- BR-010: International addresses must include country-specific validation

#### 4.1.3 Tax and Residency Status

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| UK Tax Resident | Radio | Yes | Yes/No | MLR 2017 - CDD | - | Yes |
| Tax Residency Country | Dropdown | Yes | ISO country codes; multi-select if multiple | FATCA/CRS Compliance | - | United Kingdom |
| Tax Identification Number (TIN) | Text | Conditional | Format varies by country | FATCA/CRS Compliance | 30 | 1234567890 |
| US Person Status | Radio | Yes | Yes/No | FATCA Compliance | - | No |
| US TIN/SSN | Text | Conditional | If US Person = Yes | FATCA Compliance | 20 | - |
| Other Tax Residencies | Dropdown | No | Multi-select ISO countries | CRS Compliance | - | - |
| Politically Exposed Person (PEP) | Radio | Yes | Yes/No | MLR 2017 Reg 35 | - | No |
| PEP Relationship | Dropdown | Conditional | Self, Family Member, Close Associate | MLR 2017 Reg 35 | - | - |
| PEP Details | Textarea | Conditional | If PEP = Yes; min 50 chars | MLR 2017 Reg 35 | 500 | - |

**Business Rules:**
- BR-011: If US Person = Yes, additional FATCA compliance fields required
- BR-012: If PEP = Yes, enhanced due diligence workflow triggered
- BR-013: PEP status requires senior management approval before account opening
- BR-014: Multiple tax residencies require additional documentation

---

### 4.2 Section 2: Employment and Financial Situation

#### 4.2.1 Employment Details

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Employment Status | Dropdown | Yes | Employed, Self-Employed, Retired, Unemployed, Student, Homemaker | COBS 9.2 - Suitability | - | Employed |
| Occupation | Text | Conditional | If employed/self-employed; min 2 chars | MLR 2017 - CDD | 100 | Marketing Director |
| Industry Sector | Dropdown | Conditional | Standard industry classifications | MLR 2017 - CDD | - | Marketing & Advertising |
| Employer Name | Text | Conditional | If employed; min 2 chars | MLR 2017 - CDD | 100 | ABC Ltd |
| Employer Address | Textarea | Conditional | If employed | MLR 2017 - CDD | 200 | - |
| Employment Duration | Dropdown | Conditional | <1 year, 1-3 years, 3-5 years, 5+ years | COBS 9.2 - Income stability | - | 5+ years |
| Retirement Date | Date | Conditional | If employed; future date | COBS 9.2 - Planning horizon | - | 31/03/2038 |
| Business Ownership | Radio | Yes | Yes/No | MLR 2017 - CDD | - | No |
| Business Details | Textarea | Conditional | If Yes to business ownership | MLR 2017 - CDD | 300 | - |

**Business Rules:**
- BR-015: Employment details mandatory unless retired or unemployed
- BR-016: Self-employed requires additional business documentation
- BR-017: Retirement date must be realistic based on age (minimum age 50)

#### 4.2.2 Income and Assets

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Annual Gross Income | Currency | Yes | GBP; min £0; max £99,999,999 | COBS 9.2 - Financial situation | - | £85,000 |
| Income Source | Dropdown | Yes | Salary, Business Profits, Pension, Investments, Rental, Other | COBS 9.2 / MLR 2017 | - | Salary |
| Additional Income Sources | Multi-checkbox | No | Multiple selections allowed | COBS 9.2 - Financial situation | - | Investments, Rental |
| Additional Income Amount | Currency | Conditional | If additional sources selected | COBS 9.2 - Financial situation | - | £15,000 |
| Spouse/Partner Income | Currency | No | GBP; min £0 | COBS 9.2 - Household finances | - | £45,000 |
| Total Household Income | Currency | Auto-calc | Sum of all income sources | COBS 9.2 - Financial situation | - | £145,000 |
| Net Worth | Currency | Yes | GBP; can be negative | COBS 9.2 - Financial situation | - | £450,000 |
| Liquid Assets | Currency | Yes | GBP; min £0; ≤ Net Worth | COBS 9.2 - Available capital | - | £75,000 |
| Property Value | Currency | No | GBP; min £0 | COBS 9.2 - Asset assessment | - | £500,000 |
| Property Outstanding Mortgage | Currency | Conditional | If property value > 0 | COBS 9.2 - Liability assessment | - | £250,000 |
| Investment Portfolio Value | Currency | No | GBP; min £0 | COBS 9.2 - Existing investments | - | £150,000 |
| Pension Value | Currency | No | GBP; min £0 | COBS 9.2 - Retirement planning | - | £200,000 |
| Other Assets | Currency | No | GBP; min £0 | COBS 9.2 - Asset assessment | - | £25,000 |
| Other Assets Description | Textarea | Conditional | If Other Assets > £10,000 | COBS 9.2 - Asset assessment | 200 | Classic car collection |

**Business Rules:**
- BR-018: Total Household Income auto-calculated and displayed
- BR-019: Liquid Assets cannot exceed Net Worth
- BR-020: If investment amount > £100k, source of funds documentation required
- BR-021: Net Worth validation: sum of assets minus liabilities should approximate stated net worth (±10% tolerance, flag for review if exceeded)

#### 4.2.3 Liabilities and Commitments

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Outstanding Mortgage | Currency | No | GBP; min £0 | COBS 9.2 - Liabilities | - | £250,000 |
| Credit Cards/Store Cards Debt | Currency | No | GBP; min £0 | COBS 9.2 - Liabilities | - | £2,500 |
| Personal Loans | Currency | No | GBP; min £0 | COBS 9.2 - Liabilities | - | £0 |
| Other Debts | Currency | No | GBP; min £0 | COBS 9.2 - Liabilities | - | £0 |
| Total Monthly Commitments | Currency | Yes | GBP; min £0 | COBS 9.2 - Cash flow | - | £3,500 |
| Financial Dependents | Number | Yes | Integer; 0-20 | COBS 9.2 - Family situation | - | 2 |
| Dependent Details | Textarea | Conditional | If dependents > 0 | COBS 9.2 - Family obligations | 200 | 2 children, ages 12 and 15 |

**Business Rules:**
- BR-022: Total debt should not exceed 80% of gross annual income (advisory warning if exceeded)
- BR-023: Monthly commitments reasonableness check against income
- BR-024: High debt-to-income ratio (>50%) triggers suitability concerns flag

---

### 4.3 Section 3: Investment Profile and Objectives

#### 4.3.1 Investment Knowledge and Experience

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Investment Knowledge Level | Radio | Yes | None, Basic, Good, Advanced | COBS 9.2.1R - Knowledge & experience | - | Good |
| Previous Investment Experience | Multi-checkbox | Yes | Stocks, Bonds, Funds, ETFs, Property, Alternative, None | COBS 9.2.1R - Knowledge & experience | - | Stocks, Funds |
| Years of Investment Experience | Dropdown | Yes | None, <1, 1-3, 3-5, 5-10, 10+ | COBS 9.2.1R - Knowledge & experience | - | 5-10 |
| Investment Qualifications | Multi-checkbox | No | CFA, CISI, IMC, CeMAP, Other Financial Qual, None | COBS 9.2.1R - Knowledge & experience | - | None |
| Trading Frequency (past) | Radio | Yes | Never, Rarely (<1/year), Occasionally (1-5/year), Regularly (6+/year), Frequently (monthly+) | COBS 9.2.1R - Experience | - | Occasionally |
| Largest Investment Made | Currency | Yes | GBP; min £0 | COBS 9.2.1R - Experience level | - | £50,000 |
| Investment Losses Experience | Radio | Yes | Never invested, No losses, Small losses (<10%), Moderate losses (10-25%), Significant losses (>25%) | COBS 9.2.1R - Experience | - | Small losses |
| Understand Investment Risk | Radio | Yes | Yes/No/Uncertain | COBS 9.2.1R - Knowledge | - | Yes |
| Complex Products Experience | Multi-checkbox | No | Derivatives, Structured Products, Hedge Funds, Venture Capital, Cryptocurrencies, None | COBS 9.2.1R - Complex instruments | - | None |

**Business Rules:**
- BR-025: Investment knowledge assessment impacts product eligibility
- BR-026: "None/Basic" knowledge requires enhanced suitability documentation
- BR-027: Complex products experience requires verification for high-risk investments
- BR-028: Knowledge level inconsistent with experience triggers advisor review

#### 4.3.2 Investment Objectives

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Primary Investment Objective | Radio | Yes | Capital Preservation, Income, Growth, Balanced Growth & Income | COBS 9.2.2R - Investment objectives | - | Balanced Growth & Income |
| Secondary Objectives | Multi-checkbox | No | Tax Efficiency, Ethical/ESG Investing, Inheritance Planning, Retirement Planning | COBS 9.2.2R - Investment objectives | - | Tax Efficiency, Retirement Planning |
| Investment Time Horizon | Radio | Yes | Short (<3 years), Medium (3-7 years), Long (7-15 years), Very Long (15+ years) | COBS 9.2.2R - Investment objectives | - | Long (7-15 years) |
| Planned Withdrawals | Radio | Yes | Yes/No | COBS 9.2.2R - Liquidity needs | - | No |
| Withdrawal Timing | Dropdown | Conditional | If planned withdrawals = Yes | COBS 9.2.2R - Liquidity needs | - | - |
| Withdrawal Amount/Frequency | Textarea | Conditional | If planned withdrawals = Yes | COBS 9.2.2R - Liquidity needs | 200 | - |
| Expected Return | Dropdown | Yes | Capital protection, Inflation, Inflation +1-3%, Inflation +3-5%, Inflation +5%+ | COBS 9.2.2R - Expectations | - | Inflation +3-5% |
| Investment Purpose | Multi-checkbox | Yes | Retirement, Children's Education, House Purchase, General Wealth Building, Other | COBS 9.2.2R - Investment objectives | - | Retirement |
| ESG Preferences | Radio | Yes | Essential, Important, Neutral, Not Important | COBS 9.2.2R - Preferences | - | Important |
| ESG Exclusions | Multi-checkbox | Conditional | Weapons, Tobacco, Fossil Fuels, Gambling, Alcohol, Animal Testing | COBS 9.2.2R - Investment restrictions | - | Tobacco, Weapons |

**Business Rules:**
- BR-029: Time horizon must align with investment objectives (e.g., growth requires medium+ horizon)
- BR-030: Short time horizon (<3 years) limits higher-risk strategies
- BR-031: Expected return consistency check against risk profile
- BR-032: ESG exclusions documented for portfolio construction

#### 4.3.3 Risk Tolerance and Capacity

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Risk Attitude Question 1 | Radio | Yes | 5-point scale (Very Conservative to Very Aggressive) | COBS 9.2.2R - Risk tolerance | - | Moderate |
| Risk Attitude Question 2 | Radio | Yes | Portfolio decline scenario: 5%, 10%, 15%, 20%, 25%+ acceptable | COBS 9.2.2R - Risk tolerance | - | 10% |
| Risk Attitude Question 3 | Radio | Yes | Reaction to market downturn options | COBS 9.2.2R - Risk tolerance | - | - |
| Risk Attitude Question 4 | Radio | Yes | Return vs. volatility preference | COBS 9.2.2R - Risk tolerance | - | - |
| Risk Attitude Question 5 | Radio | Yes | Investment priority ranking | COBS 9.2.2R - Risk tolerance | - | - |
| Risk Capacity Assessment | Auto-calc | Yes | Based on financials, time horizon, objectives | COBS 9.2.2R - Risk capacity | - | Medium |
| Risk Tolerance Score | Auto-calc | Yes | Calculated from attitude questions (1-10) | COBS 9.2.2R - Risk tolerance | - | 6 |
| Risk Profile Classification | Auto-calc | Yes | Defensive, Cautious, Balanced, Adventurous, Aggressive | COBS 9.2.2R - Overall risk profile | - | Balanced |
| Emergency Fund Available | Radio | Yes | Yes/No | COBS 9.2.2R - Risk capacity | - | Yes |
| Emergency Fund Duration | Dropdown | Conditional | <3 months, 3-6 months, 6-12 months, 12+ months | COBS 9.2.2R - Financial resilience | - | 6-12 months |

**Business Rules:**
- BR-033: Risk tolerance score calculated using weighted algorithm from 5 questions
- BR-034: Risk capacity vs. risk tolerance mismatch triggers advisor alert
- BR-035: Aggressive profile requires minimum net worth and investment experience thresholds
- BR-036: No emergency fund + aggressive risk profile = unsuitable flag
- BR-037: Risk profile determines recommended asset allocation ranges

---

### 4.4 Section 4: Source of Wealth and Funds (AML/KYC)

#### 4.4.1 Source of Wealth

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Primary Source of Wealth | Dropdown | Yes | Employment Income, Business Ownership, Inheritance, Investment Gains, Property Sale, Divorce Settlement, Compensation/Award, Pension, Other | MLR 2017 Reg 28 - CDD | - | Employment Income |
| Source of Wealth Details | Textarea | Yes | Min 50 chars; specific details required | MLR 2017 Reg 28 - CDD | 500 | 20 years in senior management roles in marketing sector |
| Secondary Sources of Wealth | Multi-checkbox | No | Same options as primary | MLR 2017 Reg 28 - CDD | - | Investment Gains |
| Wealth Accumulation Period | Dropdown | Yes | <5 years, 5-10 years, 10-20 years, 20+ years | MLR 2017 Reg 28 - CDD | - | 20+ years |
| Supporting Documentation | File upload | Conditional* | If wealth >£250k; PDF, JPG, PNG; max 10MB | MLR 2017 Reg 28 - CDD | - | - |

*Conditional mandatory based on amount being invested and risk rating

**Business Rules:**
- BR-038: Source of wealth required for all clients (MLR 2017 requirement)
- BR-039: Investments >£250k require documentary evidence
- BR-040: Sudden wealth (accumulated <5 years) requires enhanced verification
- BR-041: Inheritance/windfall >£100k requires probate/legal documentation

#### 4.4.2 Source of Funds (for this Investment)

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Initial Investment Amount | Currency | Yes | GBP; min £10,000; max £10,000,000 | MLR 2017 Reg 28 - CDD | - | £100,000 |
| Source of Funds | Dropdown | Yes | Savings, Sale of Assets, Maturity of Investment, Business Proceeds, Inheritance, Gift, Loan, Redundancy, Other | MLR 2017 Reg 28 - CDD | - | Savings |
| Source of Funds Details | Textarea | Yes | Min 50 chars; specific details required | MLR 2017 Reg 28 - CDD | 500 | Accumulated savings from salary over 5 years |
| Funds Currently Held | Dropdown | Yes | UK Bank Account, Offshore Account, Cash, Other Investment, Other | MLR 2017 Reg 28 - CDD | - | UK Bank Account |
| Bank Account Details | Text | Conditional | If held in bank account | MLR 2017 Reg 28 - CDD | 200 | Barclays, Account ending 1234 |
| Funds Origin Country | Dropdown | Yes | ISO country codes | MLR 2017 Reg 28 - CDD | - | United Kingdom |
| Third Party Funding | Radio | Yes | Yes/No | MLR 2017 Reg 28 - CDD | - | No |
| Third Party Details | Textarea | Conditional | If Yes; full details required | MLR 2017 Reg 28 - CDD | 500 | - |
| Third Party Relationship | Dropdown | Conditional | Spouse, Parent, Child, Business Partner, Other | MLR 2017 Reg 28 - CDD | - | - |
| Expected Future Funding | Radio | Yes | Yes/No | MLR 2017 Reg 33 - Ongoing monitoring | - | Yes |
| Future Funding Details | Textarea | Conditional | If Yes; amount, timing, source | MLR 2017 Reg 33 - Ongoing monitoring | 300 | Annual contributions of £12,000 from salary |

**Business Rules:**
- BR-042: Source of funds must be clearly documented for all investments
- BR-043: Investments >£100k require enhanced due diligence
- BR-044: Third-party funding requires additional verification and documentation
- BR-045: Cash sources >£10k require explanation and verification
- BR-046: Non-UK source of funds triggers additional compliance checks
- BR-047: Source of funds inconsistent with stated wealth triggers manual review

---

### 4.5 Section 5: Regulatory Declarations and Consent

#### 4.5.1 FCA Client Categorization

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| Professional Client Request | Radio | Yes | Yes/No | COBS 3.5 - Client categorization | - | No |
| Professional Client Criteria | Multi-checkbox | Conditional | If Yes; list of criteria | COBS 3.5.3R - Professional client | - | - |
| High Net Worth Declaration | Radio | Yes | Yes/No | COBS 3.5 - HNW categorization | - | No |
| HNW Evidence | Checkbox | Conditional | Confirm annual income >£100k or net assets >£250k | COBS 3.5 - HNW criteria | - | - |
| Sophisticated Investor Declaration | Radio | Yes | Yes/No | COBS 3.5 - Sophisticated investor | - | No |
| Accept Retail Client Classification | Checkbox | Yes | Must accept classification | COBS 3.5 - Classification | - | ✓ |

**Business Rules:**
- BR-048: Default classification: Retail Client (highest protection)
- BR-049: Professional client status requires meeting specific criteria
- BR-050: Client categorization impacts regulatory protections and disclosures

#### 4.5.2 AML/CTF Declarations

| Field Name | Data Type | Mandatory | Validation Rules | FCA/MLR Reference | Max Length | Example |
|------------|-----------|-----------|------------------|-------------------|------------|---------|
| PEP Declaration Confirmation | Checkbox | Yes | Must be checked | MLR 2017 Reg 35 - PEP | - | ✓ |
| Criminal Convictions | Radio | Yes | Yes/No | MLR 2017 Reg 28 - CDD | - | No |
| Conviction Details | Textarea | Conditional | If Yes; full details required | MLR 2017 Reg 28 - CDD | 500 | - |
| Bankruptcy/IVA History | Radio | Yes | Yes/No | MLR 2017 Reg 28 - CDD | - | No |
| Bankruptcy Details | Textarea | Conditional | If Yes; dates and details | MLR 2017 Reg 28 - CDD | 300 | - |
| Sanctions Screening Consent | Checkbox | Yes | Must be checked | MLR 2017 Reg 28 - CDD | - | ✓ |
| AML Declaration | Checkbox | Yes | Confirm funds not from criminal activity | MLR 2017 Reg 28 - CDD | - | ✓ |
| Beneficial Owner | Radio | Yes | Yes/No | MLR 2017 Reg 5 - Beneficial ownership | - | Yes |
| Beneficial Owner Details | Textarea | Conditional