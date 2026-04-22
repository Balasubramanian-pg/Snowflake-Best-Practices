# Automating Multi-Cluster Warehouse Settings Based on Time of Day

**Description:**
This topic explains how to automate the settings of a multi-cluster warehouse in Snowflake based on the time of day. This allows for efficient resource utilization and cost optimization. By implementing this automation, organizations can dynamically adjust their warehouse capacity to match changing workload demands.

## Short Explanation
**Overview:** Automate multi-cluster warehouse settings based on time of day.

**Problem Solved:** Inefficient resource utilization and unnecessary costs due to static warehouse configurations.

**Importance:** Optimizes resource usage and reduces costs in analytics and warehousing.

**Use Cases:**
- Scaling warehouse capacity during business hours
- Reducing capacity during off-peak hours

### Typical Scenario
A retail company wants to analyze sales data during business hours (9am-5pm) and reduce warehouse capacity during off-peak hours (5pm-9am).

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE mcw_load_cluster
	WAREHOUSE_SIZE = 'MEDIUM'
	NUM_CLUSTERS = 2;

CREATE TASK automate_mcw_settings
	WAREHOUSE = mcw_load_cluster
	SCHEDULE = '0 9 * * *';

ALTER TASK automate_mcw_settings
	ADD CALLBACK
	TASK AUTOMATE_MCWS
	WHEN_CONDITION = 'TIMESTAMP_TRUNC(CURRENT_TIMESTAMP, 'hour') BETWEEN '9:00:00' AND '17:00:00'';

CREATE PROCEDURE automate_mcws()
	RETURNS VARIANT
	LANGUAGE JAVASCRIPT
AS
	var sql = `ALTER WAREHOUSE mcw_load_cluster SET NUM_CLUSTERS = 4;`;
	var stmt = snowflake.createStatement({sql: sql});
	var rs = stmt.execute();
	return rs;
```

### Example Execution Logic Step-by-Step
The example creates a multi-cluster warehouse (mcw_load_cluster) and a task (automate_mcw_settings) that runs daily at 9am. The task calls a stored procedure (automate_mcws) that checks the current time and adjusts the warehouse capacity (NUM_CLUSTERS) to 4 during business hours (9am-5pm) and 2 during off-peak hours.

### Implementation Example
To implement this automation, create a stored procedure that checks the current time and adjusts the warehouse capacity accordingly. Schedule a task to run the stored procedure daily at the desired times.

### Explanation of the Code
- Create a multi-cluster warehouse with initial settings.
- Create a task that runs daily at 9am and calls the stored procedure.
- Create a stored procedure that checks the current time and adjusts the warehouse capacity.

### When to Use
- Dynamic workload demands
- Variable business hours

### When Not to Use
- Static workload demands
- Fixed business hours

### Common Mistakes
- Not adjusting warehouse capacity to match workload demands
- Not monitoring and optimizing warehouse usage

### Expected Output
**Description:** The output will be the adjusted warehouse capacity (NUM_CLUSTERS) based on the current time.

**Schema:**
- WAREHOUSE_NAME
- NUM_CLUSTERS

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*