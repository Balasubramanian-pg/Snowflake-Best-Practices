# 1. Query Historical Data with `AT`

Explanation of the topic in 3 to 5 sentences.  
Time‑travel lets you query a table as it existed at a prior point in time. Using the `AT` clause you can specify a timestamp or a statement ID to view the data snapshot. This is useful for audits, debugging, or reproducing reports that were generated in the past. Snowflake retains data for the defined Time‑Travel retention period (default 1 day, up to 90 days).

## Implementation Example
```sql
-- Query the EMPLOYEES table as it existed on 2024‑02‑15 10:30 UTC
SELECT *
FROM EMPLOYEES AT (TIMESTAMP => TO_TIMESTAMP('2024-02-15 10:30:00'));

-- Alternative: use a statement ID instead of a timestamp
SELECT *
FROM EMPLOYEES AT (STATEMENT => '01a2b3c4-5678-90ab-cdef-1234567890ab');
```

## Explanation of the Code
- The `AT` clause tells Snowflake to retrieve a historical snapshot rather than the current state.  
- `TIMESTAMP => TO_TIMESTAMP('...')` converts the string to a Snowflake timestamp literal.  
- Snowflake internally maps the timestamp to the nearest micro‑second version of the table.  
- When using `STATEMENT`, you provide the unique ID of a previously executed query; Snowflake resolves the version from that ID.  
- The result set reflects all DML that had been committed before the specified point, excluding later changes.  
- No additional storage is consumed because Snowflake uses zero‑copy clones of the underlying micro‑partitions.  
- This query runs with the same performance characteristics as a regular SELECT, leveraging automatic pruning.

## When to use  
1. **Regulatory Audits** – Retrieve employee records as they existed on the audit date to prove compliance.  
2. **Debugging ETL Pipelines** – Re‑run a SELECT on the source data as it was before a transformation broke, to pinpoint the issue.

## When not to use  
1. **Real‑time Reporting** – If you need the most current data, time‑travel adds unnecessary overhead.  
2. **Large‑scale Historical Analytics** – Scanning many years of history can exceed the retention window and cause errors.

## Common Mistakes  
- Assuming the retention period is infinite; data older than the retention window is unavailable.  
- Mixing `AT` with `FOR SYSTEM_TIME AS OF` in the same query, which leads to syntax errors.  
- Forgetting to convert string timestamps to proper `TIMESTAMP` type, causing implicit conversion warnings.  
- Using a statement ID from a different session that does not have access privileges.

## Expected Output  
A result set identical in schema to the current `EMPLOYEES` table, but containing rows as they existed at the specified timestamp or statement. For example:

| EMP_ID | NAME      | DEPT   | SALARY |
|--------|-----------|--------|--------|
| 101    | Alice     | HR     | 75000 |
| 102    | Bob       | Finance| 82000 |
| …      | …         | …      | …      |

---

# 2. Restore a Table to a Point‑in‑Time

Explanation of the topic in 3 to 5 sentences.  
If a table is accidentally modified or deleted, you can restore it to a prior state using the `CREATE OR REPLACE TABLE … AT` syntax. This operation creates a new version of the table that mirrors the snapshot at the chosen timestamp or statement ID. The original table is replaced atomically, preserving downstream dependencies. Restoration is limited to the Time‑Travel retention period.

## Implementation Example
```sql
-- Restore the SALES table to its state on 2024‑03‑01 08:00 UTC
CREATE OR REPLACE TABLE SALES
AS SELECT * FROM SALES AT (TIMESTAMP => TO_TIMESTAMP('2024-03-01 08:00:00'));

-- Alternative: restore using a statement ID
CREATE OR REPLACE TABLE SALES
AS SELECT * FROM SALES AT (STATEMENT => '0f1e2d3c-4b5a-6789-abcd-ef0123456789');
```

## Explanation of the Code
- `CREATE OR REPLACE TABLE` drops the existing table metadata and creates a new one in a single transaction.  
- The `AS SELECT * FROM SALES AT (…)` clause pulls the historical snapshot as the source data for the new table.  
- Snowflake resolves the timestamp or statement ID to the exact micro‑partition version needed.  
- All constraints, clustering keys, and column definitions are inherited from the source snapshot.  
- Because the operation is atomic, any concurrent queries either see the old version or the fully restored version, never a partially restored state.  
- The command does not copy data physically; it re‑uses the same micro‑partitions, making the restore fast and storage‑efficient.  
- After the restore, the table’s `CREATED_ON` timestamp reflects the time of the restore, while the data reflects the historical point.

## When to use  
1. **Accidental Deletion** – A user dropped the `ORDERS` table; restore it to the state before the drop.  
2. **Erroneous Bulk Update** – A faulty UPDATE inflated prices; revert the table to the point before the update.

