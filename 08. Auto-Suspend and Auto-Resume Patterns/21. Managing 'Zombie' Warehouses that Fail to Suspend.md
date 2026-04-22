# Managing 'Zombie' Warehouses that Fail to Suspend in Snowflake

**Description:**
This concept addresses a specific operational challenge in Snowflake where a virtual warehouse, despite being commanded to suspend, remains active or in a state where it continues to consume credits.

## Short Explanation
**Overview:** A 'zombie' warehouse is a Snowflake virtual warehouse that doesn't suspend as expected, often because active sessions or queries are still running on it.

**Problem Solved:** It addresses the problem of runaway credit consumption and inefficient resource management caused by warehouses that fail to suspend.

**Importance:** In cloud data warehousing, cost management is paramount. Zombie warehouses can significantly inflate costs, making it vital to monitor and manage them.

**Use Cases:**
- Unexpected Credit Usage Spikes
- Warehouse Not Suspending Automatically
- Troubleshooting Performance Issues

### Typical Scenario
Your daily cost reports show a warehouse consumed credits overnight despite no scheduled jobs running.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
SELECT query_id, session_id, user_name, warehouse_name, query_text, execution_status, start_time, end_time FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY WHERE warehouse_name = 'YOUR_ZOMBIE_WAREHOUSE_NAME' AND execution_status = 'RUNNING' AND start_time >= DATEADD(hour, -1, CURRENT_TIMESTAMP()) ORDER BY start_time DESC;
```

### Example Execution Logic Step-by-Step
This query helps to identify active queries on a specific warehouse that might be preventing its suspension.

### Implementation Example
The provided SQL query is used to find active queries that might be preventing a warehouse from suspending.

### Explanation of the Code
- SELECT query_id, session_id, user_name, warehouse_name, query_text, execution_status, start_time, end_time: This clause specifies the columns we want to retrieve from the QUERY_HISTORY view.
- FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY: This clause indicates that we are querying the QUERY_HISTORY view within the ACCOUNT_USAGE schema of the SNOWFLAKE database.
- WHERE warehouse_name = 'YOUR_ZOMBIE_WAREHOUSE_NAME' AND execution_status = 'RUNNING' AND start_time >= DATEADD(hour, -1, CURRENT_TIMESTAMP()): This clause filters the results based on three conditions.
- ORDER BY start_time DESC: This clause sorts the filtered results by the start_time column in descending order, showing the most recent running queries first.

### When to Use
- Unexpected Credit Usage Spikes
- Warehouse Not Suspending Automatically
- Troubleshooting Performance Issues

### When Not to Use
- Proactive Monitoring of Healthy Warehouses
- When Credits are Consumed by Auto-Resume
- For Identifying Hung Worksheets in the Web UI

### Common Mistakes
- Forgetting to Specify WAREHOUSE_NAME
- Ignoring SESSION_ID
- Terminating Necessary Queries

### Expected Output
**Description:** The resulting dataset will show a list of queries currently running on the specified warehouse, sorted by how recently they started.

**Schema:**
- QUERY_ID
- SESSION_ID
- USER_NAME
- WAREHOUSE_NAME
- QUERY_TEXT
- EXECUTION_STATUS
- START_TIME
- END_TIME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*