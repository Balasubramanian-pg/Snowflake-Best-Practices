# Alerting on Auto-Suspend Configuration Drifts for Fivetran and Matillion Loads in Snowflake

**Description:**
This concept refers to the practice of configuring Snowflake virtual warehouses to automatically suspend after a period of inactivity, specifically when used by data integration tools like Fivetran and Matillion.

## Short Explanation
**Overview:** Auto-suspend for Fivetran and Matillion loads is about setting up Snowflake warehouses to automatically shut down when not in use by these connectors.

**Problem Solved:** It solves the problem of incurring costs for idle compute resources in Snowflake, especially for workloads that are intermittent, like batch data loads.

**Importance:** It's important for cost optimization and resource management, ensuring that users only pay for the compute they actively use.

**Use Cases:**
- Batch Data Loads
- Transformation Workloads
- Cost Optimization for Development/Test Environments

### Typical Scenario
Your Fivetran connectors sync data from various sources into Snowflake every 3 hours. The warehouse should be active only for the duration of these syncs.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
```sql
CREATE WAREHOUSE FIVETRAN_MATILLION_WH
  WITH WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 60 -- Suspend after 60 seconds of inactivity
  AUTO_RESUME = TRUE
  INITIALLY_SUSPENDED = TRUE
  COMMENT = 'Warehouse for Fivetran and Matillion data loads, auto-suspends to save costs.';

ALTER WAREHOUSE FIVETRAN_MATILLION_WH SET AUTO_SUSPEND = 300; -- Change auto-suspend to 5 minutes
```
```

### Example Execution Logic Step-by-Step
This SQL snippet demonstrates how to create and alter a virtual warehouse in Snowflake, focusing on the AUTO_SUSPEND parameter.

### Implementation Example
While auto-suspend is a warehouse setting and not a direct SQL query *executed* by Fivetran/Matillion during a load, the configuration itself is done via SQL.

### Explanation of the Code
- CREATE WAREHOUSE FIVETRAN_MATILLION_WH: This statement initiates the creation of a new virtual warehouse named FIVETRAN_MATILLION_WH.
- WITH WAREHOUSE_SIZE = 'MEDIUM': This clause specifies the initial size of the compute cluster.
- AUTO_SUSPEND = 60: This is the key clause for cost optimization.
- AUTO_RESUME = TRUE: This ensures that when a query or task is sent to a suspended warehouse, Snowflake automatically resumes it.
- INITIALLY_SUSPENDED = TRUE: This setting ensures the warehouse is created in a suspended state, preventing immediate credit consumption.

### When to Use
- Batch Data Loads: When Fivetran or Matillion are performing scheduled, intermittent data loads.
- Transformation Workloads: For Matillion jobs that run on a schedule to perform ETL/ELT transformations.
- Cost Optimization for Development/Test Environments: In non-production environments where Fivetran/Matillion might be used for testing or ad-hoc loads.

### When Not to Use
- Continuous Data Streaming/Real-time Workloads: If Fivetran or Matillion were handling a continuous stream of data requiring near real-time processing.
- Workloads with Very Short, Frequent Bursts: If the integration tool triggers the warehouse for very short bursts.
- User-Facing Analytics Dashboards with Low Latency Requirements: If the warehouse is also used for serving dashboards or interactive queries.

### Common Mistakes
- Setting AUTO_SUSPEND too high: If the AUTO_SUSPEND value is set for too long.
- Setting AUTO_SUSPEND too low for frequent, short tasks: While less common, if a warehouse is used for very frequent, but short, tasks.
- Forgetting AUTO_RESUME = TRUE: If AUTO_RESUME is set to FALSE.
- Using a shared warehouse with different workloads: Assigning a single warehouse with an aggressive auto-suspend policy to both batch loads and interactive queries.

### Expected Output
**Description:** The expected outcome is that the specified warehouse will transition between an "active" and "suspended" state.

**Schema:**
- NAME
- STATE
- TYPE
- SIZE
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*