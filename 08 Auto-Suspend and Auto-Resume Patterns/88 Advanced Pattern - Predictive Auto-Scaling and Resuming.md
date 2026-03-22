# Predictive Auto-Scaling and Resuming in Snowflake

**Description:**
This advanced pattern in Snowflake involves intelligently managing virtual warehouses by predicting future workload demands to proactively scale compute resources up or down, and efficiently suspending/resuming them.

## Short Explanation
**Overview:** Predictive auto-scaling is about using historical data and anticipated events to adjust Snowflake warehouse resources before real-time demand changes.

**Problem Solved:** It solves the challenge of optimizing Snowflake compute costs and performance by proactively matching warehouse capacity to predicted demand, rather than reactively responding to current load.

**Importance:** It's crucial for managing cloud costs effectively, ensuring consistent query performance for end-users, and meeting strict Service Level Agreements (SLAs) for data processing and analytics.

**Use Cases:**
- Predictable Peak Workloads: Daily business hours for BI, end-of-month reporting, nightly ETL batches.
- Cost Optimization for Bursty Workloads: Sporadic, but intense, computational needs where keeping a large warehouse running constantly would be wasteful.
- Tiered Service Level Agreements (SLAs): Different workloads have varying performance requirements and cost sensitivities.

### Typical Scenario
A data warehouse primarily used by business analysts from 9 AM to 5 PM. During these hours, a LARGE warehouse is needed for concurrent queries, but after hours, a SMALL warehouse is sufficient for occasional tasks or can be suspended.

### Core Snowflake SQL Objects Used
`CREATE OR REPLACE PROCEDURE SCALE_WAREHOUSE_BASED_ON_TIME`, `CREATE OR REPLACE TASK SCALE_UP_BI_WH`, `CREATE OR REPLACE TASK SCALE_DOWN_BI_WH`

### Code Source Execution
```sql
```sql
-- 1. Create a Stored Procedure to Scale Warehouse Based on Time
CREATE OR REPLACE PROCEDURE SCALE_WAREHOUSE_BASED_ON_TIME(WAREHOUSE_NAME VARCHAR, TARGET_SIZE VARCHAR)
RETURNS VARCHAR
LANGUAGE SQL
AS
$$
DECLARE
    current_size VARCHAR;
    result VARCHAR;
BEGIN
    SELECT "size" INTO current_size FROM SNOWFLAKE.INFORMATION_SCHEMA.WAREHOUSES WHERE WAREHOUSE_NAME = :WAREHOUSE_NAME;

    IF (current_size <> :TARGET_SIZE) THEN
        EXECUTE IMMEDIATE 'ALTER WAREHOUSE ' || :WAREHOUSE_NAME || ' SET WAREHOUSE_SIZE = ''' || :TARGET_SIZE || '''';
        result := 'Warehouse ' || :WAREHOUSE_NAME || ' scaled from ' || current_size || ' to ' || :TARGET_SIZE;
    ELSE
        result := 'Warehouse ' || :WAREHOUSE_NAME || ' is already ' || current_size || '. No change needed.';
    END IF;
    RETURN result;
END;
$$;

-- 2. Create a Task to Scale Up During Business Hours (e.g., 9 AM UTC)
CREATE OR REPLACE TASK SCALE_UP_BI_WH
  WAREHOUSE = ANALYTICS_WH -- Task runs on a specific warehouse, can be a small admin warehouse
  SCHEDULE = 'USING CRON 0 9 * * MON-FRI UTC' -- Every weekday at 9 AM UTC
AS
  CALL SCALE_WAREHOUSE_BASED_ON_TIME('BI_ANALYTICS_WAREHOUSE', 'LARGE'); -- Scale your main BI warehouse to LARGE

-- 3. Create a Task to Scale Down After Business Hours (e.g., 6 PM UTC)
CREATE OR REPLACE TASK SCALE_DOWN_BI_WH
  WAREHOUSE = ANALYTICS_WH
  SCHEDULE = 'USING CRON 0 18 * * MON-FRI UTC' -- Every weekday at 6 PM UTC
AS
  CALL SCALE_WAREHOUSE_BASED_ON_TIME('BI_ANALYTICS_WAREHOUSE', 'SMALL'); -- Scale your main BI warehouse to SMALL

-- 4. Enable the tasks
ALTER TASK SCALE_UP_BI_WH RESUME;
ALTER TASK SCALE_DOWN_BI_WH RESUME;
```
```

### Example Execution Logic Step-by-Step
This example uses Snowflake Stored Procedures and Tasks to implement a scheduled, time-based scaling, which is a foundational element for predictive auto-scaling.

### Implementation Example
The provided SQL code demonstrates how to create a stored procedure and tasks to scale a warehouse based on a schedule.

### Explanation of the Code
- The `SCALE_WAREHOUSE_BASED_ON_TIME` procedure takes a warehouse name and target size as inputs and resizes the warehouse if necessary.
- The `SCALE_UP_BI_WH` and `SCALE_DOWN_BI_WH` tasks use cron schedules to execute the procedure at specific times.

### When to Use
- Predictable Peak Workloads
- Cost Optimization for Bursty Workloads
- Tiered Service Level Agreements (SLAs)

### When Not to Use
- Unpredictable, Highly Variable Workloads
- Workloads Requiring Instantaneous Start-Up
- Very Small or Constant Workloads

### Common Mistakes
- Ignoring Auto-Suspend/Auto-Resume
- Over-complicating Predictive Logic
- Not Monitoring Performance After Implementing
- Neglecting Multi-Cluster Warehouse Policies
- Not Accounting for Cache Warm-Up

### Expected Output
**Description:** The output of this pattern isn't a direct SQL result set but rather an optimized and cost-efficient Snowflake environment where warehouses adapt dynamically to workload needs.

**Schema:**
- SCHEDULED_TIME
- WAREHOUSE_NAME
- ACTION

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*