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

`Structured Query Language` (SQL) is a programming language specifically designed to interact with relational databases. It contains statements for inserting, updating, reading, and deleting data. It also provides commands for managing the database itself and the users who have access to it.

You can categorize the most commonly used SQL statements into three main groups:

1.  **DDL** (Data Definition Language) - `CREATE`, `ALTER`, `DROP`.
2.  **DML** (Data Manipulation Language) - `INSERT`, `UPDATE`, `DELETE`.
3.  **DQL** (Data Query Language) - `SELECT`.

Below is an example of SQL statements used to create a database, create a table, and insert sample data.

```sql
> CREATE DATABASE pet_store;
> USE pet_store;
> CREATE TABLE pet (id int, name varchar(255), type varchar(255));
> INSERT INTO pet VALUES (93, "Fido", "dog");
> INSERT INTO pet VALUES (14, "Puddles", "cat");
> INSERT INTO pet VALUES (77, "Chip", "bird");
> DESCRIBE pet;

+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | int          | YES  |     | NULL    |       |
| name  | varchar(255) | YES  |     | NULL    |       |
| type  | varchar(255) | YES  |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
```

The following demonstrates a simple query to read all of the pets in the database.

```sql
> SELECT id, name, type FROM pet;

+--------+----------+---------------+
| id     | name     | type          |
+--------+----------+---------------+
| 93     | Fido     | dog           |
| 14     | Puddles  | cat           |
| 77     | Chip     | bird          |
+--------+----------+---------------+
```

SQL is a **declarative** programming language. This means you declare *what* you want to achieve rather than providing a series of imperative commands that define *how* to do it.

To fully understand the examples above, we need to examine common SQL statements in more detail.

## Working with Databases

A database server, or Relational Database Management System (RDBMS), can host one or more databases. You create a database with the `CREATE DATABASE` statement. After creating the database, you execute a `USE` statement to select it for subsequent commands.

```sql
CREATE DATABASE pet_store;
USE pet_store;
```

Databases have a default character set used to represent bytes as text. Normally, you specify the character set when you execute the `CREATE` request, but you can also modify an existing database using the `ALTER DATABASE` statement. For example, if you want to use UTF-8 to represent text, you would run the following:

```sql
ALTER DATABASE pet_store CHARACTER SET utf8mb4;
```

To delete a database and all of the data it contains, use the `DROP DATABASE` statement. While this is rarely used in production, it is common in development environments when experimenting with table schemas.

```sql
DROP DATABASE pet_store;
```

## Working with Tables

Once you have a database, you can create tables to hold rows representing your objects. A table definition is similar to declaring a class in Java. When you create a table, you must specify each field and its data type.

```sql
CREATE TABLE pet (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(255) NOT NULL,
    PRIMARY KEY (id)
);
```

In this example, each field is followed by the `NOT NULL` clause, meaning a value must be provided for every row inserted.

The `id` field is annotated with the `AUTO_INCREMENT` keyword. This means you do not need to provide an `id` when inserting a row; the database will automatically assign a unique, increasing integer.

### Altering Tables

To modify an existing table, use the `ALTER TABLE` statement. The following example adds a `nickname` field after the table has already been created. This alteration does not include the `NOT NULL` clause, so existing rows will have their `nickname` set to `NULL` by default.

```sql
ALTER TABLE pet ADD COLUMN nickname VARCHAR(255);
```

To delete a table entirely, use the `DROP TABLE` statement.

```sql
DROP TABLE pet;
```

**Note:** Be certain before executing this command, as there is no way to recover the data once a table is dropped.

It is important to understand that rerunning a `CREATE TABLE` statement does not modify an existing table. If the table already exists, the command will throw an error. You can use the `IF NOT EXISTS` qualifier to prevent this error, but to change a table's definition, you must either `DROP` and recreate it or use an `ALTER TABLE` statement.

### Primary Keys and Indexes

In addition to fields, the `CREATE TABLE` statement specifies which field is the **primary key**. Primary keys must be unique; attempting to insert two rows with the same key will result in an error. Primary keys are indexed by default because the database assumes you will use the key to query the table.

