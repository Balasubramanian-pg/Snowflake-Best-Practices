# Analyzing Warehouse Events History for Suspend Gaps in Snowflake

This concept focuses on analyzing the `WAREHOUSE_EVENTS_HISTORY` view in Snowflake to identify "suspend gaps." A suspend gap occurs when a Snowflake virtual warehouse is running (and thus consuming credits) but is not actively processing queries, indicating idle time that could be converted to suspended time to save costs. By examining the history of warehouse events, we can pinpoint these periods of inefficient resource utilization.

**Short Explanation**

Analyzing suspend gaps means looking at your Snowflake warehouse activity logs to find times when a warehouse was running but doing nothing. This is important because running warehouses, even when idle, cost money. Identifying and reducing these gaps helps optimize your Snowflake spend by ensuring warehouses are suspended when not needed. It's commonly used in cost optimization and performance tuning workflows.

- **What problem does this SQL feature solve?** It helps identify periods of wasteful spending on idle Snowflake warehouses.
- **Why is it important in databases or analytics?** It's crucial for cost management and optimizing cloud resource consumption in data warehousing environments.
- **Where is it commonly used in real workflows?** Financial governance, cost optimization, and performance monitoring dashboards within Snowflake environments.

## Implementation Example

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

## Explanation of the Code

This query analyzes warehouse events to find periods where a warehouse was resumed and then suspended, but remained idle for a significant duration.

- **`WITH WarehouseStates AS (...)`**: This defines a Common Table Expression (CTE) named `WarehouseStates` to prepare the data.
    - **`SELECT warehouse_name, timestamp, event_name,...`**: Selects key information about each warehouse event.
    - **`LAG(timestamp) OVER (PARTITION BY warehouse_name ORDER BY timestamp) AS prev_timestamp`**: This window function retrieves the `timestamp` of the *previous* event for the *same* `warehouse_name`, ordered by `timestamp`. It's crucial for calculating the duration between events.
        - **What it does**: Accesses data from a previous row in the same partition.
        - **How it changes the result set**: Adds a `prev_timestamp` column, showing when the event before the current one occurred for that specific warehouse.
    - **`LAG(event_name) OVER (PARTITION BY warehouse_name ORDER BY timestamp) AS prev_event_name`**: Similar to `prev_timestamp`, this gets the `event_name` of the previous event.
        - **What it does**: Accesses data from a previous row in the same partition.
        - **How it changes the result set**: Adds a `prev_event_name` column, indicating the type of the preceding event for the warehouse.
    - **`FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_EVENTS_HISTORY`**: Specifies the source of the data, which is Snowflake's `WAREHOUSE_EVENTS_HISTORY` view within the `ACCOUNT_USAGE` schema. This view provides historical data about warehouse activities like creation, suspension, and resumption.
    - **`WHERE event_name IN ('SUSPEND_WAREHOUSE', 'RESUME_WAREHOUSE')`**: Filters the events to only include warehouse suspensions and resumptions, as these are the relevant events for identifying idle time.
        - **What it does**: Filters rows based on conditions.
        - **How it changes the result set**: Only includes rows where the `event_name` is either 'SUSPEND_WAREHOUSE' or 'RESUME_WAREHOUSE', significantly reducing the dataset.
    - **`AND timestamp >= DATEADD(month, -1, CURRENT_TIMESTAMP())`**: Limits the analysis to events from the last month for performance and relevance.
        - **What it does**: Further filters rows based on the event timestamp.
        - **How it changes the result set**: Ensures only recent events (last month) are considered.

- **`SELECT warehouse_name, prev_timestamp AS resume_time, timestamp AS suspend_time,...`**: Selects the warehouse name, the timestamp of the resume event, the timestamp of the suspend event, and the calculated idle duration.
- **`TIMESTAMPSUB(minute, prev_timestamp, timestamp) AS idle_duration_minutes`**: Calculates the difference in minutes between the current event's timestamp (which is a suspend event) and the previous event's timestamp (which is a resume event). This gives us the duration the warehouse was active between a resume and suspend, which, if no queries ran, represents idle time.
- **`FROM WarehouseStates`**: Specifies that the main query operates on the results of the `WarehouseStates` CTE.
- **`WHERE event_name = 'SUSPEND_WAREHOUSE' AND prev_event_name = 'RESUME_WAREHOUSE'`**: Filters for rows where the current event is a suspension and the immediately preceding event for that warehouse was a resumption. This isolates the periods between a resume and a subsequent suspend.
    - **What it does**: Filters rows to isolate specific event sequences.
    - **How it changes the result set**: Keeps only those records where a warehouse was resumed and then suspended, allowing for idle duration calculation.
