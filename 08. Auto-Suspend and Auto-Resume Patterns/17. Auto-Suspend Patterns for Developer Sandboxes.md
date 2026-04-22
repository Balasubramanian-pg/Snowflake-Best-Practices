# 17 Auto-Suspend Patterns for Developer Sandboxes in Snowflake

**Description:**
This concept refers to various strategies and configurations for automatically suspending virtual warehouses (compute clusters) in Snowflake, specifically tailored for developer sandboxes.

## Short Explanation
**Overview:** Auto-suspend patterns define rules for when a Snowflake virtual warehouse should automatically shut down due to inactivity.

**Problem Solved:** It solves the problem of incurring unnecessary costs from idle Snowflake virtual warehouses.

**Importance:** It's vital for cost optimization, particularly in cloud data warehousing where compute is billed on a per-second basis.

**Use Cases:**
- Individual Developer Sandboxes
- Shared Development/QA Warehouses with Sporadic Use
- Ad-hoc Reporting/Analysis Warehouses

### Typical Scenario
A developer is working on a feature, runs a few queries, then switches to coding for an hour. Their warehouse suspends, saving credits, and automatically resumes when they return to run more queries.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
```sql
-- Create a small virtual warehouse for a developer sandbox with a 5-minute auto-suspend
CREATE WAREHOUSE DEV_ANNA_WH
  WITH
    WAREHOUSE_SIZE = 'XSMALL'
    AUTO_SUSPEND = 300 -- 300 seconds = 5 minutes
    AUTO_RESUME = TRUE
    MIN_CLUSTER_COUNT = 1
    MAX_CLUSTER_COUNT = 1
    STATEMENT_TIMEOUT_IN_SECONDS = 3600 -- 1 hour timeout for individual statements
    COMMENT = 'Developer sandbox warehouse for Anna - auto-suspends after 5 min idle';

-- Alter an existing warehouse to change its auto-suspend setting
ALTER WAREHOUSE DEV_MARK_WH SET AUTO_SUSPEND = 600; -- 10 minutes
```
```

### Example Execution Logic Step-by-Step
The example primarily uses `CREATE WAREHOUSE` and `ALTER WAREHOUSE` DDL (Data Definition Language) statements in Snowflake.

### Implementation Example
```sql
-- Create a small virtual warehouse for a developer sandbox with a 5-minute auto-suspend
CREATE WAREHOUSE DEV_ANNA_WH
  WITH
    WAREHOUSE_SIZE = 'XSMALL'
    AUTO_SUSPEND = 300 -- 300 seconds = 5 minutes
    AUTO_RESUME = TRUE
    MIN_CLUSTER_COUNT = 1
    MAX_CLUSTER_COUNT = 1
    STATEMENT_TIMEOUT_IN_SECONDS = 3600 -- 1 hour timeout for individual statements
    COMMENT = 'Developer sandbox warehouse for Anna - auto-suspends after 5 min idle';

-- Alter an existing warehouse to change its auto-suspend setting
ALTER WAREHOUSE DEV_MARK_WH SET AUTO_SUSPEND = 600; -- 10 minutes
```

### Explanation of the Code
- - **`CREATE WAREHOUSE DEV_ANNA_WH ...`**: This command is used to provision a new virtual warehouse named `DEV_ANNA_WH`.
- - **`WAREHOUSE_SIZE = 'XSMALL'`**: Specifies the compute size for the warehouse.
- - **`AUTO_SUSPEND = 300`**: This is the core of the auto-suspend pattern.
- - **`AUTO_RESUME = TRUE`**: Ensures that the warehouse automatically resumes when a query is submitted to it.
- - **`MIN_CLUSTER_COUNT = 1`** and **`MAX_CLUSTER_COUNT = 1`**: These settings configure a single-cluster warehouse.
- - **`STATEMENT_TIMEOUT_IN_SECONDS = 3600`**: Sets a timeout for individual statements.
- - **`COMMENT = '...'`**: Provides a descriptive comment.
- - **`ALTER WAREHOUSE DEV_MARK_WH SET AUTO_SUSPEND = 600;`**: This command modifies an existing virtual warehouse.

### When to Use
- Individual Developer Sandboxes: When each developer has their own dedicated warehouse for ad-hoc queries, testing, or development, setting a short `AUTO_SUSPEND` (e.g., 5-10 minutes) ensures costs are minimal when they aren't actively using it.
- Shared Development/QA Warehouses with Sporadic Use: If a warehouse is shared by a small team but usage isn't constant throughout the day, a slightly longer `AUTO_SUSPEND` (e.g., 15-30 minutes) can balance cost savings with reduced resume frequency.
- Ad-hoc Reporting/Analysis Warehouses: For analysts who run complex, infrequent queries, an auto-suspend can prevent unnecessary credit consumption between reporting cycles.

### When Not to Use
- Continuously Running Production Workloads: For production warehouses that process streaming data, maintain always-on applications, or run frequent, critical jobs, `AUTO_SUSPEND = 0` (never suspend) or a very long suspend time is usually preferred.
- Performance-Sensitive Interactive Dashboards: If a warehouse serves an interactive dashboard where users expect immediate query results without any delay, the overhead of waiting for an auto-resuming warehouse can degrade the user experience.
- Long-Running Batch Jobs with Gaps: If a single, very long-running batch job has internal idle periods that are shorter than the auto-suspend time, suspending it unnecessarily will add resume overhead without significant cost savings.

### Common Mistakes
- Setting `AUTO_SUSPEND = 0` (Never Suspend) in Dev/QA: This is the most common mistake leading to runaway costs.
- Too Short `AUTO_SUSPEND` for Interactive Use: Setting `AUTO_SUSPEND` to a very low value (e.g., 60 seconds) for interactive development can lead to frequent suspensions and resumptions.
- Ignoring `STATEMENT_TIMEOUT_IN_SECONDS`: While not an auto-suspend setting, developers often neglect `STATEMENT_TIMEOUT_IN_SECONDS` in conjunction with `AUTO_SUSPEND`.

### Expected Output
**Description:** The `CREATE WAREHOUSE` and `ALTER WAREHOUSE` commands do not return a dataset in the traditional sense.

**Schema:**
- NAME
- STATE
- AUTO_SUSPEND
- AUTO_RESUME
- WAREHOUSE_SIZE
- COMMENT

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*