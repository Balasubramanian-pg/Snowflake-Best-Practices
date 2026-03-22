# Auditing Changes to Auto-Suspend Configurations

**Description:**
This topic explains how to track and manage changes to auto-suspend configurations for virtual warehouses in Snowflake, ensuring cost-effective resource utilization.

## Short Explanation
**Overview:** Auto-suspend configurations in Snowflake allow for the automatic suspension of virtual warehouses after a specified period of inactivity, helping to manage costs.

**Problem Solved:** The feature solves the problem of incurring unnecessary compute costs for idle virtual warehouses provisioned specifically for Snowflake Partner Connect integrations.

**Importance:** It's crucial for cost management and optimizing resource allocation, especially when integrating Snowflake with a variety of external analytics, ETL, or business intelligence tools via Partner Connect.

**Use Cases:**
- Dedicated Partner Connect Warehouses
- Infrequent Integration Usage
- Cost Optimization for Development/Test Environments

### Typical Scenario
A company uses Snowflake's Partner Connect to integrate with various third-party tools and wants to ensure that the virtual warehouses used by these integrations are not running continuously and incurring unnecessary costs.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
-- no snippet mapped
```

### Example Execution Logic Step-by-Step


### Implementation Example


### Explanation of the Code


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
**Description:** The expected output of this feature is observed in the behavior and cost of the virtual warehouse, with the warehouse automatically transitioning between 'Started' and 'Suspended' states based on activity.

**Schema:**


---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*