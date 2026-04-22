# Switching to 'Auto-Scale' Mode for Multi-Cluster Warehouses Off-Hours

**Description:**
This Snowflake configuration optimizes compute resources by automatically scaling multi-cluster warehouses during off-peak hours. The goal is to reduce costs while ensuring efficient data processing. By implementing this setup, organizations can dynamically adjust their warehouse capacity to match changing workloads. This approach allows for better resource utilization and cost savings.

## Short Explanation
**Overview:** Automating multi-cluster warehouse scaling during off-peak hours.

**Problem Solved:** Manually managing warehouse capacity during varying workloads.

**Importance:** Optimizes resource utilization, reduces costs, and improves efficiency.

**Use Cases:**
- Batch processing during off-peak hours
- Data integration and ETL jobs

### Typical Scenario
An organization wants to process large volumes of data during business hours but experiences reduced workload during off-peak hours. They want to optimize their Snowflake costs by automatically scaling down their multi-cluster warehouse during off-peak hours.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE my_warehouse SET AUTO_SUSPEND = 60; ALTER WAREHOUSE my_warehouse SET AUTO_RESUME = 60;
```

### Example Execution Logic Step-by-Step
To switch to 'Auto-Scale' mode off-hours, first, create a multi-cluster warehouse. Then, configure the warehouse to automatically suspend and resume based on workload demand. This is achieved by setting the AUTO_SUSPEND and AUTO_RESUME properties.

### Implementation Example
CREATE WAREHOUSE my_warehouse CLUSTER_NUMBER = 2; ALTER WAREHOUSE my_warehouse SET AUTO_SUSPEND = 60; ALTER WAREHOUSE my_warehouse SET AUTO_RESUME = 60;

### Explanation of the Code
- Step 1: Create a multi-cluster warehouse with the desired cluster number.
- Step 2: Configure the warehouse to auto-suspend after 60 minutes of inactivity and auto-resume when a query is submitted.

### When to Use
- During off-peak hours with variable workloads
- For batch processing and ETL jobs

### When Not to Use
- During peak hours with consistent high workloads
- For real-time data processing

### Common Mistakes
- Not setting the correct AUTO_SUSPEND and AUTO_RESUME values
- Not monitoring warehouse usage and adjusting configurations accordingly

### Expected Output
**Description:** The warehouse will automatically scale down during off-peak hours and scale up during peak hours.

**Schema:**
- Warehouse Name
- Cluster Number
- Auto Suspend
- Auto Resume

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*