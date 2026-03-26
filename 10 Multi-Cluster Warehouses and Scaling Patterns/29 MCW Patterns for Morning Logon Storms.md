# Multi-Cluster Warehouse Patterns for Morning Logon Storms

**Description:**
This topic discusses strategies for handling morning logon storms in Snowflake using multi-cluster warehouses. A morning logon storm occurs when a large number of users log in to the system at the same time, causing a spike in concurrent queries. A well-designed multi-cluster warehouse can help mitigate this issue by providing sufficient resources to handle the increased load.

## Short Explanation
**Overview:** Morning logon storms can cause performance issues in Snowflake due to a sudden surge in concurrent queries.

**Problem Solved:** Handling morning logon storms by scaling multi-cluster warehouses to meet the increased demand.

**Importance:** Ensures smooth and efficient data access for users, reducing wait times and improving overall system performance.

**Use Cases:**
- Handling morning logon storms
- Scaling for large-scale data ingestion
- Supporting high-concurrency workloads

### Typical Scenario
A company with hundreds of employees logging in to their Snowflake instance at the same time every morning, causing a significant spike in concurrent queries. To address this, they implement a multi-cluster warehouse with auto-scaling capabilities.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE mcw_for_logon_storms CLUSTER_NUMBER = 2 WAREHOUSE_SIZE = 'MEDIUM' AUTO_SUSPEND = 60 AUTO_RESUME = 60;
```

### Example Execution Logic Step-by-Step
The example SQL code creates a multi-cluster warehouse named 'mcw_for_logon_storms' with 2 clusters, a medium warehouse size, and auto-suspend and auto-resume features enabled.

### Implementation Example
To implement this solution, create a multi-cluster warehouse with the required specifications and set up a schedule to monitor and adjust the warehouse size as needed.

### Explanation of the Code
- Step 1: Create a multi-cluster warehouse with the required specifications.
- Step 2: Monitor the warehouse performance and adjust the size as needed to ensure optimal performance.

### When to Use
- During morning logon storms
- For large-scale data ingestion
- For high-concurrency workloads

### When Not to Use
- For low-concurrency workloads
- For small-scale data ingestion

### Common Mistakes
- Underestimating the required warehouse size
- Not monitoring warehouse performance

### Expected Output
**Description:** The expected output is a well-performing multi-cluster warehouse that can handle morning logon storms and provide smooth data access for users.

**Schema:**
- Warehouse Name
- Cluster Number
- Warehouse Size
- Status

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*