# Summary of Key Auto-Suspend Anti-Patterns in Snowflake

**Description:**
This document provides insights into common misconfigurations and practices that undermine the cost-saving benefits of Snowflake's auto-suspend feature.

## Short Explanation
**Overview:** Auto-suspend anti-patterns are common misconfigurations or practices that undermine the cost-saving benefits of Snowflake's auto-suspend feature.

**Problem Solved:** Auto-suspend aims to prevent idle warehouses from incurring costs by automatically pausing compute resources when not in use.

**Importance:** It directly impacts operational costs in a cloud environment where compute is billed by usage.

**Use Cases:**
- For interactive dashboards and ad-hoc queries
- Development and testing environments
- Workloads with clear idle periods

### Typical Scenario
Managing virtual warehouses across all types of Snowflake workloads, from BI dashboards to ETL processes, development, and ad-hoc analytics.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
ALTER WAREHOUSE my_analyst_wh SET AUTO_SUSPEND = 60, AUTO_RESUME = TRUE;
```

### Example Execution Logic Step-by-Step
Modifying the configuration of an existing Snowflake virtual warehouse.

### Implementation Example
Configuring auto-suspend using SQL.

### Explanation of the Code
- ALTER WAREHOUSE my_analyst_wh: This clause specifies that we are modifying the warehouse named my_analyst_wh.
- SET AUTO_SUSPEND = 60: This sets the auto-suspend timeout to 60 seconds.
- AUTO_RESUME = TRUE: This ensures that if a new query is submitted to a suspended warehouse, it will automatically resume.

### When to Use
- For interactive dashboards and ad-hoc queries: Setting a low AUTO_SUSPEND (e.g., 60-300 seconds) helps minimize idle costs between bursts of activity.
- Development and testing environments: Aggressive auto-suspend settings ensure that warehouses don't run unnecessarily when developers aren't actively working.
- Workloads with clear idle periods: Auto-suspend ensures it shuts down post-completion.

### When Not to Use
- Workloads with extremely frequent, short queries and high latency sensitivity: A very low AUTO_SUSPEND can lead to constant suspend/resume cycles, increasing latency.
- Warehouses serving long-running, continuous processes: Disabling auto-suspend might be appropriate to avoid the overhead of suspension checks and resumptions.
- When cache warming is critical for performance: Very short auto-suspend times cause warehouses to frequently suspend, leading to cache invalidation.

### Common Mistakes
- Setting AUTO_SUSPEND to 'Never' or a very high value: This leads to significant cost overruns as warehouses remain running indefinitely, even when idle.
- Setting AUTO_SUSPEND too low for bursty workloads: Setting it to values like 10 or 30 seconds can cause warehouses to suspend and resume too frequently, incurring more charges.
- Ignoring the impact on warehouse cache: Aggressive auto-suspend clears the cache, making queries slower.

### Expected Output
**Description:** Confirming the current auto-suspend setting for warehouses.

**Schema:**
- WAREHOUSE_NAME
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*