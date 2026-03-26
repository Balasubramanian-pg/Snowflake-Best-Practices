# Script to Identify When to Enable Multi-Cluster Warehouses (MCW)

**Description:**
This script helps determine when to enable Multi-Cluster Warehouses (MCW) in Snowflake, which allows for auto-scaling of warehouses to handle variable workloads.

## Short Explanation
**Overview:** The script analyzes workload patterns to identify when MCW should be enabled.

**Problem Solved:** It solves the problem of manually monitoring and scaling warehouses to handle fluctuating workloads.

**Importance:** Enabling MCW at the right time ensures optimal performance and cost efficiency in Snowflake.

**Use Cases:**
- Handling sudden spikes in query workload
- Managing variable data ingestion rates

### Typical Scenario
A business experiences fluctuating query workloads throughout the day, with occasional spikes in usage. They want to ensure their Snowflake warehouses are scaled appropriately to handle these changes without manual intervention.

### Core Snowflake SQL Objects Used
`CREATE TABLE`, `INSERT INTO`

### Code Source Execution
```sql

    -- Create a table to store warehouse utilization metrics
    CREATE TABLE warehouse_utilization (
      id INT,
      warehouse_name VARCHAR,
      utilization_percent FLOAT,
      timestamp TIMESTAMP
    );

    -- Insert sample data into the table
    INSERT INTO warehouse_utilization (id, warehouse_name, utilization_percent, timestamp)
    VALUES
      (1, 'my_warehouse', 50.0, '2023-01-01 00:00:00'),
      (2, 'my_warehouse', 80.0, '2023-01-01 01:00:00'),
      (3, 'my_warehouse', 30.0, '2023-01-01 02:00:00');

    -- Script to identify when to enable MCW
    SELECT 
      warehouse_name,
      AVG(utilization_percent) AS avg_utilization,
      MAX(utilization_percent) AS max_utilization
    FROM 
      warehouse_utilization
    GROUP BY 
      warehouse_name
    HAVING 
      MAX(utilization_percent) > 80;
  
```

### Example Execution Logic Step-by-Step
The script works by analyzing warehouse utilization metrics, such as average and maximum utilization percentages. If the maximum utilization exceeds a certain threshold (e.g., 80%), it indicates that MCW should be enabled.

### Implementation Example

    -- Enable MCW for the warehouse
    ALTER WAREHOUSE my_warehouse SET ENABLE_MCW = TRUE;
  

### Explanation of the Code
- Step 1: Create a table to store warehouse utilization metrics.
- Step 2: Insert sample data into the table.
- Step 3: Analyze warehouse utilization metrics to determine when to enable MCW.
- Step 4: Enable MCW for the warehouse if necessary.

### When to Use
- When experiencing fluctuating query workloads
- When managing variable data ingestion rates

### When Not to Use
- When workloads are consistently low
- When manual scaling is sufficient

### Common Mistakes
- Not monitoring warehouse utilization metrics regularly
- Not adjusting MCW settings based on changing workloads

### Expected Output
**Description:** The result set shows the warehouse name, average utilization, and maximum utilization.

**Schema:**
- warehouse_name
- avg_utilization
- max_utilization

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*