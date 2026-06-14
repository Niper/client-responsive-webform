# Multi-Step Form UX Design and User Journey Mapping

**Agent:** ui_designer
**Job:** Client Responsive Webform

---

# Multi-Step Form UX Design & User Journey Mapping
## UK Wealth Management Client Onboarding Form

---

## 1. INFORMATION ARCHITECTURE

### Form Structure (6 Steps)

```
Step 1: Personal Details
Step 2: Address Information  
Step 3: Contact Details
Step 4: Financial Fact-Find
Step 5: Investment Profile
Step 6: Declarations & Consent
```

### Detailed Content Breakdown

#### **Step 1: Personal Details**
- Title (Mr, Mrs, Ms, Miss, Dr, Other)
- Full Legal Name (First, Middle, Last)
- Preferred Name
- Date of Birth
- National Insurance Number
- Place of Birth
- Nationality/Nationalities
- Marital Status
- Number of Dependents

#### **Step 2: Address Information**
- Current Residential Address (UK Address Lookup)
- Address Line 1
- Address Line 2
- Town/City
- County
- Postcode
- Country
- Time at Current Address
- Previous Address (if < 3 years at current)
- Correspondence Address (if different)

#### **Step 3: Contact Details**
- Primary Phone Number
- Secondary Phone Number
- Mobile Number
- Email Address
- Confirm Email Address
- Preferred Contact Method
- Preferred Contact Time
- Emergency Contact Information

#### **Step 4: Financial Fact-Find**
- Employment Status
- Occupation/Job Title
- Employer Name
- Annual Income Range
- Source(s) of Wealth
- Estimated Net Worth
- Existing Investments
- Bank Details (for transfers)
- Tax Residency Status
- Political Exposure (PEP screening)

#### **Step 5: Investment Profile**
- Investment Objectives
- Investment Time Horizon
- Risk Tolerance Assessment
- Investment Experience
- Liquidity Requirements
- Ethical/ESG Preferences
- Expected Contribution Amount
- Planned Contribution Frequency

#### **Step 6: Declarations & Consent**
- FCA Client Categorisation
- Terms & Conditions
- Privacy Policy (GDPR)
- Data Processing Consent
- Marketing Preferences
- Anti-Money Laundering Declaration
- Accuracy Statement
- Electronic Signature
- Date of Completion

---

## 2. USER JOURNEY MAP

### Journey Stages

```
AWARENESS → ENTRY → PROGRESSION → COMPLETION → CONFIRMATION
```

### Detailed User Flow

