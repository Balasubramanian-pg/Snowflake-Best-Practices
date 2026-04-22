# Resource Monitor 'Suspend' Logic on Multiple Clusters in Snowflake

**Description:**
This topic explains how to suspend resource monitors across multiple clusters in Snowflake, ensuring efficient resource utilization and cost management. It covers the logic and best practices for implementing suspend logic for resource monitors. By understanding this concept, users can optimize their Snowflake usage and avoid unnecessary costs.

## Short Explanation
**Overview:** Suspending resource monitors on multiple clusters helps manage resources and costs in Snowflake.

**Problem Solved:** Prevents excessive resource usage and reduces costs by suspending monitors when not needed.

**Importance:** Crucial for optimizing resource utilization and cost management in multi-cluster environments.

**Use Cases:**
- Automating resource monitor suspension during low-usage periods
- Managing costs across multiple Snowflake clusters

### Typical Scenario
A company with multiple Snowflake clusters wants to automate the suspension of resource monitors during periods of low usage to optimize costs. They have a resource monitor set up to track compute resources across clusters and want to suspend it when usage falls below a certain threshold.

### Core Snowflake SQL Objects Used
`CREATE RESOURCE MONITOR`, `ALTER RESOURCE MONITOR`

### Code Source Execution
```sql
CREATE RESOURCE MONITOR monitor_1
  WITH (COMMENT = 'Monitor for compute resources')
  USING (WAREHOUSE = 'wh_1', CLUSTER = 'cluster_1');

ALTER RESOURCE MONITOR monitor_1 SUSPEND;
```

### Example Execution Logic Step-by-Step
To implement suspend logic for a resource monitor, first create the monitor with the required specifications. Then, use the ALTER RESOURCE MONITOR statement with the SUSPEND option to pause the monitor. This ensures that the monitor stops tracking resource usage until it is resumed.

### Implementation Example
To automate the suspension of a resource monitor based on usage, create a stored procedure that checks usage metrics and suspends the monitor when the threshold is met.

CREATE OR REPLACE PROCEDURE sp_suspend_monitor()
  RETURNS VARIANT
  LANGUAGE JAVASCRIPT
  EXECUTE AS CALLER
  RETURNS NULL ON NULL INPUT
BEGIN
  var sql = `SELECT * FROM TABLE(INFORMATION_SCHEMA.RESOURCE_MONITORS_BY_Cluster())`;
  var stmt = snowflake.createStatement({sql: sql});
  var rs = stmt.execute();
  rs.next();
  if (rs.getColumnValue(1) < 10) {
    sql = `ALTER RESOURCE MONITOR monitor_1 SUSPEND`;
    stmt = snowflake.createStatement({sql: sql});
    stmt.execute();
  }
  return;
END;

### Explanation of the Code
- Step 1: Create a stored procedure to check resource monitor usage.
- Step 2: Use the stored procedure to suspend the resource monitor when usage falls below the threshold.

### When to Use
- During low-usage periods to optimize costs
- When resource monitors are not needed

### When Not to Use
- During peak usage periods when resource monitors are needed
- When automated suspension may interfere with critical workloads

### Common Mistakes
- Forgetting to resume suspended resource monitors when needed
- Incorrectly setting suspension thresholds

### Expected Output
**Description:** The resource monitor is suspended, and resource usage is no longer tracked.

**Schema:**
- Monitor Name
- Status

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*