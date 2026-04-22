# Designing a Custom 'Auto-Stop' Watchdog Service in Snowflake

**Description:**
This concept describes building a service within Snowflake that automatically identifies and terminates long-running or runaway queries, virtual warehouses, or other Snowflake resources that might be consuming excessive credits or causing performance issues.

## Short Explanation
**Overview:** An 'Auto-Stop' Watchdog Service in Snowflake acts like a vigilant monitor, constantly checking for activities that exceed predefined thresholds, such as a query running for too long.

**Problem Solved:** It solves the problem of runaway queries or inactive, over-provisioned warehouses that can lead to unexpected and high credit consumption in Snowflake.

**Importance:** It's vital for cost governance, particularly in cloud data warehouses like Snowflake where billing is usage-based.

**Use Cases:**
- Cost Governance for Long-Running Queries
- Resource Management for Idle Warehouses
- Preventing Performance Bottlenecks

### Typical Scenario
A data analyst runs an ad-hoc query without a LIMIT clause on a very large table, which could run for hours and deplete credits.

### Core Snowflake SQL Objects Used
`CREATE OR REPLACE TABLE WATCHDOG_LOGS`, `CREATE OR REPLACE PROCEDURE AUTO_STOP_WATCHDOG_QUERY`, `CREATE OR REPLACE TASK WATCHDOG_TASK_QUERY`

### Code Source Execution
```sql
CREATE OR REPLACE TABLE WATCHDOG_LOGS (
    LOG_TIMESTAMP TIMESTAMP_NTZ DEFAULT CURRENT_TIMESTAMP(),
    RESOURCE_TYPE VARCHAR,
    RESOURCE_NAME VARCHAR,
    ACTION_TAKEN VARCHAR,
    DETAILS VARIANT
);

CREATE OR REPLACE PROCEDURE AUTO_STOP_WATCHDOG_QUERY(QUERY_DURATION_THRESHOLD_SECONDS INT)
RETURNS VARCHAR
LANGUAGE SQL
AS
$$
DECLARE
    query_id_to_kill VARCHAR;
    statement_text_to_kill VARCHAR;
    username_to_kill VARCHAR;
    status_message VARCHAR := 'No long-running queries found.';
BEGIN
    FOR query_record IN (
        SELECT
            QUERY_ID,
            STATEMENT_TEXT,
            USER_NAME
        FROM
            SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
        WHERE
            EXECUTION_STATUS = 'RUNNING'
            AND START_TIME <= DATEADD('second', -:QUERY_DURATION_THRESHOLD_SECONDS, CURRENT_TIMESTAMP())
            AND WAREHOUSE_NAME IS NOT NULL -- Exclude queries not associated with a warehouse
            AND QUERY_TAG NOT ILIKE '%WATCHDOG_PROCESS%' -- Exclude watchdog queries themselves
        ORDER BY START_TIME ASC
        LIMIT 1 -- Kill one at a time to manage impact
    )
    DO
        query_id_to_kill := query_record.QUERY_ID;
        statement_text_to_kill := query_record.STATEMENT_TEXT;
        username_to_kill := query_record.USER_NAME;

        EXECUTE IMMEDIATE 'ALTER SYSTEM CANCEL QUERIES IN WAREHOUSE ALL IF QUERY_ID = \'' || query_id_to_kill || '\'';

        INSERT INTO WATCHDOG_LOGS (RESOURCE_TYPE, RESOURCE_NAME, ACTION_TAKEN, DETAILS)
        VALUES ('QUERY', query_id_to_kill, 'CANCELLED', OBJECT_CONSTRUCT(
            'statement_text', statement_text_to_kill,
            'user_name', username_to_kill,
            'threshold_seconds', :QUERY_DURATION_THRESHOLD_SECONDS
        ));
        status_message := 'Cancelled query ' || query_id_to_kill;
    END FOR;
    RETURN status_message;
END;
$$;

CREATE OR REPLACE TASK WATCHDOG_TASK_QUERY
  WAREHOUSE = YOUR_MONITORING_WAREHOUSE
  SCHEDULE = '5 MINUTE'
AS
  CALL AUTO_STOP_WATCHDOG_QUERY(3600); -- Check for queries running longer than 1 hour (3600 seconds)
```

### Example Execution Logic Step-by-Step
This example focuses on an 'Auto-Stop' Watchdog for queries.

### Implementation Example
The watchdog service itself doesn't produce a direct dataset as its primary function, but rather a side effect of resource termination and logging.

### Explanation of the Code
- CREATE OR REPLACE TABLE WATCHDOG_LOGS (...)
- CREATE OR REPLACE PROCEDURE AUTO_STOP_WATCHDOG_QUERY(...)
- CREATE OR REPLACE TASK WATCHDOG_TASK_QUERY ...

### When to Use
- Cost Governance for Long-Running Queries
- Resource Management for Idle Warehouses
- Preventing Performance Bottlenecks

### When Not to Use
- For Critical, Legitimate Long-Running Processes
- Without Proper Logging and Notification
- As a Replacement for Query Optimization

### Common Mistakes
- Setting Thresholds Too Aggressively or Too Loosely
- Not Excluding Watchdog's Own Queries/Processes
- Lack of Notification/Alerting
- Inadequate Logging
- Running on the Same Warehouse it Monitors

### Expected Output
**Description:** The watchdog service itself doesn't produce a direct dataset as its primary function, but rather a side effect of resource termination and logging.

**Schema:**
- LOG_TIMESTAMP
- RESOURCE_TYPE
- RESOURCE_NAME
- ACTION_TAKEN
- DETAILS

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*