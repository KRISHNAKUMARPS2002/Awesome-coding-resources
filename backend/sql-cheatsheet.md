# SQL USEFUL QUERIES CHEAT SHEET

## 📁 Database Operations

### 🏗️ To create a database

```sql
CREATE DATABASE db_name;
```

### 📂 To use a database

```sql
USE db_name;
```

### 🧱 To create a table

```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(100),
    city VARCHAR(100)
)
```

### 📃 To list available tables

```sql
SELECT * FROM SYS.SYSTABLE;
```

### 📅 To select a table

```sql
SELECT * FROM users;
```

### 🔢 Count rows

````sql
SELECT COUNT(*) FROM users;
```
### Grouping

```sql
SELECT city, COUNT(*) FROM users GROUP BY city;
```

### 🔍 Apply WHERE clause

```sql
SELECT * FROM users WHERE city = 'TVM';
````

### 💾 Filter with conditions

```sql
SELECT * FROM orders WHERE order_date BETWEEN 'YYYY-MM-DD' AND 'YYYY-MM-DD';
```

```sql
SELECT * FROM orders WHERE order_date >= 'YYYY-MM-DD' AND order_date <= 'YYYY-MM-DD';
```

#### To check fields start with a letter

```sql
SELECT * FROM users WHERE name LIKE 'A%';
```

#### To check flieds end with a letter

```sql
SELECT * FROM users WHERE name LIKE '%n';
```

#### To check fields the letter anywhere in the data

```sql
SELECT * FROM users WHERE name LIKE '%great%';
```
