# Auto-Suspend for Partner Connect Integrations in Snowflake

**Description:**
This feature automatically suspends virtual warehouses used exclusively by Partner Connect integrations to manage costs and optimize resource utilization.

## Short Explanation
**Overview:** Automatically pauses compute resources for Partner Connect integrations to prevent unnecessary costs.

**Problem Solved:** Incurring unnecessary compute costs for idle virtual warehouses provisioned for Snowflake Partner Connect integrations.

**Importance:** Crucial for cost management and optimizing resource allocation when integrating Snowflake with external analytics, ETL, or business intelligence tools.

**Use Cases:**
- Dedicated Partner Connect Warehouses
- Infrequent Integration Usage
- Cost Optimization for Development/Test Environments

### Typical Scenario
A new Fivetran connector is set up, requiring a dedicated warehouse. Configure AUTO_SUSPEND to a low value like 60 seconds.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE MY_PARTNER_CONNECT_WH WITH WAREHOUSE_SIZE = 'XSMALL' AUTO_SUSPEND = 60 AUTO_RESUME = TRUE INITIALLY_SUSPENDED = TRUE COMMENT = 'Warehouse for Partner Connect integration. Auto-suspend enabled for cost management.';
```

### Example Execution Logic Step-by-Step
The provided SQL creates a new virtual warehouse named MY_PARTNER_CONNECT_WH with auto-suspend enabled.

### Implementation Example
The implementation example is provided in the source document under the 'Implementation Example' section.

### Explanation of the Code
- CREATE WAREHOUSE MY_PARTNER_CONNECT_WH: Creates a new virtual warehouse.
- WITH WAREHOUSE_SIZE = 'XSMALL': Specifies the size of the warehouse.
- AUTO_SUSPEND = 60: Sets the inactivity period after which the warehouse will automatically suspend.
- AUTO_RESUME = TRUE: Ensures that the warehouse automatically resumes when a new query is submitted.
- INITIALLY_SUSPENDED = TRUE: The warehouse will be created in a suspended state.

### When to Use
- Dedicated Partner Connect Warehouses
- Infrequent Integration Usage
- Cost Optimization for Development/Test Environments

### When Not to Use
- Warehouses for Constant Streaming or Real-time ETL
- Performance-Critical Interactive Dashboards
- Warehouses for Stored Procedures with Long Idle Times within Transactions

### Common Mistakes
- Setting AUTO_SUSPEND too high for idle warehouses
- Not setting AUTO_RESUME = TRUE
- Using a single, general-purpose warehouse for all Partner Connect integrations
- Forgetting to define a role for the Partner Connect integration

### Expected Output
**Description:** The feature doesn't produce a dataset as output. Instead, its effect is observed in the behavior and cost of the virtual warehouse.

**Schema:**
- WAREHOUSE_NAME
- STATE
- AUTO_SUSPEND

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*