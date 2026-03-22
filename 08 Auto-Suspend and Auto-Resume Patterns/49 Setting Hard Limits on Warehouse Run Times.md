# Setting Hard Limits on Warehouse Run Times in Snowflake

**Description:**
This concept refers to the ability in Snowflake to configure automatic suspension of a virtual warehouse after a specified period of inactivity. It helps manage costs and ensures efficient resource utilization by preventing warehouses from running unnecessarily.

## Short Explanation
**Overview:** Setting hard limits on warehouse run times in Snowflake means configuring a virtual warehouse to automatically shut down if it's been idle for a certain amount of time.

**Problem Solved:** It solves the problem of incurring costs for computing resources that aren't actively being used.

**Importance:** It's important for effective cost management and resource governance, especially in pay-per-use cloud models.

**Use Cases:**
- Development and Test Environments
- Ad-hoc Query Workloads
- Scheduled Data Loading/ETL with Gaps

### Typical Scenario
Automatically suspending warehouses during periods of inactivity, often for development, QA, or production systems outside of peak business hours.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE my_reporting_wh SET AUTO_SUSPEND = 300;
```

### Example Execution Logic Step-by-Step
Configuring automatic suspension of a virtual warehouse after a specified period of inactivity.

### Implementation Example
ALTER WAREHOUSE my_reporting_wh SET AUTO_SUSPEND = 300; -- Suspend after 300 seconds (5 minutes) of inactivity

### Explanation of the Code
- ALTER WAREHOUSE my_reporting_wh: This specifies that we are modifying an existing virtual warehouse named my_reporting_wh.
- SET AUTO_SUSPEND = 300;: This sets the duration, in seconds, after which a virtual warehouse will automatically suspend itself if it detects no active queries.

### When to Use
- Development and Test Environments: To minimize costs when developers or testers are not actively running queries.
- Ad-hoc Query Workloads: For warehouses primarily used by analysts for sporadic, interactive querying.
- Scheduled Data Loading/ETL with Gaps: If your data loading processes run at specific intervals and there are long periods between runs.

### When Not to Use
- Always-On, High-Concurrency Production Workloads: For critical production warehouses that need to respond instantly to a constant stream of queries.
- Workloads with Frequent, Small Bursts of Activity: If your workload consists of many small queries with brief pauses in between.
- Warehouses Supporting Persistent Connections: If applications maintain persistent connections that keep the warehouse technically 'active' even without query execution.

### Common Mistakes
- Setting AUTO_SUSPEND too low for interactive workloads: If the value is too short, users will experience frequent delays as the warehouse suspends and resumes between queries.
- Forgetting to set AUTO_SUSPEND entirely: This is the most common mistake, leading to significant cost overruns as warehouses remain running and consuming credits even when idle for extended periods.
- Confusing AUTO_SUSPEND with AUTO_RESUME: While related, AUTO_SUSPEND controls when it shuts down, and AUTO_RESUME controls whether it automatically starts when a new query arrives.

### Expected Output
**Description:** The command executes and returns a success message, indicating that the warehouse configuration has been updated.

**Schema:**
- status

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*