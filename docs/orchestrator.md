# Analyze and break down job requirements

**Agent:** orchestrator
**Job:** Client Responsive Webform

---

```json
{
  "analysis": "This project requires building a multi-step responsive web form for a UK wealth management firm to collect comprehensive client information while ensuring FCA compliance. The project involves both technical implementation (responsive UI, data validation, secure data handling) and regulatory considerations (FCA compliance, data protection under UK GDPR). Key challenges include designing an intuitive multi-step UX, implementing robust validation, ensuring security for sensitive financial data, and meeting strict regulatory requirements. The project requires collaboration between design, development, compliance, and testing teams.",
  "tasks": [
    {
      "title": "FCA Compliance and Legal Requirements Analysis",
      "description": "Conduct comprehensive analysis of FCA regulatory requirements for client onboarding in wealth management, including data collection requirements, consent mechanisms, privacy notices under UK GDPR, anti-money laundering (AML) checks, and client fact-find obligations. Identify mandatory vs. optional fields, required disclosures, and compliance documentation. Define data retention policies and audit trail requirements. Deliver compliance requirements document with specific form field requirements and validation rules.",
      "assigned_agent": "legal_compliance_analyst",
      "depends_on": [],
      "priority": "HIGH",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "Multi-Step Form UX Design and User Journey Mapping",
      "description": "Design the complete user experience for the multi-step form including information architecture, step progression logic, and visual design. Create wireframes and mockups for each form step (personal details, address information, contact details, fact-find questionnaire, declarations/consent). Ensure responsive design across mobile, tablet, and desktop. Include progress indicators, validation feedback patterns, save/resume functionality, and error handling UX. Design should optimize completion rates while maintaining professional wealth management aesthetics.",
      "assigned_agent": "ui_designer",
      "depends_on": ["task_1"],
      "priority": "HIGH",
      "estimated_effort": "4-5 days"
    },
    {
      "title": "Technical Architecture and Security Design",
      "description": "Define the technical architecture for the web form application including frontend framework selection, backend API structure, database design for client data storage, and security architecture. Design secure data transmission (HTTPS, encryption), authentication/authorization mechanisms, session management, and data encryption at rest. Plan integration points for CRM systems, document management, and potential AML screening services. Define API contracts, data models, and security protocols. Include OWASP security considerations and UK GDPR technical requirements (pseudonymization, right to erasure, etc.).",
      "assigned_agent": "technical_architect",
      "depends_on": ["task_1"],
      "priority": "HIGH",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "Full-Stack Form Application Development",
      "description": "Implement the complete multi-step responsive web form application based on approved designs and technical architecture. Frontend: Build responsive UI with form validation, multi-step navigation, progress tracking, auto-save functionality, and error handling. Implement conditional logic for fact-find questions. Backend: Develop RESTful APIs for form data submission, validation, storage, and retrieval. Implement server-side validation, data sanitization, and secure storage. Include CSRF protection, rate limiting, and input validation. Ensure mobile-first responsive design with cross-browser compatibility. Deliverable: Fully functional web form application with all specified fields and validation logic.",
      "assigned_agent": "full_stack_dev",
      "depends_on": ["task_2", "task_3"],
      "priority": "HIGH",
      "estimated_effort": "8-10 days"
    },
    {
      "title": "Data Security and Compliance Testing",
      "description": "Conduct comprehensive security testing and compliance validation of the web form application. Perform penetration testing, vulnerability assessment, and security code review focusing on OWASP Top 10. Verify encryption implementation, authentication mechanisms, and data protection controls. Test compliance with FCA requirements and UK GDPR (consent capture, privacy notices, data minimization, audit logging). Validate field-level validation rules align with regulatory requirements. Test session timeout, secure data transmission, and backup/recovery procedures. Deliver security test report and compliance checklist with any remediation items.",
      "assigned_agent": "security_analyst",
      "depends_on": ["task_4"],
      "priority": "HIGH",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "End-to-End Quality Assurance Testing",
      "description": "Execute comprehensive functional, usability, and cross-platform testing of the web form. Test all form steps, validation rules, error handling, save/resume functionality, and data submission flows. Verify responsive behavior across devices (mobile phones, tablets, desktops) and browsers (Chrome, Firefox, Safari, Edge). Test accessibility compliance (WCAG 2.1 AA standards). Validate all FCA-required fields are captured correctly. Test edge cases, boundary conditions, and negative scenarios. Create test cases, execute testing, log defects, and verify fixes. Deliver test summary report and sign-off documentation.",
      "assigned_agent": "tester",
      "depends_on": ["task_4"],
      "priority": "MEDIUM",
      "estimated_effort": "4-5 days"
    },
    {
      "title": "Documentation and Training Materials",
      "description": "Create comprehensive documentation including technical documentation (API documentation, database schema, deployment guide, system architecture), user guide for wealth management staff, administrator guide for form management/configuration, privacy notice templates, and compliance documentation. Document audit trail functionality, data retention procedures, and incident response processes. Create training materials for firm staff on how to access, review, and process submitted forms. Include troubleshooting guide and FAQ.",
      "assigned_agent": "documentation_writer",
      "depends_on": ["task_4"],
      "priority": "MEDIUM",
      "estimated_effort": "3-4 days"
    },
    {
      "title": "Deployment and Release Management",
      "description": "Plan and execute deployment of the web form application to production environment. Set up CI/CD pipeline, configure production infrastructure with appropriate security controls, SSL certificates, and monitoring. Implement logging and audit trail mechanisms. Configure backup and disaster recovery procedures. Perform production readiness review including security checklist, compliance verification, and performance validation. Execute phased rollout with monitoring. Create rollback procedures and incident response plan. Provide post-deployment support and monitoring for initial period. Deliver deployment documentation and handover to operations team.",
      "assigned_agent": "devops_engineer",
      "depends_on": ["task_5", "task_6"],
      "priority": "MEDIUM",
      "estimated_effort": "3-4 days"
    }
  ],
  "recommendations": "1. **Early Compliance Engagement**: Engage with the firm's compliance officer and legal team early to ensure all FCA requirements are captured correctly, as regulatory non-compliance could have serious consequences. 2. **User Testing**: Consider conducting usability testing with actual wealth management advisors to optimize the form completion experience and identify friction points. 3. **Data Integration**: Clarify integration requirements with existing CRM, document management, or back-office systems early in the project to avoid rework. 4. **Progressive Disclosure**: Implement smart progressive disclosure in the fact-find section to reduce form fatigue while collecting comprehensive information. 5. **Audit Trail**: Ensure robust audit logging of all form submissions, modifications, and access for regulatory audit purposes. 6. **Accessibility**: Prioritize WCAG 2.1 AA compliance to ensure the form is accessible to all clients, including those with disabilities. 7. **Staging Environment**: Maintain a staging environment that mirrors production for ongoing compliance testing and updates. 8. **Regular Reviews**: Plan for periodic reviews of form fields and compliance requirements as FCA regulations evolve."
}
```