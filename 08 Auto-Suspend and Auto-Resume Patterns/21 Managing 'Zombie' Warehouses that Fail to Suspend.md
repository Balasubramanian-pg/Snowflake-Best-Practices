# Managing 'Zombie' Warehouses that Fail to Suspend in Snowflake

This concept addresses a specific operational challenge in Snowflake where a virtual warehouse, despite being commanded to suspend, remains active or in a state where it continues to consume credits. This usually happens due to ongoing, long-running queries or sessions that prevent the warehouse from entering a suspended state, leading to unexpected credit usage.

**Short Explanation**

A "zombie" warehouse is a Snowflake virtual warehouse that doesn't suspend as expected, often because active sessions or queries are still running on it. This problem leads to unnecessary credit consumption even when the warehouse is supposed to be idle. Identifying and resolving these situations is crucial for cost optimization and efficient resource management in Snowflake.

- **What problem does this SQL feature solve?** It addresses the problem of runaway credit consumption and inefficient resource management caused by warehouses that fail to suspend.
- **Why is it important in databases or analytics?** In cloud data warehousing, cost management is paramount. Zombie warehouses can significantly inflate costs, making it vital to monitor and manage them.
- **Where is it commonly used in real workflows?** Operations teams and data platform administrators use monitoring and management techniques to ensure Snowflake warehouses suspend properly after periods of inactivity.

## Implementation Example

While there isn't a single SQL command to "manage zombie warehouses," identifying the root cause often involves querying Snowflake's account usage views. Here's an example of how to find active queries that might be preventing a warehouse from suspending.

```sql
SELECT
    query_id,
    session_id,
    user_name,
    warehouse_name,
    query_text,
    execution_status,
    start_time,
    end_time
FROM
    SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
WHERE
    warehouse_name = 'YOUR_ZOMBIE_WAREHOUSE_NAME'
    AND execution_status = 'RUNNING'
    AND start_time >= DATEADD(hour, -1, CURRENT_TIMESTAMP())
ORDER BY
    start_time DESC;
```

## Explanation of the Code

This query helps to identify active queries on a specific warehouse that might be preventing its suspension.

-   **`SELECT query_id, session_id, user_name, warehouse_name, query_text, execution_status, start_time, end_time`**: This clause specifies the columns we want to retrieve from the `QUERY_HISTORY` view. It includes identifiers for the query and session, the user who ran it, the warehouse involved, the actual SQL text, its current status, and timestamps.
    -   **What it does**: Selects specific attributes of queries.
    -   **How it changes the result set**: Determines the columns present in the output.
-   **`FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY`**: This clause indicates that we are querying the `QUERY_HISTORY` view within the `ACCOUNT_USAGE` schema of the `SNOWFLAKE` database. This view provides comprehensive information about all queries executed in the account.
    -   **What it does**: Specifies the data source for the query.
    -   **How it changes the result set**: All rows from `QUERY_HISTORY` are initially considered before filtering.
-   **`WHERE warehouse_name = 'YOUR_ZOMBIE_WAREHOUSE_NAME' AND execution_status = 'RUNNING' AND start_time >= DATEADD(hour, -1, CURRENT_TIMESTAMP())`**: This clause filters the results based on three conditions:
    -   `warehouse_name = 'YOUR_ZOMBIE_WAREHOUSE_NAME'`: Limits results to queries run on the specific warehouse you suspect is "zombie."
    -   `execution_status = 'RUNNING'`: Filters for queries that are currently active.
    -   `start_time >= DATEADD(hour, -1, CURRENT_TIMESTAMP())`: Restricts the results to queries that started within the last hour, focusing on recent activity.
    -   **What it does**: Filters rows based on the warehouse name, execution status, and start time.
    -   **How it changes the result set**: Only rows matching all three conditions are included in the final output.
-   **`ORDER BY start_time DESC`**: This clause sorts the filtered results by the `start_time` column in descending order, showing the most recent running queries first.
    -   **What it does**: Arranges the output rows.
    -   **How it changes the result set**: Orders the remaining rows from newest to oldest based on query start time.

