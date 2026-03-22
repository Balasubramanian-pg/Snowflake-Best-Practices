# Global Best Practices for Auto-Suspend Timeouts in Snowflake

**Description:**
This document outlines best practices for configuring auto-suspend timeouts in Snowflake to optimize costs and performance. It explains how auto-suspend works, its benefits, and provides guidance on when and how to use it effectively.

## Short Explanation
**Overview:** Auto-suspend is a Snowflake feature that automatically pauses a virtual warehouse after a specified period of inactivity to prevent unnecessary credit consumption.

**Problem Solved:** It solves the problem of idle compute costs by stopping a virtual warehouse when no queries are being processed for a set duration.

**Importance:** It's vital for cost governance and optimizing cloud expenditure, ensuring resources are only paid for when actively used.

**Use Cases:**
- Development and testing environments
- Ad-hoc analysis workloads
- Cost-sensitive environments

### Typical Scenario
A data scientist runs a few queries, then switches to another task for 10 minutes. The warehouse suspends, saving costs during the idle period.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`, `SHOW WAREHOUSES`

### Code Source Execution
```sql
ALTER WAREHOUSE MY_ANALYTICS_WH SET AUTO_SUSPEND = 60; SHOW WAREHOUSES LIKE 'MY_ANALYTICS_WH';
```

### Example Execution Logic Step-by-Step
The example demonstrates how to set the auto-suspend timeout for a warehouse named 'MY_ANALYTICS_WH' to 60 seconds and verify the setting.

### Implementation Example
To implement auto-suspend, use the ALTER WAREHOUSE command to set the AUTO_SUSPEND parameter. For example: ALTER WAREHOUSE MY_ANALYTICS_WH SET AUTO_SUSPEND = 60;

### Explanation of the Code
- The ALTER WAREHOUSE clause specifies that we are modifying the configuration of a virtual warehouse named 'MY_ANALYTICS_WH'.
- The SET AUTO_SUSPEND = 60 parameter sets the auto-suspend timeout to 60 seconds.
- The SHOW WAREHOUSES command is used to display information about the specified warehouse, including the auto-suspend setting.

### When to Use
- Development and testing warehouses with sporadic usage
- Ad-hoc analysis workloads with interactive analysis
- Cost-sensitive environments where credit consumption needs to be tightly controlled

### When Not to Use
- Workloads with extremely strict latency SLAs
- Workloads with short, frequent bursts of queries and high cache dependency
- Dedicated warehouses for specific, long-running applications

### Common Mistakes
- Leaving the default 10-minute auto-suspend
- Setting auto-suspend too low (e.g., < 60 seconds)
- Ignoring workload patterns and applying a one-size-fits-all auto-suspend setting

### Expected Output
**Description:** The expected output after successfully setting the AUTO_SUSPEND parameter and executing SHOW WAREHOUSES will be a table containing details about the specified warehouse.

**Schema:**
- name
- state
- type
- size
- min_cluster_count
- max_cluster_count
- started_clusters
- running
- queued
- auto_suspend
- auto_resume

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*