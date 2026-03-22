# Script to Automate Day-Night Suspend Settings in Snowflake

**Description:**
This script automates the suspension and resumption of Snowflake warehouses based on a predefined schedule to optimize costs.

## Short Explanation
**Overview:** Automates warehouse suspension and resumption based on time-of-day to optimize costs.

**Problem Solved:** Incurring costs for idle Snowflake virtual warehouses during off-peak hours.

**Importance:** Cost optimization and resource management in cloud data warehousing.

**Use Cases:**
- Data warehousing environments with predictable usage patterns
- Data lakes and analytics platforms with scheduled workloads

### Typical Scenario
A business intelligence team runs daily reports and ad-hoc queries only during standard business hours, and the script suspends warehouses overnight and on weekends.

### Core Snowflake SQL Objects Used
`CREATE PROCEDURE`, `CREATE TASK`

### Code Source Execution
```sql
CREATE OR REPLACE PROCEDURE MANAGE_WAREHOUSE_STATE(warehouse_name VARCHAR, action VARCHAR) ...
```

### Example Execution Logic Step-by-Step
The script uses a stored procedure and scheduled tasks to manage warehouse state.

### Implementation Example
The code snippet outlines creating a stored procedure and tasks to suspend and resume a warehouse daily.

### Explanation of the Code
- Step 1: Create a stored procedure to manage warehouse state
- Step 2: Create tasks to call the procedure at specific times
- Step 3: Enable the tasks

### When to Use
- Cost optimization for predictable workloads
- Resource management for development/test environments
- Tiered service level agreements (SLAs)

### When Not to Use
- 24/7 uninterrupted workloads
- Unpredictable or ad-hoc workloads with high latency sensitivity
- Workloads with high auto-suspend settings

### Common Mistakes
- Incorrect timezone/schedule definition
- Insufficient privileges for tasks
- Forgetting to resume tasks
- No monitoring/alerting for task failures

### Expected Output
**Description:** The script returns a confirmation message indicating whether the warehouse was successfully suspended or resumed.

**Schema:**
- result

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*