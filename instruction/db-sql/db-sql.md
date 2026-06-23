# Relational Databases - SQL

🖥️ [Slides](https://docs.google.com/presentation/d/1_kGyItbM3fp0PQSemvBocclT8EU87rBX)

🖥️ [Lecture Videos](#videos)

### 🔑 Key points

- How to create and drop tables
- Why `DROP TABLE IF EXISTS` is useful
- How to insert, update, delete, and retrieve data from a database table
- How to retrieve data from multiple related tables using a join
- What database transactions are and why they are necessary

---

**Structured Query Language** (SQL) is a programming language specifically designed to interact with relational databases. It includes statements for inserting, updating, reading, and deleting data. It also provides commands for managing the database itself and the users who have access to it.

SQL statements are commonly categorized into three main groups:

1.  **DDL** (Data Definition Language) – Used for defining structure: `CREATE`, `ALTER`, `DROP`.
2.  **DML** (Data Manipulation Language) – Used for managing data: `INSERT`, `UPDATE`, `DELETE`.
3.  **DQL** (Data Query Language) – Used for retrieving data: `SELECT`.

The following example demonstrates SQL statements used to create a database, create a table, and insert sample data.

```sql
CREATE DATABASE pet_store;
USE pet_store;

CREATE TABLE pet (
    id INT, 
    name VARCHAR(255), 
    type VARCHAR(255)
);

INSERT INTO pet VALUES (93, 'Fido', 'dog');
INSERT INTO pet VALUES (14, 'Puddles', 'cat');
INSERT INTO pet VALUES (77, 'Chip', 'bird');

DESCRIBE pet;
```

**Output of DESCRIBE:**
```text
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | int          | YES  |     | NULL    |       |
| name  | varchar(255) | YES  |     | NULL    |       |
| type  | varchar(255) | YES  |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
```

The following query retrieves specific columns for all pets in the database:

```sql
SELECT id, name, type FROM pet;
```

**Result:**
```text
+--------+----------+---------------+
| id     | name     | type          |
+--------+----------+---------------+
| 93     | Fido     | dog           |
| 14     | Puddles  | cat           |
| 77     | Chip     | bird          |
+--------+----------+---------------+
```

SQL is a **declarative** programming language. This means you declare *what* you want to achieve rather than providing a series of imperative commands that define *how* to do it.

## Working with Databases

A database server, or Relational Database Management System (RDBMS), can host multiple databases. You create a database with the `CREATE DATABASE` statement. After creation, use the `USE` statement to select it as the active database for subsequent commands.

```sql
CREATE DATABASE pet_store;
USE pet_store;
```

Databases use a default character set to represent bytes as text. While you can specify the character set during the `CREATE` request, you can also modify an existing database using the `ALTER DATABASE` statement. For example, to use UTF-8 (which supports a wide range of international characters and emojis), run:

```sql
ALTER DATABASE pet_store CHARACTER SET utf8mb4;
```

To delete a database and all its contained data, use the `DROP DATABASE` statement. While rarely used in production, this is common in development when resetting schemas.

```sql
DROP DATABASE pet_store;
```

## Working with Tables

Once a database is selected, you can create tables to hold rows of data. A table definition is conceptually similar to declaring a class in Java; you must specify each field (column) and its data type.

```sql
CREATE TABLE pet (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(255) NOT NULL,
    PRIMARY KEY (id)
);
```

In this example, each field is followed by the `NOT NULL` constraint, meaning a value must be provided for every row. 

The `id` field uses the `AUTO_INCREMENT` keyword. This allows the database to automatically assign a unique, increasing integer to each new row, so you don't have to provide an ID manually during insertion.

### Altering Tables

To modify an existing table structure, use `ALTER TABLE`. The following example adds a `nickname` column. Because this alteration does not include a `NOT NULL` clause, existing rows will have their `nickname` set to `NULL` by default.

```sql
ALTER TABLE pet ADD COLUMN nickname VARCHAR(255);
```

To delete a table entirely, use the `DROP TABLE` statement.

```sql
DROP TABLE pet;
```

**Note:** Use this command with caution; once a table is dropped, the data cannot be recovered.

Rerunning a `CREATE TABLE` statement will result in an error if the table already exists. You can use the `IF NOT EXISTS` qualifier to prevent this error. To change a table's definition, you must either `DROP` and recreate it or use `ALTER TABLE`.

### Primary Keys and Indexes

The `CREATE TABLE` statement specifies which field acts as the **primary key**. Primary keys must be unique for every row; attempting to insert a duplicate key will result in an error. 

Primary keys are **indexed** by default. Indexes significantly increase query performance by allowing the database to find rows without scanning the entire table. However, they consume additional storage and can slow down write operations (`INSERT`, `UPDATE`). You should only create indexes on columns that are frequently used in search criteria.

You can create indexes on other fields using the `INDEX` keyword or the `CREATE INDEX` statement:

```sql
-- Creating an index during table creation
CREATE TABLE pet (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    PRIMARY KEY (id),
    INDEX (name)
);

-- Adding an index to an existing table
CREATE INDEX index_name ON pet (name);
```

### Common SQL Data Types

| Data type | Description | Example |
| :--- | :--- | :--- |
| **INT** | Integer numbers. | 1, 10, -100 |
| **DECIMAL** | Fixed-point numbers (exact precision). | 1.23, 100.00 |
| **FLOAT** | Single-precision floating-point numbers. | 1.234567 |
| **DOUBLE** | Double-precision floating-point numbers. | 1.23456789012345 |
| **CHAR** | Fixed-length character strings. | 'A', 'USA' |
| **VARCHAR** | Variable-length character strings. | 'Fido', 'Hello World' |
| **TEXT** | Large variable-length text data. | (A long paragraph) |
| **BLOB** | Binary Large Object (for images/files). | (Binary data) |
| **DATE** | Date values (YYYY-MM-DD). | '2023-10-14' |
| **TIME** | Time values (HH:MM:SS). | '15:43:45' |
| **DATETIME** | Combined date and time values. | '2023-10-14 15:43:45' |

## Inserting, Updating, and Deleting Data

Once your table is defined, you can add data using the `INSERT` statement.

```sql
INSERT INTO pet (name, type) VALUES ('Puddles', 'cat');
```

To modify existing data, use the `UPDATE` statement. It is critical to include a `WHERE` clause; if you omit it, **every row** in the table will be updated.

```sql
UPDATE pet SET name = 'Fido' WHERE id = 1;
```

To remove rows, use the `DELETE` statement. Again, a `WHERE` clause is necessary to avoid deleting all data.

```sql
DELETE FROM pet WHERE type = 'cat';
```

If you want to delete **all** rows from a table while keeping its structure (and resetting auto-increment counters), use `TRUNCATE`.

```sql
TRUNCATE TABLE pet;
```

## Selecting Data

The `SELECT` statement is used to query data. A basic `SELECT` returns specific columns for every row:

```sql
SELECT name, type FROM pet;
```

You can filter results using `WHERE`, sort them with `ORDER BY`, and limit the result count with `LIMIT`.

```sql
SELECT name, type 
FROM pet 
WHERE type = 'dog' OR (type = 'cat' AND name = 'Puddles') 
ORDER BY name ASC
LIMIT 2;
```

Use `*` to select all columns or `COUNT()` to return the number of rows.

```sql
SELECT * FROM pet;
SELECT COUNT(*) FROM pet;
```

## Joining Tables

Relational databases often split data into multiple tables to reduce redundancy. To combine this data, use a `JOIN` clause. This relates rows from different tables based on a common column (usually a foreign key).

```sql
SELECT purchase.id AS purchaseId, purchase.ownerId, pet.id AS petId, pet.name, pet.type
FROM purchase 
JOIN pet ON purchase.petId = pet.id;
```

**Source Tables:**
*Purchase Table*
```text
+----+-------+---------+
| id | petId | ownerId |
+----+-------+---------+
|  1 |   890 |       3 |
|  2 |   891 |       3 |
|  3 |   895 |       4 |
+----+-------+---------+
```
*Pet Table*
```text
+-----+---------+------+
| id  | name    | type |
+-----+---------+------+
| 890 | Puddles | cat  |
| 891 | Fluffy  | cat  |
| 895 | Fido    | dog  |
+-----+---------+------+
```

**Joined Result:**
```text
+------------+---------+-------+---------+------+
| purchaseId | ownerId | petId | name    | type |
+------------+---------+-------+---------+------+
|          1 |       3 |   890 | Puddles | cat  |
|          2 |       3 |   891 | Fluffy  | cat  |
|          3 |       4 |   895 | Fido    | dog  |
+------------+---------+-------+---------+------+
```

## Database Transactions

A **transaction** is a sequence of one or more SQL operations executed as a single unit of work. Transactions ensure data integrity through **ACID** properties:
- **Atomicity:** All operations in the transaction succeed, or none do.
- **Consistency:** The database moves from one valid state to another.
- **Isolation:** Concurrent transactions do not interfere with each other.
- **Durability:** Once committed, changes are permanent even in a system failure.

In SQL, you typically start a transaction with `START TRANSACTION`, then either `COMMIT` to save the changes or `ROLLBACK` to undo them if an error occurs.

## Initializing Your Database

When developing applications, it is helpful to ensure the database schema exists upon startup. You can use the `IF NOT EXISTS` clause to make scripts idempotent (safe to run multiple times).

```sql
CREATE DATABASE IF NOT EXISTS pet_store;
USE pet_store;
CREATE TABLE IF NOT EXISTS pet (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(255) NOT NULL,
    PRIMARY KEY (id)
);
```

This approach is a form of **Infrastructure as Code** (IaC). By treating your schema configuration as code, you can track changes in version control and automate environment setup.

You can execute SQL scripts from a file using a command-line client like the MySQL Shell:

```sh
mysqlsh -u username -p --sql < initialize.sql
```

## Table of Common SQL Commands

| Command | Purpose | Example |
| :--- | :--- | :--- |
| **CREATE DATABASE** | Creates a new database. | `CREATE DATABASE pet_store;` |
| **DROP DATABASE** | Deletes a database and all its data. | `DROP DATABASE pet_store;` |
| **USE** | Selects a database to work in. | `USE pet_store;` |
| **CREATE TABLE** | Defines a new table. | `CREATE TABLE pet (id INT, name VARCHAR(255));` |
| **DESCRIBE** | Shows a table's structure. | `DESCRIBE pet;` |
| **ALTER TABLE** | Modifies table structure. | `ALTER TABLE pet ADD COLUMN age INT;` |
| **DROP TABLE** | Deletes a table. | `DROP TABLE pet;` |
| **INSERT INTO** | Adds new rows. | `INSERT INTO pet (name) VALUES ('Fido');` |
| **SELECT** | Retrieves data. | `SELECT * FROM pet;` |
| **UPDATE** | Modifies existing rows. | `UPDATE pet SET name = 'Rex' WHERE id = 1;` |
| **DELETE** | Removes specific rows. | `DELETE FROM pet WHERE id = 1;` |
| **TRUNCATE TABLE** | Deletes all rows but keeps structure. | `TRUNCATE TABLE pet;` |
| **CREATE INDEX** | Speeds up data retrieval. | `CREATE INDEX idx_name ON pet (name);` |
| **CREATE VIEW** | Creates a virtual table from a query. | `CREATE VIEW dogs AS SELECT * FROM pet WHERE type = 'dog';` |

## ☑ Exercise

````masteryls
{"id":"b99232fb-8952-4891-820d-3d43c212c380","title":"Interpreting a Basic SELECT Statement","type":"multiple-choice"}
Consider the following MySQL query:

```sql
SELECT title, author
FROM books
WHERE pages > 300
ORDER BY title DESC;
```

What will be the result of executing this query?

- [ ] It returns every column for books that have 300 or more pages, sorted alphabetically by title from A to Z.
- [ ] It returns only the `title` and `author` for every book in the table, regardless of page count, sorted by title.
- [x] It returns the `title` and `author` of books with more than 300 pages, sorted by the title in reverse alphabetical order.
- [ ] It updates the `books` table to set the page count to 300 for any book where the title starts with the letter D.
````

## Videos

- 🎥 [Structured Query Language (2:14)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=4f5a6bfe-5170-4c3c-97e8-ad660148d05a&start=0) - [[transcript]](https://github.com/user-attachments/files/17780823/CS_240_Structured_Query_Language_.SQL.pdf)
- 🎥 [SQL Data Types (3:48)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=91cb451f-fc88-426d-9656-ad660149a253&start=0) - [[transcript]](https://github.com/user-attachments/files/17737747/CS_240_SQL_Data_Types_Transcript.pdf)
- 🎥 [Creating and Dropping Tables (14:22)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=1f257807-3728-4239-a82b-b1a0011ad706&start=0) - [[transcript]](https://github.com/user-attachments/files/17737753/CS_240_Creating_and_Dropping_Tables_Transcript.pdf)
- 🎥 [Inserting, Updating, and Deleting Rows (6:40)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=203b8c65-d726-4282-8912-b1a0011f18e5&start=0) - [[transcript]](https://github.com/user-attachments/files/17737783/CS_240_Inserting_Updating_and_Deleting_Rows_Transcript.pdf)
- 🎥 [Retrieving Data with SQL Queries (11:15)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=e7083e5f-66a8-425f-be92-ad6601513cbd&start=0) - [[transcript]](https://github.com/user-attachments/files/17780824/CS_240_Retrieving_Data_with_SQL_Queries.pdf)
- 🎥 [Database Transactions (3:46)](https://byu.hosted.panopto.com/Panopto/Pages/Viewer.aspx?id=6e75d23e-4075-4a27-85d0-ad6601548134&start=0)- [[transcript]](https://github.com/user-attachments/files/17780825/CS_240_Database_Transactions.pdf)