# Analyze and break down job requirements

**Agent:** orchestrator
**Job:** Client Responsive Webform

---

```json
{
  "analysis": "This project requires building a regulated, multi-step web form for UK wealth management client onboarding with FCA compliance requirements. Key considerations include: data protection (UK GDPR), secure data handling, comprehensive fact-finding capabilities, responsive design for cross-device accessibility, and proper validation. The multi-step nature suggests a wizard-style interface to reduce cognitive load. FCA compliance necessitates specific data capture requirements including suitability assessment data, risk profiling, and proper consent management. The solution requires careful business analysis for regulatory requirements, robust security measures, professional UI/UX design, full-stack development, and thorough testing for compliance and functionality.",
  "tasks": [
    {
      "title": "Define FCA Compliance Requirements and Data Specification",
      "description": "Conduct detailed analysis of FCA regulatory requirements for client onboarding in wealth management. Define comprehensive data schema including: personal details (name, DOB, address, contact info), financial information (income, assets, liabilities), investment objectives, risk tolerance, fact-find questions, anti-money laundering (AML) requirements, and consent management. Document mandatory vs optional fields, validation rules, data retention policies, and GDPR compliance requirements. Deliverable: Requirements specification document with complete field definitions, regulatory mappings, and validation criteria.",
      "assigned_agent": "business_analyst",
      "depends_on": [],
      "priority": "HIGH",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "Security and Data Protection Architecture",
      "description": "Design security architecture for handling sensitive financial and personal data. Define encryption standards (at rest and in transit), secure data storage approach, access controls, audit logging requirements, UK GDPR compliance measures including right to erasure and data portability. Specify authentication mechanisms, session management, CSRF protection, and secure API design. Address PCI-DSS requirements if payment data is involved. Deliverable: Security architecture document with technical specifications and compliance checklist.",
      "assigned_agent": "security_analyst",
      "depends_on": [],
      "priority": "HIGH",
      "estimated_effort": "2-3 days"
    },
    {
      "title": "Design Multi-Step Form UI/UX and Wireframes",
      "description": "Create responsive UI/UX design for multi-step wizard interface optimized for desktop, tablet, and mobile devices. Design progressive form flow with logical grouping: Step 1 (Personal Details), Step 2 (Contact & Address), Step 3 (Financial Information), Step 4 (Investment Objectives & Risk Profile), Step 5 (Fact Find), Step 6 (Consents & Review). Include progress indicators, validation feedback, error handling, auto-save functionality, and accessible design patterns (WCAG 2.1 AA compliance). Deliverable: Complete wireframes, user flow diagrams, and responsive design specifications.",
      "assigned_agent": "ui_designer",
      "depends_on": ["task_1"],
      "priority": "HIGH",
      "estimated_effort": "4-5 days"
    },
    {
      "title": "Develop Full-Stack Web Form Application",
      "description": "Build complete multi-step form application with frontend and backend components. Frontend: Implement responsive React/Vue.js form with step navigation, real-time validation, conditional logic, auto-save to prevent data loss, and accessible components. Backend: Create RESTful API endpoints for form submission, data validation, temporary storage during completion, and final submission. Implement server-side validation matching frontend rules, UK postcode validation, email/phone verification, and secure data handling per security specifications. Include integration points for CRM/database storage. Deliverable: Fully functional web form application with documented API.",
      "assigned_agent": "full_stack_dev",
      "depends_on": ["task_2", "task_3"],
      "priority": "HIGH",
      "estimated_effort": "8-10 days"
    },
    {
      "title": "Design Data Storage and Integration Architecture",
      "description": "Design database schema for storing client information with proper normalization, indexing, and encryption. Define data pipeline for integrating submitted forms with existing CRM/wealth management systems. Specify API contracts for downstream systems, data transformation requirements, and backup/recovery procedures. Address data archival and retention policies per FCA requirements. Deliverable: Database schema, ERD diagrams, integration architecture document, and migration scripts.",
      "assigned_agent": "data_architect",
      "depends_on": ["task_1"],
      "priority": "MEDIUM",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "Comprehensive Testing and Compliance Validation",
      "description": "Execute complete testing strategy including: functional testing of all form steps and validation rules, cross-browser/device responsive testing, accessibility testing (WCAG 2.1 AA), security testing (OWASP Top 10), data validation testing, error handling scenarios, session timeout handling, and compliance verification against FCA requirements. Perform UAT with wealth management stakeholders. Document test cases, results, and compliance evidence. Deliverable: Test plan, test cases, test results report, and compliance certification document.",
      "assigned_agent": "tester",
      "depends_on": ["task_4"],
      "priority": "HIGH",
      "estimated_effort": "5-6 days"
    },
    {
      "title": "Deployment Infrastructure and Documentation",
      "description": "Set up production-ready infrastructure with SSL certificates, DDoS protection, automated backups, and monitoring. Configure CI/CD pipeline for deployment. Implement logging and monitoring for form submissions, errors, and security events. Create comprehensive technical documentation including: deployment guide, API documentation, database schema documentation, security procedures, incident response plan, and user guide for wealth management staff. Deliverable: Deployed application, infrastructure documentation, and operational runbooks.",
      "assigned_agent": "devops_engineer",
      "depends_on": ["task_6"],
      "priority": "MEDIUM",
      "estimated_effort": "3-4 days"
    }
  ],
  "recommendations": "1. Engage with FCA compliance specialists early to ensure all regulatory requirements are captured. 2. Consider implementing progressive profiling to reduce form abandonment while maintaining comprehensive data collection. 3. Build in analytics to track completion rates per step and identify drop-off points. 4. Plan for regular compliance audits and updates as FCA regulations evolve. 5. Consider integration with identity verification services (e.g., credit reference agencies) for enhanced KYC compliance. 6. Implement comprehensive audit trails for all data access and modifications. 7. Consider WCAG accessibility from the start rather than retrofitting. 8. Plan for user training sessions with wealth management staff before go-live."
}
```