# Auto-Suspend for Fivetran and Matillion Loads in Snowflake

**Description:**
This concept refers to configuring Snowflake virtual warehouses to automatically suspend after a period of inactivity, specifically when used by data integration tools like Fivetran and Matillion, to optimize costs.

## Short Explanation
**Overview:** Auto-suspend for Fivetran and Matillion loads is about setting up Snowflake warehouses to automatically shut down when not in use by these connectors.

**Problem Solved:** It solves the problem of incurring costs for idle compute resources in Snowflake, especially for workloads that are intermittent, like batch data loads.

**Importance:** It's important for cost optimization and resource management, ensuring that users only pay for the compute they actively use.

**Use Cases:**
- Batch data loads with Fivetran or Matillion
- Transformation workloads with Matillion
- Cost optimization for development/test environments

### Typical Scenario
A company uses Fivetran to load data into Snowflake every 3 hours. They configure auto-suspend to ensure the warehouse is only active during the loading process.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE FIVETRAN_MATILLION_WH WITH WAREHOUSE_SIZE = 'MEDIUM' AUTO_SUSPEND = 60 AUTO_RESUME = TRUE INITIALLY_SUSPENDED = TRUE COMMENT = 'Warehouse for Fivetran and Matillion data loads, auto-suspends to save costs.'; ALTER WAREHOUSE FIVETRAN_MATILLION_WH SET AUTO_SUSPEND = 300;
```

### Example Execution Logic Step-by-Step
The SQL snippet demonstrates creating and altering a virtual warehouse in Snowflake, focusing on the AUTO_SUSPEND parameter to automatically suspend the warehouse after a period of inactivity.

### Implementation Example
To implement auto-suspend, create a warehouse with the desired settings and modify it as needed.

### Explanation of the Code
- CREATE WAREHOUSE FIVETRAN_MATILLION_WH initiates the creation of a new virtual warehouse.
- WITH WAREHOUSE_SIZE = 'MEDIUM' specifies the initial size of the compute cluster.
- AUTO_SUSPEND = 60 tells Snowflake to automatically suspend this warehouse if it has been idle for 60 seconds.
- AUTO_RESUME = TRUE ensures that when a query or task is sent to a suspended warehouse, Snowflake automatically resumes it.
- INITIALLY_SUSPENDED = TRUE sets the initial state of the warehouse to suspended.
- ALTER WAREHOUSE FIVETRAN_MATILLION_WH SET AUTO_SUSPEND = 300 modifies the auto-suspend period to 300 seconds.

### When to Use
- Batch data loads with Fivetran or Matillion
- Transformation workloads with Matillion
- Cost optimization for development/test environments

### When Not to Use
- Continuous data streaming/real-time workloads
- Workloads with very short, frequent bursts
- User-facing analytics dashboards with low latency requirements

### Common Mistakes
- Setting AUTO_SUSPEND too high
- Setting AUTO_SUSPEND too low for frequent, short tasks
- Forgetting AUTO_RESUME = TRUE
- Using a shared warehouse with different workloads

### Expected Output
**Description:** The expected outcome is that the specified warehouse will transition between an 'active' and 'suspended' state based on the auto-suspend and auto-resume settings.

**Schema:**
- NAME
- STATE
- TYPE
- SIZE
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*