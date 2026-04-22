# Role-Based Access Control for Changing Suspend Settings in Snowflake

**Description:**
This topic discusses how to implement role-based access control for changing suspend settings in Snowflake, ensuring that only authorized users can modify auto-suspend and auto-resume settings for virtual warehouses.

## Short Explanation
**Overview:** Auto-suspend in Snowflake refers to the automatic pausing of a virtual warehouse when it has been inactive for a specified period.

**Problem Solved:** It solves the problem of incurring unnecessary compute costs for inactive Snowflake virtual warehouses.

**Importance:** It's vital for cost management, ensuring resources are efficiently utilized and not left running idle, which is common in analytics where jobs run intermittently.

**Use Cases:**
- Scheduled dbt Runs
- Ad-hoc dbt Development
- Cost-Sensitive Environments

### Typical Scenario
A dbt project runs nightly to update reporting tables. The warehouse should be active only during this one-hour window.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE DBT_ANALYTICS_WH SET AUTO_SUSPEND = 300 AUTO_RESUME = TRUE;
```

### Example Execution Logic Step-by-Step
This SQL command modifies an existing virtual warehouse named DBT_ANALYTICS_WH to suspend after 300 seconds (5 minutes) of inactivity and automatically resume when a query is submitted.

### Implementation Example
To implement role-based access control, create a custom role with the necessary privileges to modify warehouse settings, then grant this role to authorized users.

### Explanation of the Code
- The ALTER WAREHOUSE statement initiates the modification of an existing virtual warehouse.
- The SET keyword indicates that one or more parameters for the warehouse are about to be specified or changed.
- AUTO_SUSPEND = 300 sets the inactivity period after which the warehouse will automatically suspend.
- AUTO_RESUME = TRUE ensures that if the warehouse is suspended and a new query is submitted, the warehouse will automatically start up again.

### When to Use
- Scheduled dbt Runs
- Ad-hoc dbt Development
- Cost-Sensitive Environments

### When Not to Use
- Continuously Active Workloads
- Very Large dbt Models with Frequent Retries/Debugging
- Environments with High Query Latency Sensitivity

### Common Mistakes
- Setting AUTO_SUSPEND too high
- Not setting AUTO_RESUME = TRUE
- Confusing AUTO_SUSPEND with STATEMENT_TIMEOUT_IN_SECONDS

### Expected Output
**Description:** The SHOW WAREHOUSES command returns the properties of the virtual warehouse, including the AUTO_SUSPEND and AUTO_RESUME values.

**Schema:**
- NAME
- STATE
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*