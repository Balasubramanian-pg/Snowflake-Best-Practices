# Script to Generate Weekly Uptime Reports in Snowflake

**Description:**
This Snowflake SQL script generates weekly reports on system uptime, providing insights into service availability over a given week.

## Short Explanation
**Overview:** The script processes raw uptime logs, transforms them into meaningful metrics, and groups these metrics by week to produce a summary report.

**Problem Solved:** It solves the problem of aggregating continuous, granular uptime data into digestible weekly summaries, making it easier to track and analyze system availability over time.

**Importance:** It's crucial for monitoring system performance, identifying potential issues, and ensuring Service Level Agreements (SLAs) are met.

**Use Cases:**
- Regular Performance Monitoring
- SLA Reporting
- Capacity Planning and Incident Analysis

### Typical Scenario
A company needs to monitor system uptime on a weekly basis to ensure SLA compliance and identify performance trends.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
```sql
WITH UptimeEvents AS (
    SELECT
        event_timestamp,
        status,
        CASE
            WHEN status = 'UP' THEN 1
            ELSE 0
        END AS is_up_numeric
    FROM
        RAW_UPTIME_LOGS
    WHERE
        event_timestamp >= DATEADD(month, -3, CURRENT_DATE())
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
The script consists of two Common Table Expressions (CTEs) and a final SELECT statement to produce the weekly uptime report.

### Implementation Example
The script can be used to generate weekly uptime reports for a system, providing insights into service availability over a given week.

### Explanation of the Code
- The first CTE, UptimeEvents, selects relevant data from a hypothetical RAW_UPTIME_LOGS table and transforms the status column into a numeric is_up_numeric value.
- The second CTE, WeeklyAggregates, groups the UptimeEvents data by the start of each week and calculates various aggregate statistics for each week.
- The final SELECT statement calculates the weekly_uptime_percentage and presents the final report.

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
- No "Total Available Time" Context: Without a concept of total available time in a week, the percentage is just based on recorded events, not the actual potential uptime.

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