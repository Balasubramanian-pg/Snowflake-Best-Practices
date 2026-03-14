# 32 Script to Automate Day-Night Suspend Settings in Snowflake

This concept refers to a script designed to manage the automatic suspension and resumption of Snowflake warehouses based on time-of-day, typically to optimize costs by suspending warehouses during off-peak hours (night, weekends) and resuming them during working hours. It addresses the need for efficient resource management and cost control in cloud data warehousing.

**Short Explanation**

This script automates the process of turning Snowflake virtual warehouses on and off according to a predefined schedule, like suspending them at night and resuming them in the morning. This saves money by ensuring compute resources are only active when needed, preventing unnecessary charges for idle warehouses. It's crucial for optimizing cloud spending, especially for environments with predictable usage patterns, and is commonly used in data engineering and analytics teams to manage their Snowflake infrastructure efficiently.

- What problem does this SQL feature solve? It solves the problem of incurring costs for idle Snowflake virtual warehouses when they are not actively being used, by automating their suspension and resumption.
- Why is it important in databases or analytics? It's important for cost optimization and resource management, allowing organizations to maintain performance during peak hours while drastically reducing expenses during off-peak times.
- Where is it commonly used in real workflows? It's commonly used in data warehousing environments, data lakes, and analytics platforms where compute usage patterns are predictable (e.g., business hours vs. non-business hours).

## Implementation Example

```sql
-- Example: Snowflake Task and Streamlit procedure to automate warehouse suspend/resume
-- This is a simplified representation of the logic that would be orchestrated
-- usually via external schedulers or Snowflake Tasks/Streams and stored procedures.

-- 1. Create a Snowflake Stored Procedure to manage warehouse state
CREATE OR REPLACE PROCEDURE MANAGE_WAREHOUSE_STATE(warehouse_name VARCHAR, action VARCHAR)
RETURNS VARCHAR
LANGUAGE SQL
AS
$$
BEGIN
    IF (UPPER(action) = 'SUSPEND') THEN
        EXECUTE IMMEDIATE 'ALTER WAREHOUSE ' || :warehouse_name || ' SUSPEND';
        RETURN 'Warehouse ' || :warehouse_name || ' suspended.';
    ELSEIF (UPPER(action) = 'RESUME') THEN
        EXECUTE IMMEDIATE 'ALTER WAREHOUSE ' || :warehouse_name || ' RESUME';
        RETURN 'Warehouse ' || :warehouse_name || ' resumed.';
    ELSE
        RETURN 'Invalid action. Use SUSPEND or RESUME.';
    END IF;
END;
$$;

-- 2. Create Tasks to call the procedure at specific times
-- Task to suspend warehouse daily at 7 PM UTC
CREATE OR REPLACE TASK SUSPEND_ANALYTICS_WH
  WAREHOUSE = YOUR_ADMIN_WH -- Task runs on a separate, smaller warehouse
  SCHEDULE = 'USING CRON 0 19 * * * UTC' -- Every day at 7 PM UTC
AS
  CALL MANAGE_WAREHOUSE_STATE('ANALYTICS_WH', 'SUSPEND');

-- Task to resume warehouse daily at 6 AM UTC
CREATE OR REPLACE TASK RESUME_ANALYTICS_WH
  WAREHOUSE = YOUR_ADMIN_WH
  SCHEDULE = 'USING CRON 0 6 * * * UTC' -- Every day at 6 AM UTC
AS
  CALL MANAGE_WAREHOUSE_STATE('ANALYTICS_WH', 'RESUME');

-- 3. Enable the tasks
ALTER TASK SUSPEND_ANALYTICS_WH RESUME;
ALTER TASK RESUME_ANALYTICS_WH RESUME;
```

## Explanation of the Code

This code snippet outlines a common approach to automate warehouse suspension and resumption using Snowflake's native capabilities.

- `CREATE OR REPLACE PROCEDURE MANAGE_WAREHOUSE_STATE(...)`: This creates a stored procedure named `MANAGE_WAREHOUSE_STATE`.
    - **What it does:** It encapsulates the logic for altering the state of a specified warehouse (suspend or resume).
    - **How it changes the result set:** This is a DDL operation; it doesn't return a traditional result set but rather a string indicating the action taken on the warehouse.

- `CREATE OR REPLACE TASK SUSPEND_ANALYTICS_WH ... SCHEDULE = 'USING CRON 0 19 * * * UTC' AS CALL MANAGE_WAREHOUSE_STATE('ANALYTICS_WH', 'SUSPEND');`: This creates a scheduled task.
    - **What it does:** It schedules the `MANAGE_WAREHOUSE_STATE` procedure to be called daily at 7 PM UTC to suspend the `ANALYTICS_WH` warehouse.
    - **How it changes the result set:** Tasks execute DDL/DML statements at scheduled intervals; their primary effect is on the system state (warehouse status) rather than a direct result set returned to the user.

