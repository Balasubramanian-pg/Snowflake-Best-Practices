# When to Use Auto-Scale Mode for Bursty Workloads in Snowflake

**Description:**
This topic explains the benefits and use cases of using Auto-Scale Mode in Snowflake for handling bursty workloads. It provides guidance on when to leverage this feature to optimize performance and cost. Auto-Scale Mode allows Snowflake to automatically adjust the warehouse size based on the workload, making it suitable for unpredictable or spiky workloads.

## Short Explanation
**Overview:** Auto-Scale Mode in Snowflake allows for automatic adjustment of warehouse size based on workload demands.

**Problem Solved:** It solves the problem of handling unpredictable or bursty workloads without manual intervention.

**Importance:** This feature is important for optimizing performance and cost in analytics and warehousing scenarios with variable workloads.

**Use Cases:**
- Handling sudden spikes in data ingestion or query workloads
- Supporting variable business intelligence and reporting needs
- Managing data processing and analytics for IoT or social media data

### Typical Scenario
A retail company experiences a significant increase in website traffic and sales during holiday seasons, resulting in a bursty workload for their Snowflake warehouse. They use Auto-Scale Mode to ensure their warehouse can handle the increased load without manual intervention.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
-- no snippet mapped
```

### Example Execution Logic Step-by-Step


### Implementation Example


### Explanation of the Code


### When to Use
- When you have unpredictable or spiky workloads
- When you want to optimize performance and cost
- When you need to handle sudden changes in data volume or query complexity

### When Not to Use
- When you have consistent and predictable workloads
- When you require fine-grained control over warehouse size and scaling
- When you are optimizing for cost and do not need automatic scaling

### Common Mistakes
- Not monitoring warehouse performance and adjusting Auto-Scale Mode settings
- Using Auto-Scale Mode with workloads that have strict performance requirements
- Not considering the potential impact on costs

### Expected Output
**Description:** The expected output is a well-performing and cost-optimized Snowflake warehouse that can handle bursty workloads without manual intervention.

**Schema:**


---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*