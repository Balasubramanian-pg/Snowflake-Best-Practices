# Preventing Auto-Suspend During Critical Data Loads in Snowflake

**Description:**
This concept addresses a crucial operational challenge in Snowflake: ensuring that warehouses (compute clusters) remain active and available during vital data loading processes.

## Short Explanation
**Overview:** This idea is all about keeping your Snowflake virtual warehouses awake when they're busy with important data loads.

**Problem Solved:** It prevents delays or failures in data loading processes that occur if a warehouse auto-suspends in the middle of a critical operation.

**Importance:** It ensures consistent performance and reliability for ETL/ELT pipelines, especially when dealing with large volumes of data or strict SLAs.

**Use Cases:**
- In environments with scheduled data ingestion jobs
- In batch processing or any scenario where a continuous compute resource is required for a specific duration

### Typical Scenario
A nightly batch job that ingests and transforms several terabytes of data from various sources, typically running for 3 hours.

### Core Snowflake SQL Objects Used
`ALTER WAREHOUSE`

### Code Source Execution
```sql
ALTER WAREHOUSE my_data_load_wh SET AUTO_SUSPEND = 3600; -- Set auto-suspend to 1 hour (3600 seconds) for specific periods

-- OR, for critical, always-on loads during specific windows:
ALTER WAREHOUSE my_critical_load_wh SET AUTO_SUSPEND = NULL; -- Disable auto-suspend entirely
ALTER WAREHOUSE my_critical_load_wh SET AUTO_SUSPEND = 600;  -- Re-enable auto-suspend later, e.g., 10 minutes
```

### Example Execution Logic Step-by-Step
This code snippet uses the `ALTER WAREHOUSE` DDL command to modify the auto-suspend behavior of a Snowflake virtual warehouse.

### Implementation Example
While there isn't a direct "SQL feature" to prevent auto-suspend, the prevention is achieved through warehouse configuration, which is managed via DDL (Data Definition Language) SQL commands.

### Explanation of the Code
- - `ALTER WAREHOUSE my_data_load_wh`: This specifies that we are modifying the configuration of a warehouse named `my_data_load_wh`.
- - `SET AUTO_SUSPEND = 3600;`: This clause sets the `AUTO_SUSPEND` parameter. It defines the idle time in seconds after which the warehouse will automatically suspend. Here, 3600 seconds means 1 hour.
- - `SET AUTO_SUSPEND = NULL;`: This clause completely disables the auto-suspend feature for the `my_critical_load_wh` warehouse.
- - `SET AUTO_SUSPEND = 600;`: This re-enables auto-suspend for `my_critical_load_wh` with an idle timeout of 600 seconds (10 minutes) after the critical load period is over.

### When to Use
- Long-Running ETL/ELT Jobs: When you have batch data loads that might take hours to complete
- Scheduled Data Ingestion: For continuous or near real-time data streams that require the warehouse to be consistently available during specific windows
- Critical Analytical Workloads: If specific analytical queries or reports are expected to run for extended periods and must complete without interruption

### When Not to Use
- Interactive Query Workloads: For ad-hoc queries where users submit queries sporadically
- Small, Infrequent Batch Jobs: If your data loads are quick (e.g., a few minutes) and occur infrequently throughout the day
- To "Warm Up" a Warehouse Indefinitely: While disabling auto-suspend keeps a warehouse "warm," it's not a sustainable or cost-effective strategy for general performance.

### Common Mistakes
- Forgetting to Re-enable Auto-Suspend: Disabling `AUTO_SUSPEND` for a critical load and then forgetting to set it back to a reasonable value afterward
- Setting `AUTO_SUSPEND` Too Low for Long Loads: Setting the auto-suspend time to, for example, 5 minutes when a data load consistently takes 30 minutes
- Over-relying on Auto-Suspend for All Workloads: Not distinguishing between critical, long-running processes and interactive, ad-hoc queries

### Expected Output
**Description:** There is no direct "output" in the sense of a dataset from modifying warehouse parameters. The "output" is a change in the warehouse's behavior and state.

**Schema:**
- name
- state
- type
- size
- running
- queued
- is_default
- is_current
- auto_suspend
- auto_resume
- owner

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*