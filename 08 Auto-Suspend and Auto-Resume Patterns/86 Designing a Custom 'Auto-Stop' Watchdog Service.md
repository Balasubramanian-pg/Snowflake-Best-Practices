# 86 Designing a Custom 'Auto-Stop' Watchdog Service in Snowflake

This concept describes building a service within Snowflake that automatically identifies and terminates long-running or runaway queries, virtual warehouses, or other Snowflake resources that might be consuming excessive credits or causing performance issues. It exists to enforce cost governance and resource management by preventing unintentional overspending or resource contention.

**Short Explanation**

An 'Auto-Stop' Watchdog Service in Snowflake acts like a vigilant monitor, constantly checking for activities that exceed predefined thresholds, such as a query running for too long. When such an event occurs, the watchdog intervenes to stop the resource, typically a query or a warehouse, to prevent unnecessary credit consumption. This is crucial for managing Snowflake costs effectively and maintaining operational efficiency by automatically correcting resource anomalies. It's commonly used in data platforms to ensure cost control and prevent unexpected credit usage from rogue processes.

- **What problem does this SQL feature solve?** It solves the problem of runaway queries or inactive, over-provisioned warehouses that can lead to unexpected and high credit consumption in Snowflake. It prevents cost overruns and resource wastage.
- **Why is it important in databases or analytics?** It's vital for cost governance, particularly in cloud data warehouses like Snowflake where billing is usage-based. It helps maintain budget predictability and ensures resources are used efficiently.
- **Where is it commonly used in real workflows?** It's often implemented in environments with many concurrent users, complex ETL/ELT pipelines, or dynamic workloads where manual monitoring of resource usage is impractical or prone to human error.

## Implementation Example

```sql
-- Create a table to log watchdog events
CREATE OR REPLACE TABLE WATCHDOG_LOGS (
    LOG_TIMESTAMP TIMESTAMP_NTZ DEFAULT CURRENT_TIMESTAMP(),
    RESOURCE_TYPE VARCHAR,
    RESOURCE_NAME VARCHAR,
    ACTION_TAKEN VARCHAR,
    DETAILS VARIANT
);

-- Stored Procedure to check and terminate long-running queries
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

-- Schedule the watchdog procedure to run every 5 minutes
CREATE OR REPLACE TASK WATCHDOG_TASK_QUERY
  WAREHOUSE = YOUR_MONITORING_WAREHOUSE
  SCHEDULE = '5 MINUTE'
AS
  CALL AUTO_STOP_WATCHDOG_QUERY(3600); -- Check for queries running longer than 1 hour (3600 seconds)
```

## Explanation of the Code

This example focuses on an 'Auto-Stop' Watchdog for queries.

- **`CREATE OR REPLACE TABLE WATCHDOG_LOGS (...)`**:
  - **What it does:** Defines a table named `WATCHDOG_LOGS` to store records of actions taken by the watchdog service. This is crucial for auditing and understanding why a resource was stopped.
  - **How it changes the result set:** It doesn't change a result set but creates a persistent log for the watchdog's operations.

- **`CREATE OR REPLACE PROCEDURE AUTO_STOP_WATCHDOG_QUERY(...)`**:
  - **What it does:** This is the core logic. It's a stored procedure written in SQL that takes a `QUERY_DURATION_THRESHOLD_SECONDS` as input. It queries Snowflake's `ACCOUNT_USAGE.QUERY_HISTORY` view to find queries that are currently `RUNNING` and have been active longer than the specified threshold.
  - **How it changes the result set:** This procedure doesn't return a traditional dataset but rather a status message indicating if any queries were cancelled. Internally, it queries `QUERY_HISTORY` to identify targets.

- **`DECLARE ... BEGIN ... END;`**:
  - **What it does:** This block defines variables and the procedural logic for the SQL stored procedure. It iterates through identified long-running queries.

- **`SELECT QUERY_ID, STATEMENT_TEXT, USER_NAME FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY WHERE ...`**:
  - **What it does:** This `SELECT` statement retrieves details about running queries from the `QUERY_HISTORY` view.
  - **How it changes the result set:** It filters `QUERY_HISTORY` to only show queries that are currently executing (`EXECUTION_STATUS = 'RUNNING'`), started before a certain time (`START_TIME <= DATEADD(...)`), are associated with a warehouse, and are not the watchdog's own queries (`QUERY_TAG NOT ILIKE '%WATCHDOG_PROCESS%'`). The `ORDER BY` and `LIMIT 1` ensure it processes one oldest long-running query at a time.

- **`EXECUTE IMMEDIATE 'ALTER SYSTEM CANCEL QUERIES IN WAREHOUSE ALL IF QUERY_ID = \'' || query_id_to_kill || '\''`**:
  - **What it does:** This dynamic SQL statement uses the `ALTER SYSTEM CANCEL QUERIES` command to terminate the identified long-running query.
  - **How it changes the result set:** This is an action that modifies the state of the Snowflake system by stopping an active query.

- **`INSERT INTO WATCHDOG_LOGS (...) VALUES (...)`**:
  - **What it does:** After a query is cancelled, this `INSERT` statement logs the event into the `WATCHDOG_LOGS` table, providing details about the cancelled query and the action taken.
  - **How it changes the result set:** It adds a new row to the `WATCHDOG_LOGS` table.

