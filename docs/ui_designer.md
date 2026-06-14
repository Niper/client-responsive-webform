# Multi-step Form UX Design and User Flow

**Agent:** ui_designer
**Job:** Client Responsive Webform

---

# Multi-step Form UX Design and User Flow
## Client Responsive Webform for UK Wealth Management

---

## 1. Executive Summary

This document presents a comprehensive UX design for a multi-step client onboarding form tailored for UK wealth management firms. The design prioritizes mobile-first responsiveness, FCA compliance, user-friendly navigation, and robust error handling to ensure high completion rates and data accuracy.

---

## 2. User Flow Diagram

### 2.1 High-Level User Journey

```
Entry Point (Landing Page)
    ↓
[Welcome & Introduction]
    ↓
Step 1: Personal Details
    ↓
Step 2: Contact Information
    ↓
Step 3: Address Capture (UK Postcode Lookup)
    ↓
Step 4: Financial Fact-Find
    ↓
Step 5: Risk Assessment
    ↓
Step 6: Consent & Declarations
    ↓
Review & Submit
    ↓
Confirmation & Next Steps
```

### 2.2 Navigation Patterns

**Forward Navigation:**
- Primary CTA: "Continue" button (bottom right)
- Validation on step completion before progression
- Auto-save on each step completion

**Backward Navigation:**
- "Back" button (bottom left) - always visible except on Step 1
- Breadcrumb/stepper navigation at top (clickable for completed steps)
- Data preserved when navigating backwards

**Save & Resume:**
- "Save & Exit" link in header (all steps)
- Unique resume link sent via email
- Session expires after 30 days (FCA data retention consideration)

---

## 3. Progress Indicator Design

### 3.1 Desktop Progress Indicator

```
┌─────────────────────────────────────────────────────────────┐
│  [✓] Personal   [✓] Contact   [●] Address   [ ] Financial   │
│      Details        Info                        Fact-Find   │
│                                                              │
│  [ ] Risk        [ ] Consent                                │
│      Assessment      & Declarations                         │
└─────────────────────────────────────────────────────────────┘

Legend:
[✓] = Completed step (green checkmark)
[●] = Current step (blue filled circle)
[ ] = Upcoming step (grey outline circle)
```

### 3.2 Mobile Progress Indicator

```
┌──────────────────────────┐
│  Step 3 of 6             │
│  ████████░░░░░░░  50%   │
│  Address Capture         │
└──────────────────────────┘

Components:
- Numerical step indicator
- Progress bar (visual percentage)
- Current step name
```

---

## 4. Wireframes by Step

### STEP 1: Personal Details

#### Desktop Wireframe (1200px+)

