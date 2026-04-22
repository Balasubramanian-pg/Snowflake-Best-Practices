# Dynamic Auto-Suspend - Business Hours vs Off-Hours in Snowflake

**Description:**
This concept refers to dynamically adjusting the AUTO_SUSPEND setting for Snowflake virtual warehouses based on expected usage patterns, specifically differentiating between business hours and off-hours.

## Short Explanation
**Overview:** Dynamic auto-suspend means changing how quickly your Snowflake warehouse pauses itself depending on the time of day or week.

**Problem Solved:** Excessive Snowflake credit consumption due to idle warehouses.

**Importance:** It's crucial for cost optimization in cloud data warehousing, especially with Snowflake's per-second billing model.

**Use Cases:**
- Business intelligence dashboards heavily used during working hours but rarely overnight
- ETL processes that run during specific windows

### Typical Scenario
Organizations with predictable workload patterns, such as business intelligence dashboards or ETL processes.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
ALTER WAREHOUSE ANALYTICS_WH SET AUTO_SUSPEND = 300; ALTER WAREHOUSE ANALYTICS_WH SET AUTO_SUSPEND = 60;
```

### Example Execution Logic Step-by-Step
The provided SQL snippets use the ALTER WAREHOUSE command to modify the AUTO_SUSPEND property of a virtual warehouse.

### Implementation Example
Using an external scheduler (e.g., Snowflake Tasks, custom scripts, cloud functions) to run ALTER WAREHOUSE commands at scheduled times.

### Explanation of the Code
- ALTER WAREHOUSE ANALYTICS_WH SET AUTO_SUSPEND = 300; // Keep active for 5 minutes of inactivity during business hours
- ALTER WAREHOUSE ANALYTICS_WH SET AUTO_SUSPEND = 60; // Suspend after 1 minute of inactivity during off-hours

### When to Use
- Workloads with predictable peak and off-peak hours
- ETL/ELT processes with defined execution windows
- Cost-sensitive environments with variable usage

### When Not to Use
- Warehouses with highly unpredictable or constant 24/7 workloads
- When very low latency is critical regardless of usage patterns
- Overly short suspend intervals for interactive workloads

### Common Mistakes
- Forgetting to set AUTO_RESUME = TRUE
- Setting AUTO_SUSPEND too low for interactive workloads
- Not having an orchestration mechanism

### Expected Output
**Description:** The output is an altered state of the Snowflake virtual warehouse, which can be verified using SHOW WAREHOUSES.

**Schema:**
- name
- state
- type
- size
- min_cluster
- max_cluster
- started_on
- ended_on
- auto_suspend
- auto_resume

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*