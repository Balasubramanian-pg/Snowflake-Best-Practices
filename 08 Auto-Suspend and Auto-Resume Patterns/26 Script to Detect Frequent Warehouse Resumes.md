# 26 Script to Detect Frequent Warehouse Resumes in Snowflake

This concept refers to a SQL script designed to identify Snowflake virtual warehouses that are being frequently started or resumed. Frequent resumes can indicate inefficient usage patterns, potential cost optimization opportunities, or issues with workload scheduling, as each resume incurs a cost and delay. The script aims to provide visibility into this behavior.

**Short Explanation**

This script helps you find out which of your Snowflake warehouses are being turned on and off a lot. It's like checking if you're constantly flicking a light switch instead of leaving it on for a period.

-   **What problem does this SQL feature solve?** It solves the problem of identifying potentially wasteful or suboptimal virtual warehouse usage, which can lead to higher Snowflake costs and performance bottlenecks due to warehouse startup times.
-   **Why is it important in databases or analytics?** In Snowflake, warehouse resumes incur billing charges and add latency to queries. Detecting frequent resumes is crucial for cost management, performance tuning, and ensuring efficient resource allocation for data warehousing and analytics workloads.
-   **Where is it commonly used in real workflows?** It's commonly used by data platform engineers, Snowflake administrators, and FinOps teams for cost monitoring, performance optimization, and auditing warehouse activity.

## Implementation Example

```sql
SELECT
    warehouse_name,
    COUNT(*) AS num_resumes,
    MIN(start_time) AS first_resume_time,
    MAX(start_time) AS last_resume_time
FROM
    snowflake.account_usage.warehouse_events
WHERE
    event_type = 'RESUME'
    AND start_time >= DATEADD(day, -7, CURRENT_TIMESTAMP()) -- Look at the last 7 days
GROUP BY
    warehouse_name
HAVING
    num_resumes > 10 -- Adjust this threshold based on your environment
ORDER BY
    num_resumes DESC;
```

## Explanation of the Code

This query analyzes the `WAREHOUSE_EVENTS` view to find instances where warehouses were resumed.

-   **SELECT `warehouse_name`, `COUNT(*) AS num_resumes`, `MIN(start_time) AS first_resume_time`, `MAX(start_time) AS last_resume_time`**: This clause specifies the columns we want to retrieve.
    -   `warehouse_name`: Identifies the specific warehouse.
    -   `COUNT(*) AS num_resumes`: Counts the total number of resume events for each warehouse. This will be the primary metric for identifying frequent resumes.
    -   `MIN(start_time) AS first_resume_time`: Shows the timestamp of the earliest resume event within the specified period for that warehouse.
    -   `MAX(start_time) AS last_resume_time`: Shows the timestamp of the latest resume event within the specified period for that warehouse.
    -   *How it changes the result set*: It defines the structure of the output table, including the aggregated count and time range for each unique warehouse.
-   **FROM `snowflake.account_usage.warehouse_events`**: This clause specifies the source table for our data.
    -   `snowflake.account_usage.warehouse_events`: This is a system view in Snowflake that records events related to virtual warehouses, including their creation, suspension, and resumption.
    -   *How it changes the result set*: It provides the raw event data, which is then filtered and aggregated.
-   **WHERE `event_type = 'RESUME' AND start_time >= DATEADD(day, -7, CURRENT_TIMESTAMP())`**: This clause filters the rows from the `warehouse_events` table.
    -   `event_type = 'RESUME'`: Ensures we only consider events where a warehouse was started or resumed.
    -   `start_time >= DATEADD(day, -7, CURRENT_TIMESTAMP())`: Limits the analysis to events that occurred within the last 7 days from the current timestamp, focusing on recent activity.
    -   *How it changes the result set*: It reduces the number of rows to only those relevant resume events within the desired timeframe.
-   **GROUP BY `warehouse_name`**: This clause groups the filtered rows based on the `warehouse_name`.
    -   Grouping by `warehouse_name` allows the `COUNT(*)` function to aggregate the number of resumes for each distinct warehouse.
    -   *How it changes the result set*: It collapses multiple rows for the same warehouse into a single row, performing the aggregate functions on those groups.
