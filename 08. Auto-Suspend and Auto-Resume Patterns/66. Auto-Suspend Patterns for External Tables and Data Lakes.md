# Weekly Uptime Reports in Snowflake

**Description:**
This concept outlines a process to create weekly reports on system uptime using Snowflake SQL. It focuses on leveraging Snowflake's analytical capabilities to aggregate and summarize uptime data, providing insights into service availability over a given week.

## Short Explanation
**Overview:** This idea involves writing SQL queries in Snowflake to process raw uptime logs, transform them into meaningful metrics, and then group these metrics by week to produce a summary report.

**Problem Solved:** It solves the problem of aggregating continuous, granular uptime data into digestible weekly summaries, making it easier to track and analyze system availability over time.

**Importance:** It's important for transforming raw operational data into key performance indicators (KPIs) that are critical for decision-making, trend analysis, and meeting compliance requirements.

**Use Cases:**
- Regular Performance Monitoring
- SLA Reporting
- Capacity Planning and Incident Analysis

### Typical Scenario
A company needs to monitor system uptime on a weekly basis to ensure adherence to Service Level Agreements (SLAs) and to identify potential issues.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
```sql
WITH UptimeEvents AS (
    -- Assuming a table 'RAW_UPTIME_LOGS' with 'event_timestamp' (TIMESTAMP_LTZ) and 'status' (VARCHAR)
    -- 'status' could be 'UP', 'DOWN', 'PARTIAL_OUTAGE'
    SELECT
        event_timestamp,
        status,
        -- Assign a numeric value for uptime calculation (e.g., 1 for UP, 0 for DOWN)
        CASE
            WHEN status = 'UP' THEN 1
            ELSE 0
        END AS is_up_numeric
    FROM
        RAW_UPTIME_LOGS
    WHERE
        event_timestamp >= DATEADD(month, -3, CURRENT_DATE()) -- Filter for recent data
),
WeeklyAggregates AS (
    SELECT
        DATE_TRUNC('week', event_timestamp) AS week_start_date,
        COUNT(*) AS total_events,
        SUM(is_up_numeric) AS up_events_count,
        COUNT_IF(status = 'DOWN') AS down_events_count,
        MIN(event_timestamp) AS first_event_in_week,
        MAX(event_timestamp) AS last_event_in_week
    FROM
        UptimeEvents
    GROUP BY
        week_start_date
)
SELECT
    week_start_date,
    total_events,
    up_events_count,
    down_events_count,
    (up_events_count * 100.0 / total_events) AS weekly_uptime_percentage,
    first_event_in_week,
    last_event_in_week
FROM
    WeeklyAggregates
ORDER BY
    week_start_date DESC;
```
```

### Example Execution Logic Step-by-Step
This script consists of two Common Table Expressions (CTEs) and a final SELECT statement to produce the weekly uptime report.

### Implementation Example
The provided SQL script demonstrates how to implement weekly uptime reports in Snowflake.

### Explanation of the Code
- - `WITH UptimeEvents AS (...)`: This CTE selects relevant data from a hypothetical RAW_UPTIME_LOGS table and transforms the status column into a numeric is_up_numeric value (1 for 'UP', 0 otherwise).
- - `WITH WeeklyAggregates AS (...)`: This CTE groups the UptimeEvents data by the start of each week and calculates various aggregate statistics for each week.
- - Final SELECT statement: This statement calculates the weekly_uptime_percentage and presents the final report.

### When to Use
- Regular Performance Monitoring: To get a quick overview of system availability week-over-week, helping identify immediate dips or consistent issues.
- SLA Reporting: When you need to demonstrate adherence to Service Level Agreements (SLAs) that specify a minimum uptime percentage over a weekly period.
- Capacity Planning and Incident Analysis: To correlate uptime trends with system changes, deployments, or major incidents, aiding in root cause analysis and future planning.

### When Not to Use
- Real-time Monitoring: This script provides weekly summaries and is not suitable for instantaneous, second-by-second, or minute-by-minute uptime alerts.
- Detailed Root Cause Analysis: While it shows when uptime dropped, it doesn't provide the granular event details needed for deep diving into why a specific outage occurred.
- Micro-Service Specific Uptime: If you have hundreds of micro-services and need individual weekly reports for each, this general script would need significant modification or looping, becoming inefficient for a broad set of distinct entities.

### Common Mistakes
- Incorrect Time Zone Handling: Not considering the time zone of event_timestamp relative to the desired reporting week, leading to incorrect weekly groupings or events appearing in the wrong week.
- Improper Uptime Metric Definition: Calculating uptime purely based on "up" vs. "down" events without accounting for the duration of each state, which can misrepresent actual availability.
- Missing Data Gaps: Assuming continuous logging. If the logging system itself goes down, there might be gaps in RAW_UPTIME_LOGS, leading to an artificially inflated uptime percentage for periods where no "down" events were recorded.
- No "Total Available Time" Context: Without a concept of total available time in a week (e.g., 7 days * 24 hours), the percentage is just based on recorded events, not the actual potential uptime.

### Expected Output
**Description:** The resulting dataset should be a list of weekly uptime reports, showing key metrics for each week.

**Schema:**
- WEEK_START_DATE
- TOTAL_EVENTS
- UP_EVENTS_COUNT
- DOWN_EVENTS_COUNT
- WEEKLY_UPTIME_PERCENTAGE
- FIRST_EVENT_IN_WEEK
- LAST_EVENT_IN_WEEK

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*