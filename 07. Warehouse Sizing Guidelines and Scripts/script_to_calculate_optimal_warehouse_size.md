# Calculating Optimal Warehouse Size in Snowflake

**Description:**
This topic provides a script to calculate the optimal warehouse size in Snowflake, ensuring efficient resource utilization and cost savings. The script analyzes historical usage patterns and recommends a suitable warehouse size. By right-sizing your warehouse, you can avoid over-provisioning and under-provisioning, leading to better performance and cost optimization.

## Short Explanation
**Overview:** The script calculates the optimal warehouse size based on historical usage patterns.

**Problem Solved:** Determining the optimal warehouse size to ensure efficient resource utilization and cost savings.

**Importance:** Right-sizing your warehouse is crucial for better performance and cost optimization in Snowflake.

**Use Cases:**
- Analyzing historical usage patterns to determine optimal warehouse size
- Optimizing warehouse size for cost savings and performance

### Typical Scenario
A Snowflake administrator wants to optimize warehouse size for a specific role or department. They run the script, which analyzes historical usage patterns and recommends an optimal warehouse size.

### Core Snowflake SQL Objects Used
`CREATE PROCEDURE`

### Code Source Execution
```sql
CREATE OR REPLACE PROCEDURE CALCULATE_OPTIMAL_WAREHOUSE_SIZE()
  RETURNS STRING
  LANGUAGE JAVASCRIPT
  EXECUTE AS CALLER
  BEGIN
    var sql = `SELECT 
              WAREHOUSE_NAME, 
              SUM(EXECUTION_TIME) AS TOTAL_EXECUTION_TIME, 
              AVG(EXECUTION_TIME) AS AVG_EXECUTION_TIME, 
              MAX(EXECUTION_TIME) AS MAX_EXECUTION_TIME
            FROM 
              TABLE(INFORMATION_SCHEMA.WAREHOUSE_LOAD_HISTORY_BY_SESSION())
            GROUP BY 
              WAREHOUSE_NAME`;
    var stmt = snowflake.createStatement({sql: sql});
    var rs = stmt.execute();
    rs.next();
    var warehouseName = rs.getColumnValue(1);
    var totalExecutionTime = rs.getColumnValue(2);
    var avgExecutionTime = rs.getColumnValue(3);
    var maxExecutionTime = rs.getColumnValue(4);
    var optimalWarehouseSize = '';
    if (totalExecutionTime < 3600) {
      optimalWarehouseSize = 'X-Small';
    } else if (totalExecutionTime < 7200) {
      optimalWarehouseSize = 'Small';
    } else if (totalExecutionTime < 10800) {
      optimalWarehouseSize = 'Medium';
    } else {
      optimalWarehouseSize = 'Large';
    }
    return optimalWarehouseSize;
  END;
  $$;
```

### Example Execution Logic Step-by-Step
The script uses the INFORMATION_SCHEMA.WAREHOUSE_LOAD_HISTORY_BY_SESSION() table function to gather historical usage data. It then calculates the total, average, and maximum execution times for each warehouse. Based on these metrics, it recommends an optimal warehouse size.

### Implementation Example
CALL CALCULATE_OPTIMAL_WAREHOUSE_SIZE();

### Explanation of the Code
- The script creates a stored procedure called CALCULATE_OPTIMAL_WAREHOUSE_SIZE.
- It queries the INFORMATION_SCHEMA.WAREHOUSE_LOAD_HISTORY_BY_SESSION() table function to gather historical usage data.
- It calculates the total, average, and maximum execution times for each warehouse.
- Based on these metrics, it recommends an optimal warehouse size.

### When to Use
- Analyzing historical usage patterns to determine optimal warehouse size
- Optimizing warehouse size for cost savings and performance

### When Not to Use
- When there are no historical usage patterns available
- When other factors such as data volume or query complexity are not considered

### Common Mistakes
- Not considering other factors such as data volume or query complexity
- Not regularly re-running the script to ensure optimal warehouse size

### Expected Output
**Description:** The optimal warehouse size based on historical usage patterns.

**Schema:**
- OPTIMAL_WAREHOUSE_SIZE

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*