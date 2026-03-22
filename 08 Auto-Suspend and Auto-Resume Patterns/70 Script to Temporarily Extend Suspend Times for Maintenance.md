# Script to Temporarily Extend Suspend Times for Maintenance in Snowflake

**Description:**
This operational pattern involves a scripted approach to safely lengthen an auto-suspend timeout for a virtual warehouse during maintenance or batch work, preventing premature warehouse suspension that could kill long-running maintenance queries.

## Short Explanation
**Overview:** Temporarily extend auto-suspend timeout for virtual warehouses during maintenance or batch work.

**Problem Solved:** Prevents warehouses from auto-suspending in the middle of maintenance jobs, which would abort work and waste credits.

**Importance:** Keeps batch/maintenance windows predictable without leaving warehouses running forever (cost risk).

**Use Cases:**
- During deployments
- Data backfills
- Heavy transforms scheduled in quiet windows

### Typical Scenario
Running schema migrations that periodically go idle between steps, backfilling historical partitions where some queries are quick, others long, and controlled batch windows with known end times but variable activity.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
```sql
-- 1) Capture current suspend setting
SHOW WAREHOUSES LIKE 'MAINT_WH';

-- Use result: current AUTO_SUSPEND (e.g., 60 seconds)
-- 2) Temporarily extend suspend time
ALTER WAREHOUSE MAINT_WH SET AUTO_SUSPEND = 1800; -- 30 minutes

-- 3) Run maintenance work
BEGIN
  CALL RUN_MAINTENANCE();
END;

-- 4) Revert to original setting
ALTER WAREHOUSE MAINT_WH SET AUTO_SUSPEND = 60;
```
```

### Example Execution Logic Step-by-Step


### Implementation Example
```sql
-- 1) Capture current suspend setting
SHOW WAREHOUSES LIKE 'MAINT_WH';

-- Use result: current AUTO_SUSPEND (e.g., 60 seconds)
-- 2) Temporarily extend suspend time
ALTER WAREHOUSE MAINT_WH SET AUTO_SUSPEND = 1800; -- 30 minutes

-- 3) Run maintenance work
BEGIN
  CALL RUN_MAINTENANCE();
END;

-- 4) Revert to original setting
ALTER WAREHOUSE MAINT_WH SET AUTO_SUSPEND = 60;
```

### Explanation of the Code
- SHOW WAREHOUSES: discovers current warehouse config (including AUTO_SUSPEND) so you can restore it later.
- ALTER WAREHOUSE ... SET AUTO_SUSPEND: changes the idle time before Snowflake suspends; increasing it reduces interruption risk.
- CALL RUN_MAINTENANCE(): executes the actual maintenance operations while the longer timeout is active.
- Second ALTER WAREHOUSE: reverts AUTO_SUSPEND to the original value to avoid excess credit burn.

### When to Use
- Running schema migrations that periodically go idle between steps
- Backfilling historical partitions where some queries are quick, others long
- Controlled batch windows with known end times but variable activity

### When Not to Use
- Normal OLAP workloads: let auto-suspend do its job to save credits
- Unsupervised sessions: risk leaving a high timeout forever
- When warehouse concurrency handles spikes better than stretching suspend

### Common Mistakes
- Forgetting to revert the setting, leaving AUTO_SUSPEND high permanently
- Not checking current value first, so revert target is wrong
- Running maintenance on the wrong warehouse name

### Expected Output
**Description:** The SHOW WAREHOUSES command returns metadata rows like:

**Schema:**
- name
- size
- AUTO_SUSPEND
- AUTO_RESUME
- STATE

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*