```
┌─────────────────────────────────────────────────────────────────┐
│  [LOGO]                                      [Save & Exit]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [●] Personal Details → [ ] Contact → [ ] Address → ...        │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Personal Details                                       │  │
│  │                                                         │  │
│  │  Title *                                                │  │
│  │  [Dropdown: Mr/Mrs/Miss/Ms/Dr/Prof/Mx/Other]          │  │
│  │                                                         │  │
│  │  First Name *              Middle Name(s)              │  │
│  │  [________________]        [________________]          │  │
│  │                                                         │  │
│  │  Last Name *                                           │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  Preferred Name                                        │  │
│  │  [_____________________________________________]       │  │
│  │  (How would you like to be addressed?)                │  │
│  │                                                         │  │
│  │  Date of Birth *                                       │  │
│  │  [DD] / [MM] / [YYYY]                                 │  │
│  │                                                         │  │
│  │  National Insurance Number *                           │  │
│  │  [__] [__] [__] [__] [__] [__]                        │  │
│  │  (Format: AB 12 34 56 C)                              │  │
│  │                                                         │  │
│  │  Gender                                                │  │
│  │  ○ Male  ○ Female  ○ Non-binary  ○ Prefer not to say │  │
│  │                                                         │  │
│  │  Marital Status *                                      │  │
│  │  [Dropdown: Single/Married/Civil Partnership/...]     │  │
│  │                                                         │  │
│  │  Number of Dependents                                  │  │
│  │  [Dropdown: 0/1/2/3/4/5+]                             │  │
│  │                                                         │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│                                     [Continue →]               │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### Mobile Wireframe (320px-768px)

```
┌─────────────────────────┐
│ ☰  [LOGO]    Save & Exit│
├─────────────────────────┤
│ Step 1 of 6             │
│ ███████░░░░░  40%      │
│ Personal Details        │
├─────────────────────────┤
│                         │
│ Title *                 │
│ [Dropdown ▼]           │
│                         │
│ First Name *            │
│ [________________]     │
│                         │
│ Middle Name(s)          │
│ [________________]     │
│                         │
│ Last Name *             │
│ [________________]     │
│                         │
│ Preferred Name          │
│ [________________]     │
│ (How you'd like to be  │
│  addressed)             │
│                         │
│ Date of Birth *         │
│ [DD]/[MM]/[YYYY]       │
│                         │
│ National Insurance No * │
│ [___________________]  │
│                         │
│ Gender                  │
│ ○ Male                 │
│ ○ Female               │
│ ○ Non-binary           │
│ ○ Prefer not to say    │
│                         │
│ Marital Status *        │
│ [Dropdown ▼]           │
│                         │
│ Number of Dependents    │
│ [Dropdown ▼]           │
│                         │
│ [    Continue →    ]   │
│                         │
└─────────────────────────┘
```

---

### STEP 2: Contact Information

#### Desktop Wireframe

```
┌─────────────────────────────────────────────────────────────────┐
│  [LOGO]                                      [Save & Exit]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [✓] Personal Details → [●] Contact → [ ] Address → ...        │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Contact Information                                    │  │
│  │                                                         │  │
│  │  Email Address *                                        │  │
│  │  [_____________________________________________]       │  │
│  │  ℹ️ We'll send your progress link to this address      │  │
│  │                                                         │  │
│  │  Confirm Email Address *                                │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  Mobile Phone *                                         │  │
│  │  [+44] [_______________________________________]       │  │
│  │  ☑️ This number can receive SMS                        │  │
│  │                                                         │  │
│  │  Home Phone                                             │  │
│  │  [+44] [_______________________________________]       │  │
│  │                                                         │  │
│  │  Work Phone                                             │  │
│  │  [+44] [_______________________________________]       │  │
│  │  Extension: [_______]                                  │  │
│  │                                                         │  │
│  │  Preferred Contact Method *                             │  │
│  │  ○ Email  ○ Mobile Phone  ○ Home Phone  ○ Work Phone  │  │
│  │                                                         │  │
│  │  Best Time to Contact                                   │  │
│  │  ☐ Morning (9am-12pm)                                  │  │
│  │  ☐ Afternoon (12pm-5pm)                                │  │
│  │  ☐ Evening (5pm-7pm)                                   │  │
│  │                                                         │  │
│  │  Emergency Contact                                      │  │
│  │  ─────────────────────────────────────────             │  │
│  │  Name: [_______________________________________]       │  │
│  │  Relationship: [Dropdown ▼]                            │  │
│  │  Phone: [+44] [________________________________]       │  │
│  │                                                         │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [← Back]                                    [Continue →]      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

### STEP 3: Address Capture (UK Postcode Lookup)

#### Desktop Wireframe

