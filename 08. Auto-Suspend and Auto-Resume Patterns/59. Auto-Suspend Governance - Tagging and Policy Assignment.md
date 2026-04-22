# Auto-Suspend Governance with Tagging and Policy Assignment in Snowflake

**Description:**
This concept ensures that Snowflake virtual warehouses automatically suspend after a period of inactivity, optimizing cost and resource utilization. It uses Snowflake's tagging and policy assignment features to implement governance around auto-suspension behavior.

## Short Explanation
**Overview:** Using Snowflake tags to classify virtual warehouses based on their intended auto-suspend behavior and retrieve that information.

**Problem Solved:** Prevents runaway costs by automatically suspending idle Snowflake virtual warehouses.

**Importance:** Crucial for cost optimization and resource governance in cloud data platforms.

**Use Cases:**
- Cost Optimization for Diverse Workloads
- Compliance and Auditing
- Automated Policy Enforcement/Reporting

### Typical Scenario
A Snowflake environment with multiple virtual warehouses supporting different types of workloads, requiring different idle times before suspension to balance cost and responsiveness.

### Core Snowflake SQL Objects Used
`CREATE TAG`, `ALTER WAREHOUSE`, `SELECT`

### Code Source Execution
```sql
```sql
CREATE TAG IF NOT EXISTS governance.auto_suspend_policy 
  COMMENT = 'Tag to categorize auto-suspend behavior: Cost-Sensitive, Standard, Long-Running';

ALTER WAREHOUSE my_cost_sensitive_wh SET TAG governance.auto_suspend_policy = 'Cost-Sensitive';
ALTER WAREHOUSE my_cost_sensitive_wh SET AUTO_SUSPEND = 60; 

CREATE WAREHOUSE my_standard_wh WITH 
  WAREHOUSE_SIZE = 'SMALL'
  AUTO_SUSPEND = 300 
  AUTO_RESUME = TRUE;

ALTER WAREHOUSE my_standard_wh SET TAG governance.auto_suspend_policy = 'Standard';

SELECT
    warehouse_name,
    auto_suspend,
    GET_TAG('governance.auto_suspend_policy', 'WAREHOUSE', warehouse_name) AS auto_suspend_tag
FROM snowflake.account_usage.warehouses
WHERE deleted IS NULL;
```
```

### Example Execution Logic Step-by-Step
The provided SQL code demonstrates how to use Snowflake tags to classify virtual warehouses based on their intended auto-suspend behavior and then retrieve that information.

### Implementation Example
```sql
-- 1. Create a Custom Tag for Auto-Suspend Policy
CREATE TAG IF NOT EXISTS governance.auto_suspend_policy 
  COMMENT = 'Tag to categorize auto-suspend behavior: Cost-Sensitive, Standard, Long-Running';

-- 2. Apply the Tag to a Virtual Warehouse
ALTER WAREHOUSE my_cost_sensitive_wh SET TAG governance.auto_suspend_policy = 'Cost-Sensitive';

-- 3. Create a Custom Policy (e.g., a default auto-suspend for cost-sensitive)
ALTER WAREHOUSE my_cost_sensitive_wh SET AUTO_SUSPEND = 60; 

-- 4. Create another warehouse with a different auto-suspend policy (conceptual)
CREATE WAREHOUSE my_standard_wh WITH 
  WAREHOUSE_SIZE = 'SMALL'
  AUTO_SUSPEND = 300 
  AUTO_RESUME = TRUE;

-- 5. Apply a different tag value to the standard warehouse
ALTER WAREHOUSE my_standard_wh SET TAG governance.auto_suspend_policy = 'Standard';

-- 6. Retrieve warehouses and their auto-suspend settings along with the tag
SELECT
    warehouse_name,
    auto_suspend,
    GET_TAG('governance.auto_suspend_policy', 'WAREHOUSE', warehouse_name) AS auto_suspend_tag
FROM snowflake.account_usage.warehouses
WHERE deleted IS NULL;
```

### Explanation of the Code
- Step 1: Create a custom tag for auto-suspend policy.
- Step 2: Apply the tag to a virtual warehouse.
- Step 3: Set the auto-suspend policy for the warehouse.
- Step 4: Create another warehouse with a different auto-suspend policy.
- Step 5: Apply a different tag value to the standard warehouse.
- Step 6: Retrieve warehouses and their auto-suspend settings along with the tag.

### When to Use
- Cost Optimization for Diverse Workloads
- Compliance and Auditing
- Automated Policy Enforcement/Reporting

### When Not to Use
- Static, Single-Warehouse Environments
- Warehouses with Continuous, Long-Running Workloads
- Ignoring Actual Usage Patterns

### Common Mistakes
- Setting `AUTO_SUSPEND = 0` but still applying a short auto-suspend tag
- Not having a clear naming convention or definition for tag values
- Forgetting to actually set the `AUTO_SUSPEND` parameter
- Over-tagging or Under-tagging

### Expected Output
**Description:** A table showing the warehouse name, its configured `AUTO_SUSPEND` value, and the value of the `governance.auto_suspend_policy` tag.

**Schema:**
- WAREHOUSE_NAME
- AUTO_SUSPEND
- AUTO_SUSPEND_TAG

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*