# Account Admin Usage Guidelines

Establishing rigorous guidelines for the ACCOUNTADMIN role ensures that our most powerful administrative capabilities are reserved for foundational platform configurations, safeguarding the trust and integrity of our entire data ecosystem.

### Purpose
Our goal is to protect the core of our data infrastructure by enforcing strict stewardship over the highest level of system access. By deliberately restricting ACCOUNTADMIN usage, we empower our engineering and security teams to operate with confidence, knowing that systemic risks are systematically mitigated.

### Focus Areas
*   **Delegation of Authority:** Distributing operational tasks to purpose-built system roles like SYSADMIN and SECURITYADMIN to maintain clear operational boundaries.
*   **Credential Protection:** Mandating the highest standards of authentication for any identity capable of assuming enterprise-wide administrative control.
*   **Continuous Visibility:** Maintaining unblinking observability over all actions performed by account administrators to guarantee accountability.
*   **Access Minimization:** Aggressively limiting the number of human users who hold ultimate platform privileges.

> [!NOTE]  
> The ACCOUNTADMIN role encapsulates the combined privileges of the SYSADMIN and SECURITYADMIN roles, making it the most potent access level within the Snowflake architecture.

### Activities
*   Conduct regular, uncompromising audits of all users assigned the top-tier administrative role.
*   Configure account-level parameters, integrations, and resource monitors exclusively through this elevated access tier.
*   Establish automated alerts that trigger instantly whenever an ACCOUNTADMIN executes a query or modifies a platform configuration.
*   Transition all routine data engineering, object creation, and user provisioning workflows to delegated functional roles.

> [!WARNING]  
> Never use the ACCOUNTADMIN role to execute day-to-day data manipulation queries, run automated data pipelines, or create standard database objects.

### Enablers
*   Native monitoring tools and Snowflake system views to continuously track high-privilege activity and query history.
*   Single Sign-On capabilities paired with robust identity governance frameworks to manage lifecycle access.
*   Network policies that restrict login capabilities for administrative accounts to known, secure corporate IP ranges.

> [!CAUTION]  
> Failing to enforce Multi-Factor Authentication on every single user with ACCOUNTADMIN privileges exposes your entire enterprise data platform to catastrophic compromise.

> [!TIP]  
> Provision at least two, but ideally no more than five, dedicated human users with ACCOUNTADMIN privileges to ensure operational redundancy without creating an unnecessarily large security surface area.

### Stakeholder Integration
*   **Information Security:** Partner to establish authentication baselines, monitor threat vectors, and review elevated-access audit logs.
*   **Platform Engineering:** Collaborate to ensure that infrastructure-as-code deployments securely manage account-level configurations without exposing or hardcoding sensitive credentials.
*   **Compliance and Audit:** Work together to document the exact chain of custody and justification for every administrative intervention, fulfilling regulatory obligations.

> [!IMPORTANT]  
> Designate specific break-glass administrative accounts that remain dormant under normal operations, are heavily monitored, and are accessible only during critical enterprise incidents.

### Indicators of Success
*   Absolute zero count of operational or analytical queries executed under the ACCOUNTADMIN role in production environments.
*   One hundred percent compliance with multi-factor authentication mandates for all highly privileged accounts.
*   Instantaneous, actionable alerts successfully routed to security operations for any administrative login event.
*   Seamless execution of internal and external security audits regarding data platform access controls.
