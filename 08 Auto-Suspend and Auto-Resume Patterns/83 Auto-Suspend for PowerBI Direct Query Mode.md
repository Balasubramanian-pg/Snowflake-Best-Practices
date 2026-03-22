# Auto-Suspend for PowerBI Direct Query Mode in Snowflake

**Description:**
This concept focuses on automatically suspending Snowflake warehouses when used for PowerBI Direct Query Mode, specifically handling idle time to manage costs effectively.

## Short Explanation
**Overview:** Auto-suspend in Snowflake is a cost-saving mechanism that shuts down a virtual warehouse after a specified period of inactivity.

**Problem Solved:** It solves the problem of incurring unnecessary costs due to Snowflake warehouses remaining active when no actual queries are running, especially in scenarios where BI tools maintain persistent connections.

**Importance:** Cost optimization is crucial in cloud data warehousing. This feature ensures efficient resource utilization and prevents idle compute charges, vital for budget management in analytics environments.

**Use Cases:**
- PowerBI Direct Query Mode with intermittent user queries
- Interactive analytics environments with sporadic query execution

### Typical Scenario
A PowerBI dashboard accessed sporadically throughout the day, where the warehouse would run 24/7 without auto-suspend, but with auto-suspend, it only spins up for the few minutes users interact with the dashboard.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE powerbi_direct_query_wh SET AUTO_SUSPEND = 60, AUTO_RESUME = TRUE;
```

### Example Execution Logic Step-by-Step
Configuring a Snowflake warehouse with auto-suspend to manage costs when used with PowerBI Direct Query Mode.

### Implementation Example
ALTER WAREHOUSE powerbi_direct_query_wh SET AUTO_SUSPEND = 60, AUTO_RESUME = TRUE; ALTER USER powerbi_user SET STATEMENT_TIMEOUT_IN_SECONDS = 300;

### Explanation of the Code
- The first SQL statement modifies an existing virtual warehouse named powerbi_direct_query_wh to auto-suspend after 60 seconds of inactivity and auto-resume when a new query is submitted.
- The second SQL statement sets the STATEMENT_TIMEOUT_IN_SECONDS for a specific user, powerbi_user, to 300 seconds, which can help release resources faster if queries hang or if PowerBI tries to keep a long-lived 'ping' connection.

### When to Use
- Cost Optimization for BI Workloads: When PowerBI (or similar BI tools) is used in Direct Query mode, warehouses can remain active even during periods of user inactivity due to persistent connections.
- Interactive Analytics Environments: For ad-hoc querying or interactive analytics where users run queries periodically, auto-suspend prevents continuous billing for idle time.

### When Not to Use
- Constant High Workload: For warehouses that are always processing a continuous stream of queries or have very short intervals between queries, aggressive AUTO_SUSPEND settings can lead to frequent suspensions and resumptions.
- Performance-Critical Real-time Applications: For applications requiring sub-second response times and continuous availability, cold starts from auto-resume might introduce unacceptable latency.

### Common Mistakes
- Setting AUTO_SUSPEND too high (e.g., 1 hour): This can lead to significant idle compute costs, especially with intermittent PowerBI direct queries.
- Setting AUTO_SUSPEND too low (e.g., 1 minute) for frequently accessed dashboards: If users are interacting with a dashboard every few minutes, the warehouse might repeatedly suspend and resume, leading to cold start delays and potentially frustrating user experience.

### Expected Output
**Description:** The expected outcome is observed in cost reports and warehouse state monitoring, where the billed time for the POWERBI_DIRECT_QUERY_WH would show periods of inactivity (no billing) interspersed with periods of active billing.

**Schema:**
- WAREHOUSE_NAME
- STATE
- AUTO_SUSPEND

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*