# Using Resource Monitors to Force Suspension in Snowflake

Resource Monitors in Snowflake are powerful tools designed to manage and control credit consumption by virtual warehouses. They allow administrators to define thresholds and actions, such as suspending a warehouse, to prevent unexpected cost overruns and ensure efficient resource allocation. Their primary purpose is to help maintain budget adherence and operational stability by automatically reacting to usage patterns.

**Short Explanation**

Resource Monitors prevent excessive credit usage by automatically taking actions like suspending a virtual warehouse when predefined credit thresholds are met. This feature solves the problem of uncontrolled spending on compute resources, ensuring that database operations stay within budget. It's crucial in any Snowflake environment to manage costs effectively and is commonly used to govern warehouse usage in development, testing, and production environments.

- **What problem does this SQL feature solve?** It solves the problem of runaway credit consumption and unexpected billing costs by automating the enforcement of usage limits.
- **Why is it important in databases or analytics?** It provides financial governance and prevents a single query or team from consuming all available compute resources, impacting other workloads.
- **Where is it commonly used in real workflows?** It's used across all environments (dev, test, prod) to cap spending, prevent large data processing jobs from running indefinitely, and manage department-specific budgets.

## Implementation Example

```sql
-- Step 1: Create a Resource Monitor
CREATE RESOURCE MONITOR my_daily_monitor
  WITH CREDIT_QUOTA = 1000
  FREQUENCY = DAILY
  START_TIMESTAMP = '2026-03-14 00:00:00 -0500' -- Adjust timezone if necessary
  TRIGGERS
    ON 90 PERCENT DO SUSPEND_IMMEDIATE
    ON 100 PERCENT DO SUSPEND;

-- Step 2: Assign the Resource Monitor to a Virtual Warehouse
ALTER WAREHOUSE my_warehouse SET RESOURCE_MONITOR = my_daily_monitor;

-- Step 3: Check Resource Monitor status (Optional)
SHOW RESOURCE MONITORS;

-- Step 4: Check Warehouse status (Optional, to see if it's suspended)
SHOW WAREHOUSES;
```

## Explanation of the Code

This example demonstrates the creation and assignment of a Resource Monitor to enforce credit limits and trigger suspension actions.

- `CREATE RESOURCE MONITOR my_daily_monitor`: This command initializes a new Resource Monitor named `my_daily_monitor`.
- `WITH CREDIT_QUOTA = 1000`: This sets the total credit limit for the monitor to 1000 credits over its defined frequency.
- `FREQUENCY = DAILY`: Specifies that the credit quota resets daily. Other options include `WEEKLY`, `MONTHLY`, `YEARLY`, or `NEVER`.
- `START_TIMESTAMP = '2026-03-14 00:00:00 -0500'`: Defines when the monitoring period begins. This ensures consistent billing cycle alignment.
- `TRIGGERS`: This section defines the actions to be taken when certain credit usage thresholds are reached.
    - `ON 90 PERCENT DO SUSPEND_IMMEDIATE`: When 90% of the `CREDIT_QUOTA` is consumed, the associated virtual warehouse(s) will immediately suspend any running queries and then suspend the warehouse.
    - `ON 100 PERCENT DO SUSPEND`: When 100% of the `CREDIT_QUOTA` is consumed, the associated virtual warehouse(s) will wait for all running queries to complete before suspending the warehouse.
- `ALTER WAREHOUSE my_warehouse SET RESOURCE_MONITOR = my_daily_monitor;`: This command links the `my_daily_monitor` to the `my_warehouse` virtual warehouse, making it subject to the monitor's rules.
- `SHOW RESOURCE MONITORS;` and `SHOW WAREHOUSES;`: These are optional administrative commands to verify the creation and assignment of the monitor and the status of the warehouse, respectively.

## When to Use

1.  **Cost Control for Specific Departments/Projects:** Assign a monitor with a specific credit quota to warehouses used by particular teams or projects to ensure they stay within their allocated budget.
    *   *Scenario:* A data science team has a monthly budget of 5000 credits for their analytical queries. A monthly monitor is set to suspend their dedicated warehouse when 90% of credits are consumed, giving them a warning, and suspend immediately at 100%.
2.  **Preventing Runaway Queries:** Automatically suspend a warehouse if a single, inefficient query or workload consumes an abnormal amount of credits, preventing further costs.
    *   *Scenario:* A new ETL job is accidentally misconfigured and starts processing an entire dataset repeatedly. A monitor on the ETL warehouse detects unusually high credit consumption within an hour and suspends the warehouse, stopping the costly job.
