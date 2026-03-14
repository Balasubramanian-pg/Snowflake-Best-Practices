# 70 Script to Temporarily Extend Suspend Times for Maintenance in Snowflake

This isn't a single Snowflake SQL keyword but an operational pattern: a scripted approach to safely lengthen an auto-suspend timeout for a virtual warehouse during maintenance or batch work. It exists to avoid premature warehouse suspension that could kill long-running maintenance queries, while ensuring you revert to normal cost controls afterward.

**Short Explanation**

Write 2-3 sentences explaining the idea in simple terms.
- What problem does this SQL feature solve? Prevents warehouses from auto-suspending in the middle of maintenance jobs, which would abort work and waste credits.
- Why is it important in databases or analytics? Keeps batch/maintenance windows predictable without leaving warehouses running forever (cost risk).
- Where is it commonly used in real workflows? During deployments, data backfills, and heavy transforms scheduled in quiet windows.

## Implementation Example

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

## Explanation of the Code

Break the query into logical parts and explain what each section does. For each clause used (SELECT, FROM, WHERE, GROUP BY, JOIN, HAVING, ORDER BY) explain:
- SHOW WAREHOUSES: discovers current warehouse config (including AUTO_SUSPEND) so you can restore it later.
- ALTER WAREHOUSE ... SET AUTO_SUSPEND: changes the idle time before Snowflake suspends; increasing it reduces interruption risk.
- CALL RUN_MAINTENANCE(): executes the actual maintenance operations while the longer timeout is active.
- Second ALTER WAREHOUSE: reverts AUTO_SUSPEND to the original value to avoid excess credit burn.

## When to Use

- Running schema migrations that periodically go idle between steps.
- Backfilling historical partitions where some queries are quick, others long.
- Controlled batch windows with known end times but variable activity.

## When Not to Use

- Normal OLAP workloads: let auto-suspend do its job to save credits.
- Unsupervised sessions: risk leaving a high timeout forever.
- When warehouse concurrency handles spikes better than stretching suspend.

## Common Mistakes

- Forgetting to revert the setting, leaving AUTO_SUSPEND high permanently.
- Not checking current value first, so revert target is wrong.
- Running maintenance on the wrong warehouse name.

## Expected Output

Describe what the resulting dataset should look like. Show an example table with 2-3 rows and explain the columns.

`SHOW WAREHOUSES` returns metadata rows like:

| name     | size | AUTO_SUSPEND | AUTO_RESUME | STATE    |
|----------|------|--------------|-------------|----------|
| MAINT_WH | X-SM | 60           | true        | SUSPENDED |
| MAINT_WH | X-SM | 1800         | true        | STARTED   |

Columns: name (warehouse), AUTO_SUSPEND (idle seconds before suspend), STATE (current activity). The script temporarily changes AUTO_SUSPEND, then reverts it.