- **`CREATE OR REPLACE TASK WATCHDOG_TASK_QUERY ... SCHEDULE = '5 MINUTE' AS CALL AUTO_STOP_WATCHDOG_QUERY(3600);`**:
  - **What it does:** This `CREATE TASK` statement schedules the `AUTO_STOP_WATCHDOG_QUERY` stored procedure to run automatically every 5 minutes. The procedure is called with `3600`, meaning it will look for queries running longer than 1 hour.
  - **How it changes the result set:** It creates a scheduled background job in Snowflake. It doesn't produce a direct result set itself but orchestrates the execution of the watchdog procedure.

## When to Use

1.  **Cost Governance for Long-Running Queries:** When you want to prevent individual queries from consuming excessive credits, especially in environments where users might accidentally submit inefficient or open-ended queries.
    *   *Example Scenario:* A data analyst runs an ad-hoc query without a `LIMIT` clause on a very large table, which could run for hours and deplete credits. The watchdog automatically stops it after a defined threshold (e.g., 30 minutes).

2.  **Resource Management for Idle Warehouses:** While Snowflake has auto-suspend, custom watchdogs can enforce stricter or more granular rules, such as suspending warehouses after specific inactive periods even if they have active sessions but no running queries.
    *   *Example Scenario:* A developer leaves a large warehouse running after their work, but Snowflake's default auto-suspend is set too high (e.g., 1 hour). A watchdog could suspend it after 15 minutes of true inactivity (no queries processed).

3.  **Preventing Performance Bottlenecks:** To ensure that critical workloads are not impacted by resource hogs. By terminating runaway processes, the watchdog helps free up warehouse resources.
    *   *Example Scenario:* During peak business hours, a complex report generation query is unexpectedly taking hours. The watchdog terminates it, allowing other, more critical reports or dashboards to run without delay.

## When Not to Use

1.  **For Critical, Legitimate Long-Running Processes:** If you have known, intentionally long-running processes (e.g., initial data loads, complex model training, or data transformations on massive datasets) that are expected to take hours, a generic auto-stop watchdog might interfere.
    *   *Situation:* An ETL process runs for 5 hours every night to transform petabytes of data. If the watchdog is set to stop queries after 3 hours, it would constantly kill this essential job.

2.  **Without Proper Logging and Notification:** Implementing an auto-stop without a robust logging mechanism (like `WATCHDOG_LOGS` above) and alerts to the affected users or administrators can lead to confusion, frustration, and difficulty in debugging.
    *   *Situation:* Users' queries are mysteriously stopping, but there's no log to explain why, who stopped them, or how to prevent it. This causes production support nightmares.

3.  **As a Replacement for Query Optimization:** While useful, an auto-stop watchdog is a reactive measure. It shouldn't be the primary strategy for managing query performance or costs. Proactive query optimization, proper table design, and warehouse sizing are generally more effective.
    *   *Situation:* You have many frequently run, inefficient queries. Relying on the watchdog to kill them instead of optimizing the queries themselves leads to repeated failures and user dissatisfaction.

## Common Mistakes

1.  **Setting Thresholds Too Aggressively or Too Loosely:** If the auto-stop threshold is too short, it will prematurely terminate legitimate operations. If it's too long, it defeats the purpose of cost control.
2.  **Not Excluding Watchdog's Own Queries/Processes:** The watchdog service itself can become a target if it's not explicitly excluded, leading to an infinite loop of the watchdog trying to stop itself.
3.  **Lack of Notification/Alerting:** Terminating a user's query or suspending a warehouse without notifying the relevant stakeholders can cause confusion, data integrity issues (if mid-transaction), or operational delays.
4.  **Inadequate Logging:** Without detailed logs, it's impossible to debug why a resource was stopped, identify patterns of abuse, or track the watchdog's effectiveness.
5.  **Running on the Same Warehouse it Monitors:** If the watchdog task runs on the very warehouse it's monitoring for auto-suspension, it can prevent the warehouse from suspending itself, creating a Catch-22. Dedicated monitoring warehouses are essential.

## Expected Output

The watchdog service itself doesn't produce a direct dataset as its primary function, but rather a side effect of resource termination and logging. The key "output" is the state change in the Snowflake environment (queries stopped, warehouses suspended) and the record of these actions.

Here's an example of what the `WATCHDOG_LOGS` table would look like:

| LOG_TIMESTAMP             | RESOURCE_TYPE | RESOURCE_NAME                        | ACTION_TAKEN | DETAILS                                                                                   |
| :------------------------ | :------------ | :----------------------------------- | :----------- | :---------------------------------------------------------------------------------------- |
| 2026-03-14 10:35:01.123   | QUERY         | `abc123efg456hi`                     | CANCELLED    | `{"statement_text": "SELECT * FROM large_table WHERE 1=1", "user_name": "ANALYST_A", "threshold_seconds": 3600}` |
| 2026-03-14 10:40:05.456   | QUERY         | `jkl789mno012pqr`                    | CANCELLED    | `{"statement_text": "CREATE OR REPLACE TABLE another_large_table AS SELECT ...", "user_name": "ETL_USER", "threshold_seconds": 3600}` |

-   **LOG_TIMESTAMP**: When the watchdog action was logged.
-   **RESOURCE_TYPE**: The type of Snowflake resource affected (e.g., `QUERY`, `WAREHOUSE`).
-   **RESOURCE_NAME**: The identifier of the resource (e.g., `QUERY_ID`, `WAREHOUSE_NAME`).
-   **ACTION_TAKEN**: The action performed by the watchdog (e.g., `CANCELLED`, `SUSPENDED`).
-   **DETAILS**: A JSON object containing additional context, such as the full SQL statement, the user who initiated it, and the threshold that was exceeded.