3.  **Managing Non-Production Environments:** Set strict credit limits on development or testing warehouses, which typically have lower budget allowances, to prevent accidental overspending.
    *   *Scenario:* The development environment's warehouse has a daily quota of 100 credits. A monitor is configured to suspend it at 100% usage, ensuring that developers don't inadvertently incur high costs during testing.

## When Not to Use

1.  **When High Availability and Uninterrupted Processing are Critical:** Applying `SUSPEND_IMMEDIATE` on mission-critical warehouses can abruptly stop essential services.
    *   *Scenario:* An analytics dashboard relies on near real-time data from a dedicated warehouse. Using `SUSPEND_IMMEDIATE` could disrupt reporting and business operations, making `NOTIFY` a better choice.
2.  **For Temporary Spikes in Legitimate Workload:** If a warehouse experiences predictable, but intense, periods of high usage (e.g., end-of-quarter reporting), an overly restrictive monitor could hinder legitimate operations.
    *   *Scenario:* A finance team runs a large, credit-intensive report once a month. A daily or weekly monitor might suspend their warehouse prematurely, requiring manual intervention or adjustment.
3.  **When Granular Cost Attribution is Needed Within a Single Warehouse:** Resource monitors track aggregate credit usage for assigned warehouses, not individual user or query consumption.
    *   *Scenario:* You need to understand which specific users or queries within a shared warehouse are consuming the most credits. Resource monitors only tell you the total for the warehouse; querying `ACCOUNT_USAGE` views (e.g., `QUERY_HISTORY`) would be more appropriate.

## Common Mistakes

1.  **Setting Triggers Too Aggressively:** Using `SUSPEND_IMMEDIATE` at a low percentage (e.g., 50%) for critical warehouses can lead to frequent, unnecessary interruptions.
2.  **Not Assigning the Monitor to a Warehouse:** Creating a Resource Monitor but forgetting to link it to any virtual warehouses makes it ineffective.
3.  **Incorrect `START_TIMESTAMP` or `FREQUENCY`:** Misaligning the monitor's start time or frequency with your billing cycle or expected usage patterns can lead to confusing credit resets or premature suspensions.
4.  **Forgetting About Multi-Cluster Warehouses:** A Resource Monitor applies to the entire multi-cluster warehouse. If individual clusters have varying needs, a single monitor might not be flexible enough.

## Expected Output

The expected output after creating and assigning a resource monitor isn't a direct query result in the traditional sense, but rather a change in how your Snowflake environment behaves regarding credit consumption and warehouse state.

If you run `SHOW RESOURCE MONITORS;` you would see a table describing your monitors:

| name              | credit_quota | frequency | start_timestamp                   | end_timestamp | notify_at  | suspend_at | suspend_immediately_at | level | warehouses                       | owner       | comment |
| :---------------- | :----------- | :-------- | :-------------------------------- | :------------ | :--------- | :--------- | :--------------------- | :---- | :------------------------------- | :---------- | :------ |
| MY_DAILY_MONITOR  | 1000         | DAILY     | 2026-03-14 00:00:00.000 -0500     | NULL          | 90,100     | 100        | 90                     | WAREHOUSE | MY_WAREHOUSE                     | ACCOUNTADMIN |         |

-   **name**: The unique name given to the resource monitor (e.g., `MY_DAILY_MONITOR`).
-   **credit_quota**: The total credits allowed before actions are triggered (e.g., `1000`).
-   **frequency**: How often the credit quota resets (e.g., `DAILY`).
-   **start_timestamp**: When the monitoring period begins (e.g., `2026-03-14 00:00:00.000 -0500`).
-   **suspend_immediately_at**: The percentage at which the `SUSPEND_IMMEDIATE` action is triggered (e.g., `90`).
-   **suspend_at**: The percentage at which the `SUSPEND` action is triggered (e.g., `100`).
-   **warehouses**: A comma-separated list of warehouses controlled by this monitor (e.g., `MY_WAREHOUSE`).

If `MY_WAREHOUSE` consumes 900 credits, it will be immediately suspended. If it then reaches 1000 credits (if not already suspended or if the `SUSPEND_IMMEDIATE` action was not set), it would be suspended after current queries complete. The `SHOW WAREHOUSES;` command would then show `MY_WAREHOUSE` with a `STATE` of `SUSPENDED`.