## When not to use  
1. **Permanent Data Loss** – If the required point is older than the retention window, restoration is impossible.  
2. **Schema Changes Required** – If you need to modify column types while restoring, use `CREATE TABLE … AS SELECT` with explicit casts instead.

## Common Mistakes  
- Restoring without verifying the timestamp, leading to an older-than‑expected dataset.  
- Forgetting that `CREATE OR REPLACE` also drops dependent objects like views that reference the table.  
- Assuming that the restored table retains the original `TABLE_ID`; it gets a new internal ID.  
- Overlooking that any streams on the table are reset, potentially causing data loss in CDC pipelines.

## Expected Output  
The `SALES` table now contains rows exactly as they were at the specified point. A sample view:

| SALE_ID | PRODUCT   | QUANTITY | SALE_DATE           |
|---------|-----------|----------|---------------------|
| 5001    | Widget A  | 10       | 2024‑02‑28 14:23:00 |
| 5002    | Gadget B  | 5        | 2024‑02‑28 15:10:00 |
| …       | …         | …        | …                   |

---

# 3. Clone a Table at a Specific Timestamp

Explanation of the topic in 3 to 5 sentences.  
Zero‑copy cloning creates a new table that points to the same underlying micro‑partitions as the source, without duplicating data. By adding an `AT` clause, you can clone the table as it existed at a particular point in time. The clone is independent for future DML, allowing safe experimentation on historical data. Cloning is instantaneous and cost‑effective because storage is shared until changes diverge.

## Implementation Example
```sql
-- Clone the INVENTORY table as it existed on 2024‑01‑31
CREATE TABLE INVENTORY_CLONE CLONE INVENTORY AT (TIMESTAMP => TO_TIMESTAMP('2024-01-31 23:59:59'));

-- Alternative: clone using a statement ID
CREATE TABLE INVENTORY_CLONE CLONE INVENTORY AT (STATEMENT => 'a1b2c3d4-5678-90ab-cdef-1234567890ab');
```

## Explanation of the Code
- `CREATE TABLE … CLONE` initiates a zero‑copy clone, referencing the source’s micro‑partitions.  
- Adding `AT (TIMESTAMP => …)` tells Snowflake to resolve the source version at that exact moment.  
- The clone inherits the source schema, clustering keys, and column definitions.  
- No data is physically copied; the clone’s metadata points to the same storage blocks, saving time and cost.  
- Subsequent DML on the clone creates new micro‑partitions, diverging from the source only where changes occur.  
- The clone can be dropped independently without affecting the original table.  
- The operation respects the source’s Time‑Travel retention; attempting to clone beyond that window fails.

## When to use  
1. **What‑If Analysis** – Create a snapshot of sales data before a promotion to model potential outcomes.  
2. **Testing Schema Changes** – Clone a production table at a known point, apply DDL, and validate without impacting live data.

## When not to use  
1. **Long‑Term Archival** – Clones still depend on the source’s retention; for permanent archives, use `COPY INTO` to external storage.  
2. **Cross‑Region Replication** – Zero‑copy clones are limited to the same database and region; use Snowflake Replication for multi‑region needs.

## Common Mistakes  
- Assuming the clone is a full physical copy; forgetting that storage costs accrue only when data diverges.  
- Cloning a table that is already dropped; the operation will fail.  
- Overlooking that the clone inherits the source’s data retention policy, which may cause unexpected data expiry.  
- Forgetting to grant privileges on the new clone; users may encounter “insufficient privileges” errors.

## Expected Output  
A new table `INVENTORY_CLONE` with the same columns as `INVENTORY`, containing the historical rows:

| ITEM_ID | DESCRIPTION | STOCK_QTY | LAST_RESTOCK       |
|---------|-------------|-----------|--------------------|
| 2001    | Widget X    | 150       | 2023‑12‑15 08:00:00|
| 2002    | Gadget Y    | 75        | 2024‑01‑10 12:30:00|
| …       | …           | …         | …                  |

---

# 4. Time‑Travel with Streams

Explanation of the topic in 3 to 5 sentences.  
Streams capture DML changes (INSERT, UPDATE, DELETE) on a source table, providing a change‑data‑capture (CDC) view. When combined with Time‑Travel, a stream can be created on a historical version of a table, allowing you to replay changes that occurred after that point. This is valuable for reconstructing data pipelines or re‑processing missed events. Streams are automatically refreshed as new commits occur.

## Implementation Example
```sql
-- Create a stream on the ORDERS table as of 2024‑02‑01
CREATE OR REPLACE STREAM ORDERS_STREAM_ON_20240201
ON TABLE ORDERS AT (TIMESTAMP => TO_TIMESTAMP('2024-02-01 00:00:00'));

-- Query the stream to see changes since that timestamp
SELECT *
FROM ORDERS_STREAM_ON_20240201;
```

