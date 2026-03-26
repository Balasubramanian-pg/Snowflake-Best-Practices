# Impact of Local Disk Cache on New Snowflake Clusters

**Description:**
This topic discusses the impact of local disk cache on the performance of new Snowflake clusters. Local disk cache can improve query performance by reducing the need for data retrieval from remote storage. However, it also has implications for cluster sizing, data freshness, and storage usage.

## Short Explanation
**Overview:** Local disk cache stores frequently accessed data on the cluster's local disks, reducing the need for remote storage access.

**Problem Solved:** Slow query performance due to remote storage access

**Importance:** Improved query performance and reduced latency

**Use Cases:**
- Real-time analytics
- High-performance data warehousing

### Typical Scenario
A company sets up a new Snowflake cluster for real-time analytics and wants to optimize query performance. They consider using local disk cache to improve performance.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
-- no snippet mapped
```

### Example Execution Logic Step-by-Step


### Implementation Example


### Explanation of the Code


### When to Use
- New clusters with high query concurrency
- Clusters with limited remote storage bandwidth

### When Not to Use
- Clusters with limited local disk storage
- Clusters with low query concurrency

### Common Mistakes
- Overestimating local disk cache benefits
- Not monitoring cache performance

### Expected Output
**Description:** Improved query performance and reduced latency

**Schema:**


---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*