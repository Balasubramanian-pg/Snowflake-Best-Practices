# Warehouse Sizing for Data Archiving in Snowflake

**Description:**
This guide provides best practices and scripts for right-sizing Snowflake warehouses for data archiving workloads, ensuring optimal performance and cost-effectiveness. Proper warehouse sizing is crucial for efficient data archiving, as it directly impacts query performance, storage costs, and compute resource utilization. A well-sized warehouse for data archiving enables organizations to balance query performance with cost, ensuring that data is readily available for analysis while minimizing expenses. Effective warehouse sizing also helps prevent over-provisioning, which can lead to unnecessary costs, or under-provisioning, which can result in poor query performance.

## Short Explanation
**Overview:** Warehouse sizing for data archiving in Snowflake involves determining the optimal warehouse configuration to balance query performance and cost for archival workloads.

**Problem Solved:** Ensures efficient data archiving, optimal query performance, and cost-effectiveness in Snowflake.

**Importance:** Proper warehouse sizing for data archiving is crucial for organizations to analyze historical data while controlling costs and ensuring performance.

**Use Cases:**
- Archiving historical sales data for analysis
- Storing and querying large datasets for compliance and reporting

### Typical Scenario
A retail company needs to archive historical sales data for analysis, requiring a Snowflake warehouse that balances query performance with cost. The company has 10 TB of sales data to archive and expects to query the data monthly. They want to ensure that their warehouse is right-sized to handle the archival workload without over-provisioning or under-provisioning resources.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE archiving_wh
  WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 300
  AUTO_RESUME = 300;

ALTER WAREHOUSE archiving_wh SET STATEMENT_QUEUED_TIMEOUT_IN_SECONDS = 300;
```

### Example Execution Logic Step-by-Step
To right-size a Snowflake warehouse for data archiving, start by creating a new warehouse with an appropriate size (e.g., MEDIUM) and auto-suspend and auto-resume settings to optimize cost and performance. Adjust the queued statement timeout to prevent queries from timing out during execution. Monitor query performance and adjust the warehouse size as needed to ensure optimal performance.

### Implementation Example
CREATE WAREHOUSE archiving_wh
  WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 300
  AUTO_RESUME = 300;

ALTER WAREHOUSE archiving_wh SET STATEMENT_QUEUED_TIMEOUT_IN_SECONDS = 300;

-- Grant privileges to a role
GRANT USAGE ON WAREHOUSE archiving_wh TO ROLE archiving_role;

### Explanation of the Code
- Step 1: Create a new warehouse with a suitable size (e.g., MEDIUM) and auto-suspend and auto-resume settings.
- Step 2: Adjust the queued statement timeout to prevent queries from timing out during execution.
- Step 3: Grant privileges to a role to use the warehouse.

### When to Use
- When archiving large datasets for analysis and querying
- When optimizing Snowflake costs for data archiving workloads

### When Not to Use
- For real-time data warehousing workloads
- For high-performance data processing workloads

### Common Mistakes
- Over-provisioning warehouse resources, leading to unnecessary costs
- Under-provisioning warehouse resources, resulting in poor query performance

### Expected Output
**Description:** The resulting warehouse configuration and performance metrics, such as query execution times and costs.

**Schema:**
- Warehouse Name
- Warehouse Size
- Auto-Suspend
- Auto-Resume
- Queued Statement Timeout

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*