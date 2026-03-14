# 59 Auto-Suspend Governance - Tagging and Policy Assignment in Snowflake

This concept refers to the practice of using Snowflake's tagging and policy assignment features to implement governance around auto-suspension behavior, particularly for virtual warehouses. It ensures that warehouses automatically suspend after a period of inactivity, optimizing cost and resource utilization, and that these behaviors are consistently applied and tracked using a structured metadata approach.

**Short Explanation**

This SQL feature solves the problem of uncontrolled cloud costs by automatically pausing compute resources (warehouses) when they are not in use. It's important in databases and analytics to manage expenses efficiently and prevent idle resources from incurring unnecessary charges. It's commonly used in data warehousing environments to enforce cost governance and resource management policies across various teams and projects.

- **What problem does this SQL feature solve?** It prevents runaway costs by automatically suspending idle Snowflake virtual warehouses, ensuring resources are only consumed when actively processing queries.
- **Why is it important in databases or analytics?** It's crucial for cost optimization and resource governance in cloud data platforms, as idle compute can quickly become expensive.
- **Where is it commonly used in real workflows?** In any environment where multiple virtual warehouses are provisioned for different teams, projects, or workloads, and cost control is a priority.

## Implementation Example

```sql
-- 1. Create a Custom Tag for Auto-Suspend Policy
CREATE TAG IF NOT EXISTS governance.auto_suspend_policy
  COMMENT = 'Tag to categorize auto-suspend behavior: Cost-Sensitive, Standard, Long-Running';

-- 2. Apply the Tag to a Virtual Warehouse
--    Assign 'Cost-Sensitive' policy indicating aggressive auto-suspend
ALTER WAREHOUSE my_cost_sensitive_wh SET TAG governance.auto_suspend_policy = 'Cost-Sensitive';

-- 3. Create a Custom Policy (e.g., a default auto-suspend for cost-sensitive)
--    Note: Auto-suspend is a warehouse parameter, not a separate policy object.
--    This is how you'd typically set it, linking it conceptually to the tag.
ALTER WAREHOUSE my_cost_sensitive_wh SET AUTO_SUSPEND = 60; -- Suspend after 60 seconds of inactivity

-- 4. Create another warehouse with a different auto-suspend policy (conceptual)
CREATE WAREHOUSE my_standard_wh WITH
  WAREHOUSE_SIZE = 'SMALL'
  AUTO_SUSPEND = 300 -- Suspend after 300 seconds (5 minutes)
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

## Explanation of the Code

This code demonstrates how to use Snowflake tags to classify virtual warehouses based on their intended auto-suspend behavior and then retrieve that information.

- `CREATE TAG IF NOT EXISTS governance.auto_suspend_policy COMMENT = '...'`:
    - **What it does**: This statement defines a new custom tag named `auto_suspend_policy` within the `governance` schema. Tags are metadata objects that can be applied to other Snowflake objects.
    - **How it changes the result set**: It creates a new tag in the Snowflake account. This doesn't directly change a result set but enables future tagging operations.
- `ALTER WAREHOUSE my_cost_sensitive_wh SET TAG governance.auto_suspend_policy = 'Cost-Sensitive';`:
    - **What it does**: This statement assigns the previously created tag `auto_suspend_policy` with the value `'Cost-Sensitive'` to the virtual warehouse named `my_cost_sensitive_wh`.
    - **How it changes the result set**: It attaches metadata to the specified warehouse. This doesn't change a query's result set directly, but the tag can be queried later.
- `ALTER WAREHOUSE my_cost_sensitive_wh SET AUTO_SUSPEND = 60;`:
    - **What it does**: This statement configures the `my_cost_sensitive_wh` warehouse to automatically suspend after 60 seconds of inactivity. This is the direct implementation of an auto-suspend policy.
    - **How it changes the result set**: This modifies the operational parameter of the warehouse, affecting its runtime behavior and cost. Not a query result set.
- `CREATE WAREHOUSE my_standard_wh WITH ... AUTO_SUSPEND = 300 ...`:
    - **What it does**: Creates another warehouse, `my_standard_wh`, with an `AUTO_SUSPEND` setting of 300 seconds (5 minutes).
    - **How it changes the result set**: Creates a new warehouse object in the account.
- `ALTER WAREHOUSE my_standard_wh SET TAG governance.auto_suspend_policy = 'Standard';`:
    - **What it does**: Assigns a different tag value (`'Standard'`) to `my_standard_wh`, further categorizing its auto-suspend intent.
    - **How it changes the result set**: Attaches metadata to the warehouse.
- `SELECT warehouse_name, auto_suspend, GET_TAG('governance.auto_suspend_policy', 'WAREHOUSE', warehouse_name) AS auto_suspend_tag FROM snowflake.account_usage.warehouses WHERE deleted IS NULL;`:
    - **What it does**: This query retrieves the name, `auto_suspend` setting, and the value of the `governance.auto_suspend_policy` tag for all active virtual warehouses in the account.
    - **How it changes the result set**:
        - `SELECT`: Specifies the columns to return: `warehouse_name`, `auto_suspend`, and the tag value.
        - `FROM snowflake.account_usage.warehouses`: Specifies the source table, which contains metadata about all warehouses.
        - `WHERE deleted IS NULL`: Filters the results to include only warehouses that have not been deleted.
        - `GET_TAG('governance.auto_suspend_policy', 'WAREHOUSE', warehouse_name)`: This function retrieves the value of the specified tag for each warehouse, allowing you to link the conceptual policy (tag) with the actual setting (`auto_suspend`).

## When to Use

1.  **Cost Optimization for Diverse Workloads**: When you have warehouses supporting different types of workloads (e.g., interactive dashboards, ETL jobs, ad-hoc queries), and each requires a different idle time before suspension to balance cost and responsiveness.
    *   **Scenario**: A "Reporting_WH" should suspend after 1 minute, while an "ETL_WH" might suspend after 30 minutes because its jobs run intermittently. Tags help track and enforce these distinct policies.
2.  **Compliance and Auditing**: To ensure that all new or existing warehouses adhere to predefined governance policies regarding resource usage and cost management.
    *   **Scenario**: An internal audit requires verification that all production warehouses have an auto-suspend policy set to a maximum of 15 minutes. Tagging helps categorize and easily audit these settings.
3.  **Automated Policy Enforcement/Reporting**: Integrating with external tools or scripts that can read Snowflake tags to dynamically adjust warehouse parameters or generate cost-management reports.
    *   **Scenario**: A Python script runs daily, identifies all warehouses tagged "High_Cost_Risk", and verifies their `AUTO_SUSPEND` is set to less than 5 minutes, flagging any non-compliant ones.

## When Not to Use

1.  **Static, Single-Warehouse Environments**: If your Snowflake environment consists of only one or two warehouses with consistent usage patterns and no complex cost management needs.
    *   **Reason**: The overhead of creating and managing tags and policies would outweigh the benefits. Simple manual configuration is sufficient.
2.  **Warehouses with Continuous, Long-Running Workloads**: For warehouses that are expected to be constantly active or have very long-running queries where any auto-suspension would be detrimental to performance or job completion.
    *   **Reason**: Auto-suspend should be `0` (never suspend) for such warehouses, and applying a governance tag for auto-suspend would be redundant or misleading.
3.  **Ignoring Actual Usage Patterns**: If policies are assigned based on tags without regularly reviewing actual warehouse usage.
    *   **Reason**: A warehouse tagged "Cost-Sensitive" might suddenly have continuous long-running jobs, leading to frequent suspensions and resumptions, which can negatively impact performance and user experience. Tags should align with real-world needs.

## Common Mistakes

1.  **Setting `AUTO_SUSPEND = 0` but still applying a "short auto-suspend" tag**: This creates a discrepancy between the metadata (tag) and the actual configuration, leading to confusion and potential governance failures.
2.  **Not having a clear naming convention or definition for tag values**: Using ambiguous tag values like `'Policy_A'` or `'Type_1'` makes it difficult for others to understand the intended auto-suspend behavior without further documentation.
3.  **Forgetting to actually set the `AUTO_SUSPEND` parameter**: Assigning a tag like `'Aggressive_Suspend'` without actually configuring the `AUTO_SUSPEND` parameter on the warehouse means the policy is only conceptual and not enforced.
4.  **Over-tagging or Under-tagging**: Creating too many granular tags that are rarely used or failing to tag critical resources, leading to inconsistent governance.

## Expected Output

The expected output from the `SELECT` query in the implementation example would be a table showing the warehouse name, its configured `AUTO_SUSPEND` value (in seconds), and the value of the `governance.auto_suspend_policy` tag assigned to it. This allows for a clear overview of both the operational setting and the governance classification.

| WAREHOUSE_NAME        | AUTO_SUSPEND | AUTO_SUSPEND_TAG |
|-----------------------|--------------|------------------|
| MY_COST_SENSITIVE_WH  | 60           | Cost-Sensitive   |
| MY_STANDARD_WH        | 300          | Standard         |
| ANOTHER_WAREHOUSE     | 600          | NULL             |

- **WAREHOUSE_NAME**: The unique identifier for the virtual warehouse.
- **AUTO_SUSPEND**: The number of seconds of inactivity after which the warehouse automatically suspends.
- **AUTO_SUSPEND_TAG**: The value of the `governance.auto_suspend_policy` tag assigned to the warehouse, indicating its classification for auto-suspend behavior. `NULL` means no tag was assigned.
