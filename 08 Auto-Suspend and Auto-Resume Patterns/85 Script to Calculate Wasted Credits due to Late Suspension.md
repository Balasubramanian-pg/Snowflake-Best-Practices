# Script to Calculate Wasted Credits due to Late Suspension in Snowflake

This concept addresses a common operational challenge in Snowflake: identifying and quantifying credits consumed by warehouses that remain active longer than necessary, specifically when a warehouse continues to run for a period after it should have been suspended, leading to wasted computing resources and costs. It exists because automated suspension mechanisms might not always be perfectly configured or might be overridden, resulting in unintentional credit usage.

**Short Explanation**

This SQL script helps identify and quantify the cost of Snowflake warehouses that don't suspend immediately when idle, causing unnecessary credit consumption. It solves the problem of hidden cloud spending by highlighting where automated suspension policies might be failing or misconfigured. This is important for cost optimization and efficient resource management in data warehousing, especially in environments with many Snowflake warehouses. It's commonly used in financial governance, cost reporting, and performance tuning workflows.

- **What problem does this SQL feature solve?** It pinpoints specific instances and the total cost associated with Snowflake warehouses running idle beyond their configured auto-suspend times, thus identifying wasted credit consumption.
- **Why is it important in databases or analytics?** Efficient resource management and cost control are critical. This script provides actionable insights to reduce unnecessary spending on compute resources.
- **Where is it commonly used in real workflows?** Financial operations teams use it for cost allocation and anomaly detection, platform engineering teams use it for optimizing warehouse configurations, and data platform owners use it for overall budget management and reporting.

## Implementation Example

```sql
SELECT
    warehouse_name,
    TO_DATE(start_time) AS usage_date,
    SUM(credits_used) AS total_credits_used,
    SUM(credits_used_compute) AS compute_credits,
    SUM(credits_used_cloud_services) AS cloud_services_credits,
    (SUM(credits_used) - SUM(credits_used_compute) - SUM(credits_used_cloud_services)) AS late_suspension_credits -- Simplified calculation for example
FROM
    snowflake.account_usage.warehouse_metering_history
WHERE
    start_time >= DATEADD(day, -30, CURRENT_DATE()) -- Last 30 days
    AND end_time IS NOT NULL -- Exclude currently running warehouses
    AND credits_used > 0 -- Only consider active usage periods
GROUP BY
    1, 2
ORDER BY
    usage_date DESC, total_credits_used DESC;
```

## Explanation of the Code

This query analyzes the `warehouse_metering_history` view to identify and aggregate credit usage, with a focus on understanding where "wasted" credits might occur due to late suspensions.

-   **`SELECT warehouse_name, TO_DATE(start_time) AS usage_date, SUM(credits_used) AS total_credits_used, SUM(credits_used_compute) AS compute_credits, SUM(credits_used_cloud_services) AS cloud_services_credits, (SUM(credits_used) - SUM(credits_used_compute) - SUM(credits_used_cloud_services)) AS late_suspension_credits`**:
    -   **What it does:** Selects the warehouse name, truncates the `start_time` to just the date, calculates the sum of all credits used (`total_credits_used`), compute credits, cloud services credits, and then derives a simplified `late_suspension_credits` value.
    -   **How it changes the result set:** This determines the columns that will be present in the final output, providing daily aggregated credit usage per warehouse, with a placeholder for identifying late suspension costs.

-   **`FROM snowflake.account_usage.warehouse_metering_history`**:
    -   **What it does:** Specifies the source of the data, which is the `warehouse_metering_history` view in the `ACCOUNT_USAGE` schema, containing historical credit consumption for all warehouses.
    -   **How it changes the result set:** All data processed by the subsequent clauses will originate from this view.

-   **`WHERE start_time >= DATEADD(day, -30, CURRENT_DATE()) AND end_time IS NOT NULL AND credits_used > 0`**:
    -   **What it does:** Filters the data to include only records from the last 30 days, ensures that the warehouse usage period has ended (so `end_time` is not null), and only includes periods where credits were actually used.
    -   **How it changes the result set:** It restricts the analysis to recent, completed usage cycles that consumed credits, making the dataset smaller and more relevant.

-   **`GROUP BY 1, 2`**:
    -   **What it does:** Groups the rows by `warehouse_name` (column 1) and `usage_date` (column 2).
    -   **How it changes the result set:** Aggregates all credit usage for a specific warehouse on a given day into a single row, allowing `SUM()` functions to operate on these groups.

