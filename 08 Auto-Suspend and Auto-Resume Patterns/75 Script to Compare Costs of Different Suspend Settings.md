# Script to Compare Costs of Different Suspend Settings in Snowflake

This concept involves writing a SQL script in Snowflake to analyze and compare the cost implications of various warehouse suspend settings. It exists to help Snowflake users optimize their compute spending by understanding how different automatic suspension policies affect credit consumption based on their workload patterns.

**Short Explanation**

This script analyzes historical warehouse usage data to simulate the costs of different auto-suspend configurations. It helps users identify the most cost-effective suspend setting for their Snowflake virtual warehouses.

- **What problem does this SQL feature solve?** It solves the problem of inefficient cloud resource utilization and unexpected Snowflake credit consumption due to suboptimal warehouse suspend settings.
- **Why is it important in databases or analytics?** In cloud data platforms like Snowflake, compute costs are directly tied to active warehouse time. Optimizing suspend settings can lead to significant cost savings, which is crucial for budget management in data analytics.
- **Where is it commonly used in real workflows?** Data platform administrators, financial analysts, and cloud cost optimization teams frequently use such analyses to fine-tune Snowflake configurations and reduce operational expenses.

## Implementation Example

```sql
WITH WAREHOUSE_USAGE AS (
    SELECT
        START_TIME,
        END_TIME,
        WAREHOUSE_ID,
        WAREHOUSE_NAME,
        CREDITS_USED,
        -- Calculate actual active duration in seconds
        DATEDIFF(SECOND, START_TIME, END_TIME) AS ACTUAL_ACTIVE_SECONDS
    FROM
        SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY
    WHERE
        START_TIME >= DATEADD(MONTH, -1, CURRENT_DATE()) -- Last 30 days of data
        AND WAREHOUSE_NAME = 'MY_ANALYTICS_WH' -- Specify the warehouse to analyze
),
SIMULATED_SUSPEND_COSTS AS (
    SELECT
        WU.WAREHOUSE_NAME,
        -- Original cost
        SUM(WU.CREDITS_USED) AS ACTUAL_CREDITS_USED,
        -- Simulate 1 minute (60 seconds) auto-suspend
        SUM(CASE WHEN WU.ACTUAL_ACTIVE_SECONDS > 60 THEN WU.CREDITS_USED ELSE 0.0001 -- Minimum charge for quick activity
            END) AS SIMULATED_CREDITS_1_MIN,
        -- Simulate 5 minutes (300 seconds) auto-suspend
        SUM(CASE WHEN WU.ACTUAL_ACTIVE_SECONDS > 300 THEN WU.CREDITS_USED ELSE 0.0001
            END) AS SIMULATED_CREDITS_5_MIN,
        -- Simulate 10 minutes (600 seconds) auto-suspend
        SUM(CASE WHEN WU.ACTUAL_ACTIVE_SECONDS > 600 THEN WU.CREDITS_USED ELSE 0.0001
            END) AS SIMULATED_CREDITS_10_MIN,
        -- Simulate 20 minutes (1200 seconds) auto-suspend
        SUM(CASE WHEN WU.ACTUAL_ACTIVE_SECONDS > 1200 THEN WU.CREDITS_USED ELSE 0.0001
            END) AS SIMULATED_CREDITS_20_MIN
    FROM
        WAREHOUSE_USAGE WU
    GROUP BY
        WU.WAREHOUSE_NAME
)
SELECT
    SSC.WAREHOUSE_NAME,
    SSC.ACTUAL_CREDITS_USED,
    SSC.SIMULATED_CREDITS_1_MIN,
    SSC.SIMULATED_CREDITS_5_MIN,
    SSC.SIMULATED_CREDITS_10_MIN,
    SSC.SIMULATED_CREDITS_20_MIN
FROM
    SIMULATED_SUSPEND_COSTS SSC;
```

## Explanation of the Code

This SQL script uses Common Table Expressions (CTEs) to organize the analysis of Snowflake warehouse metering history.

- **`WAREHOUSE_USAGE` CTE**:
    - **What it does**: This CTE retrieves raw usage data for a specific Snowflake warehouse over the last month.
    - **How it changes the result set**: It provides the foundational data for the analysis, including start/end times, warehouse details, actual credits used, and importantly, the calculated `ACTUAL_ACTIVE_SECONDS` for each metering event.
    - **`SELECT`**: Specifies the columns to retrieve (`START_TIME`, `END_TIME`, `WAREHOUSE_ID`, `WAREHOUSE_NAME`, `CREDITS_USED`, `ACTUAL_ACTIVE_SECONDS`).
    - **`FROM`**: Queries the `SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY` view, which contains historical usage data for all warehouses.
    - **`WHERE`**: Filters the data to the last 30 days (`START_TIME >= DATEADD(MONTH, -1, CURRENT_DATE())`) and to a specific warehouse (`WAREHOUSE_NAME = 'MY_ANALYTICS_WH'`).
    - **`DATEDIFF(SECOND, START_TIME, END_TIME)`**: This function calculates the duration of each warehouse activity period in seconds.