-   **HAVING `num_resumes > 10`**: This clause filters the *grouped* results.
    -   `num_resumes > 10`: Only shows warehouses that have been resumed more than 10 times in the specified period. This threshold (`10`) is customizable based on what's considered "frequent" in a particular environment.
    -   *How it changes the result set*: It further narrows down the result set to only those warehouses exhibiting high resume frequency.
-   **ORDER BY `num_resumes DESC`**: This clause sorts the final result set.
    -   `num_resumes DESC`: Orders the output in descending order based on the `num_resumes` column, so warehouses with the most frequent resumes appear at the top.
    -   *How it changes the result set*: It arranges the final rows in a meaningful order, making it easy to identify the top offenders.

## When to Use

1.  **Cost Optimization Audits:** Regularly run this script to identify warehouses contributing to unnecessary costs due to frequent starts and stops, especially small warehouses used for ad-hoc queries.
    *   *Scenario:* A monthly review of Snowflake spending shows an unexpected increase in "Compute" costs, and you suspect inefficient warehouse usage.
2.  **Performance Troubleshooting:** Investigate if specific reports or applications are experiencing delays due to constant warehouse cold starts.
    *   *Scenario:* Users complain that their dashboards or data loading jobs are occasionally slow, and you want to check if warehouse resume times are a factor.
3.  **Workload Scheduling Review:** Analyze if current workload scheduling or auto-suspend settings are appropriate for the usage patterns.
    *   *Scenario:* You've set warehouses to auto-suspend after 5 minutes, but this script reveals a warehouse resuming every 10 minutes, indicating the auto-suspend time might be too short for the workload.

## When Not to Use

1.  **General Warehouse Status Check:** If you just need to see if a warehouse is currently running or suspended, this script is overkill. Use `SHOW WAREHOUSES;` instead.
    *   *Inefficiency:* This script scans historical event data, which is more resource-intensive than a simple status check.
2.  **Detailed Query Performance Analysis:** For understanding individual query performance or specific bottlenecks within a running warehouse, this script doesn't provide that level of detail. Use `QUERY_HISTORY` or query profile for that.
    *   *Wrong approach:* This script focuses on warehouse *events*, not internal query execution metrics.
3.  **Real-time Alerting (Without Automation):** Manually running this script for real-time alerts on excessive resumes isn't practical. For real-time monitoring, integrate with Snowflake's alert mechanisms or external monitoring tools.
    *   *Inefficiency:* It's a retrospective analysis, not a proactive alert system on its own.

## Common Mistakes

1.  **Ignoring the Timeframe:** Not adjusting the `DATEADD` function to a relevant period (e.g., looking at a very short window when usage is sporadic, or a very long window that dilutes recent issues).
2.  **Incorrect Threshold:** Setting the `HAVING num_resumes > X` threshold too high (missing actual issues) or too low (generating too much noise and false positives).
3.  **Misinterpreting Results:** Assuming frequent resumes are *always* bad. Some workloads, like infrequent ad-hoc queries, might genuinely benefit from auto-suspend and resume, but the frequency should still be reasonable relative to the workload.
4.  **Not Considering Warehouse Size/Type:** A higher resume count for a small warehouse might be less impactful than for a large, expensive warehouse. The script doesn't inherently account for warehouse size in its ranking.

## Expected Output

The resulting dataset will show a list of Snowflake virtual warehouses that have been resumed frequently within the specified timeframe, ordered by the number of times they were resumed. Each row will represent a single warehouse.

| WAREHOUSE\_NAME | NUM\_RESUMES | FIRST\_RESUME\_TIME      | LAST\_RESUME\_TIME       |
| :-------------- | :----------- | :----------------------- | :----------------------- |
| ANALYTICS\_WH   | 55           | 2026-03-07 10:05:30.123  | 2026-03-14 15:20:01.456  |
| DATA\_LOAD\_WH  | 32           | 2026-03-08 08:00:00.000  | 2026-03-14 11:30:45.789  |
| REPORTING\_WH   | 18           | 2026-03-10 14:10:20.987  | 2026-03-13 18:00:10.000  |

-   **WAREHOUSE\_NAME**: The name of the Snowflake virtual warehouse.
-   **NUM\_RESUMES**: The total count of 'RESUME' events for that warehouse within the last 7 days.
-   **FIRST\_RESUME\_TIME**: The timestamp of the earliest resume event for that warehouse in the period.
-   **LAST\_RESUME\_TIME**: The timestamp of the latest resume event for that warehouse in the period.
