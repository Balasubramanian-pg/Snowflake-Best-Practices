# Script to Compare Costs of Different Suspend Settings in Snowflake

**Description:**
This Snowflake script analyzes historical warehouse usage data to simulate the costs of different auto-suspend configurations. It helps users identify the most cost-effective suspend setting for their Snowflake virtual warehouses.

## Short Explanation
**Overview:** The script simulates the costs of various auto-suspend settings for Snowflake warehouses.

**Problem Solved:** It solves the problem of inefficient cloud resource utilization and unexpected Snowflake credit consumption due to suboptimal warehouse suspend settings.

**Importance:** Optimizing suspend settings can lead to significant cost savings, which is crucial for budget management in data analytics.

**Use Cases:**
- Cost Optimization Audits
- Performance vs. Cost Analysis
- New Warehouse Provisioning

### Typical Scenario
A Snowflake user wants to optimize their warehouse suspend settings to reduce costs.

### Core Snowflake SQL Objects Used
`WAREHOUSE_USAGE CTE`, `SIMULATED_SUSPEND_COSTS CTE`

### Code Source Execution
```sql
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
```

### Example Execution Logic Step-by-Step
The script uses two Common Table Expressions (CTEs) to analyze Snowflake warehouse metering history and simulate the costs of different auto-suspend settings.

### Implementation Example
The script analyzes historical warehouse usage data to simulate the costs of different auto-suspend configurations.

### Explanation of the Code
- The WAREHOUSE_USAGE CTE retrieves raw usage data for a specific Snowflake warehouse over the last month.
- The SIMULATED_SUSPEND_COSTS CTE calculates the total credits used under the current settings and simulates credit usage under different auto-suspend settings (1, 5, 10, and 20 minutes).

### When to Use
- Cost Optimization Audits: When trying to identify areas for cost reduction in your Snowflake account.
- Performance vs. Cost Analysis: To strike a balance between query performance (by keeping warehouses warm) and cost efficiency.
- New Warehouse Provisioning: Before setting up new warehouses, to determine an optimal initial auto-suspend policy based on anticipated workload patterns.

### When Not to Use
- For Real-time Monitoring: This script uses historical data and is not designed for real-time alerts or operational monitoring of warehouse status.
- For Deep Workload Analysis: While it helps with cost, it doesn't provide insights into query performance, concurrency issues, or specific query runtimes.
- Without Sufficient Historical Data: The accuracy of the simulation relies heavily on a representative period of historical warehouse usage.

### Common Mistakes
- Ignoring Minimum Charge: Not accounting for Snowflake's 60-second minimum billing increment for short warehouse activities.
- Using Insufficient Data History: Analyzing only a very short period (e.g., one day) might not capture the full range of workload patterns, leading to misleading recommendations.
- Analyzing the Wrong Warehouse: Accidentally running the script for a warehouse with a completely different workload pattern than the one you intend to optimize.

### Expected Output
**Description:** The resulting dataset will be a single row (for the analyzed warehouse) showing the actual credits used over the specified period, alongside the estimated credits that would have been consumed under various auto-suspend configurations.

**Schema:**
- WAREHOUSE_NAME
- ACTUAL_CREDITS_USED
- SIMULATED_CREDITS_1_MIN
- SIMULATED_CREDITS_5_MIN
- SIMULATED_CREDITS_10_MIN
- SIMULATED_CREDITS_20_MIN

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*