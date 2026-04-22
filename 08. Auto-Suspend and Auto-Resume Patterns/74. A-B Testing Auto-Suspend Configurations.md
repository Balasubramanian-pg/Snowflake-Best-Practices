# A-B Testing Auto-Suspend Configurations Using Snowflake Resource Monitors

**Description:**
This document explains how to implement auto-suspend configurations in Snowflake using Resource Monitors for cost control and efficient resource management.

## Short Explanation
**Overview:** Resource Monitors in Snowflake help manage and control credit consumption by virtual warehouses, allowing administrators to define thresholds and actions such as suspending a warehouse.

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
- Step 1: Create a Resource Monitor
- Step 2: Assign the Resource Monitor to a Virtual Warehouse

### When to Use
- Cost Control for Specific Departments/Projects
- Preventing Runaway Queries
- Managing Non-Production Environments

### When Not to Use
- When High Availability and Uninterrupted Processing are Critical
- For Temporary Spikes in Legitimate Workload
- When Granular Cost Attribution is Needed Within a Single Warehouse

### Common Mistakes
- Setting Triggers Too Aggressively
- Not Assigning the Monitor to a Warehouse
- Incorrect `START_TIMESTAMP` or `FREQUENCY`
- Forgetting About Multi-Cluster Warehouses

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