- **`SIMULATED_SUSPEND_COSTS` CTE**:
    - **What it does**: This CTE calculates the total credits used under the current settings and simulates credit usage under different auto-suspend settings (1, 5, 10, and 20 minutes).
    - **How it changes the result set**: It aggregates the usage data and applies conditional logic to estimate costs for various suspend thresholds.
    - **`SELECT`**: Selects the `WAREHOUSE_NAME` and calculates several `SUM` aggregates for actual and simulated credit usage.
    - **`SUM(WU.CREDITS_USED)`**: This sums up the `CREDITS_USED` as recorded, representing the actual cost.
    - **`SUM(CASE WHEN WU.ACTUAL_ACTIVE_SECONDS > THEN WU.CREDITS_USED ELSE 0.0001 END)`**: This is the core of the simulation. For each activity period:[THRESHOLD]
        - If the `ACTUAL_ACTIVE_SECONDS` is *greater* than the simulated auto-suspend `THRESHOLD` (e.g., 60 seconds for 1 minute), it assumes the warehouse would have been active for that duration and includes its `CREDITS_USED`.
        - If the `ACTUAL_ACTIVE_SECONDS` is *less than or equal to* the `THRESHOLD`, it assumes the warehouse would have suspended, incurring only the minimum charge for a brief activity (Snowflake charges for a minimum of 60 seconds if a warehouse runs for less than 60 seconds, which translates to 0.0001 credits for a `XSMALL` warehouse; this value can be adjusted based on the specific warehouse size for more accuracy).
    - **`GROUP BY`**: Groups the results by `WAREHOUSE_NAME` to get totals for each warehouse.

- **Final `SELECT` statement**:
    - **What it does**: Presents the calculated actual and simulated costs in a readable format.
    - **How it changes the result set**: This is the final output table, showing the credit consumption comparison.
    - **`SELECT`**: Retrieves all the aggregated credit columns from the `SIMULATED_SUSPEND_COSTS` CTE.
    - **`FROM`**: References the `SIMULATED_SUSPEND_COSTS` CTE.

## When to Use

1. **Cost Optimization Audits**: When trying to identify areas for cost reduction in your Snowflake account.
    * *Scenario*: Your monthly Snowflake bill is higher than expected, and you suspect that warehouses are running idle for too long. This script helps pinpoint which warehouses could benefit from tighter auto-suspend settings.
2. **Performance vs. Cost Analysis**: To strike a balance between query performance (by keeping warehouses warm) and cost efficiency.
    * *Scenario*: A team complains about slow query startup times after a warehouse suspends quickly. You can use this script to show the credit savings gained by a short suspend time versus the cost of keeping it warm longer.
3. **New Warehouse Provisioning**: Before setting up new warehouses, to determine an optimal initial auto-suspend policy based on anticipated workload patterns.
    * *Scenario*: You are launching a new data ingestion pipeline that runs intermittently throughout the day. You can simulate different suspend settings on a similar existing warehouse's usage pattern to choose the best initial setting for the new warehouse.

## When Not to Use

1. **For Real-time Monitoring**: This script uses historical data and is not designed for real-time alerts or operational monitoring of warehouse status.
    * *Situation*: You need to know immediately if a warehouse is running inefficiently right now. This script won't help with live monitoring.
2. **For Deep Workload Analysis**: While it helps with cost, it doesn't provide insights into query performance, concurrency issues, or specific query runtimes.
    * *Situation*: You need to understand *why* queries are slow or which queries are consuming the most resources. This script focuses solely on cost related to suspend settings.
3. **Without Sufficient Historical Data**: The accuracy of the simulation relies heavily on a representative period of historical warehouse usage.
    * *Situation*: You've only been using a warehouse for a few days, or its workload has drastically changed. The historical data won't accurately reflect future usage patterns, making the simulation less reliable.

## Common Mistakes

1. **Ignoring Minimum Charge**: Not accounting for Snowflake's 60-second minimum billing increment for short warehouse activities. If an activity lasts 10 seconds, it's still billed for 60 seconds. The `0.0001` in the `CASE` statement is an approximation for an XSMALL warehouse's 60-second charge; this needs to be scaled for larger warehouses or calculated precisely.
2. **Using Insufficient Data History**: Analyzing only a very short period (e.g., one day) might not capture the full range of workload patterns, leading to misleading recommendations. A month or more of data is usually better.
3. **Analyzing the Wrong Warehouse**: Accidentally running the script for a warehouse with a completely different workload pattern than the one you intend to optimize. Always ensure the `WAREHOUSE_NAME` filter is correct and relevant.
4. **Not Considering User Experience**: Optimizing purely for cost might lead to overly aggressive suspend settings, causing frequent cold starts and frustrating users with query delays. Balance cost savings with acceptable user experience.

## Expected Output

The resulting dataset will be a single row (for the analyzed warehouse) showing the actual credits used over the specified period, alongside the estimated credits that would have been consumed under various auto-suspend configurations. Each column will represent the total credits for that specific suspend setting.

| WAREHOUSE_NAME | ACTUAL_CREDITS_USED | SIMULATED_CREDITS_1_MIN | SIMULATED_CREDITS_5_MIN | SIMULATED_CREDITS_10_MIN | SIMULATED_CREDITS_20_MIN |
| :------------- | :------------------ | :---------------------- | :---------------------- | :----------------------- | :----------------------- |
| MY_ANALYTICS_WH | 125.75 | 102.30 | 98.15 | 105.40 | 118.90 |

**Explanation of Columns:**

- **`WAREHOUSE_NAME`**: The name of the Snowflake virtual warehouse analyzed.
- **`ACTUAL_CREDITS_USED`**: The total number of Snowflake credits actually consumed by the warehouse during the analyzed period under its current auto-suspend setting.
- **`SIMULATED_CREDITS_1_MIN`**: The estimated total credits that would have been consumed if the warehouse had an auto-suspend setting of 1 minute.
- **`SIMULATED_CREDITS_5_MIN`**: The estimated total credits that would have been consumed if the warehouse had an auto-suspend setting of 5 minutes.
- **`SIMULATED_CREDITS_10_MIN`**: The estimated total credits that would have been consumed if the warehouse had an auto-suspend setting of 10 minutes.
-   **`SIMULATED_CREDITS_20_MIN`**: The estimated total credits that would have been consumed if the warehouse had an auto-suspend setting of 20 minutes.
