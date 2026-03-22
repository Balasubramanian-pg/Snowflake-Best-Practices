# Auto-Suspend Patterns for Marketing Analytics Tools in Snowflake

**Description:**
This concept refers to strategies and configurations for automatically suspending Snowflake warehouses when not actively used by marketing analytics tools, optimizing costs by preventing unnecessary compute consumption.

## Short Explanation
**Overview:** Implementing automatic shutdown rules for Snowflake compute resources tailored for marketing analytics workloads.

**Problem Solved:** Addresses financial inefficiency of leaving Snowflake compute warehouses running when no queries are executed.

**Importance:** Critical for cost management in cloud data platforms, ensuring organizations only pay for compute resources when actively processing data.

**Use Cases:**
- Marketing analytics tools with bursty, ad-hoc queries followed by periods of inactivity.
- Data platforms connected to business intelligence dashboards, ETL/ELT pipelines, and ad-hoc query tools.

### Typical Scenario
A marketing team runs a few dashboards in the morning, then leaves them idle for the rest of the day. A 300-second (5-minute) auto-suspend would save significant costs.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE MARKETING_ANALYTICS_WH SET AUTO_SUSPEND = 300;
ALTER WAREHOUSE ETL_LOAD_WH SET AUTO_SUSPEND = 60;
ALTER WAREHOUSE AD_HOC_QUERY_WH SET AUTO_SUSPEND = 900;
```

### Example Execution Logic Step-by-Step
Demonstrates setting the AUTO_SUSPEND parameter for different Snowflake warehouses to automatically suspend after a specified period of inactivity.

### Implementation Example
ALTER WAREHOUSE MARKETING_ANALYTICS_WH SET AUTO_SUSPEND = 300;
ALTER WAREHOUSE ETL_LOAD_WH SET AUTO_SUSPEND = 60;
ALTER WAREHOUSE AD_HOC_QUERY_WH SET AUTO_SUSPEND = 900;

### Explanation of the Code
- Specifies the particular warehouse to modify.
- Sets the AUTO_SUSPEND parameter to a specified number of seconds.
- Demonstrates different auto-suspend settings for various warehouses (MARKETING_ANALYTICS_WH, ETL_LOAD_WH, AD_HOC_QUERY_WH).

### When to Use
- Cost Optimization for Intermittent Workloads.
- Dedicated Warehouses for Specific Tools/Users.
- Balancing Cost and Performance for Reporting.

### When Not to Use
- Warehouses Handling Real-time or Mission-Critical Queries.
- Workloads with Very High Query Frequency and Short Pauses.
- Periods of Intensive, Continuous Data Loading or Transformation (ELT/ETL).

### Common Mistakes
- Setting AUTO_SUSPEND = 0 (or too high for interactive workloads).
- Not having enough warehouses with appropriate auto-suspend settings.
- Ignoring the impact of AUTO_RESUME.
- Misunderstanding the STATEMENT_QUEUED_TIMEOUT_IN_SECONDS parameter.

### Expected Output
**Description:** The ALTER WAREHOUSE commands do not produce a visible dataset. Instead, they execute successfully and return a message indicating the change was applied.

**Schema:**
- WAREHOUSE_NAME
- AUTO_SUSPEND_SECONDS

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*