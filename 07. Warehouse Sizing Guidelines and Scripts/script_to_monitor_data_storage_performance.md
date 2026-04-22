# Monitoring Data Storage Performance in Snowflake

**Description:**
This topic provides a script to monitor data storage performance in Snowflake, helping users understand storage usage, data distribution, and potential optimization opportunities.

## Short Explanation
**Overview:** Monitoring data storage performance is crucial for optimizing Snowflake usage and cost.

**Problem Solved:** Identifying storage performance bottlenecks and optimization opportunities.

**Importance:** Ensures efficient data storage, reduces costs, and improves query performance.

**Use Cases:**
- Monitoring storage usage and data growth
- Identifying optimization opportunities for data storage

### Typical Scenario
A data engineer wants to monitor data storage performance in a Snowflake warehouse to optimize storage usage and reduce costs.

### Core Snowflake SQL Objects Used
`CREATE VIEW`

### Code Source Execution
```sql
CREATE OR REPLACE VIEW storage_performance AS
SELECT 
    database_name,
    schema_name,
    table_name,
    data_bytes,
    data_bytes_compressed,
    compression_ratio
FROM 
    TABLE(INFORMATION_SCHEMA.TABLE_STORAGE)
ORDER BY 
    data_bytes DESC;
```

### Example Execution Logic Step-by-Step
This script creates a view that retrieves storage performance metrics for all tables in the current database, including data bytes, compressed data bytes, and compression ratio.

### Implementation Example
To implement this script, simply execute the CREATE VIEW statement in Snowflake. You can then query the view like any other table.

### Explanation of the Code
- The script uses the INFORMATION_SCHEMA.TABLE_STORAGE table to retrieve storage performance metrics.
- The view includes columns for database name, schema name, table name, data bytes, compressed data bytes, and compression ratio.
- The results are ordered by data bytes in descending order to show the largest tables first.

### When to Use
- Monitoring storage usage and data growth
- Identifying optimization opportunities for data storage

### When Not to Use
- For real-time query performance monitoring
- For detailed query optimization

### Common Mistakes
- Not regularly updating the view to reflect changing storage performance
- Not using the view to inform data storage optimization decisions

### Expected Output
**Description:** The view returns a list of tables with their storage performance metrics.

**Schema:**
- database_name
- schema_name
- table_name
- data_bytes
- data_bytes_compressed
- compression_ratio

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*