- `CREATE OR REPLACE TASK RESUME_ANALYTICS_WH ... SCHEDULE = 'USING CRON 0 6 * * * UTC' AS CALL MANAGE_WAREHOUSE_STATE('ANALYTICS_WH', 'RESUME');`: This creates another scheduled task.
    - **What it does:** It schedules the `MANAGE_WAREHOUSE_STATE` procedure to be called daily at 6 AM UTC to resume the `ANALYTICS_WH` warehouse.
    - **How it changes the result set:** Similar to the suspend task, it affects the system state (warehouse status).

- `ALTER TASK SUSPEND_ANALYTICS_WH RESUME;` and `ALTER TASK RESUME_ANALYTICS_WH RESUME;`: These statements activate the previously created tasks.
    - **What it does:** They enable the tasks so that they start executing according to their defined schedules.
    - **How it changes the result set:** These are DDL operations; they modify the state of the tasks (from suspended to active).

## When to Use

1.  **Cost Optimization for Predictable Workloads:** When your data processing or analytics workloads are concentrated during specific hours (e.g., 9 AM to 5 PM local time), and there's minimal activity outside those hours.
    *   *Example Scenario:* A business intelligence team runs daily reports and ad-hoc queries only during standard business hours. The script can suspend warehouses overnight and on weekends, significantly reducing compute costs.
2.  **Resource Management for Development/Test Environments:** For non-production environments that are only used by developers during their working hours.
    *   *Example Scenario:* A development team uses a dedicated warehouse for testing new data pipelines. The script suspends this warehouse after typical development hours to avoid unnecessary billing when no one is actively working.
3.  **Tiered Service Level Agreements (SLAs):** When different warehouses are dedicated to different user groups with varying usage patterns and budget constraints.
    *   *Example Scenario:* A "reporting" warehouse needs to be active during daylight hours for business users, while an "ETL" warehouse might run continuously or on a different schedule. The script manages each according to its specific SLA.

## When Not to Use

1.  **24/7 Uninterrupted Workloads:** For critical, always-on applications or real-time data ingestion where any delay from warehouse startup is unacceptable.
    *   *Example Scenario:* A real-time dashboard powering customer-facing applications that must be available and performant around the clock. Suspending its underlying warehouse would cause outages or significant delays.
2.  **Unpredictable or Ad-Hoc Workloads with High Latency Sensitivity:** When workloads are highly erratic, and users expect immediate responsiveness without waiting for a warehouse to resume (which can take a few seconds).
    *   *Example Scenario:* A data science team frequently runs experimental queries at any hour of the day or night. If their warehouse is suspended, the initial query will experience a delay while the warehouse starts up.
3.  **Workloads with High Auto-Suspend Settings:** If your warehouse already has a very short auto-suspend period (e.g., 5 minutes of inactivity), the cost savings from a day-night script might be minimal, and the script adds unnecessary complexity.
    *   *Example Scenario:* A small analytics warehouse used occasionally throughout the day automatically suspends after 5 minutes of inactivity. A day-night script might provide negligible additional savings for this pattern.

## Common Mistakes

1.  **Incorrect Timezone/Schedule Definition:** Misconfiguring the `CRON` expression or not accounting for the correct timezone (UTC vs. local) can lead to warehouses suspending during peak hours or remaining active overnight.
2.  **Insufficient Privileges for Tasks:** The role executing the `CREATE TASK` and `ALTER WAREHOUSE` commands might not have the necessary `OPERATE` or `MONITOR` privileges on the target warehouses, leading to task failures.
3.  **Forgetting to Resume Tasks:** After creation, tasks are typically in a `SUSPENDED` state. Developers often forget the `ALTER TASK ... RESUME` command, leading to the automation not running.
4.  **No Monitoring/Alerting for Task Failures:** Assuming the script will always work without setting up monitoring for task failures means you might not realize warehouses are failing to suspend or resume until unexpected cost increases or performance issues arise.

## Expected Output

The script itself doesn't produce a visible "output" in the traditional sense of a query returning data. Instead, its "output" is a change in the state of the Snowflake virtual warehouses and a corresponding impact on billing.

When the stored procedure `MANAGE_WAREHOUSE_STATE` is called (either manually or by a task), it returns a string message.

**Example Table (from calling the procedure directly):**

| result |
| :----- |
| Warehouse ANALYTICS_WH suspended. |
| Warehouse ANALYTICS_WH resumed. |

**Explanation of the columns:**

-   **result:** This column would contain the confirmation message returned by the `MANAGE_WAREHOUSE_STATE` procedure, indicating whether the specified warehouse was successfully suspended or resumed. The actual effect is visible in the Snowflake UI or by querying `SHOW WAREHOUSES;` to see the warehouse status (`STARTED` or `SUSPENDED`).
