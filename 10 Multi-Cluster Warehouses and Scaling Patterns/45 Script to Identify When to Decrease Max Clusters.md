# Script to Identify When to Decrease Max Clusters in Snowflake

**Description:**
This Snowflake script helps identify when to decrease the maximum number of clusters in a multi-cluster warehouse, optimizing resource utilization and cost. It analyzes cluster usage and provides insights for informed decisions. By running this script, you can determine if your current max cluster configuration is over-provisioned.

## Short Explanation
**Overview:** The script analyzes cluster usage to determine if the max cluster configuration can be decreased.

**Problem Solved:** Over-provisioning of clusters leading to unnecessary costs.

**Importance:** Optimizing resource utilization and cost in Snowflake multi-cluster warehouses.

**Use Cases:**
- Right-sizing clusters for cost savings
- Identifying underutilized clusters

### Typical Scenario
A Snowflake administrator notices that the multi-cluster warehouse is consistently running with fewer clusters than the configured maximum. They want to determine if the max cluster configuration can be decreased to optimize resource utilization and cost.

### Core Snowflake SQL Objects Used
`CREATE VIEW`

### Code Source Execution
```sql

    CREATE OR REPLACE VIEW cluster_usage AS
    SELECT 
      'cluster_usage' AS metric,
      SUM(number_of_clusters) AS total_clusters,
      COUNT(DISTINCT cluster_number) AS active_clusters,
      AVG(utilization) AS avg_utilization
    FROM 
      TABLE(INFORMATION_SCHEMA.WAREHOUSE_LOAD_HISTORY_BY_WAREHOUSE(
        warehouse_name => 'my_warehouse',
        start_date => DATE_TRUNC('day', CURRENT_DATE - 7),
        end_date => DATE_TRUNC('day', CURRENT_DATE)
      ));
  
```

### Example Execution Logic Step-by-Step
The script creates a view to analyze cluster usage over a 7-day period. It calculates the total number of clusters, active clusters, and average utilization. This information helps determine if the max cluster configuration can be decreased.

### Implementation Example

    -- Create a stored procedure to analyze cluster usage and recommend adjustments
    CREATE OR REPLACE PROCEDURE adjust_max_clusters()
    RETURNS STRING
    LANGUAGE JAVASCRIPT
    EXECUTE AS CALLER
    BEGIN
      var sql = 'SELECT * FROM cluster_usage';
      var stmt = snowflake.createStatement({sql: sql});
      var rs = stmt.execute();
      rs.next();
      var totalClusters = rs.getColumnValue(1);
      var activeClusters = rs.getColumnValue(2);
      var avgUtilization = rs.getColumnValue(3);
      
      if (avgUtilization < 0.5 && activeClusters < totalClusters * 0.8) {
        return 'Decrease max clusters by 1';
      } else {
        return 'No adjustment needed';
      }
    END;
  

### Explanation of the Code
- Step 1: Create a view to analyze cluster usage
- Step 2: Create a stored procedure to analyze cluster usage and recommend adjustments

### When to Use
- Regularly review cluster usage to optimize resource utilization
- When adding new clusters to a multi-cluster warehouse

### When Not to Use
- During peak usage periods
- When cluster utilization is close to 100%

### Common Mistakes
- Not regularly reviewing cluster usage
- Over-provisioning clusters

### Expected Output
**Description:** The script outputs a recommendation to decrease max clusters or no adjustment needed.

**Schema:**
- metric
- total_clusters
- active_clusters
- avg_utilization

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*