```
┌─────────────────────────────────────────────────────────────────┐
│  [LOGO]                                      [Save & Exit]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [✓] Personal → [✓] Contact → [●] Address → [ ] Financial...   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Address Information                                    │  │
│  │                                                         │  │
│  │  Current Residential Address *                          │  │
│  │  ─────────────────────────────────────────             │  │
│  │                                                         │  │
│  │  🔍 Find Address by Postcode                           │  │
│  │                                                         │  │
│  │  UK Postcode *                                          │  │
│  │  [____________]  [Find Address]                        │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────┐     │  │
│  │  │ ✓ 12 addresses found for "SW1A 1AA"          │     │  │
│  │  │                                               │     │  │
│  │  │ Select your address:                          │     │  │
│  │  │ [Dropdown ▼]                                  │     │  │
│  │  │ - 10 Downing Street                           │     │  │
│  │  │ - 11 Downing Street                           │     │  │
│  │  │ - 12 Downing Street                           │     │  │
│  │  │ ...                                            │     │  │
│  │  │                                               │     │  │
│  │  │ [Or enter address manually]                   │     │  │
│  │  └──────────────────────────────────────────────┘     │  │
│  │                                                         │  │
│  │  Address Line 1 *                                       │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  Address Line 2                                         │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  Town/City *                                            │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  County                                                 │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  Postcode *                                             │  │
│  │  [____________]                                        │  │
│  │                                                         │  │
│  │  How long at this address? *                            │  │
│  │  Years: [__]  Months: [__]                             │  │
│  │                                                         │  │
│  │  ☐ I have lived here for less than 3 years             │  │
│  │                                                         │  │
│  │  [If checked, show Previous Address section]           │  │
│  │                                                         │  │
│  │  Correspondence Address                                 │  │
│  │  ─────────────────────────────────────────             │  │
│  │  ○ Same as residential address                         │  │
│  │  ○ Different address (fields expand below)             │  │
│  │                                                         │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [← Back]                                    [Continue →]      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### Interaction Notes:
- Postcode lookup uses Royal Mail PAF API or equivalent
- Real-time validation of postcode format
- Manual entry fallback if address not found
- Previous address section conditionally appears if residence < 3 years
- Auto-format postcode on blur (e.g., "sw1a1aa" → "SW1A 1AA")

---

### STEP 4: Financial Fact-Find

#### Desktop Wireframe

```
┌─────────────────────────────────────────────────────────────────┐
│  [LOGO]                                      [Save & Exit]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [✓] Personal → [✓] Contact → [✓] Address → [●] Financial      │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Financial Fact-Find                                    │  │
│  │                                                         │  │
│  │  All information provided is treated confidentially     │  │
│  │  and in accordance with FCA regulations.                │  │
│  │                                                         │  │
│  │  ═══════════════════════════════════════════           │  │
│  │  EMPLOYMENT INFORMATION                                 │  │
│  │  ═══════════════════════════════════════════           │  │
│  │                                                         │  │
│  │  Employment Status *                                    │  │
│  │  [Dropdown: Employed/Self-employed/Retired/...]        │  │
│  │                                                         │  │
│  │  Occupation/Job Title *                                 │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  Employer Name                                          │  │
│  │  [_____________________________________________]       │  │
│  │                                                         │  │
│  │  Industry Sector                                        │  │
│  │  [Dropdown: Finance/Healthcare/Technology/...]         │  │
│  │                                                         │  │
│  │  Years in Current Role                                  │  │
│  │  [__]                                                  │  │
│  │                                                         │  │
│  │  ═══════════════════════════════════════════           │  │
│  │  INCOME INFORMATION                                     │  │
│  │  ═══════════════════════════════════════════           │  │
│  │                                                         │  │
│  │  Annual Gross Income (before tax) *                     │  │
│  │  £ [_____________]                                     │  │
│  │                                                         │  │
│  │  Additional Income Sources                              │  │
│  │  ☐ Rental Income      £ [__________] per year         │  │
│  │  ☐ Investment Income  £ [__________] per year         │  │
│  │  ☐ Pension Income     £ [__________] per year         │  │
│  │  ☐ Other Income       £ [__________] per year         │  │
│  │                                                         │  │
│  │  ═══════════════════════════════════════════           │  │
│  │  ASSETS & LIABILITIES                                   │  │
│  │  ═══════════════════════════════════════════           │  │
│  │                                                         │  │
│  │  Estimated Total Assets *                               │  │
│  │  £ [_____________]                                     │  │
│  │  (Include property, savings, investments, pensions)    │  │
│  │                                                         │  │
│  │  Breakdown (Optional):                                  │  │
│  │  Property Value:        £ [__________]                 │  │
│  │  Savings/Cash:          £ [__________]                 │  │
│  │  Investments:           £ [__________]                 │  │
│  │  Pension Pots:          £ [__________]                 │  │
│  │  Other Assets:          £ [__________]                 │  │
│  │                                                         │  │
│  │  Estimated Total Liabilities                            │  │
│  │  £ [_____________]                                     │  │
│  │                                                         │  │
│  │  Breakdown (Optional):                                  │  │
│  │  Mortgage Outstanding:  £ [__________]                 │  │
│  │  Loans:                 £ [__________]                 │  │
│  │  Credit Cards:          £ [__________]                 │  │
│  │  Other Debts:           £ [__________]                 │  │
│  │                                                         │  │
│  │  ═══════════════════════════════════════════           │  │
│  │  FINANCIAL OBJECTIVES                                   │  │
│  │  ═══════════════════════════════════════════           │  │
│  │                                                         │  │
│  │  What are your primary financial goals? *               │  │
│  │  (Select all that apply)                                │  │
│  │                                                         │  │
│  │  ☐ Retirement Planning                                 │  │
│  │  ☐ Wealth Accumulation                                 │  │
│  │  ☐ Tax Planning                                        │  │
│  │  ☐ Estate Planning                                     │  │
│  │  ☐ Education Funding                                   │  │
│  │  ☐ Property Purchase                                   │  │
│  │  ☐ Business Investment                                 │  │
│  │  ☐ Debt Reduction                                      │  │
│  │  ☐ Income Generation                                   │  │
│  │  ☐ Other: [_______________________________]           │  │
│  │                                                         │  │
│  │  Investment Timeline *                                  │  │
│  │  ○ Short-term (0-3 years)                              │  │
│  │  ○ Medium-term (3-10 years)                            │  │
│  │  ○ Long-term (10+ years)                               │  │
│  │                                                         │  │
│  │  Existing Financial Adviser?                            │  │
│  │  ○ Yes  ○ No                                           │  │
│  │                                                         │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [← Back]                                    [Continue →]      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### Mobile Adaptation Notes:
- Sections collapse into accordions on mobile
- Currency inputs formatted with £ symbol
- Number inputs show numeric keyboard on mobile
- Checkboxes become larger touch targets (44px minimum)

