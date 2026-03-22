# Auto-Suspend for Data Science Workloads (Jupyter-Python) in Snowflake

**Description:**
This topic explains how to use auto-suspend in Snowflake for data science workloads, particularly those run from Jupyter notebooks using Python. Auto-suspend helps optimize costs by automatically pausing a virtual warehouse after a specified period of inactivity.

## Short Explanation
**Overview:** Auto-suspend automatically turns off your Snowflake warehouse if it's not being actively used, saving you money on compute costs.

**Problem Solved:** It solves the problem of incurring unnecessary compute costs when data science workloads (or any workloads) are idle.

**Importance:** In analytics and data science, interactive sessions can be sporadic. Auto-suspend ensures that you only pay for the compute resources when queries are actually running.

**Use Cases:**
- Interactive Data Exploration and Jupyter Notebooks
- Development and Testing Environments
- Low-Frequency Batch Processing

### Typical Scenario
A data scientist is iterating on a Python script to clean data in Snowflake via a Jupyter notebook, running queries intermittently to check results.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`, `CREATE WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE DS_ANALYTICS_WH SET AUTO_SUSPEND = 600;

CREATE WAREHOUSE DS_DEV_WH
  WITH WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 300
  AUTO_RESUME = TRUE
  COMMENT = 'Warehouse for data science development with auto-suspend after 5 minutes';
```

### Example Execution Logic Step-by-Step
The SQL example demonstrates how to configure or create a Snowflake warehouse with auto-suspend functionality.

### Implementation Example
The implementation example shows how to set auto-suspend for an existing warehouse and create a new warehouse with auto-suspend.

### Explanation of the Code
- ALTER WAREHOUSE DS_ANALYTICS_WH: Targets a specific existing compute resource in Snowflake.
- SET AUTO_SUSPEND = 600: Defines the idle time threshold after which the warehouse will pause.
- CREATE WAREHOUSE DS_DEV_WH: Provisions a new compute cluster.
- WITH WAREHOUSE_SIZE = 'MEDIUM': Determines the processing power and cost per credit for the warehouse.
- AUTO_SUSPEND = 300: Configures the inactivity period for auto-suspension during creation.
- AUTO_RESUME = TRUE: Enables seamless resumption of operations when a workload needs compute again.

### When to Use
- Interactive Data Exploration and Jupyter Notebooks
- Development and Testing Environments
- Low-Frequency Batch Processing

### When Not to Use
- Critical Production Workloads with High Concurrency/Low Latency
- High-Frequency ETL/ELT Pipelines
- Warehouses Supporting Persistent Connections

### Common Mistakes
- Setting AUTO_SUSPEND too Low for Interactive Use
- Forgetting to Set AUTO_RESUME = TRUE
- Confusing AUTO_SUSPEND with STATEMENT_TIMEOUT_IN_SECONDS
- Using Auto-Suspend for Critical Production Warehouses

### Expected Output
**Description:** The ALTER WAREHOUSE and CREATE WAREHOUSE commands do not return a data set. Instead, they execute DDL (Data Definition Language) operations, modifying the state of your Snowflake account.

**Schema:**
- +------------------------------------------+
- | status                                   |
- +------------------------------------------+
- | Statement executed successfully.         |
- +------------------------------------------+

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*