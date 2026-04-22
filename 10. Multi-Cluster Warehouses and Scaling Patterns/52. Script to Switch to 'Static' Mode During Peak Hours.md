# Switching to Static Mode During Peak Hours for Multi-Cluster Warehouses

**Description:**
This topic discusses a script to switch a multi-cluster warehouse to 'static' mode during peak hours to ensure predictable performance and cost. The script helps manage warehouse resources efficiently during high-demand periods. By switching to static mode, organizations can maintain consistent query performance and avoid unexpected costs.

## Short Explanation
**Overview:** Switching a multi-cluster warehouse to static mode during peak hours ensures predictable performance and cost.

**Problem Solved:** Unpredictable performance and cost during peak hours

**Importance:** Ensures consistent query performance and cost management in analytics and warehousing

**Use Cases:**
- E-commerce peak shopping hours
- Financial close and reporting periods

### Typical Scenario
An e-commerce company experiences high traffic during holiday sales, resulting in increased query loads on their Snowflake warehouse. To ensure predictable performance, they switch their multi-cluster warehouse to static mode during peak hours.

### Core Snowflake SQL Objects Used


### Code Source Execution
```sql
ALTER WAREHOUSE my_warehouse SET MODE = 'STATIC' MIN_CLUSTERS = 2 MAX_CLUSTERS = 5;
```

### Example Execution Logic Step-by-Step
The script switches a multi-cluster warehouse to static mode by setting the minimum and maximum number of clusters. This ensures that the warehouse uses a fixed number of clusters during peak hours, providing predictable performance and cost.

### Implementation Example
CREATE OR REPLACE PROCEDURE switch_to_static_mode()
  RETURNS STRING
  LANGUAGE JAVASCRIPT
  EXECUTE AS CALLER
  BEGIN
    var sql = `ALTER WAREHOUSE my_warehouse SET MODE = 'STATIC' MIN_CLUSTERS = 2 MAX_CLUSTERS = 5;`;
    var stmt = snowflake.createStatement({sql: sql});
    var result = stmt.execute();
    return 'Static mode activated';
  END;
  CALL switch_to_static_mode();

### Explanation of the Code
- Step 1: Create a stored procedure to switch the warehouse to static mode
- Step 2: Use the ALTER WAREHOUSE command to set the mode to 'STATIC' and specify the minimum and maximum number of clusters

### When to Use
- During peak hours or high-demand periods
- When predictable performance and cost are critical

### When Not to Use
- During low-traffic periods when auto-scaling is beneficial
- When the warehouse is not under heavy load

### Common Mistakes
- Not setting the correct minimum and maximum cluster sizes
- Not testing the script before executing it in production

### Expected Output
**Description:** The result of the script is a confirmation message indicating that static mode has been activated

**Schema:**


---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*