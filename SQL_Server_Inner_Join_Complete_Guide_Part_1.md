# SQL Server Complete Guide -- Part 1 (INNER JOIN)

## Project Overview

**Project Name:** SQL Server INNER JOIN Complete Learning Guide

**Author:** Suman Shekhar

This project demonstrates how `INNER JOIN` works in SQL Server using a
simple Books Management System. The goal is to learn not only the
syntax, but also the reasoning behind each query, common interview
questions, best practices, and common mistakes.

------------------------------------------------------------------------

# Step 1: Create Database

## Purpose

Creates a new database named `db_inner_join`.

### Why?

A database is the top-level container that stores tables, views, stored
procedures, functions, indexes, and many other database objects.

### SQL

``` sql
CREATE DATABASE db_inner_join;
GO

USE db_inner_join;
GO
```

**Best Practices**

-   Use meaningful database names.
-   Avoid spaces in database names.
-   Separate development and production databases.

**Interview Tip**

**Q:** Difference between `CREATE DATABASE` and `CREATE SCHEMA`?

-   `CREATE DATABASE` creates an entirely new database.
-   `CREATE SCHEMA` creates a namespace inside an existing database.

------------------------------------------------------------------------

# Step 2: Create Tables

## Authors Table

Relationship:

``` text
Authors
author_id (PK)
      │
      ▼
Books.author_id (FK)
```

``` sql
CREATE TABLE authors
(
    author_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    birth_year INT
);
```

## Books Table

``` sql
CREATE TABLE books
(
    book_id INT PRIMARY KEY,
    title VARCHAR(100),
    author_id INT,
    publication_year INT,
    price DECIMAL(6,2)
);
```

**Best Practices**

-   Use primary keys.
-   Normalize data.
-   Avoid storing repeated author information inside the `books` table.

------------------------------------------------------------------------

# Step 3: Insert Sample Data

## Authors

``` sql
INSERT INTO authors
(author_id, first_name, last_name, birth_year)
VALUES
(1,'Jane','Austen',1775),
(2,'George','Orwell',1903),
(3,'Ernest','Hemingway',1899),
(4,'Agatha','Christie',1890),
(5,'J.K.','Rowling',1965);
```

## Books

``` sql
INSERT INTO books
(book_id,title,author_id,publication_year,price)
VALUES
(101,'Pride and Prejudice',1,1813,12.99),
(102,'1984',2,1949,14.50),
(103,'Animal Farm',2,1945,11.75),
(104,'The Old Man and the Sea',3,1952,10.99),
(105,'Murder on the Orient Express',4,1934,13.25),
(106,'Death on the Nile',4,1937,12.50),
(107,'Emma',1,1815,11.99),
(108,'For Whom the Bell Tolls',3,1940,15.75);
```

------------------------------------------------------------------------

# Step 4: Verify Data

``` sql
SELECT * FROM authors;

SELECT * FROM books;
```

Purpose: Verify that all records were inserted successfully before
writing joins.

------------------------------------------------------------------------

# Step 5: SQL INNER JOIN

## What is INNER JOIN?

`INNER JOIN` returns only the rows that have matching values in both
tables.

Relationship:

``` text
Books.author_id
      │
      ▼
Authors.author_id
```

``` sql
SELECT *
FROM books
INNER JOIN authors
ON books.author_id = authors.author_id;
```

### Why do duplicate columns appear?

Both tables contain `author_id`, so `SELECT *` returns both columns.
This is expected behavior.

### Best Practice

Avoid `SELECT *` in production. Select only the columns you need.

``` sql
SELECT
    b.book_id,
    b.title,
    a.first_name,
    a.last_name
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id;
```

------------------------------------------------------------------------

# Step 6: Table Aliases

Aliases improve readability and reduce typing.

``` sql
SELECT
    b.book_id,
    b.title,
    a.first_name,
    a.last_name
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id
WHERE publication_year > 1940;
```

------------------------------------------------------------------------

# Step 7: ORDER BY

Sort results by author birth year.

``` sql
SELECT
    b.book_id,
    b.title,
    a.first_name,
    a.last_name,
    a.birth_year
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id
ORDER BY a.birth_year ASC;
```

This also works:

``` sql
ORDER BY birth_year ASC;
```

It works because `birth_year` exists in only one table. If multiple
tables contained the same column name, SQL Server would throw an
**Ambiguous column name** error.

------------------------------------------------------------------------

# Step 8: GROUP BY and COUNT()

Question: How many books has each author written?

``` sql
SELECT
COUNT(b.book_id) AS book_written,
a.first_name
FROM authors AS a
INNER JOIN books AS b
ON a.author_id=b.author_id
GROUP BY a.first_name;
```

A more professional version:

``` sql
SELECT
a.first_name,
a.last_name,
COUNT(*) AS book_written
FROM authors AS a
INNER JOIN books AS b
ON a.author_id=b.author_id
GROUP BY
a.author_id,
a.first_name,
a.last_name;
```

### Why GROUP BY?

Every selected column must either:

-   Be inside an aggregate function (`COUNT`, `SUM`, `AVG`, etc.), or
-   Be included in the `GROUP BY` clause.

Otherwise SQL Server raises a grouping error.

------------------------------------------------------------------------

# Topics Covered

-   Create Database
-   Create Tables
-   Insert Sample Data
-   Verify Data
-   INNER JOIN
-   Table Aliases
-   WHERE with INNER JOIN
-   ORDER BY
-   GROUP BY
-   COUNT()

**Next:** Part 2 covers Many-to-Many Relationships, Junction Tables,
`STRING_AGG()`, Multiple INNER JOINs, `WHERE` vs `ON`, Interview
Questions, and Best Practices.
