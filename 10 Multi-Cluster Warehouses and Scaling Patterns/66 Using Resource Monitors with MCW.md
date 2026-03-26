# Using Resource Monitors with Multi-Cluster Warehouses in Snowflake

**Description:**
This topic explains how to utilize resource monitors to track and manage resource usage with multi-cluster warehouses in Snowflake, ensuring efficient resource allocation and cost management.

## Short Explanation
**Overview:** Resource monitors in Snowflake allow you to track and alert on resource usage, helping you manage costs and optimize performance.

**Problem Solved:** Resource monitors solve the problem of uncontrolled resource usage and costs by providing visibility into warehouse usage and alerting on thresholds.

**Importance:** This matters in analytics and warehousing because it helps organizations optimize resource utilization, reduce costs, and improve performance.

**Use Cases:**
- Monitoring resource usage for a multi-cluster warehouse
- Setting alerts for resource thresholds

### Typical Scenario
A company uses a multi-cluster warehouse to support their analytics workload and wants to monitor resource usage to ensure efficient resource allocation and cost management.

### Core Snowflake SQL Objects Used
`CREATE RESOURCE MONITOR`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE RESOURCE MONITOR my_monitor WITH (cpu = 80, memory = 70);
ALTER WAREHOUSE my_warehouse SET RESOURCE_MONITOR = my_monitor;
```

### Example Execution Logic Step-by-Step
This example demonstrates how to create a resource monitor with CPU and memory thresholds and attach it to a multi-cluster warehouse.

### Implementation Example
To implement resource monitoring for a multi-cluster warehouse, create a resource monitor with the desired thresholds and attach it to the warehouse using the ALTER WAREHOUSE statement.

### Explanation of the Code
- Step 1: Create a resource monitor with CPU and memory thresholds using the CREATE RESOURCE MONITOR statement.
- Step 2: Attach the resource monitor to the multi-cluster warehouse using the ALTER WAREHOUSE statement.

### When to Use
- Use a resource monitor when you want to track resource usage for a multi-cluster warehouse
- Use a resource monitor when you want to set alerts for resource thresholds

### When Not to Use
- Do not use a resource monitor for a single-cluster warehouse
- Do not use a resource monitor if you do not need to track resource usage

### Common Mistakes
- Not setting realistic thresholds for resource usage
- Not monitoring resource usage regularly

### Expected Output
**Description:** The expected output is a list of resource monitor metrics, including CPU and memory usage, and alerts when thresholds are exceeded.

**Schema:**
- metric_name
- metric_value
- threshold

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*