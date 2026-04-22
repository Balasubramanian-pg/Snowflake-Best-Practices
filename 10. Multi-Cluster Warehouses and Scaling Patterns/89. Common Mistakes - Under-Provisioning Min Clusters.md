# Avoiding Common Mistakes with Under-Provisioning Min Clusters in Snowflake

**Description:**
This topic explains the importance of proper cluster provisioning in Snowflake, specifically the risks of under-provisioning minimum clusters. It provides best practices and examples to help users avoid common mistakes.

## Short Explanation
**Overview:** Under-provisioning minimum clusters can lead to performance issues and query queuing in Snowflake.

**Problem Solved:** Ensuring adequate cluster provisioning to meet query demands.

**Importance:** Proper cluster sizing is crucial for optimal performance and cost-effectiveness.

**Use Cases:**
- Real-time data ingestion and processing
- Large-scale data warehousing

### Typical Scenario
A business with fluctuating query workloads needs to ensure their Snowflake clusters are properly sized to handle peak demands.

### Core Snowflake SQL Objects Used
`CREATE WAREHOUSE`, `ALTER WAREHOUSE`

### Code Source Execution
```sql
CREATE WAREHOUSE my_warehouse
  WAREHOUSE_SIZE = 'X-LARGE'
  MIN_CLUSTER_NUMBER = 2
  MAX_CLUSTER_NUMBER = 4;
ALTER WAREHOUSE my_warehouse SET MIN_CLUSTER_NUMBER = 3;
```

### Example Execution Logic Step-by-Step
To avoid under-provisioning, start by assessing your query workload and expected growth. Create a warehouse with an initial minimum cluster number, then monitor and adjust as needed.

### Implementation Example
Create a warehouse with a minimum cluster number of 2 and a maximum cluster number of 4. Monitor query performance and adjust the minimum cluster number upwards if queuing occurs.

### Explanation of the Code
- Step 1: Create a warehouse with the desired properties.
- Step 2: Monitor performance and adjust the minimum cluster number.

### When to Use
- Fluctuating query workloads
- Large-scale data processing

### When Not to Use
- Steady-state workloads with predictable performance requirements

### Common Mistakes
- Under-provisioning minimum clusters
- Failing to monitor and adjust cluster sizes

### Expected Output
**Description:** Properly sized clusters with optimal performance and cost-effectiveness.

**Schema:**
- Cluster Name
- Status
- Number of Clusters

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*