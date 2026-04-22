# Auto-Suspend Strategies for UAT and Staging Environments in Snowflake

**Description:**
This concept explores various methods and configurations to automatically suspend Snowflake warehouses in User Acceptance Testing (UAT) and Staging environments to optimize cost by preventing idle compute resources from running unnecessarily.

## Short Explanation
**Overview:** Auto-suspend strategies for Snowflake warehouses automatically shut down compute resources after a specified period of inactivity.

**Problem Solved:** It solves the problem of incurring costs for idle warehouses, which is particularly relevant in non-production environments like UAT and Staging where usage patterns can be intermittent.

**Importance:** It's crucial for cost management, especially in cloud-based data platforms where compute usage is billed by the second or minute.

**Use Cases:**
- Non-Production Environments (UAT, Staging, Dev) with intermittent usage patterns
- Ad-Hoc Query Workloads for data analysts or scientists who run interactive queries
- Scheduled but Infrequent Tasks like nightly data loads into staging

### Typical Scenario
A UAT team tests a new feature for an hour in the morning, then doesn't use the warehouse again until the afternoon. Auto-suspend ensures the warehouse pauses during the mid-day lull.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`, `SHOW WAREHOUSES`

### Code Source Execution
```sql
```sql
-- Create a new warehouse for UAT with a 5-minute auto-suspend
CREATE WAREHOUSE UAT_WH
  WITH WAREHOUSE_SIZE = 'SMALL'
  AUTO_SUSPEND = 300 -- 300 seconds = 5 minutes
  AUTO_RESUME = TRUE
  COMMENT = 'Warehouse for UAT environment with aggressive auto-suspend for cost savings.';

-- Alter an existing Staging warehouse to have a 10-minute auto-suspend
ALTER WAREHOUSE STAGING_WH
  SET AUTO_SUSPEND = 600 -- 600 seconds = 10 minutes;

-- View current auto-suspend settings for all warehouses
SHOW WAREHOUSES LIKE '%_WH%';
```
```

### Example Execution Logic Step-by-Step
The SQL example demonstrates how to configure and view auto-suspend settings for Snowflake warehouses.

### Implementation Example
```sql
-- Create a new warehouse for UAT with a 5-minute auto-suspend
CREATE WAREHOUSE UAT_WH
  WITH WAREHOUSE_SIZE = 'SMALL'
  AUTO_SUSPEND = 300 -- 300 seconds = 5 minutes
  AUTO_RESUME = TRUE
  COMMENT = 'Warehouse for UAT environment with aggressive auto-suspend for cost savings.';

-- Alter an existing Staging warehouse to have a 10-minute auto-suspend
ALTER WAREHOUSE STAGING_WH
  SET AUTO_SUSPEND = 600 -- 600 seconds = 10 minutes;
```

### Explanation of the Code
- The `CREATE WAREHOUSE UAT_WH ...` statement is used to define and instantiate a new compute resource (warehouse) named `UAT_WH`.
- The `WITH WAREHOUSE_SIZE = 'SMALL'` specifies the initial size of the compute cluster for the warehouse.
- The `AUTO_SUSPEND = 300` parameter defines the inactivity period, in seconds, after which the warehouse will automatically suspend.
- The `AUTO_RESUME = TRUE` parameter ensures that the warehouse automatically resumes when a new query is submitted to it.
- The `ALTER WAREHOUSE STAGING_WH SET AUTO_SUSPEND = 600;` statement modifies an existing warehouse named `STAGING_WH`.

### When to Use
- Non-Production Environments (UAT, Staging, Dev) with intermittent usage patterns
- Ad-Hoc Query Workloads for data analysts or scientists who run interactive queries
- Scheduled but Infrequent Tasks like nightly data loads into staging

### When Not to Use
- Production Workloads with High Concurrency and Low Latency Requirements
- Long-Running ETL/ELT Jobs that Span Hours
- Environments with Very Frequent, Short Bursts of Activity

### Common Mistakes
- Setting `AUTO_SUSPEND = 0`, which means the warehouse will never automatically suspend
- Overlooking Auto-Resume Latency, which can lead to a poor user experience
- Inconsistent Auto-Suspend Settings Across Environments

### Expected Output
**Description:** The result set will be a table displaying metadata about your warehouses, including their current auto-suspend configuration.

**Schema:**
- name
- state
- type
- size
- auto_suspend

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*