Indexes significantly increase query performance, but they also consume additional storage and memory. You should only create indexes when necessary for performance.

You can create indexes on fields other than the primary key using the `INDEX` keyword. In the example below, an index is added to the `name` field.

```sql
CREATE TABLE pet (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(255) NOT NULL,
    PRIMARY KEY (id),
    INDEX (name)
);
```

If you decide you need an index after the table is created, use the `CREATE INDEX` statement:

```sql
CREATE INDEX index_name ON pet (name);
```

### Types

Below are common SQL data types used when creating tables:

| Data type | Description                              | Example                                                   |
| --------- | ---------------------------------------- | --------------------------------------------------------- |
| INT       | Integer numbers.                         | 1, 10, -100                                               |
| DECIMAL   | Fixed-point decimal numbers.             | 1.23, 100.00, -5.6789                                     |
| FLOAT     | Single-precision floating-point numbers. | 1.23456789, 100.000000, -5.67890123                       |
| DOUBLE    | Double-precision floating-point numbers. | 1.234567890123456, 100.00000000000000, -5.678901234567890 |
| CHAR      | Fixed-length character strings.          | 'A', 'Hello, world!', '1234567890'                        |
| VARCHAR   | Variable-length character strings.       | 'A', 'Hello, world!', '1234567890'                        |
| TEXT      | Variable-length text data.               | 'This is a long text string that can be any length.'      |
| BLOB      | Variable-length binary data.             | `Binary data of any length`                               |
| DATE      | Date values.                             | '2023-10-14'                                              |
| TIME      | Time values.                             | '15:43:45'                                                |
| DATETIME  | Date and time values.                    | '2023-10-14 15:43:45'                                     |

## Inserting, Updating, and Deleting Data

Once your table is ready, you can add data using the `INSERT` statement. You must supply values for all `NOT NULL` fields.

```sql
INSERT INTO pet (name, type) VALUES ('Puddles', 'cat');
```

To modify existing data, use the `UPDATE` statement. It is critical to include a `WHERE` clause to specify which rows to update. If you omit the `WHERE` clause, **all rows** in the table will be updated.

```sql
UPDATE pet SET name = 'Fido' WHERE id = 1;
```

To remove rows, use the `DELETE` statement with a `WHERE` clause. The following deletes all rows where the pet type is 'cat'.

```sql
DELETE FROM pet WHERE type = 'cat';
```

If you want to delete **all** data from a table while keeping the table structure intact, use the `TRUNCATE` statement.

```sql
TRUNCATE TABLE pet;
```

## Selecting Data

The `SELECT` statement is the primary way to query data. A basic `SELECT` returns specific columns for every row:

```sql
SELECT name, type FROM pet;
```

To filter results, add a `WHERE` clause. These can include boolean logic and wildcards. You can also use `LIMIT` to restrict the number of rows returned.

```sql
SELECT name, type FROM pet WHERE type='dog' OR (type='cat' AND name='Puddles') LIMIT 2;
```

You can select all columns using `*` or count the total number of rows using the `COUNT` function.

```sql
SELECT * FROM pet;
SELECT COUNT(*) FROM pet;
```

## Joining Tables

To combine data from multiple tables, use a `JOIN` clause. This allows you to create a temporary view that relates rows from different tables. The following example joins the `purchase` and `pet` tables based on matching pet IDs.

```sql
SELECT purchase.id AS purchaseId, purchase.ownerId, pet.id AS petId, pet.name, pet.type
 FROM purchase JOIN pet ON purchase.petId = pet.id;
```

Given the following source tables:

**Source**

```sql
+----+-------+---------+
| id | petId | ownerId |
+----+-------+---------+
|  1 |   890 |       3 |
|  2 |   891 |       3 |
|  3 |   895 |       4 |
+----+-------+---------+

+-----+---------+------+----------+
| id  | name    | type | nickname |
+-----+---------+------+----------+
| 890 | Puddles | cat  | NULL     |
| 891 | Fluffy  | cat  | NULL     |
| 892 | Willie  | cat  | NULL     |
| 893 | George  | bird | NULL     |
| 894 | Buddy   | dog  | NULL     |
| 895 | Fido    | dog  | NULL     |
+-----+---------+------+----------+
```

