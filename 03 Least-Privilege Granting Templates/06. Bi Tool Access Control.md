# BI Tool Access Control

BI tool access control in Snowflake ensures that business intelligence platforms interact with data securely, enforcing least-privilege access and maintaining governance over sensitive information.

**Purpose**
BI tool access control in Snowflake is designed to regulate how tools like Tableau, Power BI, or Looker connect to and query data. It prevents unauthorized data exposure by restricting BI tools to specific roles, warehouses, and datasets, while enabling auditable and scalable access for analytics teams. This approach balances self-service analytics with data security, ensuring users only access data relevant to their roles.

**Focus Areas**
- **Role-Based Access:** Assign dedicated roles to BI tools, limiting their permissions to required datasets and operations.
- **Warehouse Isolation:** Restrict BI tools to specific virtual warehouses to manage resource usage and query performance.
- **Network Policies:** Control which IP addresses or ranges can connect to Snowflake via BI tools.
- **Data Masking and Row-Level Security:** Apply dynamic data masking or row-level filters to limit data visibility within BI reports.
- **Audit and Monitoring:** Track BI tool activity, including queries, logins, and data exports, for compliance and anomaly detection.

> [!IMPORTANT]
> BI tools often use service accounts to connect to Snowflake. These accounts must never be granted excessive privileges, such as `ACCOUNTADMIN` or `SECURITYADMIN`. Always follow the principle of least privilege.

**Activities**

**1. Create Dedicated Roles for BI Tools**
- Define custom roles tailored to the needs of each BI tool (e.g., `BI_ANALYST_ROLE`, `BI_REPORTING_ROLE`).
- Grant these roles access only to the databases, schemas, and tables required for their function.

**2. Configure Service Accounts**
- Create unique service accounts for each BI tool (e.g., `svc_tableau`, `svc_powerbi`).
- Assign the dedicated BI roles to these service accounts.

**3. Restrict Warehouse Access**
- Assign BI service accounts to specific warehouses to control compute costs and query performance.
- Example: Restrict the `svc_tableau` account to the `BI_WH` warehouse.

**4. Apply Network Policies**
- Use Snowflake network policies to allowlist IP ranges for BI tool connections.
- Example: Allow only the corporate VPN IP range to connect via Tableau.

**5. Enable Data Security Features**
- Use dynamic data masking to obfuscate sensitive columns (e.g., SSN, salary) in BI reports.
- Implement row-level security to filter data based on user attributes (e.g., region, department).

**6. Monitor and Audit Activity**
- Review query history and login attempts for BI service accounts.
- Set up alerts for unusual activity, such as large data exports or failed login attempts.

> [!NOTE]
> BI tools often cache data locally. Ensure your data governance policy addresses how cached data is secured and retained in the BI platform itself.

**Enablers**

**1. Role and Warehouse Management**
- Create roles and assign them to BI service accounts:
  ```sql
  -- Create a role for BI analysts
  CREATE ROLE bi_analyst_role;

  -- Grant access to required databases and schemas
  GRANT USAGE ON DATABASE analytics_db TO ROLE bi_analyst_role;
  GRANT USAGE ON SCHEMA analytics_db.reporting TO ROLE bi_analyst_role;
  GRANT SELECT ON ALL TABLES IN SCHEMA analytics_db.reporting TO ROLE bi_analyst_role;

  -- Create a service account for Tableau
  CREATE USER svc_tableau PASSWORD = 'secure_password123';

  -- Assign the role to the service account
  GRANT ROLE bi_analyst_role TO USER svc_tableau;
  ```

**2. Warehouse Assignment**
- Restrict the service account to a specific warehouse:
  ```sql
  -- Create a warehouse for BI tools
  CREATE WAREHOUSE bi_wh WITH WAREHOUSE_SIZE = 'MEDIUM';

  -- Assign the warehouse as the default for the service account
  ALTER USER svc_tableau SET DEFAULT_WAREHOUSE = bi_wh;
  ```

**3. Network Policy Configuration**
- Create a network policy to restrict BI tool connections:
  ```sql
  -- Create a network policy for corporate IPs
  CREATE NETWORK POLICY bi_tool_policy
    ALLOWED_IP_LIST = ('192.0.2.0/24', '203.0.113.0/24');

  -- Assign the policy to the service account
  ALTER USER svc_tableau SET NETWORK_POLICY = bi_tool_policy;
  ```

**4. Dynamic Data Masking**
- Apply masking policies to sensitive columns:
  ```sql
  -- Create a masking policy for the 'salary' column
  CREATE MASKING POLICY salary_mask AS (val NUMBER) RETURNS NUMBER ->
    CASE
      WHEN CURRENT_ROLE() IN ('BI_ANALYST_ROLE') THEN val
      ELSE '*****'
    END;

  -- Apply the policy to the column
  ALTER TABLE analytics_db.reporting.employees
    MODIFY COLUMN salary SET MASKING POLICY salary_mask;
  ```

**5. Row-Level Security**
- Implement row-level filters to restrict data visibility:
  ```sql
  -- Create a row access policy for regional data
  CREATE ROW ACCESS POLICY regional_filter AS (region STRING) RETURNS BOOLEAN ->
    CASE
      WHEN CURRENT_ROLE() = 'BI_ANALYST_ROLE' AND region = 'North America' THEN TRUE
      ELSE FALSE
    END;

  -- Apply the policy to the table
  ALTER TABLE analytics_db.reporting.sales
    ADD ROW ACCESS POLICY regional_filter ON (region);
  ```

> [!TIP]
> Use Snowflake’s `QUERY_HISTORY` view to monitor BI tool activity:
> ```sql
> SELECT query_text, user_name, warehouse_name, execution_status
> FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY())
> WHERE user_name = 'SVC_TABLEAU'
> ORDER BY start_time DESC;
> ```

**Stakeholder Integration**
- **Security Teams:** Define access policies, approve role assignments, and review audit logs for compliance.
- **BI Administrators:** Configure BI tools to use Snowflake service accounts and manage connection settings.
- **Data Owners:** Validate that BI tools access only authorized datasets and apply necessary security policies.
- **Audit Teams:** Monitor BI tool activity and ensure adherence to data governance policies.

> [!CAUTION]
> BI tools often require broad read access to generate reports. Avoid granting `SELECT` privileges on entire databases or schemas unless absolutely necessary. Use schema-level or table-level grants to limit exposure.

**Indicators of Success**
- **Least-Privilege Enforcement:** BI tools operate with roles that have only the necessary permissions, with no excessive access.
- **Audit Trail Completeness:** All BI tool queries and data exports are logged and reviewable.
- **Incident-Free Operations:** No security incidents or data leaks traced back to BI tool access.
- **User Satisfaction:** Analytics teams report seamless access to required data without unnecessary restrictions.
- **Compliance Adherence:** Audits confirm BI tool access aligns with organizational policies and regulatory requirements.

> [!WARNING]
> Never embed Snowflake credentials directly in BI tool configuration files. Always use secure credential storage (e.g., secrets management tools) and rotate passwords regularly.
