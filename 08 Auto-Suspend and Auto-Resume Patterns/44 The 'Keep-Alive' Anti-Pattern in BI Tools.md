# The 'Keep-Alive' Anti-Pattern in BI Tools

**Description:**
This anti-pattern involves sending unnecessary, repetitive queries to Snowflake from BI tools to maintain a 'warm' cache or prevent connection timeouts, leading to wasted resources and potential performance issues.

## Short Explanation
**Overview:** The 'Keep-Alive' anti-pattern refers to the practice of repeatedly querying a table or view with trivial queries to keep data 'hot' or prevent connection drops.

**Problem Solved:** This anti-pattern doesn't solve a problem; it creates problems, often stemming from a misunderstanding of caching mechanisms or connection management.

**Importance:** It's crucial to understand this anti-pattern as it leads to wasted compute resources, increased costs, and can negatively impact performance for legitimate queries.

**Use Cases:**
- Avoiding cache misses or connection timeouts in BI tools

### Typical Scenario
BI dashboards configured to refresh frequently with trivial queries or scheduled jobs running 'SELECT 1' or 'SELECT COUNT(*)' statements against large tables.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
SELECT 1 FROM my_large_fact_table WHERE 1=0;
```

### Example Execution Logic Step-by-Step
A simple SELECT statement that attempts to retrieve data from a large fact table, but with a WHERE clause that always returns zero rows.

### Implementation Example
This query, while seemingly harmless, if run repeatedly and unnecessarily by a BI tool, exemplifies the 'Keep-Alive' anti-pattern.

### Explanation of the Code
- The SELECT 1 clause specifies that the query should return a constant value '1' for each row that satisfies the WHERE condition.
- The FROM my_large_fact_table clause indicates the table from which the data is being retrieved.
- The WHERE 1=0 clause filters the results, ensuring no rows are returned.

### When to Use
- Health checks (carefully)
- Testing connection strings

### When Not to Use
- To 'warm' the cache
- To prevent connection timeouts in BI tools
- As a substitute for proper indexing/optimization

### Common Mistakes
- Believing it improves performance
- Ignoring costs
- Overlooking contention
- Not configuring BI tool timeouts

### Expected Output
**Description:** A single result set containing one column and zero rows.

**Schema:**
- 1

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*