```
┌─────────────────────────────────────────────────────────────┐
│ LANDING / INTRODUCTION                                       │
│ - Welcome message                                            │
│ - Time estimate (10-15 minutes)                              │
│ - What you'll need (documents list)                          │
│ - Security & privacy assurance                               │
│ - [Save & Resume Later] option introduced                    │
│ - [Start Application] CTA                                    │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ PROGRESS THROUGH STEPS 1-6                                   │
│ - Persistent progress indicator                              │
│ - Step title and description                                 │
│ - Form fields with inline validation                         │
│ - Help tooltips for complex fields                           │
│ - [Save & Exit] | [Previous] | [Continue] navigation         │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ REVIEW & SUBMIT                                              │
│ - Summary of all entered information                         │
│ - Edit links for each section                                │
│ - Final declarations checkboxes                              │
│ - [Submit Application] CTA                                   │
└─────────────────────────────────────────────────────────────┘
                           ↓
┌─────────────────────────────────────────────────────────────┐
│ CONFIRMATION                                                 │
│ - Success message                                            │
│ - Reference number                                           │
│ - What happens next                                          │
│ - Email confirmation sent                                    │
│ - [Download PDF Copy] option                                 │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. WIREFRAMES

### 3.1 Desktop Layout (1440px+)

```
┌────────────────────────────────────────────────────────────────────┐
│  [LOGO]                                    Client Application      │
├────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │ PROGRESS BAR                                                  │ │
│  │ ●━━━━━ ○━━━━━ ○━━━━━ ○━━━━━ ○━━━━━ ○                       │ │
│  │ Personal  Address  Contact  Fact-Find  Profile  Declarations  │ │
│  │                    Step 1 of 6                                │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │                                                              │  │
│  │  Personal Details                                    [i] Help │  │
│  │  ─────────────────────────────────────────────────────────  │  │
│  │                                                              │  │
│  │  Please provide your personal information as it appears on   │  │
│  │  official documents.                                         │  │
│  │                                                              │  │
│  │  Title *                                                     │  │
│  │  [ Select ▼ ]                                                │  │
│  │                                                              │  │
│  │  First Name *                    Middle Name(s)              │  │
│  │  [________________]              [________________]          │  │
│  │                                                              │  │
│  │  Last Name *                                                 │  │
│  │  [_________________________________________]                 │  │
│  │                                                              │  │
│  │  Preferred Name (if different)                               │  │
│  │  [_________________________________________]                 │  │
│  │                                                              │  │
│  │  Date of Birth *                                             │  │
│  │  [DD] / [MM] / [YYYY]                                        │  │
│  │                                                              │  │
│  │  National Insurance Number *            [?]                  │  │
│  │  [___] [___] [___] [___]                                     │  │
│  │                                                              │  │
│  │  Place of Birth *                                            │  │
│  │  [_________________________________________]                 │  │
│  │                                                              │  │
│  │  Nationality *                                               │  │
│  │  [ Select ▼ ]                    □ Dual nationality          │  │
│  │                                                              │  │
│  │  Marital Status *                Number of Dependents        │  │
│  │  [ Select ▼ ]                    [___]                       │  │
│  │                                                              │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  * Required fields                                                  │
│                                                                     │
│  [Save & Exit]              [Previous]        [Continue →]         │
│                                                                     │
├────────────────────────────────────────────────────────────────────┤
│  Secure Form • Your data is encrypted • Privacy Policy             │
└────────────────────────────────────────────────────────────────────┘
```

### 3.2 Tablet Layout (768px - 1024px)

```
┌──────────────────────────────────────────┐
│  [LOGO]          Client Application      │
├──────────────────────────────────────────┤
│                                           │
│  ●━━ ○━━ ○━━ ○━━ ○━━ ○                  │
│  Step 1 of 6: Personal Details           │
│                                           │
│  ┌────────────────────────────────────┐  │
│  │                                     │  │
│  │  Title *                            │  │
│  │  [ Select ▼ ]                       │  │
│  │                                     │  │
│  │  First Name *                       │  │
│  │  [__________________________]       │  │
│  │                                     │  │
│  │  Middle Name(s)                     │  │
│  │  [__________________________]       │  │
│  │                                     │  │
│  │  Last Name *                        │  │
│  │  [__________________________]       │  │
│  │                                     │  │
│  │  Date of Birth *                    │  │
│  │  [DD] / [MM] / [YYYY]               │  │
│  │                                     │  │
│  │  [Continue fields...]               │  │
│  │                                     │  │
│  └────────────────────────────────────┘  │
│                                           │
│  [Save & Exit]        [Continue →]       │
│                                           │
└──────────────────────────────────────────┘
```

### 3.3 Mobile Layout (320px - 767px)

```
┌─────────────────────────┐
│ [≡]  [LOGO]      [i]    │
├─────────────────────────┤
│                          │
│ ●━ ○━ ○━ ○━ ○━ ○       │
│ Step 1 of 6              │
│                          │
│ Personal Details         │
│ ──────────────────────  │
│                          │
│ Title *                  │
│ [Select ▼]              │
│                          │
│ First Name *             │
│ [________________]       │
│                          │
│ Middle Name(s)           │
│ [________________]       │
│                          │
│ Last Name *              │
│ [________________]       │
│                          │
│ Date of Birth *          │
│ [DD] [MM] [YYYY]         │
│                          │
│ [Continue...]            │
│                          │
├─────────────────────────┤
│ [Save & Exit]            │
│ [Continue →]             │
└─────────────────────────┘
```

---

## 4. VISUAL DESIGN SPECIFICATIONS

### 4.1 Color Palette (Wealth Management Professional)

```
Primary Colors:
- Navy Blue:      #1A365D (Trust, stability)
- Gold Accent:    #C9A961 (Premium, wealth)
- White:          #FFFFFF (Clean, professional)

Secondary Colors:
- Light Grey:     #F7F9FC (Backgrounds)
- Medium Grey:    #E2E8F0 (Borders)
- Dark Grey:      #4A5568 (Body text)

Functional Colors:
- Success:        #48BB78 (Validation, completion)
- Error:          #E53E3E (Validation errors)
- Warning:        #DD6B20 (Important notices)
- Info:           #4299E1 (Help tooltips)
```

### 4.2 Typography

```
Headings:
- Font Family: 'Inter' or 'Roboto'
- H1: 32px/40px, Weight: 600
- H2: 24px/32px, Weight: 600
- H3: 20px/28px, Weight: 500

