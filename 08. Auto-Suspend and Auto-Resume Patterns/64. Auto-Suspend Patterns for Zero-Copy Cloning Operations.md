# Optimizing Snowflake Auto-Suspend for Looker and Tableau Extracts

**Description:**
This document discusses the importance of Snowflake's AUTO_SUSPEND feature for cost management in BI tool workloads. It provides best practices on setting auto-suspend for virtual warehouses used by Looker and Tableau.

## Short Explanation
**Overview:** Auto-suspend in Snowflake automatically pauses a virtual warehouse after a specified period of inactivity, which helps in cost management.

**Problem Solved:** It solves the problem of incurring costs for idle compute resources.

**Importance:** It's critical for cost optimization in cloud data warehousing, especially for BI tools with intermittent query patterns.

**Use Cases:**
- BI Tool Warehouses (Looker, Tableau)
- Development/QA Environments
- ETL/Batch Workloads

### Typical Scenario
A company uses Looker and Tableau for business intelligence. These tools generate short, bursty queries or extract data periodically, leading to idle warehouse time. By setting AUTO_SUSPEND to an optimal duration (e.g., 60-120 seconds), the company can prevent unnecessary credit usage.

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
The provided SQL examples demonstrate how to create a new virtual warehouse or modify an existing one to utilize the AUTO_SUSPEND feature. This allows the warehouse to automatically pause after a specified period of inactivity, reducing costs.

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
- The CREATE WAREHOUSE statement creates a new virtual warehouse named bi_queries_wh with a specified warehouse size, auto-suspend duration, and auto-resume enabled.
- The ALTER WAREHOUSE statement modifies an existing warehouse to set the auto-suspend duration and enable auto-resume.

### When to Use
- BI Tool Warehouses (Looker, Tableau): When dedicated warehouses serve interactive dashboards or data extract jobs that run intermittently.
- Development/QA Environments: For warehouses used by developers for ad-hoc querying and testing with unpredictable usage patterns.
- ETL/Batch Workloads: For frequent, short batch processes where an auto-suspend of 1 minute or less, paired with AUTO_RESUME, can be highly effective.

### When Not to Use
- Workloads Heavily Reliant on Warehouse Cache: If queries frequently re-use cached data for performance, an aggressive auto-suspend will clear the cache, potentially increasing query latency.
- Very Frequent, Continuous Workloads: For warehouses processing a constant stream of queries with almost no idle time, suspending the warehouse would lead to frequent restarts, adding latency and potentially increasing credit consumption.

### Common Mistakes
- Setting AUTO_SUSPEND Too Long: Leaving the default AUTO_SUSPEND of 5 minutes or setting it to a value that's too high for the workload directly leads to paying for significant idle time.
- Disabling AUTO_SUSPEND Entirely: Disabling auto-suspend (AUTO_SUSPEND = 0) is a major cost driver, as the warehouse will run continuously even with no activity.
- Not Understanding Cache Impact: Developers might set AUTO_SUSPEND too aggressively for cache-sensitive workloads, leading to degraded performance due to frequent cache clearing and cold starts.

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