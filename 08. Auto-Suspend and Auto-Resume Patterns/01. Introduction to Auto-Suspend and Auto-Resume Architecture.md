# Introduction to Auto-Suspend and Auto-Resume Architecture in Snowflake

**Description:**
Snowflake's Auto-Suspend and Auto-Resume architecture optimizes compute resource utilization and controls costs by automatically pausing a virtual warehouse when inactive and restarting it when a query is submitted.

## Short Explanation
**Overview:** Auto-Suspend and Auto-Resume is a feature that allows Snowflake to automatically pause and resume virtual warehouses based on activity levels.

**Problem Solved:** It solves the problem of over-provisioning and wasted compute resources when a data warehouse is not actively processing queries.

**Importance:** It ensures cost efficiency and resource optimization, as users only pay for active compute time, not idle time.

**Use Cases:**
- Cost Optimization for Intermittent Workloads
- Development and Testing Environments
- Low-Latency Reporting for User-Facing Applications

### Typical Scenario
A marketing team runs ad-hoc reports throughout the day, but there are significant idle periods between queries.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
```sql
CREATE WAREHOUSE my_analyst_warehouse
WITH
  WAREHOUSE_SIZE = 'MEDIUM',
  AUTO_SUSPEND = 300, -- 300 seconds = 5 minutes
  AUTO_RESUME = TRUE,
  COMMENT = 'Warehouse for ad-hoc analytics with auto-suspend/resume.';

ALTER WAREHOUSE my_etl_warehouse
SET
  AUTO_SUSPEND = 600, -- 600 seconds = 10 minutes
  AUTO_RESUME = TRUE;

SHOW WAREHOUSES;
```
```

### Example Execution Logic Step-by-Step
The provided SQL code snippets demonstrate how to configure Auto-Suspend and Auto-Resume settings for a Snowflake virtual warehouse.

### Implementation Example
```sql
-- Create a new warehouse with auto-suspend after 5 minutes and auto-resume enabled
CREATE WAREHOUSE my_analyst_warehouse
WITH
  WAREHOUSE_SIZE = 'MEDIUM',
  AUTO_SUSPEND = 300, -- 300 seconds = 5 minutes
  AUTO_RESUME = TRUE,
  COMMENT = 'Warehouse for ad-hoc analytics with auto-suspend/resume.';

-- Or, alter an existing warehouse to enable these features
ALTER WAREHOUSE my_etl_warehouse
SET
  AUTO_SUSPEND = 600, -- 600 seconds = 10 minutes
  AUTO_RESUME = TRUE;

-- Show the current status of your warehouses
SHOW WAREHOUSES;
```

### Explanation of the Code
- `CREATE WAREHOUSE my_analyst_warehouse ...`: This statement defines and provisions a new virtual warehouse.
- `ALTER WAREHOUSE my_etl_warehouse SET ...`: This statement modifies the properties of an already existing warehouse.
- `SHOW WAREHOUSES;`: This command lists all warehouses you have access to, along with their current status and configuration.

### When to Use
- Cost Optimization for Intermittent Workloads
- Development and Testing Environments
- Low-Latency Reporting for User-Facing Applications

### When Not to Use
- Strict Low-Latency, High-Concurrency Workloads
- When Initial Query Warm-up is Critical
- Extremely High-Volume, Always-On ETL

### Common Mistakes
- Setting `AUTO_SUSPEND` too low for interactive users
- Forgetting to set `AUTO_RESUME = TRUE`
- Ignoring the impact of warehouse size on resume time
- Not monitoring warehouse credit usage

### Expected Output
**Description:** The `SHOW WAREHOUSES` command returns a result set describing your warehouses.

**Schema:**
- name
- state
- size
- auto_suspend
- auto_resume
- comment

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*