# Designing for Low Throughput Long Queries in Snowflake

**Description:**
This topic discusses strategies for optimizing Snowflake performance when dealing with low-throughput, long-running queries. It provides best practices for designing and executing queries that require extended periods to complete. By implementing these strategies, users can minimize performance impacts and optimize resource utilization.

## Short Explanation
**Overview:** Optimizing low-throughput, long-running queries in Snowflake.

**Problem Solved:** Minimizing performance impacts of long-running queries on Snowflake resources.

**Importance:** Ensuring efficient resource utilization and query performance in analytics and warehousing workloads.

**Use Cases:**
- Data migrations or large data transformations
- Complex analytics or data science workloads

### Typical Scenario
A data analyst needs to perform a complex data transformation on a large dataset, which requires several hours to complete. The goal is to minimize the impact on other users and ensure efficient resource utilization.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE low_throughput_warehouse
  WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 60
  AUTO_RESUME = 60;
```

### Example Execution Logic Step-by-Step
Create a warehouse with a medium size and configure auto-suspend and auto-resume to optimize resource utilization for low-throughput, long-running queries.

### Implementation Example
CREATE WAREHOUSE low_throughput_warehouse
  WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 60
  AUTO_RESUME = 60;

ALTER SESSION SET WAREHOUSE = low_throughput_warehouse;

### Explanation of the Code
- Step 1: Create a warehouse with a medium size to allocate sufficient resources for the long-running query.
- Step 2: Configure auto-suspend and auto-resume to automatically suspend the warehouse after 60 minutes of inactivity and resume it when needed.

### When to Use
- Data migrations or large data transformations
- Complex analytics or data science workloads

### When Not to Use
- High-throughput, short-running queries
- Interactive querying or real-time analytics

### Common Mistakes
- Over-provisioning warehouse resources for low-throughput queries
- Failing to configure auto-suspend and auto-resume

### Expected Output
**Description:** The warehouse is created and configured for low-throughput, long-running queries.

**Schema:**
- WAREHOUSE_NAME
- WAREHOUSE_SIZE
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*