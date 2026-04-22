# Auto-Suspend for Looker and Tableau Extracts in Snowflake

**Description:**
This feature automatically pauses a virtual warehouse after a specified period of inactivity, stopping credit consumption. It's crucial for cost management, especially with BI tools like Looker and Tableau that often generate short, bursty queries or extract data periodically, leading to idle warehouse time.

## Short Explanation
**Overview:** Auto-suspend in Snowflake is a setting for virtual warehouses that automatically shuts them down after a period of no activity.

**Problem Solved:** It solves the problem of incurring costs for idle compute resources.

**Importance:** It's critical for cost optimization in cloud data warehousing.

**Use Cases:**
- BI Tool Warehouses (Looker, Tableau)
- Development/QA Environments
- ETL/Batch Workloads

### Typical Scenario
A business uses Looker or Tableau for data analysis and wants to optimize costs by automatically suspending the virtual warehouse during idle periods.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
```sql
-- Create a new warehouse specifically for BI tools with auto-suspend set to 60 seconds
CREATE WAREHOUSE bi_queries_wh
  WAREHOUSE_SIZE = SMALL
  AUTO_SUSPEND = 60
  AUTO_RESUME = TRUE
  COMMENT = 'Warehouse for Looker and Tableau queries/extracts with aggressive auto-suspend';

-- Alternatively, alter an existing warehouse to enable auto-suspend
ALTER WAREHOUSE existing_analytics_wh
  SET AUTO_SUSPEND = 120,
      AUTO_RESUME = TRUE;
```
```

### Example Execution Logic Step-by-Step
The example demonstrates creating a new virtual warehouse with auto-suspend enabled and modifying an existing warehouse to use auto-suspend.

### Implementation Example
```sql
-- Create a new warehouse specifically for BI tools with auto-suspend set to 60 seconds
CREATE WAREHOUSE bi_queries_wh
  WAREHOUSE_SIZE = SMALL
  AUTO_SUSPEND = 60
  AUTO_RESUME = TRUE
  COMMENT = 'Warehouse for Looker and Tableau queries/extracts with aggressive auto-suspend';

-- Alternatively, alter an existing warehouse to enable auto-suspend
ALTER WAREHOUSE existing_analytics_wh
  SET AUTO_SUSPEND = 120,
      AUTO_RESUME = TRUE;
```

### Explanation of the Code
- The CREATE WAREHOUSE statement creates a new virtual warehouse named bi_queries_wh with auto-suspend set to 60 seconds.
- The ALTER WAREHOUSE statement modifies an existing warehouse to use auto-suspend.

### When to Use
- BI Tool Warehouses (Looker, Tableau): When dedicated warehouses serve interactive dashboards or data extract jobs that run intermittently.
- Development/QA Environments: For warehouses used by developers for ad-hoc querying and testing.
- ETL/Batch Workloads: While some ETL jobs are long-running, if you have frequent, short batch processes, an auto-suspend of 1 minute or less, paired with AUTO_RESUME, can be highly effective.

### When Not to Use
- Workloads Heavily Reliant on Warehouse Cache: If queries frequently re-use cached data for performance.
- Very Frequent, Continuous Workloads: For warehouses processing a constant stream of queries with almost no idle time.
- When Auto-Resume is Disabled: If AUTO_RESUME is FALSE, the warehouse will suspend and require manual intervention to restart.

### Common Mistakes
- Setting AUTO_SUSPEND Too Long: Leaving the default AUTO_SUSPEND of 5 minutes or setting it to a value that's too high for the workload.
- Disabling AUTO_SUSPEND Entirely: Disabling auto-suspend (AUTO_SUSPEND = 0) is a major cost driver.
- Not Understanding Cache Impact: Developers might set AUTO_SUSPEND too aggressively for cache-sensitive workloads.

### Expected Output
**Description:** The AUTO_SUSPEND setting itself doesn't produce a direct dataset output. Its effect is on the operational behavior and cost of the Snowflake warehouse.

**Schema:**
- TIMESTAMP
- WAREHOUSE_NAME
- STATE
- CREDITS_USED_PER_MINUTE
- DURATION_SECONDS

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*