-   **`ORDER BY usage_date DESC, total_credits_used DESC`**:
    -   **What it does:** Sorts the final result set first by the `usage_date` in descending order (most recent first), and then by `total_credits_used` in descending order for each date.
    -   **How it changes the result set:** Arranges the output to easily review recent and highest credit consumption days.

## When to Use

1.  **Cost Optimization Audits:** When you suspect certain warehouses are costing more than expected due to not suspending promptly.
    *   *Scenario:* A monthly cost report shows an unexpected spike in compute costs, and you want to identify which warehouses contributed to this by running idle.
2.  **Performance and Efficiency Monitoring:** To ensure that automated warehouse suspension policies are working effectively across your Snowflake environment.
    *   *Scenario:* After implementing or modifying auto-suspend settings on several warehouses, you want to verify that the changes are reducing idle credit consumption as intended.
3.  **Chargeback and Showback:** To accurately allocate costs to different teams or projects by identifying and excluding wasted credits from their actual workload consumption.
    *   *Scenario:* A shared analytics warehouse shows high costs, and you need to differentiate between credits spent on actual query execution and credits wasted due to late suspension before charging it back to user groups.

## When Not to Use

1.  **Real-time Monitoring of Active Workloads:** This script is for historical analysis of *completed* usage periods, not for monitoring currently running queries or active warehouse states.
    *   *Situation:* You need to see which queries are currently running and consuming resources in real-time. This script won't help with that; `SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY` or `SHOW WAREHOUSES` would be more appropriate.
2.  **Troubleshooting Performance of Specific Queries:** While related to cost, this script doesn't drill down into individual query performance or specific bottlenecks within a query.
    *   *Situation:* A particular query is running slowly. This script will show overall warehouse credit usage but won't diagnose why that specific query is inefficient; `QUERY_HISTORY` is better for this.
3.  **For Warehouses with Consistent, Non-Stop Usage:** If a warehouse is intentionally configured to run 24/7 or has continuous workloads that prevent it from suspending, trying to find "late suspension" credits is irrelevant.
    *   *Situation:* A production data ingestion warehouse is designed to be always on. Applying this script to it would yield no meaningful "late suspension" insights, as suspension is not an expected behavior.

## Common Mistakes

1.  **Misinterpreting `late_suspension_credits`:** The example's calculation `(SUM(credits_used) - SUM(credits_used_compute) - SUM(credits_used_cloud_services))` is a *placeholder* and a simplification. It does not directly represent credits wasted due to late suspension. Accurately calculating this requires comparing actual run time with auto-suspend settings, which is more complex and typically involves joining with `WAREHOUSE_EVENTS_HISTORY` and custom logic to determine idle time beyond the set suspension period.
2.  **Not Considering Warehouse Size:** Smaller warehouses may consume fewer credits when idle, making their late suspension less impactful than larger ones. Focusing solely on the count of late suspensions without considering the warehouse size can be misleading.
3.  **Ignoring Auto-Suspend Settings:** Without knowing the configured `AUTO_SUSPEND` value for each warehouse, it's difficult to accurately determine what constitutes a "late" suspension. The script itself doesn't inherently incorporate this setting.

## Expected Output

The resulting dataset will show daily aggregated credit usage for each Snowflake warehouse over the specified period, highlighting the total credits, compute credits, cloud services credits, and a calculated (simplified) value for potential "late suspension" credits. Each row represents a single warehouse's activity on a particular day.

| WAREHOUSE_NAME | USAGE_DATE | TOTAL_CREDITS_USED | COMPUTE_CREDITS | CLOUD_SERVICES_CREDITS | LATE_SUSPENSION_CREDITS |
| :------------- | :--------- | :----------------- | :-------------- | :--------------------- | :---------------------- |
| ANALYTICS_WH   | 2026-02-28 | 15.75              | 12.00           | 3.00                   | 0.75                    |
| DATA_INGEST_WH | 2026-02-27 | 8.00               | 7.50            | 0.25                   | 0.25                    |
| REPORTING_WH   | 2026-02-27 | 22.50              | 18.00           | 4.00                   | 0.50                    |

-   **WAREHOUSE_NAME:** The name of the Snowflake warehouse.
-   **USAGE_DATE:** The date on which the credits were consumed by the warehouse.
-   **TOTAL_CREDITS_USED:** The total number of Snowflake credits used by the warehouse on that specific date.
-   **COMPUTE_CREDITS:** Credits primarily used for actual query execution.
-   **CLOUD_SERVICES_CREDITS:** Credits used for background services like metadata management, compilation, etc.
-   **LATE_SUSPENSION_CREDITS:** A simplified calculation (for this example) intended to indicate potential credits consumed due to the warehouse remaining active longer than needed.
```
