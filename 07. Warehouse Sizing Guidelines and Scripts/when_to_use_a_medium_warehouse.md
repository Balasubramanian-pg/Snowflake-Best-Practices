# When to Use a Medium Warehouse in Snowflake

**Description:**
This topic explains the scenarios where a medium warehouse is suitable in Snowflake, including data processing and analytics use cases. A medium warehouse provides a balance between performance and cost. It's essential to understand when to use a medium warehouse to optimize your Snowflake environment. This guide provides best practices and examples for using a medium warehouse.

## Short Explanation
**Overview:** A medium warehouse in Snowflake is a compute resource that provides a balance between performance and cost.

**Problem Solved:** It solves the problem of choosing the right warehouse size for data processing and analytics workloads.

**Importance:** It matters in analytics or warehousing because it helps optimize performance and cost.

**Use Cases:**
- Data processing and analytics
- Data transformation and loading

### Typical Scenario
A company needs to process and analyze large datasets on a regular basis, but doesn't require the highest level of performance. They have multiple users and groups accessing the data, and need to ensure that the warehouse can handle concurrent queries.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE medium_warehouse
  WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 60
  AUTO_RESUME = 10
```

### Example Execution Logic Step-by-Step
To create a medium warehouse, you can use the CREATE WAREHOUSE statement with the WAREHOUSE_SIZE parameter set to 'MEDIUM'. You can also configure auto-suspend and auto-resume to optimize performance and cost.

### Implementation Example
CREATE WAREHOUSE medium_warehouse
  WAREHOUSE_SIZE = 'MEDIUM'
  AUTO_SUSPEND = 60
  AUTO_RESUME = 10;

ALTER ACCOUNT SET DEFAULT_WAREHOUSE = 'medium_warehouse';

### Explanation of the Code
- Step 1: Create a medium warehouse with the CREATE WAREHOUSE statement.
- Step 2: Configure auto-suspend and auto-resume to optimize performance and cost.
- Step 3: Set the medium warehouse as the default warehouse for the account.

### When to Use
- Use a medium warehouse when you need to process and analyze large datasets on a regular basis.
- Use a medium warehouse when you have multiple users and groups accessing the data concurrently.

### When Not to Use
- Don't use a medium warehouse for very small datasets or low-traffic workloads.
- Don't use a medium warehouse for real-time data processing or high-priority workloads that require high performance.

### Common Mistakes
- Mistake 1: Using a medium warehouse for very small datasets or low-traffic workloads, which can lead to over-provisioning and unnecessary costs.
- Mistake 2: Not configuring auto-suspend and auto-resume, which can lead to wasted resources and increased costs.

### Expected Output
**Description:** The output will be a summary of the warehouse configuration, including the warehouse size, auto-suspend, and auto-resume settings.

**Schema:**
- WAREHOUSE_NAME
- WAREHOUSE_SIZE
- AUTO_SUSPEND
- AUTO_RESUME

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*