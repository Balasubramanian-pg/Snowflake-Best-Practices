# Monitoring Data Retention Performance in Snowflake

**Description:**
This topic provides a script to monitor data retention performance in Snowflake, ensuring that data is retained for the required period while optimizing storage and performance.

## Short Explanation
**Overview:** Snowflake's data retention policy determines how long data is kept in a table before it is automatically deleted.

**Problem Solved:** Monitoring data retention performance helps ensure that data is retained for the required period, and storage is optimized.

**Importance:** Proper data retention is crucial for compliance, auditing, and historical analysis.

**Use Cases:**
- Monitoring data retention for compliance
- Optimizing storage costs

### Typical Scenario
A company wants to ensure that sales data is retained for at least 365 days for auditing purposes while optimizing storage costs.

### Core Snowflake SQL Objects Used
`CREATE TABLE`, `ALTER TABLE`

### Code Source Execution
```sql
CREATE OR REPLACE PROCEDURE monitor_data_retention()
RETURNS VARIANT
LANGUAGE JAVASCRIPT
EXECUTE AS CALLER
BEGIN
    var sql = `SELECT 
            table_name, 
            retention_period_in_days, 
            data_retention_start_time
        FROM 
            information_schema.tables
        WHERE 
            table_type = 'BASE TABLE'`;
    var stmt = snowflake.createStatement({sql: sql});
    var rs = stmt.execute();
    var rows = [];
    while (rs.next()) {
        rows.push({
            table_name: rs.getColumnValue(1),
            retention_period_in_days: rs.getColumnValue(2),
            data_retention_start_time: rs.getColumnValue(3)
        });
    }
    return rows;
END;
```

### Example Execution Logic Step-by-Step
The provided JavaScript stored procedure, `monitor_data_retention`, queries the `information_schema.tables` view to retrieve the table name, retention period in days, and data retention start time for all base tables. This helps monitor data retention performance and identify tables that may require adjustments.

### Implementation Example
To implement this script, create a new stored procedure in Snowflake and execute it to retrieve the data retention information.

### Explanation of the Code
- Step 1: Create a JavaScript stored procedure named `monitor_data_retention`.
- Step 2: Use the `information_schema.tables` view to retrieve the required data retention information.

### When to Use
- When monitoring data retention for compliance purposes
- When optimizing storage costs

### When Not to Use
- When data retention policies are not a concern
- When using external storage solutions

### Common Mistakes
- Not considering data retention periods when designing tables
- Not regularly monitoring data retention performance

### Expected Output
**Description:** The output will be a list of objects containing the table name, retention period in days, and data retention start time.

**Schema:**
- table_name
- retention_period_in_days
- data_retention_start_time

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*