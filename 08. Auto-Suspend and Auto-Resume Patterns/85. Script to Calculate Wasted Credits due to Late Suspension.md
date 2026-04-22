# Script to Calculate Wasted Credits due to Late Suspension in Snowflake

**Description:**
This SQL script helps identify and quantify the cost of Snowflake warehouses that don't suspend immediately when idle, causing unnecessary credit consumption.

## Short Explanation
**Overview:** Identifies and quantifies wasted Snowflake credits due to late warehouse suspension.

**Problem Solved:** Pinpoints specific instances and the total cost associated with Snowflake warehouses running idle beyond their configured auto-suspend times.

**Importance:** Efficient resource management and cost control are critical in data warehousing.

**Use Cases:**
- Cost Optimization Audits
- Performance and Efficiency Monitoring
- Chargeback and Showback

### Typical Scenario
A monthly cost report shows an unexpected spike in compute costs, and you want to identify which warehouses contributed to this by running idle.

### Core Snowflake SQL Objects Used
`snowflake.account_usage.warehouse_metering_history`

### Code Source Execution
```sql
SELECT warehouse_name, TO_DATE(start_time) AS usage_date, SUM(credits_used) AS total_credits_used, SUM(credits_used_compute) AS compute_credits, SUM(credits_used_cloud_services) AS cloud_services_credits, (SUM(credits_used) - SUM(credits_used_compute) - SUM(credits_used_cloud_services)) AS late_suspension_credits FROM snowflake.account_usage.warehouse_metering_history WHERE start_time >= DATEADD(day, -30, CURRENT_DATE()) AND end_time IS NOT NULL AND credits_used > 0 GROUP BY 1, 2 ORDER BY usage_date DESC, total_credits_used DESC;
```

### Example Execution Logic Step-by-Step
Analyzes the warehouse_metering_history view to identify and aggregate credit usage, with a focus on understanding where 'wasted' credits might occur due to late suspensions.

### Implementation Example
The query analyzes the warehouse_metering_history view to identify and aggregate credit usage.

### Explanation of the Code
- Selects the warehouse name, truncates the start_time to just the date, calculates the sum of all credits used (total_credits_used), compute credits, cloud services credits, and then derives a simplified late_suspension_credits value.
- Specifies the source of the data, which is the warehouse_metering_history view in the ACCOUNT_USAGE schema.
- Filters the data to include only records from the last 30 days, ensures that the warehouse usage period has ended (so end_time is not null), and only includes periods where credits were actually used.
- Groups the rows by warehouse_name (column 1) and usage_date (column 2).
- Sorts the final result set first by the usage_date in descending order (most recent first), and then by total_credits_used in descending order for each date.

### When to Use
- Cost Optimization Audits: When you suspect certain warehouses are costing more than expected due to not suspending promptly.
- Performance and Efficiency Monitoring: To ensure that automated warehouse suspension policies are working effectively across your Snowflake environment.
- Chargeback and Showback: To accurately allocate costs to different teams or projects by identifying and excluding wasted credits from their actual workload consumption.

### When Not to Use
- Real-time Monitoring of Active Workloads: This script is for historical analysis of completed usage periods, not for monitoring currently running queries or active warehouse states.
- Troubleshooting Performance of Specific Queries: While related to cost, this script doesn't drill down into individual query performance or specific bottlenecks within a query.
- For Warehouses with Consistent, Non-Stop Usage: If a warehouse is intentionally configured to run 24/7 or has continuous workloads that prevent it from suspending, trying to find 'late suspension' credits is irrelevant.

### Common Mistakes
- Misinterpreting late_suspension_credits: The example's calculation is a placeholder and a simplification.
- Not Considering Warehouse Size: Smaller warehouses may consume fewer credits when idle, making their late suspension less impactful than larger ones.
- Ignoring Auto-Suspend Settings: Without knowing the configured AUTO_SUSPEND value for each warehouse, it's difficult to accurately determine what constitutes a 'late' suspension.

### Expected Output
**Description:** The resulting dataset will show daily aggregated credit usage for each Snowflake warehouse over the specified period.

**Schema:**
- WAREHOUSE_NAME
- USAGE_DATE
- TOTAL_CREDITS_USED
- COMPUTE_CREDITS
- CLOUD_SERVICES_CREDITS
- LATE_SUSPENSION_CREDITS

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*