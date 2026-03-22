# Script to Identify Snowflake Warehouses with Null Auto-Suspend

**Description:**
This Snowflake script identifies virtual warehouses with an undefined or 'null' AUTO_SUSPEND parameter, crucial for cost management and resource optimization.

## Short Explanation
**Overview:** This SQL script helps Snowflake administrators find warehouses where the automatic suspension setting (AUTO_SUSPEND) isn't explicitly defined.

**Problem Solved:** It solves the problem of identifying potentially misconfigured or unoptimized Snowflake warehouses that could lead to unexpected credit consumption.

**Importance:** Properly configured AUTO_SUSPEND prevents warehouses from running idly and incurring unnecessary costs.

**Use Cases:**
- Cost Optimization Audits
- New Warehouse Provisioning
- Troubleshooting High Costs

### Typical Scenario
A Snowflake administrator wants to ensure all warehouses have appropriate cost-saving configurations enabled.

### Core Snowflake SQL Objects Used
`SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSES`

### Code Source Execution
```sql
SELECT "name", "state", "auto_suspend" FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSES WHERE "deleted_on" IS NULL AND "auto_suspend" IS NULL;
```

### Example Execution Logic Step-by-Step
This query fetches specific details about Snowflake warehouses, filtering for those with undefined AUTO_SUSPEND settings.

### Implementation Example
The provided SQL script can be used directly in Snowflake to identify warehouses with null auto-suspend.

### Explanation of the Code
- The SELECT clause specifies the columns to retrieve: name, state, and auto_suspend.
- The FROM clause specifies the data source: SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSES.
- The WHERE clause filters the rows returned by the query, ensuring only active warehouses with undefined auto_suspend are considered.

### When to Use
- Regularly audit warehouse configurations to ensure optimal cost efficiency.
- After creating new warehouses, run this script as a check to confirm that all critical parameters are correctly set.
- If there's an unexpected spike in Snowflake credit consumption, use this script to quickly identify contributing warehouses.

### When Not to Use
- When focusing on performance tuning, as this script is for configuration auditing, not performance analysis.
- To manage warehouse operations directly (start/stop/resize), as this script is read-only for identification.

### Common Mistakes
- Forgetting to filter out deleted warehouses.
- Assuming NULL auto_suspend means 'never suspend'.
- Not having appropriate roles/permissions to execute the query.

### Expected Output
**Description:** The resulting dataset will show a list of active Snowflake warehouses where the AUTO_SUSPEND parameter has not been explicitly defined.

**Schema:**
- name
- state
- auto_suspend

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*