Body:
- Font Family: 'Inter' or 'Roboto'
- Body: 16px/24px, Weight: 400
- Small: 14px/20px, Weight: 400
- Tiny: 12px/16px, Weight: 400

Input Fields:
- Font Size: 16px (prevents zoom on iOS)
- Line Height: 24px
```

### 4.3 Spacing System

```
Base Unit: 8px

Spacing Scale:
- xs:  4px   (tight spacing)
- sm:  8px   (compact spacing)
- md:  16px  (standard spacing)
- lg:  24px  (comfortable spacing)
- xl:  32px  (section spacing)
- 2xl: 48px  (major section spacing)
```

### 4.4 Component Specifications

#### Input Fields
```
Default State:
- Height: 48px
- Border: 1px solid #E2E8F0
- Border Radius: 6px
- Padding: 12px 16px
- Background: #FFFFFF

Focus State:
- Border: 2px solid #1A365D
- Box Shadow: 0 0 0 3px rgba(26, 54, 93, 0.1)

Error State:
- Border: 2px solid #E53E3E
- Background: #FFF5F5

Success State:
- Border: 1px solid #48BB78
- Icon: ✓ (right-aligned, green)
```

#### Buttons
```
Primary Button (Continue):
- Background: #1A365D
- Color: #FFFFFF
- Height: 48px
- Padding: 12px 32px
- Border Radius: 6px
- Font Weight: 500

Hover:
- Background: #2D4A7C

Secondary Button (Previous):
- Background: #FFFFFF
- Color: #1A365D
- Border: 2px solid #1A365D

