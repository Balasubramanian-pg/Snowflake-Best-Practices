# Using Resource Monitors to Force Suspension in Snowflake

**Description:**
This topic explains how to use Resource Monitors in Snowflake to manage and control credit consumption by virtual warehouses, preventing unexpected cost overruns and ensuring efficient resource allocation.

## Short Explanation
**Overview:** Resource Monitors in Snowflake are powerful tools designed to manage and control credit consumption by virtual warehouses.

**Problem Solved:** It solves the problem of runaway credit consumption and unexpected billing costs by automating the enforcement of usage limits.

**Importance:** It provides financial governance and prevents a single query or team from consuming all available compute resources, impacting other workloads.

**Use Cases:**
- Cost Control for Specific Departments/Projects
- Preventing Runaway Queries
- Managing Non-Production Environments

### Typical Scenario
A data science team has a monthly budget of 5000 credits for their analytical queries. A monthly monitor is set to suspend their dedicated warehouse when 90% of credits are consumed, giving them a warning, and suspend immediately at 100%.

### Core Snowflake SQL Objects Used
`CREATE RESOURCE MONITOR`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
```sql
CREATE RESOURCE MONITOR my_daily_monitor
  WITH CREDIT_QUOTA = 1000
  FREQUENCY = DAILY
  START_TIMESTAMP = '2026-03-14 00:00:00 -0500' 
  TRIGGERS
    ON 90 PERCENT DO SUSPEND_IMMEDIATE
    ON 100 PERCENT DO SUSPEND;

ALTER WAREHOUSE my_warehouse SET RESOURCE_MONITOR = my_daily_monitor;

SHOW RESOURCE MONITORS;

SHOW WAREHOUSES;
```
```

### Example Execution Logic Step-by-Step
This example demonstrates the creation and assignment of a Resource Monitor to enforce credit limits and trigger suspension actions.

### Implementation Example
```sql
-- Step 1: Create a Resource Monitor
CREATE RESOURCE MONITOR my_daily_monitor
  WITH CREDIT_QUOTA = 1000
  FREQUENCY = DAILY
  START_TIMESTAMP = '2026-03-14 00:00:00 -0500' 
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

### Explanation of the Code
- ```sql
CREATE RESOURCE MONITOR my_daily_monitor
  WITH CREDIT_QUOTA = 1000
  FREQUENCY = DAILY
  START_TIMESTAMP = '2026-03-14 00:00:00 -0500' 
  TRIGGERS
    ON 90 PERCENT DO SUSPEND_IMMEDIATE
    ON 100 PERCENT DO SUSPEND;
```
- ```sql
ALTER WAREHOUSE my_warehouse SET RESOURCE_MONITOR = my_daily_monitor;
```
- ```sql
SHOW RESOURCE MONITORS;
```
- ```sql
SHOW WAREHOUSES;
```

### When to Use
- Cost Control for Specific Departments/Projects: Assign a monitor with a specific credit quota to warehouses used by particular teams or projects to ensure they stay within their allocated budget.
- Preventing Runaway Queries: Automatically suspend a warehouse if a single, inefficient query or workload consumes an abnormal amount of credits, preventing further costs.
- Managing Non-Production Environments: Set strict credit limits on development or testing warehouses, which typically have lower budget allowances, to prevent accidental overspending.

### When Not to Use
- When High Availability and Uninterrupted Processing are Critical: Applying `SUSPEND_IMMEDIATE` on mission-critical warehouses can abruptly stop essential services.
- For Temporary Spikes in Legitimate Workload: If a warehouse experiences predictable, but intense, periods of high usage (e.g., end-of-quarter reporting), an overly restrictive monitor could hinder legitimate operations.
- When Granular Cost Attribution is Needed Within a Single Warehouse: Resource monitors track aggregate credit usage for assigned warehouses, not individual user or query consumption.

### Common Mistakes
- Setting Triggers Too Aggressively: Using `SUSPEND_IMMEDIATE` at a low percentage (e.g., 50%) for critical warehouses can lead to frequent, unnecessary interruptions.
- Not Assigning the Monitor to a Warehouse: Creating a Resource Monitor but forgetting to link it to any virtual warehouses makes it ineffective.
- Incorrect `START_TIMESTAMP` or `FREQUENCY`: Misaligning the monitor's start time or frequency with your billing cycle or expected usage patterns can lead to confusing credit resets or premature suspensions.
- Forgetting About Multi-Cluster Warehouses: A Resource Monitor applies to the entire multi-cluster warehouse. If individual clusters have varying needs, a single monitor might not be flexible enough.

### Expected Output
**Description:** The expected output after creating and assigning a resource monitor isn't a direct query result in the traditional sense, but rather a change in how your Snowflake environment behaves regarding credit consumption and warehouse state.

**Schema:**
- name
- credit_quota
- frequency
- start_timestamp
- end_timestamp
- notify_at
- suspend_at
- suspend_immediately_at
- level
- warehouses
- owner
- comment

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*