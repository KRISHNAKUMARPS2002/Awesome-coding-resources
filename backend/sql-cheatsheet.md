# SQL USEFULL QUERIES CHEAT SHEET

🛠 DATABASE OPERATIONS

-- Create a new database
CREATE DATABASE db_name;

-- Use a database
USE db_name;

-- Show all databases
SHOW DATABASES;

-- Delete a database
DROP DATABASE db_name;
🧱 TABLE OPERATIONS
sql
Copy
Edit
-- Create a table
CREATE TABLE users (
id INT PRIMARY KEY AUTO_INCREMENT,
name VARCHAR(100),
email VARCHAR(100),
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Show all tables
SHOW TABLES;

-- Describe table structure
DESCRIBE users;

-- Drop a table
DROP TABLE users;

-- Rename a table
RENAME TABLE old_name TO new_name;

-- Add a column
ALTER TABLE users ADD age INT;

-- Modify a column
ALTER TABLE users MODIFY age VARCHAR(10);

-- Drop a column
ALTER TABLE users DROP COLUMN age;

📥 INSERT DATA

-- Insert one row
INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com');

-- Insert multiple rows
INSERT INTO users (name, email) VALUES
('Alice', 'alice@example.com'),
('Bob', 'bob@example.com');

📤 SELECT DATA

-- Select all columns
SELECT \* FROM users;

-- Select specific columns
SELECT name, email FROM users;

-- With WHERE condition
SELECT \* FROM users WHERE name = 'Alice';

-- Pattern matching
SELECT \* FROM users WHERE name LIKE '%oh%';

-- Order by column
SELECT \* FROM users ORDER BY name ASC;

-- Limit results
SELECT \* FROM users LIMIT 10;

-- Select unique values
SELECT DISTINCT city FROM users;

✏️ UPDATE DATA

-- Update a record
UPDATE users SET name = 'Jane Doe' WHERE id = 1;

-- Update multiple fields
UPDATE users SET name = 'Jane', age = 25 WHERE email = 'jane@example.com';

❌ DELETE DATA

-- Delete specific record
DELETE FROM users WHERE id = 5;

-- Delete all records (use with caution)
DELETE FROM users;

🔗 JOINS
-- INNER JOIN
SELECT orders.id, users.name
FROM orders
INNER JOIN users ON orders.user_id = users.id;

-- LEFT JOIN
SELECT users.name, orders.product
FROM users
LEFT JOIN orders ON users.id = orders.user_id;

-- RIGHT JOIN
SELECT users.name, orders.product
FROM users
RIGHT JOIN orders ON users.id = orders.user_id;

📊 GROUPING & AGGREGATION

-- Count
SELECT COUNT(\*) FROM users;

-- Sum
SELECT SUM(amount) FROM payments;

-- Average
SELECT AVG(age) FROM users;

-- Min / Max
SELECT MIN(age), MAX(age) FROM users;

-- Group by with count
SELECT city, COUNT(\*) FROM users GROUP BY city;

-- Group by with condition (HAVING)
SELECT city, COUNT(\_) as total FROM users GROUP BY city HAVING total > 5;

🔍 ADVANCED FILTERING

-- IN
SELECT \_ FROM users WHERE city IN ('Mumbai', 'Delhi');

-- BETWEEN
SELECT \* FROM orders WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';

-- IS NULL / IS NOT NULL
SELECT \* FROM users WHERE phone IS NULL;

-- EXISTS
SELECT \_ FROM users WHERE EXISTS (
SELECT 1 FROM orders WHERE users.id = orders.user_id
);

🧪 SUBQUERIES

-- Subquery in SELECT
SELECT name, (SELECT COUNT(\_) FROM orders WHERE user_id = users.id) AS total_orders
FROM users;

-- Subquery in WHERE
SELECT name FROM users WHERE id IN (
SELECT user_id FROM orders WHERE amount > 500
);

🧰 INDEXING & PERFORMANCE

-- Create an index
CREATE INDEX idx_email ON users(email);

-- Drop an index
DROP INDEX idx_email ON users;

🔒 USER & PRIVILEGES (MySQL)

-- Create a new user
CREATE USER 'dev'@'localhost' IDENTIFIED BY 'password';

-- Grant privileges
GRANT ALL PRIVILEGES ON db_name.\* TO 'dev'@'localhost';

-- Apply changes
FLUSH PRIVILEGES;