Ghost Button (Save & Exit):
- Background: transparent
- Color: #4A5568
- Border: 1px solid #E2E8F0
```

#### Progress Indicator
```
Width: 100%
Height: 4px per step line
Active Step: Filled circle + solid line (#1A365D)
Completed Step: Filled circle + solid line (#48BB78)
Upcoming Step: Hollow circle + dashed line (#E2E8F0)
```

---

## 5. INTERACTION PATTERNS

### 5.1 Validation Logic

#### Real-Time Validation
```javascript
Validation Timing:
- On Blur: Validate field when user leaves it
- On Submit: Validate entire step before progression
- No validation on typing (reduces friction)

Validation Display:
- Error Icon: Red × appears in field
- Error Message: Below field in red text
- Field Highlight: Red border on error
- Success Icon: Green ✓ for correct entries
```

#### Field-Specific Validation

```
Email:
- Format check (RFC 5322)
- Confirm email must match
- Real-time mismatch warning

National Insurance Number:
- Format: AB123456C
- Pattern validation
- Checksum validation

Postcode:
- UK postcode format
- Integration with address lookup API
- Auto-formatting (uppercase, spacing)

Phone Number:
- UK format validation
- International format support
- Auto-formatting

Date of Birth:
- Age validation (18+)
- Format: DD/MM/YYYY
- Calendar picker option
```

### 5.2 Progressive Disclosure

```
Complex Sections:
- Show "Additional Information" as collapsed
- Expand on click/tap
- Remember state during session

Conditional Fields:
- Dual nationality → Show second nationality field
- Time at address < 3 years → Show previous address
- "Other" selections → Show text input
- PEP status = Yes → Show additional screening
```

### 5.3 Save & Resume Functionality

```
Auto-Save:
- Save draft every 30 seconds
- Save on step completion
- Save on "Save & Exit" button

Session Management:
- Generate unique session ID
- Email magic link for resume
- Session expires after 30 days
- Clear security message about data storage

Resume Experience:
- Email with secure link
- Return to last completed step
- Show progress summary
- Option to start fresh
```

### 5.4 Error Handling

```
Field Errors:
┌──────────────────────────────┐
│ Email Address *               │
│ [invalid@email]          ✗   │
│ ⚠ Please enter a valid email │
│   address                     │
└──────────────────────────────┘

Step Errors (Top of Form):
┌────────────────────────────────────┐
│ ⚠ Please correct the following:    │
│   • Email address is invalid       │
│   • National Insurance is required │
│   [Jump to first error]            │
└────────────────────────────────────┘

System Errors:
┌────────────────────────────────────┐
│ ⚠ Unable to save your progress     │
│   Please check your connection and │
│   try again.                       │
│   [Retry]  [Save Offline]          │
└────────────────────────────────────┘
```

---

## 6. ACCESSIBILITY FEATURES

### 6.1 WCAG 2.1 AA Compliance

```
Keyboard Navigation:
- Full tab order support
- Focus indicators (visible outline)
- Skip to content link
- Keyboard shortcuts for navigation
  • Alt + N: Next step
  • Alt + P: Previous step
  • Alt + S: Save & Exit

Screen Reader Support:
- Semantic HTML structure
- ARIA labels on all inputs
- ARIA live regions for errors
- Progress announcements
- Step descriptions

Visual Accessibility:
- Color contrast ratio > 4.5:1 (text)
- Color contrast ratio > 3:1 (UI components)
- No color-only indicators
- Resizable text up to 200%
- Clear focus states

Motor Accessibility:
- Touch targets minimum 44x44px
- Generous spacing between elements
- Error prevention (confirmation dialogs)
- Undo functionality where appropriate
```

### 6.2 Inclusive Design

```
Language Support:
- Clear, plain English (no jargon)
- Glossary tooltips for technical terms
- Option for Welsh language (UK requirement)

Cognitive Accessibility:
- Simple, linear progression
- Clear step titles and descriptions
- Time estimate provided upfront
- No time limits (or generous limits)
- Consistent layout across steps
- Visual progress indicators

Assistive Technology:
- Compatible with screen readers (JAWS, NVDA)
- Voice input support
- High contrast mode support
- Browser zoom support
```

---

## 7. RESPONSIVE BREAKPOINTS

```
Mobile Small:    320px - 374px
Mobile:          375px - 767px
Tablet:          768px - 1024px
Desktop Small:   1025px - 1439px
Desktop:         1440px+

Layout Adjustments:

Mobile (< 768px):
- Single column layout
- Full-width inputs
- Stacked buttons
- Simplified progress bar
- Collapsible help sections
- Sticky navigation bar

Tablet (768px - 1024px):
- Single column with wider max-width
- Two-column for related fields
- Side-by-side buttons
- Expanded progress bar

Desktop (1025px+):
- Maximum width: 960px centered
- Two-column optimal layout
- Full progress bar with labels
- Sticky sidebar with progress summary
```

---

## 8. MOCKUPS - HIGH FIDELITY

### 8.1 Step 1: Personal Details (Desktop)

```
┌────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  [WEALTHCO LOGO]                              Client Application   │
│                                                                     │
├────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │ Progress: 16% Complete                                        │ │
│  │ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━│ │
│  │                                                                │ │
│  │ ● ──────── ○ ──────── ○ ──────── ○ ──────── ○ ──────── ○    │ │
│  │ Personal   Address    Contact   Fact-Find  Profile  Declarations│
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │                                                              │  │
│  │  Personal Details                                            │  │
│  │  ════════════════════════════════════════════════════════   │  │
│  │                                                              │  │
│  │  Please provide your personal information exactly as it      │  │
│  │  appears on your official identification documents.          │  │
│  │                                                              │  │
│  │  ┌─────────────────┐  ┌───────────────────────────────────┐│  │
│  │  │ Title *         │  │ First Name *                       ││  │
│  │  │ Mr          ▼  │  │ Jonathan                          ││  │
│  │  └─────────────────┘  └───────────────────────────────────┘│  │
│  │                                                              │  │
│  │  ┌──────────────────────────────┐ ┌──────────────────────┐ │  │
│  │  │ Middle Name(s)                │ │ Last Name *           │ │  │
│  │  │ Alexander                     │ │ Smith-Thompson        │ │  │
│  │  └──────────────────────────────┘ └──────────────────────┘ │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │ Preferred Name (if different from above)              │  │  │
│  │  │ Jon                                                   │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────┐                  │  │
│  │  │ Date of Birth *                  [?] │                  │  │
│  │  │ [15] / [06] / [1985]                 │                  │  │
│  │  │ ✓ Age: 38 years                      │                  │  │
│  │  └──────────────────────────────────────┘                  │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────┐                  │  │
│  │  │ National Insurance Number *      [?] │                  │  │
│  │  │ AB 12 34 56 C                    ✓  │                  │  │
│  │  └──────────────────────────────────────┘                  │  │
│  │                                                              │  │
│  │  ┌─────────────────────────┐ ┌─────────────────────────┐  │  │
│  │  │ Place of Birth *         │ │ Nationality *            │  │  │
│  │  │ London, UK               │ │ British              ▼ │  │  │
│  │  └─────────────────────────┘ └─────────────────────────┘  │  │
│  │                                                              │  │
│  │  □ I hold dual nationality                                  │  │
│  │                                                              │  │
│  │  ┌─────────────────────────┐ ┌─────────────────────────┐  │  │
│  │  │ Marital Status *         │ │ Number of Dependents     │  │  │
│  │  │ Married              ▼  │ │ 2                        │  │  │
│  │  └─────────────────────────┘ └─────────────────────────┘  │  │
│  │                                                              │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                                                                     │
│  * Required fields                                                  │
│                                                                     │
│  ┌────────────┐                    ┌───────────────────────────┐  │
│  │ Save & Exit│                    │      Continue  →          │  │
│  └────────────┘                    └───────────────────────────┘  │
│                                                                     │
├────────────────────────────────────────────────────────────────────┤
│  🔒 Secure & Encrypted  |  Privacy Policy  |  Need Help? Chat     │
└────────────────────────────────────────────────────────────────────┘
```

### 8.2 Step 4: Financial Fact-Find (Desktop)

```
┌────────────────────────────────────────────────────────────────────┐
│  [WEALTHCO LOGO]                              Client Application   │
├────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━│
│  ● ━━━━━━━━ ● ━━━━━━━━ ● ━━━━━━━━ ● ──────── ○ ──────── ○      │
│  Personal   Address    Contact   Fact-Find  Profile  Declarations  │
│                                   Step 4 of 6: 66% Complete         │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │                                                              │  │
│  │  Financial Fact-Find                                         │  │
│  │  ════════════════════════════════════════════════════════   │  │
│  │                                                              │  │
│  │  This information helps us understand your financial         │  │
│  │  situation and recommend appropriate solutions.              │  │
│  │                                                              │  │
│  │  Employment Information                                      │  │
│  │  ──────────────────────────────────────────────────────     │  │
│  │                                                              │  │
│  │  ┌──────────────────────────┐ ┌───────────────────────────┐│  │
│  │  │ Employment Status *       │ │ Occupation/Job Title *     ││  │
│  │  │ Employed Full-Time    ▼  │ │ Senior Software Engineer  ││  │
│  │  └──────────────────────────┘ └───────────────────────────┘│  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │ Employer Name *                                       │  │  │
│  │  │ Tech Solutions Ltd                                    │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                                                              │  │
│  │  Income & Assets                                             │  │
│  │  ──────────────────────────────────────────────────────     │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │ Annual Gross Income (£) *                        [?] │  │  │
│  │  │ ○ Under £25,000      ○ £25,000 - £50,000             │  │  │
│  │  │ ● £50,000 - £100,000 ○ £100,000 - £250,000           │  │  │
│  │  │ ○ £250,000 - £500,000 ○ Over £500,000                │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │ Source(s) of Wealth * (select all that apply)        │  │  │
│  │  │ ☑ Employment Income    ☑ Business Ownership           │  │  │
│  │  │ □ Inheritance          □ Investment Returns            │  │  │
│  │  │ ☑ Property/Real Estate □ Other                        │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │ Estimated Net Worth (£) *                        [?] │  │  │
│  │  │ ○ Under £100,000     ○ £100,000 - £250,000           │  │  │
│  │  │ ● £250,000 - £500,000 ○ £500,000 - £1,000,000        │  │  │
│  │  │ ○ £1,000,000 - £5,000,000 ○ Over £5,000,000          │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                                                              │  │
│  │  Tax & Regulatory                                            │  │
│  │  ──────────────────────────────────────────────────────     │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │ UK Tax Residency Status *                            │  │  │
│  │  │ ● UK Tax Resident                                     │  │  │
│  │  │ ○ Non-UK Tax Resident                                │  │  │
│  │  │ ○ UK Resident but non-domiciled                      │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                                                              │  │
│  │  ┌──────────────────────────────────────────────────────┐  │  │
│  │  │ Are you a Politically Exposed Person (PEP)? *    [?] │  │  │
│  │  │ ● No                                                  │  │  │
│  │  │ ○ Yes - I hold or have held a prominent public role  │  │  │
│  │  │ ○ Yes - Close family member is a PEP                 │  │  │
│  │  └──────────────────────────────────────────────────────┘  │  │
│  │                