# Auto-Suspend Patterns for Long-Running ETL Jobs in Snowflake

**Description:**
This concept refers to strategies and techniques designed to automatically pause or suspend Snowflake warehouses when long-running Extract, Transform, Load (ETL) jobs are idle or have completed their tasks.

## Short Explanation
**Overview:** Auto-suspend patterns help optimize cost by ensuring compute resources are only active when actively processing data, preventing unnecessary billing for idle time.

**Problem Solved:** It solves the problem of incurring unnecessary costs due to idle Snowflake virtual warehouses after ETL operations are complete or during periods of inactivity within long-running processes.

**Importance:** It's crucial for cost optimization in cloud data warehousing, particularly in Snowflake's consumption-based pricing model.

**Use Cases:**
- After batch ETL/ELT jobs complete
- For ad-hoc or infrequent workloads
- During expected downtime or maintenance windows

### Typical Scenario
A nightly ETL job finishes at 3 AM. The last step in the orchestration calls ALTER WAREHOUSE MY_ETL_WH SUSPEND; to ensure it's not billing until the next run.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`, `CREATE TASK`, `CREATE PROCEDURE`

### Code Source Execution
```sql
```sql
-- Example 1: Setting a short auto-suspend for an ETL-specific warehouse
ALTER WAREHOUSE ETL_LOAD_WH SET AUTO_SUSPEND = 60; -- Suspend after 60 seconds of inactivity

-- Example 2: Immediately suspending a warehouse after a series of ETL tasks
ALTER WAREHOUSE ETL_TRANSFORM_WH SUSPEND;

-- Example 3: Creating a transient task that automatically suspends a warehouse
CREATE TASK my_suspend_task
  WAREHOUSE = MONITORING_WH -- A small, always-on warehouse for tasks
  SCHEDULE = 'USING CRON 0 0 * * * UTC' -- Runs daily at midnight UTC
AS
  ALTER WAREHOUSE LONG_RUNNING_ETL_WH SUSPEND; -- Suspends the target warehouse

-- Example 4: Using a stored procedure to manage warehouse state
CREATE OR REPLACE PROCEDURE MANAGE_ETL_WAREHOUSE(action VARCHAR)
RETURNS VARCHAR
LANGUAGE SQL
AS
$$
BEGIN
    IF (action = 'RESUME') THEN
        ALTER WAREHOUSE ETL_COMPUTE_WH RESUME;
        RETURN 'ETL_COMPUTE_WH resumed.';
    ELSEIF (action = 'SUSPEND') THEN
        ALTER WAREHOUSE ETL_COMPUTE_WH SUSPEND;
        RETURN 'ETL_COMPUTE_WH suspended.';
    ELSE
        RETURN 'Invalid action. Use "RESUME" or "SUSPEND".';
    END IF;
END;
$$;

-- Call the procedure to suspend after an ETL job
CALL MANAGE_ETL_WAREHOUSE('SUSPEND');
```
```

### Example Execution Logic Step-by-Step
The provided SQL examples illustrate how to manage warehouse auto-suspension and suspension as part of ETL patterns.

### Implementation Example
The SQL code examples provided can be used to implement auto-suspend patterns for long-running ETL jobs in Snowflake.

### Explanation of the Code
- The ALTER WAREHOUSE command modifies an existing virtual warehouse's auto-suspend setting.
- The CREATE TASK command creates a scheduled task that executes a SQL statement at a specified interval.
- The CREATE PROCEDURE command creates a stored procedure that encapsulates logic for managing a warehouse's state.

### When to Use
- After batch ETL/ELT jobs complete
- For ad-hoc or infrequent workloads
- During expected downtime or maintenance windows

### When Not to Use
- For continuously running services or streaming data
- When warehouse resumption latency is critical
- During the middle of a multi-step ETL process

### Common Mistakes
- Forgetting to set AUTO_SUSPEND or suspend warehouses
- Setting AUTO_SUSPEND too high or too low
- Suspending a warehouse that other dependent processes rely on

### Expected Output
**Description:** The expected output in the Snowflake UI or command-line interface is usually a confirmation message.

**Schema:**
- Status
- Message

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*