## When to Use

1.  **Unexpected Credit Usage Spikes**: If you observe higher-than-expected credit consumption, especially during off-peak hours, checking for zombie warehouses is a primary step.
    *   *Scenario*: Your daily cost reports show a warehouse consumed credits overnight despite no scheduled jobs running. You'd use this query to see what was active.
2.  **Warehouse Not Suspending Automatically**: When a warehouse is configured for auto-suspend but consistently remains active.
    *   *Scenario*: You set a 5-minute auto-suspend, but the warehouse has been active for hours. This query helps pinpoint why.
3.  **Troubleshooting Performance Issues**: Sometimes, a stuck query or session on a warehouse can impact performance for other queries or prevent scaling operations.
    *   *Scenario*: A warehouse is slow, and you suspect a long-running, overlooked query is hogging resources.

## When Not to Use

1.  **Proactive Monitoring of Healthy Warehouses**: This approach is reactive, used for troubleshooting. For general health monitoring, scheduled tasks checking `WAREHOUSE_LOAD_HISTORY` or `WAREHOUSE_METERING_HISTORY` are better.
2.  **When Credits are Consumed by Auto-Resume**: If a warehouse is repeatedly suspending and resuming due to legitimate, albeit frequent, workloads, it's not a "zombie." This query would show the active queries but not necessarily indicate a problem.
3.  **For Identifying Hung Worksheets in the Web UI**: While the underlying cause might be a running query, the immediate problem (a browser tab that appears stuck) might be better addressed by checking browser or network issues first before diving into Snowflake’s account usage.

## Common Mistakes

1.  **Forgetting to Specify `WAREHOUSE_NAME`**: Running the query without filtering by `warehouse_name` will return all running queries across all warehouses, making it harder to pinpoint the issue.
2.  **Ignoring `SESSION_ID`**: Once a running query is identified, understanding its `session_id` is crucial for potentially terminating the session if the query is truly stuck or unauthorized.
3.  **Terminating Necessary Queries**: Incorrectly identifying a legitimate, long-running batch job as a "zombie" query and terminating it, leading to data inconsistencies or job failures. Always verify the query's purpose and user before taking action.

## Expected Output

The resulting dataset will show a list of queries currently running on the specified warehouse, sorted by how recently they started. Each row represents an active query that could potentially be preventing the warehouse from suspending.

| QUERY\_ID | SESSION\_ID | USER\_NAME | WAREHOUSE\_NAME | QUERY\_TEXT | EXECUTION\_STATUS | START\_TIME | END\_TIME |
| :---------------------------------: | :---------------------------------: | :---------------------------------: | :---------------------------------: | :-------------------------------------------------------------------------------------: | :---------------------------------: | :---------------------------------: | :---------------------------------: |
| 1a2b3c4d... | 5e6f7g8h... | ANALYST\_USER | YOUR\_ZOMBIE\_WAREHOUSE\_NAME | `SELECT COUNT(*) FROM LARGE_TABLE;` | RUNNING | 2026-03-14 10:35:00.000 +0000 | NULL |
| 9i0j1k2l... | 3m4n5o6p... | ETL\_SERVICE | YOUR\_ZOMBIE\_WAREHOUSE\_NAME | `INSERT INTO AGG_DATA SELECT ...` | RUNNING | 2026-03-14 10:30:15.000 +0000 | NULL |

-   **QUERY\_ID**: Unique identifier for the query.
-   **SESSION\_ID**: Unique identifier for the user session that submitted the query.
-   **USER\_NAME**: The user or service account that executed the query.
-   **WAREHOUSE\_NAME**: The virtual warehouse where the query is running.
-   **QUERY\_TEXT**: The actual SQL code of the running query.
-   **EXECUTION\_STATUS**: Will be 'RUNNING' for all rows in this result.
-   **START\_TIME**: The timestamp when the query started execution.
-   **END\_TIME**: Will be `NULL` for running queries.
