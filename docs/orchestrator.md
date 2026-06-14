# Analyze and break down job requirements

**Agent:** orchestrator
**Job:** Client Responsive Webform

---

```json
{
  "analysis": "This project requires building a regulated, multi-step responsive web form for UK wealth management client onboarding. The key considerations are: (1) FCA compliance requirements for data collection and storage, (2) multi-step form UX design for complex information gathering, (3) secure data handling given the sensitive financial nature, (4) UK-specific validation (postcodes, phone formats, regulatory fields), and (5) responsive design for accessibility across devices. The work spans regulatory analysis, UX design, full-stack development, security implementation, and compliance validation.",
  "tasks": [
    {
      "title": "FCA Compliance and Regulatory Requirements Analysis",
      "description": "Conduct comprehensive analysis of FCA regulations for client onboarding in wealth management. Identify mandatory data fields, consent requirements, data retention policies, right-to-be-forgotten provisions, and GDPR compliance measures. Document specific requirements for fact-find questionnaires, suitability assessments, and client classification. Deliverable: Compliance requirements document with mandatory fields, validation rules, consent text, and regulatory constraints.",
      "assigned_agent": "legal_compliance_analyst",
      "depends_on": [],
      "priority": "HIGH",
      "estimated_effort": "2-3 days"
    },
    {
      "title": "Multi-step Form UX Design and User Flow",
      "description": "Design the multi-step form user experience including step progression, navigation patterns, progress indicators, and error handling. Create wireframes for all form steps covering: (1) Personal details, (2) Contact information, (3) Address capture with UK postcode lookup, (4) Financial fact-find questions, (5) Risk assessment, (6) Consent and declarations. Ensure mobile-first responsive design. Include validation feedback patterns and save/resume functionality design. Deliverable: Complete wireframe set with user flow diagrams and interaction specifications.",
      "assigned_agent": "ui_designer",
      "depends_on": ["FCA Compliance and Regulatory Requirements Analysis"],
      "priority": "HIGH",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "Data Architecture and Security Design",
      "description": "Design secure data model for client information storage including encryption strategy for PII and sensitive financial data. Define database schema with appropriate field types, validation constraints, and audit trail requirements. Plan data encryption at rest and in transit. Design secure API contracts for form submission. Include GDPR-compliant data retention and deletion mechanisms. Address UK postcode validation and address lookup integration requirements. Deliverable: Data model documentation, API specifications, and security architecture document.",
      "assigned_agent": "data_architect",
      "depends_on": ["FCA Compliance and Regulatory Requirements Analysis"],
      "priority": "HIGH",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "Backend API and Data Processing Development",
      "description": "Develop secure backend API endpoints for form data submission, validation, and storage. Implement: (1) Multi-step form data persistence with save/resume capability, (2) Server-side validation for all inputs including UK-specific formats (postcodes, phone numbers, NI numbers), (3) Data encryption and secure storage, (4) Audit logging for compliance, (5) Integration with UK postcode lookup service (e.g., Royal Mail PAF), (6) Email notification system for form completion. Implement rate limiting and CSRF protection. Deliverable: Production-ready backend API with comprehensive validation and security measures.",
      "assigned_agent": "backend_dev",
      "depends_on": ["Data Architecture and Security Design"],
      "priority": "HIGH",
      "estimated_effort": "5-7 days"
    },
    {
      "title": "Responsive Frontend Form Implementation",
      "description": "Develop responsive, accessible multi-step form interface based on approved designs. Implement: (1) Progressive form steps with validation, (2) Mobile-first responsive layout, (3) Real-time client-side validation with user-friendly error messages, (4) UK postcode lookup integration, (5) Progress saving and resume functionality, (6) Accessibility compliance (WCAG 2.1 AA), (7) Form state management, (8) Loading states and error handling. Use modern framework (React/Vue) with form validation library. Ensure cross-browser compatibility. Deliverable: Fully functional, responsive frontend application with comprehensive form handling.",
      "assigned_agent": "frontend_dev",
      "depends_on": ["Multi-step Form UX Design and User Flow", "Backend API and Data Processing Development"],
      "priority": "HIGH",
      "estimated_effort": "6-8 days"
    },
    {
      "title": "Security Testing and Compliance Validation",
      "description": "Conduct comprehensive security testing including: (1) Penetration testing for common vulnerabilities (SQL injection, XSS, CSRF), (2) Data encryption verification, (3) Authentication and authorization testing, (4) GDPR compliance verification (data access, deletion, portability), (5) FCA regulatory requirement validation against checklist, (6) Privacy policy and consent mechanism verification. Test UK-specific validation rules. Deliverable: Security test report with findings, compliance checklist confirmation, and remediation recommendations.",
      "assigned_agent": "security_analyst",
      "depends_on": ["Backend API and Data Processing Development", "Responsive Frontend Form Implementation"],
      "priority": "HIGH",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "End-to-End Testing and UAT Preparation",
      "description": "Develop and execute comprehensive test plan covering: (1) Functional testing of all form steps and validation rules, (2) Responsive design testing across devices and browsers, (3) Integration testing for postcode lookup and email notifications, (4) Form state persistence and resume testing, (5) Accessibility testing with screen readers, (6) Performance testing under load, (7) UAT test case preparation for business stakeholders. Document all test cases and results. Deliverable: Test execution report, UAT documentation, and defect log with resolutions.",
      "assigned_agent": "tester",
      "depends_on": ["Responsive Frontend Form Implementation"],
      "priority": "MEDIUM",
      "estimated_effort": "4-5 days"
    },
    {
      "title": "Documentation and Deployment Package",
      "description": "Create comprehensive documentation including: (1) User guide for form completion, (2) Administrator guide for accessing submitted data, (3) Technical documentation for API endpoints and data model, (4) Compliance documentation mapping FCA requirements to implementation, (5) Deployment guide with environment configuration, (6) Security and data handling procedures, (7) Incident response procedures for data breaches. Prepare production deployment checklist and rollback plan. Deliverable: Complete documentation suite and deployment package ready for production release.",
      "assigned_agent": "documentation_writer",
      "depends_on": ["End-to-End Testing and UAT Preparation", "Security Testing and Compliance Validation"],
      "priority": "MEDIUM",
      "estimated_effort": "3-4 days"
    }
  ],
  "recommendations": "1. Engage with FCA compliance early and consider consulting with a financial services regulatory expert. 2. Implement progressive enhancement to ensure form works even with JavaScript disabled for maximum accessibility. 3. Consider implementing electronic signature capability if required for regulatory declarations. 4. Plan for regular compliance audits and updates as FCA regulations evolve. 5. Implement comprehensive analytics to identify drop-off points in the multi-step form. 6. Consider adding live chat support within the form for user assistance. 7. Plan for integration with existing CRM or wealth management systems for seamless data flow. 8. Budget for ongoing maintenance including security patches and regulatory updates."
}
```