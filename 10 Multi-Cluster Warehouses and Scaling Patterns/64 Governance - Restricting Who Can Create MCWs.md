# Restricting Who Can Create Multi-Cluster Warehouses in Snowflake

**Description:**
This topic discusses how to govern and restrict which users or roles can create Multi-Cluster Warehouses (MCWs) in Snowflake. By controlling MCW creation, organizations can better manage resources and costs. This is achieved through a combination of Snowflake roles, privileges, and access control.

## Short Explanation
**Overview:** Restricting MCW creation helps manage resources and costs.

**Problem Solved:** Unauthorized or uncontrolled creation of MCWs can lead to unexpected costs and resource utilization.

**Importance:** It matters in analytics or warehousing for cost management and resource optimization.

**Use Cases:**
- Restricting MCW creation to specific teams
- Implementing cost control measures

### Typical Scenario
A Snowflake administrator wants to allow only specific teams or users to create MCWs, while restricting others to prevent uncontrolled resource usage.

### Core Snowflake SQL Objects Used
`CREATE ROLE`, `GRANT PRIVILEGE`

### Code Source Execution
```sql
CREATE ROLE mcw_admin;
GRANT CREATE WAREHOUSE ON ACCOUNT TO ROLE mcw_admin;
GRANT MONITOR WAREHOUSE ON ACCOUNT TO ROLE mcw_admin;
```

### Example Execution Logic Step-by-Step
To restrict who can create MCWs, first create a custom role (e.g., 'mcw_admin'). Then, grant the 'CREATE WAREHOUSE' privilege on the account to this role. Finally, grant the 'MONITOR WAREHOUSE' privilege to ensure the role can monitor MCW usage.

### Implementation Example
To implement this, a Snowflake administrator would execute the following SQL commands:
1. Create a new role for MCW administration.
2. Grant the necessary privileges to this role.
3. Assign this role to the desired users or teams.

### Explanation of the Code
- Step 1: Create a custom role for MCW administration.
- Step 2: Grant 'CREATE WAREHOUSE' and 'MONITOR WAREHOUSE' privileges to the role.
- Step 3: Assign the role to users or teams that should have MCW creation rights.

### When to Use
- When you need to control MCW creation for cost management
- When you want to restrict MCW creation to specific teams or users

### When Not to Use
- When all users should have unrestricted MCW creation rights
- In development environments where cost management is not a concern

### Common Mistakes
- Not granting the 'MONITOR WAREHOUSE' privilege
- Not restricting MCW creation to specific roles or users

### Expected Output
**Description:** The expected output is a controlled and governed environment where only authorized users or roles can create MCWs.

**Schema:**
- Role Name
- Privilege

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*