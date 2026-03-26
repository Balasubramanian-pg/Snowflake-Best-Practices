# Table Design Patterns: Wide vs Narrow in Snowflake

**Description:**
This topic discusses the trade-offs between wide and narrow table design patterns in Snowflake, a cloud-based data warehousing platform. A wide table design combines many columns into a single table, while a narrow table design uses multiple tables with fewer columns each. The choice of design pattern affects data storage, querying, and maintenance.

## Short Explanation
**Overview:** Wide and narrow table design patterns in Snowflake impact data modeling, query performance, and storage.

**Problem Solved:** Choosing the right table design pattern helps optimize data storage, querying, and maintenance in Snowflake.

**Importance:** Proper table design is crucial for efficient data analysis and reduces storage costs in Snowflake.

**Use Cases:**
- Real-time analytics
- Data warehousing
- Data integration

### Typical Scenario
A company wants to store customer information, orders, and order details in Snowflake. They must decide between a wide table design with all columns in one table or a narrow design with separate tables for customers, orders, and order details.

### Core Snowflake SQL Objects Used
`CREATE TABLE`

### Code Source Execution
```sql
CREATE TABLE customers (id INT, name VARCHAR, email VARCHAR);
CREATE TABLE orders (id INT, customer_id INT, order_date DATE);
CREATE TABLE order_details (order_id INT, product_id INT, quantity INT);
```

### Example Execution Logic Step-by-Step
In a wide table design, all columns are stored in a single table:
CREATE TABLE wide_table (id INT, name VARCHAR, email VARCHAR, order_date DATE, product_id INT, quantity INT);
In a narrow table design, separate tables are created:
CREATE TABLE customers (id INT, name VARCHAR, email VARCHAR);
CREATE TABLE orders (id INT, customer_id INT, order_date DATE);
CREATE TABLE order_details (order_id INT, product_id INT, quantity INT);

### Implementation Example
CREATE TABLE customers (id INT, name VARCHAR, email VARCHAR);
CREATE TABLE orders (id INT, customer_id INT, order_date DATE);
CREATE TABLE order_details (order_id INT, product_id INT, quantity INT);
INSERT INTO customers (id, name, email) VALUES (1, 'John Doe', 'john.doe@example.com');
INSERT INTO orders (id, customer_id, order_date) VALUES (1, 1, '2022-01-01');
INSERT INTO order_details (order_id, product_id, quantity) VALUES (1, 1, 2);

### Explanation of the Code
- Step 1: Create tables for customers, orders, and order details.
- Step 2: Insert sample data into each table.
- Step 3: Query the data using joins to retrieve related information.

### When to Use
- Use a wide table design when:
- Use a narrow table design when:

### When Not to Use
- Avoid wide tables when data is sparse or mostly null.
- Avoid narrow tables when data is highly correlated and frequently queried together.

### Common Mistakes
- Over-normalization leading to complex queries.
- Under-normalization leading to data redundancy and inconsistencies.

### Expected Output
**Description:** The result set will contain customer information, order details, and product information.

**Schema:**
- id
- name
- email
- order_date
- product_id
- quantity

---
*Auto-enriched via Snowflake Studio | meta-llama/llama-4-scout-17b-16e-instruct*