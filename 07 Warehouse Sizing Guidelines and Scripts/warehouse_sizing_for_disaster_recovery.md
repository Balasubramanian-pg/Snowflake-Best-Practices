# Warehouse Sizing for Disaster Recovery in Snowflake

**Description:**
This topic provides guidelines and best practices for sizing a Snowflake warehouse to ensure disaster recovery. It covers the importance of warehouse sizing, use cases, and provides a realistic SQL implementation.

## Short Explanation
**Overview:** Warehouse sizing for disaster recovery in Snowflake involves determining the optimal warehouse size to ensure business continuity in the event of a disaster.

**Problem Solved:** Ensures that critical workloads can continue uninterrupted in the event of a disaster, minimizing data loss and downtime.

**Importance:** Crucial for organizations that require high availability and disaster recovery capabilities for their Snowflake workloads.

**Use Cases:**
- Disaster recovery planning
- Business continuity planning
- Warehouse optimization for high availability

### Typical Scenario
A financial services organization needs to ensure that their Snowflake warehouse can handle critical workloads during a disaster, such as a data center outage. They need to size their warehouse to handle the expected workload and ensure business continuity.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE dr_wh WITH WAREHOUSE_SIZE = 'MEDIUM' AND AUTO_SUSPEND = 60 AND AUTO_RESUME = 60;
```

### Example Execution Logic Step-by-Step
The SQL code creates a new warehouse named 'dr_wh' with a medium size, auto-suspend and auto-resume features enabled. This ensures that the warehouse is only active when needed, reducing costs.

### Implementation Example
To implement warehouse sizing for disaster recovery, create a new warehouse with the required specifications and monitor its performance during a disaster scenario.

### Explanation of the Code
- Step 1: Determine the required warehouse size based on workload and performance requirements.
- Step 2: Create a new warehouse with the required specifications using the CREATE WAREHOUSE statement.

### When to Use
- During disaster recovery planning and business continuity planning
- When optimizing warehouse performance for high availability

### When Not to Use
- For non-critical workloads
- When cost is a major concern and auto-suspend and auto-resume features are not feasible

### Common Mistakes
- Underestimating the required warehouse size, leading to performance issues during a disaster
- Overestimating the required warehouse size, leading to increased costs

### Expected Output
**Description:** The output will be a list of warehouses with their specifications, including size, auto-suspend and auto-resume features.

**Schema:**
- name
- warehouse_size
- auto_suspend
- auto_resume

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*