- **`AND idle_duration_minutes > 10`**: This is a crucial filter that defines what constitutes a "gap." It only shows idle periods longer than a specified threshold (e.g., 10 minutes), to focus on significant inefficiencies.
    - **What it does**: Filters out short, acceptable idle periods.
    - **How it changes the result set**: Only includes idle periods that exceed the defined threshold.
- **`ORDER BY idle_duration_minutes DESC`**: Sorts the results by the longest idle duration first, making it easy to identify the biggest cost-saving opportunities.
    - **What it does**: Arranges the output rows.
    - **How it changes the result set**: Presents the suspend gaps from longest to shortest duration.

## When to Use

1. **Cost Optimization:** When you notice higher-than-expected Snowflake credit consumption and suspect idle warehouses are contributing significantly.
    * **Example Scenario:** Your monthly Snowflake bill shows a 15% increase, and you want to identify which warehouses are costing the most due to unnecessary runtime.
2. **Performance Tuning & Auto-Suspend Configuration:** To evaluate if your warehouse auto-suspend settings are optimal. If you see many long suspend gaps, you might need to lower the `AUTO_SUSPEND` parameter.
    * **Example Scenario:** A data engineering team frequently resumes a warehouse for a short task but forgets to suspend it, leading to hours of idle time before auto-suspend kicks in. This analysis can highlight warehouses with long idle durations.
3. **Resource Allocation Review:** To understand the actual usage patterns of your warehouses and whether they are appropriately sized or if workload balancing is needed.
    * **Example Scenario:** A small warehouse is frequently resumed and then suspended after short, bursty loads, but analysis shows it remains active for 30-minute intervals between these bursts, suggesting auto-suspend is too high.

## When Not to Use

1. **Real-time Monitoring:** The `ACCOUNT_USAGE` views have a latency (up to 3 hours for `WAREHOUSE_EVENTS_HISTORY` ), making them unsuitable for real-time operational monitoring or immediate alerts.
2. **Troubleshooting Active Query Issues:** If you're trying to debug why a specific query is slow right now, this historical view won't help. Use `QUERY_HISTORY` or Snowsight's query profile for active troubleshooting.
3. **Predictive Analysis without Additional Data:** While it identifies past gaps, it doesn't inherently predict future patterns without integrating with other metrics (like `QUERY_HISTORY` to understand if queries *actually* ran during the "idle" period) or advanced analytics.

## Common Mistakes

1. **Confusing `SUSPEND_WAREHOUSE` with immediate cost savings:** A warehouse might be "suspended" in the event history, but it still incurs costs until all running statements complete. The actual metering stops when the warehouse is fully shut down.
2. **Not accounting for actual query execution:** The gap calculation only considers `RESUME_WAREHOUSE` and `SUSPEND_WAREHOUSE` events. A warehouse might appear "idle" between these events, but it could have been actively running queries. For a complete picture, cross-reference with `QUERY_HISTORY` to confirm if queries were running during the calculated `idle_duration_minutes`.
3. **Setting an arbitrary `idle_duration_minutes` threshold:** The "gap" threshold (e.g., 10 minutes) should be set thoughtfully based on business requirements, auto-suspend settings, and typical workload patterns. Too low, and you'll find too much noise; too high, and you'll miss optimization opportunities.
4. **Ignoring multi-cluster warehouses:** For multi-cluster warehouses, `SUSPEND_CLUSTER` and `RESUME_CLUSTER` events also occur. A more complex analysis might be needed to fully understand cost implications for individual clusters.

## Expected Output

The resulting dataset will show periods where a warehouse was active between a resume and a suspend event, exceeding a defined idle duration. Each row represents a "suspend gap" for a specific warehouse.

| WAREHOUSE_NAME | RESUME_TIME | SUSPEND_TIME | IDLE_DURATION_MINUTES |
| :------------- | :--------------------------- | :--------------------------- | :-------------------- |
| MY_ANALYTICS_WH | 2026-02-28 09:05:00.000 +0000 | 2026-02-28 09:35:00.000 +0000 | 30 |
| ETL_PROCESSING_WH | 2026-03-01 14:10:00.000 +0000 | 2026-03-01 14:25:00.000 +0000 | 15 |
| DEV_SANDBOX_WH | 2026-03-02 10:00:00.000 +0000 | 2026-03-02 11:00:00.000 +0000 | 60 |

- **WAREHOUSE_NAME**: The name of the Snowflake virtual warehouse.
- **RESUME_TIME**: The timestamp when the warehouse was resumed.
- **SUSPEND_TIME**: The timestamp when the warehouse was suspended.
-   **IDLE_DURATION_MINUTES**: The calculated duration in minutes between the resume and suspend events, representing the potential idle time.
