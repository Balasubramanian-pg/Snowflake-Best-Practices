# The 'Immediate Suspend' Pattern in Snowflake

**Description:**
The 'Immediate Suspend' pattern in Snowflake refers to a strategy for controlling compute warehouse costs by immediately suspending a virtual warehouse when it becomes idle.

## Short Explanation
**Overview:** This pattern involves setting the AUTO_SUSPEND parameter of a Snowflake virtual warehouse to a very low value, making it shut down quickly after its last query completes.

**Problem Solved:** It solves the problem of paying for idle compute resources in Snowflake warehouses.

**Importance:** It's critical for cost management, ensuring efficient use of cloud resources and preventing budget overruns, especially with variable workloads.

**Use Cases:**
- Development/testing environments
- Ad-hoc query warehouses
- Batch processing warehouses that aren't continuously active

### Typical Scenario
A data analyst runs a few queries in the morning, then doesn't touch the warehouse for hours. Immediate suspend ensures it shuts down soon after their last query.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE my_compute_wh SET AUTO_SUSPEND = 60; ALTER WAREHOUSE my_compute_wh SET AUTO_RESUME = TRUE;
```

### Example Execution Logic Step-by-Step
This example uses the ALTER WAREHOUSE statement to modify an existing virtual warehouse.

### Implementation Example
ALTER WAREHOUSE my_compute_wh SET AUTO_SUSPEND = 60 AUTO_RESUME = TRUE;

### Explanation of the Code
- ALTER WAREHOUSE my_compute_wh: This clause specifies that we are modifying the configuration of a virtual warehouse named my_compute_wh.
- SET: This keyword indicates that we are changing specific parameters of the warehouse.
- AUTO_SUSPEND = 60: This parameter defines the idle time (in seconds) after which the warehouse will automatically suspend itself.
- AUTO_RESUME = TRUE: This parameter ensures that the warehouse will automatically start up again when a new query is submitted to it.

### When to Use
- Cost Optimization for Sporadic Workloads
- Batch Processing with Gaps
- Multiple, Smaller Warehouses

### When Not to Use
- High-Concurrency, Continuous Workloads
- Workloads Sensitive to Startup Latency
- Large, Multi-Cluster Warehouses (Especially Enterprise with Query Acceleration/Search Optimization)

### Common Mistakes
- Setting AUTO_SUSPEND too high for cost-sensitive workloads
- Forgetting AUTO_RESUME = TRUE
- Applying to critical production warehouses without testing
- Not considering the cost of startup time

### Expected Output
**Description:** The expected output is a modified warehouse configuration where the AUTO_SUSPEND parameter is set to a low value, ensuring quicker suspension and cost savings.

**Schema:**
- NAME
- STATE
- TYPE
- SIZE
- MIN_CLUSTER_COUNT
- MAX_CLUSTER_COUNT
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*