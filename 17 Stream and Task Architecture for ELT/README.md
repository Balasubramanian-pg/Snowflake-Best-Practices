# Database Indexing
Database indexing is a technique used to improve the speed of data retrieval operations by providing a quick way to locate specific data. Indexing creates a data structure that facilitates faster access to data, reducing the time it takes to execute queries. Indexing can be particularly useful for large datasets, where queries can take a significant amount of time to execute without indexing. By creating an index on a column or set of columns, the database can quickly locate the required data, improving overall system performance.

## Implementation Example
```sql
CREATE INDEX idx_employee_id ON employees (employee_id);
CREATE INDEX idx_employee_name ON employees (employee_name);
```
Few examples and alternatives of SQL Code:
- Creating a composite index: `CREATE INDEX idx_employee_id_name ON employees (employee_id, employee_name);`
- Creating a unique index: `CREATE UNIQUE INDEX idx_employee_id ON employees (employee_id);`

## Explanation of the Code
The code implements database indexing using the following steps:
* Identify the columns that are frequently used in WHERE, JOIN, and ORDER BY clauses, as these are the most likely candidates for indexing.
* Determine the type of index to create, such as a single-column index or a composite index.
* Use the CREATE INDEX statement to create the index, specifying the name of the index, the table, and the column(s) to be indexed.
* Consider creating a unique index if the column(s) being indexed contain unique values.
* Use the INDEX keyword to specify the index name and the column(s) to be indexed.
* Test the performance of the index by executing queries and monitoring the execution time.
* Consider reorganizing or rebuilding the index periodically to maintain optimal performance.

## When to use 
Database indexing is useful in the following scenarios:
- **Frequent filtering**: When a column is frequently used in the WHERE clause, an index can speed up the filtering process.
- **Join operations**: When joining two tables on a common column, an index on the join column can improve the performance of the join operation.
- **Sorting and grouping**: When a column is used in the ORDER BY or GROUP BY clause, an index can speed up the sorting and grouping process.

## When not to use 
Database indexing is not recommended in the following scenarios:
- **Small tables**: For small tables, the overhead of maintaining an index can outweigh the benefits of improved query performance.
- **Infrequently used columns**: If a column is rarely used in queries, the index may not provide significant performance benefits.
- **Frequently updated tables**: If a table is frequently updated, the index may need to be rebuilt or reorganized frequently, which can impact performance.

## Common Mistakes
* Creating an index on a column with low cardinality (i.e., few unique values) can lead to poor performance.
* Failing to maintain the index by reorganizing or rebuilding it periodically can lead to performance degradation.
* Creating too many indexes on a table can lead to increased storage requirements and slower write performance.
* Not considering the data distribution and query patterns when creating an index can lead to poor performance.

## Expected Output
The expected output of a query that uses an index will typically be faster execution times and improved performance. For example, consider a table `employees` with an index on the `employee_id` column:
| employee_id | employee_name |
| --- | --- |
| 1 | John Smith |
| 2 | Jane Doe |
| 3 | Bob Johnson |
If we execute a query that filters on the `employee_id` column, such as `SELECT * FROM employees WHERE employee_id = 1;`, the index will allow the database to quickly locate the required data, resulting in faster execution times. The output will be:
| employee_id | employee_name |
| --- | --- |
| 1 | John Smith |