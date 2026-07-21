# SQL Server Complete Guide -- Part 2 (Advanced INNER JOIN)

# Step 9: Understanding Database Relationships

## One-to-One (1:1)

One record in Table A relates to one record in Table B.

``` text
Person
person_id
   │
   ▼
Passport
person_id
```

## One-to-Many (1:N)

One author can write many books.

``` text
Authors
author_id
   │
   ▼
Books
author_id
```

## Many-to-Many (M:N)

A book can belong to many categories, and a category can contain many
books.

``` text
Books
  |
  | book_id
  ▼
Book_Categories
  ▲
  | category_id
Categories
```

A junction (bridge) table is required because relational databases
cannot directly represent many-to-many relationships.

------------------------------------------------------------------------

# Step 10: Create Categories Table

``` sql
CREATE TABLE categories
(
    category_id INT PRIMARY KEY,
    category_name VARCHAR(50)
);

INSERT INTO categories (category_id, category_name)
VALUES
(1,'Fiction'),
(2,'Classic'),
(3,'Romance'),
(4,'Political'),
(5,'Mystery'),
(6,'Adventure');
```

------------------------------------------------------------------------

# Step 11: Create Junction Table

``` sql
CREATE TABLE book_categories
(
    book_id INT,
    category_id INT,
    PRIMARY KEY(book_id, category_id)
);

INSERT INTO book_categories (book_id, category_id)
VALUES
(101,1),(101,2),(101,3),
(102,1),(102,2),(102,4),
(103,1),(103,2),(103,4),
(104,1),(104,2),(104,6),
(105,1),(105,5),
(106,1),(106,5),
(107,1),(107,2),(107,3),
(108,1),(108,2),(108,6);
```

**Best Practices**

-   Use a composite primary key.
-   Prevent duplicate mappings.
-   Use foreign keys in production.

------------------------------------------------------------------------

# Step 12: Verify Categories

``` sql
SELECT * FROM categories;
```

------------------------------------------------------------------------

# Step 13: Multiple INNER JOIN

``` sql
SELECT
    b.title,
    a.first_name,
    c.category_name
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id
INNER JOIN book_categories AS bc
ON b.book_id = bc.book_id
INNER JOIN categories AS c
ON bc.category_id = c.category_id;
```

## Why do duplicate book names appear?

This is expected.

Example:

  Book   Category
  ------ -----------
  1984   Fiction
  1984   Classic
  1984   Political

Each row represents a unique Book--Category relationship.

------------------------------------------------------------------------

# Step 14: STRING_AGG()

`STRING_AGG()` combines multiple rows into a single comma-separated
string.

``` sql
SELECT
    b.title,
    a.first_name,
    STRING_AGG(c.category_name, ', ') AS Categories
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id
INNER JOIN book_categories AS bc
ON b.book_id = bc.book_id
INNER JOIN categories AS c
ON bc.category_id = c.category_id
GROUP BY
b.book_id,
b.title,
a.first_name;
```

## Why not GROUP_CONCAT()?

-   `GROUP_CONCAT()` is a MySQL function.
-   SQL Server uses `STRING_AGG()`.

------------------------------------------------------------------------

# Step 15: Advanced Filtering Using WHERE

``` sql
SELECT
    b.title,
    a.first_name,
    b.publication_year,
    a.birth_year,
    STRING_AGG(c.category_name, ', ') AS Categories
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id
INNER JOIN book_categories AS bc
ON b.book_id = bc.book_id
INNER JOIN categories AS c
ON bc.category_id = c.category_id
WHERE b.publication_year < 1950
AND a.birth_year < 1900
GROUP BY
b.book_id,
b.title,
a.first_name,
b.publication_year,
a.birth_year;
```

Execution order:

1.  FROM
2.  JOIN
3.  WHERE
4.  GROUP BY
5.  SELECT
6.  ORDER BY

------------------------------------------------------------------------

# Step 16: Filtering in ON Clause

Original query:

``` sql
SELECT
    b.title,
    a.first_name,
    b.publication_year,
    a.birth_year,
    STRING_AGG(c.category_name, ', ')
FROM books AS b
INNER JOIN authors AS a
ON b.author_id = a.author_id
INNER JOIN book_categories AS bc
ON b.book_id = bc.book_id
INNER JOIN categories AS c
ON bc.category_id = c.category_id
AND b.publication_year < 1950
AND a.birth_year < 1900
GROUP BY
b.book_id,
b.title,
a.first_name,
b.publication_year,
a.birth_year;
```

## WHERE vs ON

`ON` determines which rows participate in the JOIN.

`WHERE` filters rows after the JOIN result is produced.

For an `INNER JOIN`, both approaches usually return the same result.

For a `LEFT JOIN`, they can produce different results.

**Best Practice**

-   Keep JOIN conditions in `ON`.
-   Keep filtering conditions in `WHERE`.

------------------------------------------------------------------------

# Step 17: Common Interview Questions

1.  Difference between INNER JOIN and LEFT JOIN?
2.  Why use aliases?
3.  Why do duplicate rows appear after JOIN?
4.  Why does `SELECT *` return duplicate columns?
5.  Why is `GROUP BY` required?
6.  Why use `STRING_AGG()`?
7.  Why doesn't `GROUP_CONCAT()` work in SQL Server?
8.  What causes an **Ambiguous column name** error?

------------------------------------------------------------------------

# Step 18: Best Practices

-   Avoid `SELECT *` in production.
-   Use meaningful aliases.
-   Normalize your database.
-   Create primary and foreign keys.
-   Keep JOIN conditions in `ON`.
-   Keep filtering in `WHERE`.
-   Group only by required columns.
-   Write readable SQL.

------------------------------------------------------------------------

# Step 19: Common Beginner Mistakes

-   Forgetting `GROUP BY`
-   Confusing `WHERE` and `ON`
-   Using `GROUP_CONCAT()` in SQL Server
-   Not understanding many-to-many relationships
-   Ignoring duplicate rows after JOIN
-   Not using aliases

------------------------------------------------------------------------

# Course Summary

You have now completed a practical guide covering:

-   INNER JOIN
-   One-to-One
-   One-to-Many
-   Many-to-Many
-   Junction Tables
-   Multiple INNER JOINs
-   STRING_AGG()
-   WHERE vs ON
-   Execution Order
-   Interview Questions
-   Best Practices
-   Common Mistakes

This knowledge provides a solid foundation for writing professional SQL
Server JOIN queries.