## Explanation of the Code
- `CREATE OR REPLACE STREAM … ON TABLE … AT (…)` defines a stream that starts tracking changes from the specified historical version.  
- Snowflake materializes the stream as a virtual table exposing columns `METADATA$ACTION`, `METADATA$ROW_ID`, and the source columns.  
- The `AT` clause ensures the stream’s baseline is the snapshot at the given timestamp, not the current table state.  
- As new DML occurs, Snowflake appends change rows to the stream, which can be consumed by downstream processes.  
- The stream’s offset is stored internally; each query consumes only unprocessed rows, guaranteeing exactly‑once semantics.  
- If the source table’s retention expires, the stream becomes invalid and must be recreated.  
- Streams can be used in `INSERT … SELECT` pipelines to apply incremental loads to a target table.

## When to use  
1. **Re‑processing Missed Events** – After a downstream outage, replay changes from a known point to catch up.  
2. **Auditing Data Mutations** – Track how rows in a financial ledger evolve over time for compliance reporting.

## When not to use  
1. **Static Historical Snapshots** – If you only need a point‑in‑time view, a simple `AT` query is cheaper.  
2. **High‑Frequency Updates** – Streams add overhead; for millions of updates per second, consider external CDC tools.

## Common Mistakes  
- Forgetting to advance the stream offset, causing the same changes to be processed repeatedly.  
- Creating a stream on a table that will be dropped before the stream is consumed, leading to loss of change data.  
- Assuming the stream retains data beyond the source’s Time‑Travel window; it does not.  
- Neglecting to grant `SELECT` on the stream to the consuming role, resulting in permission errors.

## Expected Output  
A virtual table showing rows changed since 2024‑02‑01:

| METADATA$ACTION | ORDER_ID | CUSTOMER_ID | STATUS   | AMOUNT |
|-----------------|----------|-------------|----------|--------|
| INSERT          | 9001     | 300         | PENDING  | 125.00 |
| UPDATE          | 9002     | 301         | SHIPPED  | 200.00 |
| DELETE          | 9003     | 302         | CANCELED | 0.00   |
| …               | …        | …           | …        | …      |

---

# 5. Failback Using `UNDROP` for Tables

Explanation of the topic in 3 to 5 sentences.  
When a table is dropped, Snowflake retains a hidden “recycle bin” entry for the duration of the Time‑Travel retention period. The `UNDROP` command restores the dropped table to its original name, schema, and data state. This operation is instantaneous and does not require a full clone or data copy. It is only possible while the table’s metadata is still within the retention window.

## Implementation Example
```sql
-- Undrop the DEPARTMENTS table that was dropped yesterday
UNDROP TABLE DEPARTMENTS;

-- Verify the table is back
SELECT * FROM DEPARTMENTS LIMIT 5;
```

## Explanation of the Code
- `UNDROP TABLE` looks up the most recent dropped version of the specified table name.  
- Snowflake validates that the table’s metadata is still within the configured Time‑Travel retention.  
- The command restores the table’s definition, clustering, and all micro‑partitions to their pre‑drop state.  
- No data is copied; the operation simply re‑links the hidden metadata to the active namespace.  
- If a table with the same name already exists, `UNDROP` fails, requiring you to rename or drop the existing object first.  
- After undrop, any streams or tasks that referenced the table continue to function, as the internal `TABLE_ID` is preserved.  
- The restored table’s `CREATED_ON` timestamp reflects the original creation time, not the undrop time.

## When to use  
1. **Human Error Recovery** – A developer accidentally dropped a staging table; undrop restores it instantly.  
2. **Rollback of Temporary Cleanup** – A scheduled job removed temporary tables; undrop brings them back for debugging.

## When not to use  
1. **Beyond Retention Window** – If the table was dropped more than the retention period ago, undrop is impossible.  
2. **Name Conflict** – If another object now occupies the original name, you must rename or drop that object first.

## Common Mistakes  
- Assuming `UNDROP` works after the retention period; it will return “object not found”.  
- Forgetting to re‑grant privileges after undrop; the restored table inherits original grants, but new roles may need explicit rights.  
- Trying to undrop a table that was purged with `DROP TABLE ... PURGE`; such tables have no recycle entry.  
- Overlooking dependent objects (e.g., views) that may have become invalid after the drop and need recompilation.

## Expected Output  
The `DEPARTMENTS` table is fully restored and queryable:

| DEPT_ID | DEPT_NAME   | MANAGER_ID |
|---------|-------------|------------|
| 10      | Finance     | 2001       |
| 20      | Engineering | 2002       |
| …       | …           | …          |

---

# 6. Failback Using `UNDROP` for Databases

Explanation of the topic in 3 to 5 sentences.  
Snowflake also retains dropped databases for the Time‑Travel retention period, allowing you to