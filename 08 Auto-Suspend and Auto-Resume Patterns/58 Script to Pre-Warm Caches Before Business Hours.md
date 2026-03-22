# Analyzing Warehouse Events History for Suspend Gaps in Snowflake

**Description:**
This concept focuses on analyzing the WAREHOUSE_EVENTS_HISTORY view in Snowflake to identify "suspend gaps." A suspend gap occurs when a Snowflake virtual warehouse is running (and thus consuming credits) but is not actively processing queries, indicating idle time that could be converted to suspended time to save costs.

## Short Explanation
**Overview:** Analyzing suspend gaps means looking at your Snowflake warehouse activity logs to find times when a warehouse was running but doing nothing.

**Problem Solved:** It helps identify periods of wasteful spending on idle Snowflake warehouses.

**Importance:** It's crucial for cost management and optimizing cloud resource consumption in data warehousing environments.

**Use Cases:**
- Financial governance
- Cost optimization
- Performance monitoring dashboards within Snowflake environments

### Typical Scenario
Your monthly Snowflake bill shows a 15% increase, and you want to identify which warehouses are costing the most due to unnecessary runtime.

### Core Snowflake SQL Objects Used
`WAREHOUSE_EVENTS_HISTORY`

### Code Source Execution
```sql
```sql
WITH WarehouseStates AS (
    SELECT
        warehouse_name,
        timestamp,
        event_name,
        LAG(timestamp) OVER (PARTITION BY warehouse_name ORDER BY timestamp) AS prev_timestamp,
        LAG(event_name) OVER (PARTITION BY warehouse_name ORDER BY timestamp) AS prev_event_name
    FROM
        SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_EVENTS_HISTORY
    WHERE
        event_name IN ('SUSPEND_WAREHOUSE', 'RESUME_WAREHOUSE')
        AND timestamp >= DATEADD(month, -1, CURRENT_TIMESTAMP()) -- Look back for the last month
)
SELECT
    warehouse_name,
    prev_timestamp AS resume_time,
    timestamp AS suspend_time,
    TIMESTAMPSUB(minute, prev_timestamp, timestamp) AS idle_duration_minutes
FROM
    WarehouseStates
WHERE
    event_name = 'SUSPEND_WAREHOUSE'
    AND prev_event_name = 'RESUME_WAREHOUSE'
    AND idle_duration_minutes > 10 -- Define a gap threshold (e.g., more than 10 minutes idle)
ORDER BY
    idle_duration_minutes DESC;
```
```

### Example Execution Logic Step-by-Step
This query analyzes warehouse events to find periods where a warehouse was resumed and then suspended, but remained idle for a significant duration.

### Implementation Example
The resulting dataset will show periods where a warehouse was active between a resume and a suspend event, exceeding a defined idle duration.

### Explanation of the Code
- The query uses a Common Table Expression (CTE) named WarehouseStates to prepare the data.
- It selects key information about each warehouse event and uses window functions to retrieve the timestamp and event name of the previous event for the same warehouse.
- The query filters events to only include warehouse suspensions and resumptions, and limits the analysis to events from the last month.
- It calculates the difference in minutes between the current event's timestamp (which is a suspend event) and the previous event's timestamp (which is a resume event).
- The query filters for rows where the current event is a suspension and the immediately preceding event for that warehouse was a resumption.
- It sorts the results by the longest idle duration first.

### When to Use
- Cost Optimization: When you notice higher-than-expected Snowflake credit consumption and suspect idle warehouses are contributing significantly.
- Performance Tuning & Auto-Suspend Configuration: To evaluate if your warehouse auto-suspend settings are optimal.
- Resource Allocation Review: To understand the actual usage patterns of your warehouses and whether they are appropriately sized or if workload balancing is needed.

### When Not to Use
- Real-time Monitoring: The ACCOUNT_USAGE views have a latency (up to 3 hours for WAREHOUSE_EVENTS_HISTORY), making them unsuitable for real-time operational monitoring or immediate alerts.
- Troubleshooting Active Query Issues: If you're trying to debug why a specific query is slow right now, this historical view won't help.

### Common Mistakes
- Confusing SUSPEND_WAREHOUSE with immediate cost savings: A warehouse might be "suspended" in the event history, but it still incurs costs until all running statements complete.
- Not accounting for actual query execution: The gap calculation only considers RESUME_WAREHOUSE and SUSPEND_WAREHOUSE events.
- Setting an arbitrary idle_duration_minutes threshold: The "gap" threshold should be set thoughtfully based on business requirements, auto-suspend settings, and typical workload patterns.

### Expected Output
**Description:** The resulting dataset will show periods where a warehouse was active between a resume and a suspend event, exceeding a defined idle duration.

**Schema:**
- WAREHOUSE_NAME
- RESUME_TIME
- SUSPEND_TIME
- IDLE_DURATION_MINUTES

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*