---

### STEP 5: Risk Assessment

#### Desktop Wireframe

```
┌─────────────────────────────────────────────────────────────────┐
│  [LOGO]                                      [Save & Exit]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [✓] Personal → [✓] Contact → [✓] Address → [✓] Financial →    │
│  [●] Risk Assessment → [ ] Consent                              │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Investment Risk Assessment                             │  │
│  │                                                         │  │
│  │  This assessment helps us understand your attitude to  │  │
│  │  investment risk and ensure suitable recommendations.  │  │
│  │                                                         │  │
│  │  ─────────────────────────────────────────────         │  │
│  │                                                         │  │
│  │  Question 1 of 6                                        │  │
│  │                                                         │  │
│  │  How would you describe your investment knowledge? *    │  │
│  │                                                         │  │
│  │  ○ Limited - I have little to no investment experience │  │
│  │                                                         │  │
│  │  ○ Basic - I understand some investment concepts       │  │
│  │                                                         │  │
│  │  ○ Good - I have reasonable investment knowledge       │  │
│  │                                                         │  │
│  │  ○ Extensive - I have significant investment           │  │
│  │    experience and understanding                         │  │
│  │                                                         │  │
│  │  ─────────────────────────────────────────────         │  │
│  │                                                         │  │
│  │  Question 2 of 6                                        │  │
│  │                                                         │  │
│  │  If the value of your investment fell by 20% in a year,│  │
│  │  what would you do? *                                   │  │
│  │                                                         │  │
│  │  ○ Sell immediately to prevent further losses          │  │
│  │                                                         │  │
│  │  ○ Sell some of the investment                         │  │
│  │                                                         │  │
│  │  ○ Hold the investment and wait for recovery           │  │
│  │                                                         │  │
│  │  ○ Invest more to take advantage of lower prices       │  │
│  │                                                         │  │
│  │  ─────────────────────────────────────────────         │  │
│  │                                                         │  │
│  │  Question 3 of 6                                        │  │
│  │                                                         │  │
│  │  What is your primary investment objective? *           │  │
│  │                                                         │  │
│  │  ○ Capital preservation - protecting what I have       │  │
│  │                                                         │  │
│  │  ○ Income generation - regular returns                 │  │
│  │                                                         │  │
│  │  ○ Balanced growth - moderate capital appreciation     │  │
│  │                                                         │  │
│  │  ○ Capital growth - maximize long-term returns         │  │
│  │                                                         │  │
│  │  ─────────────────────────────────────────────         │  │
│  │                                                         │  │
│  │  Question 4 of 6                                        │  │
│  │                                                         │  │
│  │  Which best describes your financial situation? *       │  │
│  │                                                         │  │
│  │  ○ I have limited savings and cannot afford losses     │  │
│  │                                                         │  │
│  │  ○ I have adequate savings for emergencies             │  │
│  │                                                         │  │
│  │  ○ I have substantial savings and can withstand        │  │
│  │    short-term losses                                    │  │
│  │                                                         │  │
│  │  ○ I have significant wealth and high risk capacity    │  │
│  │                                                         │  │
│  │  ─────────────────────────────────────────────         │  │
│  │                                                         │  │
│  │  Question 5 of 6                                        │  │
│  │                                                         │  │
│  │  When do you expect to need access to this money? *     │  │
│  │                                                         │  │
│  │  ○ Within 1 year                                       │  │
│  │  ○ 1-3 years                                           │  │
│  │  ○ 3-5 years                                           │  │
│  │  ○ 5-10 years                                          │  │
│  │  ○ More than 10 years                                  │  │
│  │  ○ No specific timeframe                               │  │
│  │                                                         │  │
│  │  ─────────────────────────────────────────────         │  │
│  │                                                         │  │
│  │  Question 6 of 6                                        │  │
│  │                                                         │  │
│  │  Which investment scenario appeals to you most? *       │  │
│  │                                                         │  │
│  │  ○ Guaranteed 3% return with no risk of loss           │  │
│  │                                                         │  │
│  │  ○ Potential 5% return with small risk of loss         │  │
│  │                                                         │  │
│  │  ○ Potential 8% return with moderate risk of loss      │  │
│  │                                                         │  │
│  │  ○ Potential 12% return with significant risk of loss  │  │
│  │                                                         │  │
│  │  ─────────────────────────────────────────────         │  │
│  │                                                         │  │
│  │  ┌──────────────────────────────────────────────┐     │  │
│  │  │ ℹ️ Risk Profile Indicator                     │     │  │
│  │  │                                               │     │  │
│  │  │ Based on your answers:                        │     │  │
│  │  │                                               │     │  │
│  │  │ [▓▓▓▓▓░░░░░]                                 │     │  │
│  │  │                                               │     │  │
│  │  │ Preliminary Risk Profile: MODERATE            │     │  │
│  │  │                                               │     │  │
│  │  │ Final assessment will be completed by your    │     │  │
│  │  │ adviser during consultation.                  │     │  │
│  │  └──────────────────────────────────────────────┘     │  │
│  │                                                         │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                                 │
│  [← Back]                                    [Continue →]      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### Risk Assessment Logic:
- Questions weighted to calculate risk score
- Live risk indicator updates as user answers
- Risk profiles: Defensive, Cautious, Balanced, Growth, Aggressive
- Cannot proceed without answering all required questions

---

### STEP 6: Consent & Declarations

#### Desktop Wireframe

```
┌─────────────────────────────────────────────────────────────────┐
│  [LOGO]                                      [Save & Exit]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [✓] Personal → [✓] Contact → [✓] Address → [✓] Financial →    │
│  [✓] Risk → [●] Consent & Declarations                          │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │  Consent & Declarations                                 │  │
│  │                                                         │  │
│  │  Please read and confirm the following statements       │  │
│  │                                                         │  │
│  │  ═══════════════════════════════════════════           │  │
│  │  DATA PROTECTION & PRIVACY                              │  │
│  │  ═══════════════════════════════════════════           │  │
│  │                                                         │  │
│  │  ☐ * I confirm that I have read and understood the     │  │
│  │      [Privacy Policy] and consent to the processing    │  │
│  │      of my personal data in accordance with UK GDPR    │  │
│  │      and Data Protection Act 2018.                     │  │
│  │                                                         │  │
│  │  ☐ * I consent to [Firm Name] storing and processing   │  │
│  │      my sensitive personal data (financial information,│  │
│  │      risk assessment) for the purpose of providing     │  │
│  │      financial advice and services.                    │  │
│  │                                                         │  │
│  │  ☐   I consent to receiving marketing communications   │  │
│  │      about products and services (optional)            │  │
│  │                                                         │  │
│  │  ═══════════════════════════════════════════           │  │
│  │  FCA REGULATORY REQUIREMENTS                            │  │
│  │  ═══════════════════════════════════════════           │  │
│  │                                                         │  │
│  │  ☐ * I confirm that the information provided in this   │  │
│  │      form is true, accurate, and complete to the best  │  │
│  │      of my knowledge.                                  │  │
│  │                                                         │  │
│