# Handling 'Spilling to Remote Storage' with Multi-Cluster Warehouses in Snowflake

**Description:**
This topic explains how to handle 'spilling to remote storage' when using Multi-Cluster Warehouses (MCW) in Snowflake. Spilling occurs when a query requires more memory than is available, and Snowflake spills the data to remote storage. This can impact performance and cost. The topic provides best practices and examples for optimizing MCW configurations to minimize spilling.

## Short Explanation
**Overview:** Optimizing Multi-Cluster Warehouses to minimize spilling to remote storage.

**Problem Solved:** Reduces performance impact and costs associated with spilling to remote storage.

**Importance:** Critical for large-scale data processing and analytics workloads.

**Use Cases:**
- Data warehousing
- Big data analytics
- Machine learning

### Typical Scenario
A company runs a data warehousing workload with large fact tables and complex queries, causing frequent spilling to remote storage. They need to optimize their MCW configuration to improve performance and reduce costs.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
-- no snippet mapped
```

### Example Execution Logic Step-by-Step


### Implementation Example


### Explanation of the Code


### When to Use
- Large-scale data processing
- Complex queries with high memory requirements

### When Not to Use
- Small-scale data processing
- Queries with low memory requirements

### Common Mistakes
- Underestimating memory requirements
- Not monitoring spilling metrics

### Expected Output
**Description:** Reduced spilling to remote storage, improved query performance, and optimized MCW costs.

**Schema:**


---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*