The result of the `JOIN` would be:

**Result**

```sql
+------------+---------+-------+---------+------+
| purchaseId | ownerId | petId | name    | type |
+------------+---------+-------+---------+------+
|          1 |       3 |   890 | Puddles | cat  |
|          2 |       3 |   891 | Fluffy  | cat  |
|          3 |       4 |   895 | Fido    | dog  |
+------------+---------+-------+---------+------+
```

## Initializing Your Database

When developing an application, it is helpful to ensure the database and tables exist upon startup. You can execute `CREATE DATABASE` and `CREATE TABLE` calls with the `IF NOT EXISTS` clause.

```sql
CREATE DATABASE IF NOT EXISTS petshop;
USE petshop;
CREATE TABLE IF NOT EXISTS pet (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(255) NOT NULL,
    PRIMARY KEY (id)
);
```

This approach is known as **Infrastructure as Code** (IaC). It treats environment configuration as code, reducing human error and allowing you to track infrastructure changes through version control.

You can also write SQL statements in a text file and execute them via a command-line client like the MySQL Shell (`mysqlsh`). For example, a script might drop an old database and recreate a fresh schema for testing:

```sql
DROP DATABASE IF EXISTS pet_store;
CREATE DATABASE pet_store;
USE pet_store;

CREATE TABLE pet (
    id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    type VARCHAR(255) NOT NULL,
    PRIMARY KEY (id),
    INDEX (name)
);
```

You could run this script using a command like this:

```sh
➜ mysqlsh -u yourusername -p --sql < initialize.sql
```

## Table of Common SQL Commands

The following table summarizes the commands covered in this topic:

| Command         | Purpose                                                                 | Example statement                                                                                                            |
| --------------- | ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| CREATE DATABASE | Creates a new database.                                                 | `CREATE DATABASE pet_store;`                                                                                                 |
| ALTER DATABASE  | Modifies database settings (e.g., character set).                       | `ALTER DATABASE pet_store CHARACTER SET utf8mb4;`                                                                            |
| DROP DATABASE   | Deletes a database and its contents.                                    | `DROP DATABASE pet_store;`                                                                                                   |
| USE             | Selects a database for subsequent commands.                             | `USE pet_store;`                                                                                                             |
| CREATE TABLE    | Creates a new table.                                                    | `CREATE TABLE pet (id INT NOT NULL AUTO_INCREMENT, name VARCHAR(255), PRIMARY KEY (id));`                                    |
| DESCRIBE        | Shows the structure/fields of a table.                                  | `DESCRIBE pet;`                                                                                                               |
| ALTER TABLE     | Modifies a table's structure.                                           | `ALTER TABLE pet ADD COLUMN nickname VARCHAR(255);`                                                                          |
| DROP TABLE      | Deletes a table.                                                        | `DROP TABLE pet;`                                                                                                            |
| INSERT INTO     | Adds new rows to a table.                                               | `INSERT INTO pet (name, type) VALUES ('Puddles', 'cat');`                                                                    |
| SELECT          | Retrieves data from a table.                                            | `SELECT name, type FROM pet;`                                                                                                |
| UPDATE          | Modifies existing data.                                                 | `UPDATE pet SET name = 'Fido' WHERE id = 1;`                                                                                 |
| DELETE          | Removes rows from a table.                                              | `DELETE FROM pet WHERE id = 1;`                                                                                              |
| CREATE INDEX    | Adds an index for faster searching.                                     | `CREATE INDEX pet_name_index ON pet (name);`                                                                                 |
| DROP INDEX      | Removes an index.                                                       | `DROP INDEX pet_name_index ON pet;`                                                                                          |
| TRUNCATE TABLE  | Deletes all rows but keeps the table structure.                        | `TRUNCATE TABLE pet;`                                                                                                        |
| CREATE VIEW     | Creates a virtual table based on a query.                               | `CREATE VIEW cats AS SELECT * FROM pet WHERE type = 'cat';`                                                                  |
| DROP VIEW       | Deletes a view.                                                         | `DROP VIEW cats;`                                                                                                            |

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
