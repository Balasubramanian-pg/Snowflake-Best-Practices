# Impact of Altering Min-Max Clusters on Running Queries in Snowflake

**Description:**
This topic explains the effects of changing the minimum and maximum cluster sizes of a multi-cluster warehouse on queries that are currently running. Altering these settings can significantly impact query performance and resource allocation.

## Short Explanation
**Overview:** Changing min-max clusters affects resource allocation for running queries.

**Problem Solved:** Managing query performance and resource allocation during peak or off-peak hours.

**Importance:** Ensures efficient use of resources and optimal query performance.

**Use Cases:**
- Handling sudden spikes in query workload
- Optimizing resource utilization during off-peak hours

### Typical Scenario
A company experiences a sudden surge in query workload and needs to adjust its multi-cluster warehouse settings to ensure efficient resource allocation and optimal query performance.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE my_warehouse SET MIN_CLUSTER_COUNT = 2, MAX_CLUSTER_COUNT = 5;
```

### Example Execution Logic Step-by-Step
To adjust the minimum and maximum cluster counts of a multi-cluster warehouse, use the ALTER WAREHOUSE command. For example, to set the minimum cluster count to 2 and the maximum cluster count to 5, execute the following SQL command: ALTER WAREHOUSE my_warehouse SET MIN_CLUSTER_COUNT = 2, MAX_CLUSTER_COUNT = 5;

### Implementation Example
Suppose we have a multi-cluster warehouse named 'my_warehouse' with an initial configuration of MIN_CLUSTER_COUNT = 1 and MAX_CLUSTER_COUNT = 3. We can modify these settings as follows:

ALTER WAREHOUSE my_warehouse SET MIN_CLUSTER_COUNT = 2, MAX_CLUSTER_COUNT = 5;

This change will allow the warehouse to automatically scale between 2 and 5 clusters based on the workload.

### Explanation of the Code
- Step 1: Identify the multi-cluster warehouse to be modified.
- Step 2: Use the ALTER WAREHOUSE command to set the new MIN_CLUSTER_COUNT and MAX_CLUSTER_COUNT values.

### When to Use
- During peak hours to ensure sufficient resources
- During off-peak hours to optimize resource utilization

### When Not to Use
- When queries are sensitive to cluster changes
- When the warehouse is under heavy load and scaling is not feasible

### Common Mistakes
- Not monitoring query performance after altering settings
- Setting unrealistic minimum or maximum cluster counts

### Expected Output
**Description:** The result of the ALTER WAREHOUSE command is a confirmation that the settings have been updated.

**Schema:**
- Statement
- Status

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*