# How to Avoid Underprovisioning in Snowflake

**Description:**
This guide provides strategies and best practices to help Snowflake users avoid underprovisioning their warehouses, ensuring optimal performance and cost-effectiveness. Underprovisioning can lead to query queuing, reduced performance, and increased costs in the long run. By following these guidelines, users can right-size their warehouses and achieve better query performance. Proper warehouse sizing is crucial for efficient data processing and analysis.

## Short Explanation
**Overview:** Avoiding underprovisioning in Snowflake is crucial for maintaining optimal warehouse performance and cost efficiency.

**Problem Solved:** Underprovisioning leads to query queuing, reduced performance, and potential cost increases due to longer query execution times.

**Importance:** Proper warehouse sizing ensures efficient data processing, reduces costs, and improves query performance.

**Use Cases:**
- Real-time data ingestion and processing
- Large-scale data analytics and reporting

### Typical Scenario
A company experiences frequent query queuing and performance issues during peak hours due to underprovisioning. They need to analyze their query workload, assess their warehouse size, and adjust it accordingly to ensure smooth data processing.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE my_warehouse
  WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 60
  AUTO_RESUME = 180;
```

### Example Execution Logic Step-by-Step
To avoid underprovisioning, start by analyzing your query workload using Snowflake's QUERY_HISTORY and QUERY_METRICS views. Then, adjust your warehouse size based on the maximum concurrency and data volume. For example, you can create a new warehouse with a larger size or modify an existing one.

### Implementation Example
CREATE WAREHOUSE my_large_warehouse
  WAREHOUSE_SIZE = 'LARGE'
  AUTO_SUSPEND = 120
  AUTO_RESUME = 240;

ALTER WAREHOUSE my_existing_warehouse
  SET WAREHOUSE_SIZE = 'LARGE';

### Explanation of the Code
- Step 1: Create a new warehouse with a suitable size (e.g., LARGE) and auto-suspend/auto-resume settings.
- Step 2: Alternatively, modify an existing warehouse to increase its size and adjust settings as needed.

### When to Use
- When experiencing frequent query queuing or performance issues
- When increasing data volume or concurrency

### When Not to Use
- When resources are limited and cost is a significant concern
- When query workload is predictable and low

### Common Mistakes
- Underestimating query workload and data volume
- Not monitoring warehouse performance and adjusting size accordingly

### Expected Output
**Description:** The result of proper warehouse sizing and avoiding underprovisioning is improved query performance, reduced query queuing, and cost optimization.

**Schema:**
- Warehouse Name
- Warehouse Size
- Query Count
- Total Execution Time

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*