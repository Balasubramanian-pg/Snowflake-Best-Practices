# The 'Follow-the-Sun' Warehouse Pattern

**Description:**
This Snowflake pattern optimizes costs and maintains efficient data operations by leveraging auto-suspend and auto-resume features across different regions.

## Short Explanation
**Overview:** The 'Follow-the-Sun' Warehouse Pattern is a strategy for managing virtual warehouses across multiple regions to optimize costs and performance.

**Problem Solved:** It solves the problem of high costs and performance degradation due to misconfigured auto-suspend settings in Snowflake.

**Importance:** It's crucial in cloud data warehousing for managing consumption-based billing and ensuring resources are aligned with actual demand.

**Use Cases:**
- Interactive dashboards and ad-hoc queries
- Development and testing environments
- Workloads with clear idle periods

### Typical Scenario
A business with global users wants to optimize their Snowflake costs and performance by strategically suspending and resuming warehouses based on usage patterns across different regions.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
ALTER WAREHOUSE my_analyst_wh SET AUTO_SUSPEND = 60, AUTO_RESUME = TRUE;
```

### Example Execution Logic Step-by-Step
The SQL command modifies the configuration of an existing Snowflake virtual warehouse, setting the auto-suspend timeout to 60 seconds and enabling auto-resume.

### Implementation Example
To implement the 'Follow-the-Sun' pattern, configure multiple warehouses across regions with optimal auto-suspend and auto-resume settings based on anticipated usage patterns.

### Explanation of the Code
- `ALTER WAREHOUSE my_analyst_wh`: This clause specifies that we are modifying the warehouse named `my_analyst_wh`.
- `SET AUTO_SUSPEND = 60`: This sets the auto-suspend timeout to 60 seconds. If the warehouse remains inactive for 60 consecutive seconds, it will automatically suspend.
- `AUTO_RESUME = TRUE`: This ensures that if a new query is submitted to a suspended warehouse, it will automatically resume.

### When to Use
- For interactive dashboards and ad-hoc queries
- Development and testing environments
- Workloads with clear idle periods

### When Not to Use
- Workloads with extremely frequent, short queries and high latency sensitivity
- Warehouses serving long-running, continuous processes
- When cache warming is critical for performance

### Common Mistakes
- Setting `AUTO_SUSPEND` to 'Never' or a very high value
- Setting `AUTO_SUSPEND` too low for bursty workloads
- Ignoring the impact on warehouse cache

### Expected Output
**Description:** The expected output is a configured virtual warehouse with optimal auto-suspend and auto-resume settings.

**Schema:**
- WAREHOUSE_NAME
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*