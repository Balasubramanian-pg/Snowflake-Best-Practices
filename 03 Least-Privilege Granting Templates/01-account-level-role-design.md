# Account Level Role Design


Account-level role design in Snowflake establishes the foundational governance architecture required to empower organizations with secure, frictionless data access while maintaining rigorous enterprise compliance.

### Purpose
Our goal is to build a scalable access model that aligns technical capabilities with business intent. By structuring roles thoughtfully at the account level, we eliminate friction for data consumers and ensure security is an enabler of innovation, rather than a bottleneck.

### Focus Areas
*   **Role Hierarchy:** Structuring roles in a directed acyclic graph to ensure privileges flow logically to higher-level administrative roles.
*   **Separation of Duties:** Distinguishing between user management, security administration, and system object creation to mitigate risk.
*   **Least Privilege:** Ensuring users and automated systems possess only the minimum permissions necessary to execute their specific workloads.
*   **Role Taxonomy:** Establishing a clear, predictable naming convention that maps directly to organizational functions and data domains.

> [!NOTE]  
> A highly effective capability model separates Access Roles, which grant privileges on specific database objects, from Functional Roles, which represent business functions and are assigned directly to human or machine users.

### Activities
*   Design a unified custom role hierarchy that integrates cleanly with Snowflake's standard system roles.
*   Map organizational structures and operational workflows into discrete, logical functional roles.
*   Audit existing user permissions and systematically transition legacy grants into the structured Role-Based Access Control model.
*   Define and configure default roles for specific workloads to optimize compute resource allocation.

>[!CAUTION]  
> Limit the use of the ACCOUNTADMIN role to a bare minimum of designated individuals. Routine operational, security, or data tasks must never be executed under this overarching role.

> [!WARNING]  
> Never grant privileges directly to users. All permissions must be routed through structured roles to prevent security sprawl and unmanageable audit complexities.

### Enablers
*   Infrastructure as Code methodologies to automate, version-control, and deploy role hierarchies predictably.
*   Snowflake's native anchor roles (SYSADMIN, SECURITYADMIN, USERADMIN) to standardize the summit of your custom hierarchy.
*   Dynamic data masking and row-level access policies, which reduce the need to create distinct roles for every granular data-sharing scenario.

> [!TIP]  
> Provision your role hierarchy programmatically. This ensures development, testing, and production environments remain perfectly consistent and configuration drift is instantly identifiable.

### Stakeholder Integration
*   **Security and Compliance:** Partner closely to define the boundaries of access, ensuring systemic auditability across all data domains.
*   **Data Engineering:** Collaborate to design operational roles that support robust deployment pipelines and continuous data integration.
*   **Business Leaders:** Engage directly to understand domain-specific needs, ensuring functional roles map accurately to actual human workflows and decision-making processes.

>[!IMPORTANT]  
> All custom roles that own database objects must ultimately be granted to the SYSADMIN role. Failing to complete this inheritance loop results in orphaned objects that central administrators cannot view or manage.

### Indicators of Success
*   Accelerated time-to-productivity for newly onboarded engineers, analysts, and automated services.
*   Zero instances of privilege escalation, orphaned data objects, or unauthorized data exposure.
*   Clean, comprehensible audit trails that instantly map query execution back to a specific, validated business function.
*   Significant reduction in operational support tickets related to access provisioning and permission troubleshooting.
