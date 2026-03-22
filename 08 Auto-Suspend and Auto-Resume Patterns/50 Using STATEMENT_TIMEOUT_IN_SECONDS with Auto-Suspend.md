# Optimizing Snowflake Resource Consumption with STATEMENT_TIMEOUT_IN_SECONDS and Auto-Suspend

**Description:**
This concept combines STATEMENT_TIMEOUT_IN_SECONDS and Auto-Suspend to manage warehouse resource consumption and prevent runaway queries.

## Short Explanation
**Overview:** Automatically stopping long-running queries and pausing computing resources (warehouses) in Snowflake to save money and prevent issues.

**Problem Solved:** Prevents resource exhaustion and high costs from inefficient or runaway queries, ensuring warehouses aren't left running unnecessarily.

**Importance:** Crucial for cost optimization, performance stability, and maintaining service levels, especially in environments with complex queries or fluctuating workloads.

**Use Cases:**
- Cost Management for Development/Testing Warehouses
- Preventing Runaway Queries in Production ETL/ELT
- Optimizing Ad-Hoc Analytics and Reporting

### Typical Scenario
A development team frequently runs experimental or inefficient queries that might accidentally run for hours, consuming credits, and tends to leave their dedicated dev warehouse running overnight.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
ALTER SESSION SET STATEMENT_TIMEOUT_IN_SECONDS = 300;
ALTER WAREHOUSE my_reporting_wh SET STATEMENT_TIMEOUT_IN_SECONDS = 600;
ALTER WAREHOUSE my_reporting_wh SET AUTO_SUSPEND = 600;
```

### Example Execution Logic Step-by-Step
This code snippet demonstrates configuring statement timeouts and auto-suspend in Snowflake.

### Implementation Example
```sql
-- Set the statement timeout for the current session to 300 seconds (5 minutes)
ALTER SESSION SET STATEMENT_TIMEOUT_IN_SECONDS = 300;

-- Or set it at the warehouse level for all sessions using that warehouse
ALTER WAREHOUSE my_reporting_wh SET STATEMENT_TIMEOUT_IN_SECONDS = 600;

-- Configure auto-suspend for a warehouse (e.g., 600 seconds of inactivity)
ALTER WAREHOUSE my_reporting_wh SET AUTO_SUSPEND = 600;

-- Example of a potentially long-running query that could be affected
SELECT
    c.customer_id,
    c.customer_name,
    COUNT(o.order_id) AS total_orders,
    SUM(od.quantity * od.price) AS total_revenue
FROM
    customers c
JOIN
    orders o ON c.customer_id = o.customer_id
JOIN
    order_details od ON o.order_id = od.order_id
WHERE
    o.order_date >= '2023-01-01'
GROUP BY
    c.customer_id,
    c.customer_name
HAVING
    COUNT(o.order_id) > 10
ORDER BY
    total_revenue DESC
LIMIT 100;
```

### Explanation of the Code
- - `ALTER SESSION SET STATEMENT_TIMEOUT_IN_SECONDS = 300;`: Sets the STATEMENT_TIMEOUT_IN_SECONDS parameter for the current user session.
- - `ALTER WAREHOUSE my_reporting_wh SET STATEMENT_TIMEOUT_IN_SECONDS = 600;`: Sets the STATEMENT_TIMEOUT_IN_SECONDS parameter for a specific warehouse.
- - `ALTER WAREHOUSE my_reporting_wh SET AUTO_SUSPEND = 600;`: Configures the my_reporting_wh warehouse to automatically suspend after 600 seconds of inactivity.

### When to Use
- Cost Management for Development/Testing Warehouses: Set STATEMENT_TIMEOUT_IN_SECONDS to a low value and configure AUTO_SUSPEND to a short duration.
- Preventing Runaway Queries in Production ETL/ELT: Apply STATEMENT_TIMEOUT_IN_SECONDS to the warehouse used by the ETL process.
- Optimizing Ad-Hoc Analytics and Reporting: Implement STATEMENT_TIMEOUT_IN_SECONDS and AUTO_SUSPEND to balance responsiveness with cost savings.

### When Not to Use
- For Known Long-Running, Critical Batches: Avoid setting STATEMENT_TIMEOUT_IN_SECONDS for legitimate long-running processes.
- When Warehouse Resumption Latency is Unacceptable: Keep the warehouse AUTO_SUSPEND setting at 0 or a very high value for near-instantaneous query response times.
- For Queries That Retrieve Extremely Large Result Sets: Consider using data unloading tools or COPY INTO <location> commands for large data transfers.

### Common Mistakes
- Setting Timeouts Too Aggressively: Analyze typical and peak query runtimes before setting STATEMENT_TIMEOUT_IN_SECONDS.
- Ignoring the Hierarchy of Timeout Settings: Understand the hierarchy of parameters to avoid unexpected behavior.
- Over-relying on Auto-Suspend for Cost Control: Optimize query performance in addition to using AUTO_SUSPEND.
- Not Monitoring Canceled Queries: Monitor QUERY_HISTORY to identify and optimize problematic SQL or adjust timeout settings.

### Expected Output
**Description:** A standard result set based on the SELECT statement, showing customer information aggregated by orders and revenue.

**Schema:**
- CUSTOMER_ID
- CUSTOMER_NAME
- TOTAL_ORDERS
- TOTAL_REVENUE

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*