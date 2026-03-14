# Script to Identify Warehouses with Null Auto-Suspend in Snowflake

This concept revolves around a specific SQL script used within Snowflake to monitor the configuration of virtual warehouses. It helps administrators quickly find warehouses that might have an undefined or 'null' setting for their `AUTO_SUSPEND` parameter, which is crucial for cost management and resource optimization. Understanding this script allows for proactive identification of potential efficiency issues.

**Short Explanation**

This SQL feature helps Snowflake users find warehouses where the automatic suspension setting (`AUTO_SUSPEND`) isn't explicitly defined. This is important because properly configured `AUTO_SUSPEND` prevents warehouses from running idly and incurring unnecessary costs. It's commonly used by Snowflake administrators and operations teams for cost control, auditing, and maintaining optimal resource utilization.

- **What problem does this SQL feature solve?** It solves the problem of identifying potentially misconfigured or unoptimized Snowflake warehouses that could lead to unexpected credit consumption due to not suspending automatically.
- **Why is it important in databases or analytics?** In Snowflake, where billing is based on compute usage, proper `AUTO_SUSPEND` settings are vital for controlling costs. A null `AUTO_SUSPEND` could mean the warehouse never suspends, wasting money.
- **Where is it commonly used in real workflows?** It's typically used in daily or weekly administrative checks, cost optimization initiatives, and during warehouse provisioning audits in organizations heavily utilizing Snowflake.

## Implementation Example

```sql
SELECT
    "name",
    "state",
    "auto_suspend"
FROM
    SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSES
WHERE
    "deleted_on" IS NULL AND "auto_suspend" IS NULL;
```

## Explanation of the Code

This query is designed to fetch specific details about your Snowflake warehouses, filtering for those with undefined `AUTO_SUSPEND` settings.

- **`SELECT "name", "state", "auto_suspend"`**: This clause specifies the columns you want to retrieve.
    - `name`: What it does - Selects the name of each virtual warehouse. How it changes the result set - Provides the unique identifier for each warehouse.
    - `state`: What it does - Selects the current operational state of the warehouse (e.g., 'STARTED', 'SUSPENDED'). How it changes the result set - Shows the active status of the warehouse.
    - `auto_suspend`: What it does - Selects the auto-suspension timeout setting for each warehouse, in seconds. How it changes the result set - Displays the value (or NULL) for the auto-suspend parameter.

- **`FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSES`**: This clause specifies the data source.
    - What it does - It queries the `WAREHOUSES` view within the `ACCOUNT_USAGE` schema, which provides historical and current information about all warehouses in your Snowflake account.
    - How it changes the result set - It defines the raw dataset from which the `SELECT` statement pulls information.

- **`WHERE "deleted_on" IS NULL AND "auto_suspend" IS NULL`**: This clause filters the rows returned by the query.
    - What it does - It includes two conditions:
        1. `"deleted_on" IS NULL`: Ensures that only currently active (not deleted) warehouses are considered.
        2. `"auto_suspend" IS NULL`: Filters for warehouses where the `AUTO_SUSPEND` parameter is explicitly not set or is recorded as `NULL`.
    - How it changes the result set - It narrows down the results to only show active warehouses that have an undefined or missing `AUTO_SUSPEND` configuration.

## When to Use

1.  **Cost Optimization Audits:** Regularly audit warehouse configurations to ensure optimal cost efficiency. If `AUTO_SUSPEND` is NULL, the warehouse may never suspend, leading to continuous billing.
    *   *Scenario:* A new data team provisions several warehouses, and an administrator wants to ensure all new warehouses have appropriate cost-saving configurations enabled.
2.  **New Warehouse Provisioning:** After creating new warehouses, run this script as a check to confirm that all critical parameters, especially `AUTO_SUSPEND`, are correctly set and not left in a default or undefined state.
    *   *Scenario:* A DevOps engineer has automated warehouse creation; this script can be part of a post-creation validation step.
3.  **Troubleshooting High Costs:** If there's an unexpected spike in Snowflake credit consumption, this script can quickly identify warehouses that might be contributing to the issue due by not suspending automatically.
    *   *Scenario:* The finance department reports increased Snowflake spending; the operations team uses this script to pinpoint potential misconfigurations.

## When Not to Use

1.  **When focusing on performance tuning:** This script is for configuration auditing, not for analyzing warehouse performance metrics like query latency or concurrency.
    *   *Inefficiency:* Analyzing query history or warehouse load for performance optimization requires different views and metrics (e.g., `QUERY_HISTORY`, `WAREHOUSE_LOAD_HISTORY`).
2.  **To manage warehouse operations directly (start/stop/resize):** This is a read-only script for identification, not for altering warehouse properties or states.
    *   *Wrong approach:* To modify `AUTO_SUSPEND` or change warehouse size, you would use `ALTER WAREHOUSE` commands.
3.  **For real-time operational alerts:** While useful for identifying issues, `ACCOUNT_USAGE` views have a latency of up to 3 hours. For immediate alerts on warehouse state changes, external monitoring tools or other Snowflake features like `INFORMATION_SCHEMA` might be more suitable.
    *   *Inefficiency:* If you need to know *the exact moment* a warehouse changes state, `ACCOUNT_USAGE` data might be too delayed.

## Common Mistakes

1.  **Forgetting `WHERE "deleted_on" IS NULL`**: Not filtering out deleted warehouses can clutter the results with irrelevant historical data, making it harder to focus on active issues.
2.  **Assuming `NULL` means "never suspend"**: While functionally true in many contexts (if not explicitly set, it won't suspend), it's important to differentiate `NULL` from an explicitly set high value (e.g., 14400 seconds for 4 hours). Both can lead to high costs, but `NULL` indicates a missing configuration.
3.  **Not having appropriate roles/permissions**: Users without the `ACCOUNTADMIN` role or equivalent permissions on the `SNOWFLAKE.ACCOUNT_USAGE` schema will not be able to execute this query successfully.

## Expected Output

The resulting dataset will show a list of active Snowflake warehouses where the `AUTO_SUSPEND` parameter has not been explicitly defined. Each row represents one such warehouse, providing its name, current state, and confirming the `NULL` value for `auto_suspend`.

| name               | state     | auto_suspend |
| :----------------- | :-------- | :----------- |
| ANALYTICS_WH_LARGE | STARTED   | NULL         |
| ETL_WAREHOUSE      | SUSPENDED | NULL         |
| DEV_COMPUTE_WH     | STARTED   | NULL         |

- **name**: The unique identifier for the virtual warehouse.
- **state**: The current operational status of the warehouse (e.g., STARTED, SUSPENDED, RESUMING).
- **auto_suspend**: The value for the automatic suspension timeout. In this output, it will always be `NULL`, indicating that the parameter is not set for these specific warehouses.
