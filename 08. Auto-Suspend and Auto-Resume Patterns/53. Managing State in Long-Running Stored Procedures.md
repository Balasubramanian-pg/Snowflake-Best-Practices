# Managing State in Long-Running Stored Procedures

**Description:**
Understanding the relationship between Snowflake's cache eviction and auto-suspend timing to optimize performance and manage costs.

## Short Explanation
**Overview:** This concept explores how Snowflake's cache eviction and auto-suspend mechanisms interact, impacting performance and costs.

**Problem Solved:** It helps mitigate potential performance degradation and cost inefficiencies when Snowflake warehouses frequently suspend and resume.

**Importance:** It directly impacts query performance and operational costs in a cloud data warehouse environment.

**Use Cases:**
- Workloads with sporadic usage
- Interactive dashboards with infrequent access
- Cost optimization initiatives

### Typical Scenario
A data architect optimizing warehouse auto-suspend times for various workloads to balance cost savings with performance needs.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE my_test_warehouse SET AUTO_SUSPEND = 60;
```

### Example Execution Logic Step-by-Step
Demonstrating how to set up a scenario to observe the effects of cache eviction in conjunction with auto-suspend.

### Implementation Example
Setting up a small warehouse with a short auto-suspend, querying a table to populate the cache, and observing cache re-population time after auto-suspend.

### Explanation of the Code
- Step 1: Create or alter a warehouse with a short auto-suspend
- Step 2: Use the warehouse
- Step 3: Execute a query to populate the cache
- Step 4: Wait for the warehouse to auto-suspend
- Step 5: Execute the same query again to observe cache re-population time

### When to Use
- Workloads with sporadic usage
- Interactive dashboards with infrequent access
- Cost optimization initiatives

### When Not to Use
- Constantly running or high concurrency workloads
- Workloads with no data locality or extremely large data sets
- Warehouses with very long auto-suspend times

### Common Mistakes
- Setting auto-suspend too short without considering cache
- Ignoring query patterns
- Over-relying on local cache for all performance

### Expected Output
**Description:** The output observed would be in query execution times in the Snowflake Query History and warehouse credit consumption reports.

**Schema:**
- C_CUSTKEY
- C_NAME
- C_ADDRESS
- TOTAL_ORDER_VALUE

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*