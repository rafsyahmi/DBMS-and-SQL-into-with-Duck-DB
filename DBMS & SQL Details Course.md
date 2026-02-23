# Overview DBMS & SQL

# **DBMS & SQL Course** 

# Document 

- [About SQL](https://www.codecademy.com/resources/docs/sql/about-sql) 

# Definition 

**S**tructured **Q**uery **L**anguage, and is the programming language implemented by a database management system (DBMS) used for managing and querying data held in a relational database.

# Classification of Commands (QDMC)

1. ## Data Query Language (DQL)

   * performing queries on data within schema objects (e.g: retrieving )  
   * SELECT

2. ## Data Definition Language (DDL)

   * define the database schema (to create and modify the structure of the database object)  
   * CREATE, DROP, ALTER

3. ## Data Manipulation Language (DML)

   * to modify the data stored in the database table   
   * INSERT, UPDATE, DELETE

4. ## Data Control Language (DCL)

   * Giving the controls and properties of the DBMS (rights and permissions to database objects.)  
   * GRANT, REVOKE

# Type of Database & Query Language

Most Common \- Microsoft SQL Server (MSSQL)  
Other variant \- MySQL (Standard SQL) , SQLite (Non-standard SQL Variant) , PostgreSQL (PSQL)

# Keys & Keywords

## Keys

- A vital component in the data base management system (DBMS)  
- Help in organising data  in a structured manner ( avoiding duplication, creating relations & making the access easy to the data in a database  
- Creating relationships between different tables  
- Essential for maintaining the integrity and accuracy of the data stored in a database

## Category of Keys

### Primary Key

- a unique field within a table that identifies each record distinctly from each other document  
- Prevent duplication  & presence of null value (the basis of the data accuracy)

### Candidate Key

- Another name for a possible primary key  
- Has characteristics of a primary key but not chosen as primary key fields

### Super  Key

- A set of attributes that uniquely identifies a record within a table  
- May contain many attributes more than the need for uniquely  search for records in a database

### Foreign Key

- Key that is used to relate between tables  
- Direct to the primary key in another table, which checks on referential integrity and therefore maintains consistency in data

### Composite Key

- More than one column is used to generate the keys for the table  
- It avoids the case of having the same values in different column fields where  individual columns may have the same values

### Alternate Key

- They are other keys that can be used in a table but were not used in defining  the primary key for the table  
- Use as an added method of identifying records with a unique number

### Unique Key

- Key that guarantees that no two record will have the same  value in the designated field  
- However, it permits one null value. (It ensures that every record is unique, but it is not the primary key.)

## Operators

- Operators are unique keywords used to perform arithmetic, comparison, and logical operations

## Types of Operators

### Arithmetic Operators

- used to perform arithmetic on numeric types  
- \+: Addition  
- \-: Subtraction  
- \*: Multiplication  
- /: Division  
- %: Modulo (remainder)

### Comparison Operators

- Comparison operators can be used to compare two values:  
- \=: Equal to  
- \>: Greater than  
- \<: Less than  
- \>=: Greater than or equal to  
- \<=: Less than or equal to  
- \!=: Not equal  
- \<\>: Not equal to

  SELECT \*

  FROM students

  WHERE gpa \> 25;

### Logical Operators

- Logical operators can be used to combine multiple conditions such as AND, OR, NOT or perform operations such as NOT and BETWEEN.

* AND  
  Tests if all conditions in a given expression evaluate to TRUE.

* BETWEEN  
  Selects values, inclusively of beginning and end values, within a given range. BETWEEN works with numbers, text, or date data types.

* Equals To

Queries for items equal to a specified condition.

* EXISTS  
  Tests a subquery and returns TRUE if at least one record satisfies it.

* ELSE  
  To defer to another condition if the return of the previous conditions is not fullfiled at the last   
    
* GREATER THAN  
  Queries for items that are greater than a given value  
    
* IF  
  Applies conditional logic to queries and returns a value.

* IIF  
  Performs inline conditional checks within SELECT statements to return different values based on a condition.

* IN  
  Allows the user to specify multiple values in the WHERE clause.

* IS NOT NULL  
  Checks if a value is not NULL.

* IS NULL  
  Checks if a value is NULL.

* LIKE  
  Returns \`TRUE\` if its first text argument matches the wildcard pattern in its second argument.

* NOT  
  Queries for items in an expression that return NOT TRUE for some condition(s).

* NOT EQUAL TOC  
  ompares two values and returns true if they are not equal.

* NULL-SAFE EQUAL  
  Performs an equality comparison similar to the \= operator, using the logic of an XNOR gate.  
    
* OR  
  Test if any condition in a given expression evaluates to TRUE.

* SIMILAR TO  
  Used to compare a string to a regular expression  
    
* UPDATE  
  Modifies existing records in a table according to specified conditions.


## Aggregated Function

In SQL, aggregate functions perform a calculation on a set of values and return **a single value**. They are often used with the GROUP BY clause of the SELECT statement.

Note: Except for COUNT(\*), aggregate functions ignore all NULL values.

* AVG()

Returns the average value in a column.

* COUNT()

Returns the number of rows that match the specified criteria.

* MAX()

Returns the largest value in a column.

* MIN()

Returns the smallest value in a column.

* SUM()

Returns the sum of all the value in that column.

# Common Commands

# ALTER TABLE

Adds, deletes, modifies or changes the data type of a column in a table.

ALTER TABLE table\_name  
ADD column\_name ; ( to add nee column to the table )

# AND\*\*

to combine multiple conditions in a WHERE clause to make the result set more specific and useful.

Eg;

SELECT \*/column\_header    
FROM table\_name   
WHERE 1st condition BETWEEN value  AND value  
   AND 2nd condition \= value 

 TRUE operator displays a row if all the conditions are true.

# AS

Renames a column or table with an alias for display, does not permanently change table/column names in the database.must be using “single quote sign \- ‘ “

Eg ; 

SELECT column\_header  AS ‘rename for the column\_header’  
FROM table\_name ;

# BETWEEN\*\*

The BETWEEN operator is used in a WHERE clause to filter the result set within a certain range. It accepts two values: numbers, text, or dates.

Eg;

SELECT \*/column\_header    
FROM table\_name   
WHERE condition BETWEEN value  AND value;  \# condition usually refers to column \_header , value can be either  numbers, text, or dates

# CASE

- Returns different outputs based on the conditions of each statement.  
- Allows us to create different outputs SELECT  statement).   
- It is SQL’s way of handling if-then logic.

Eg;

SELECT column\_header ,  
 CASE  
  WHEN condition THEN 'value/text'  
  ELSE 'value/text'  
 END AS ‘‘name for the column\_header’’   
 FROM  table\_name;                               

\*Must end with the END statement

# CREATE INDEX

Creates an index on existing columns in a table. Indexes cannot be seen by the user of a database; they just help speed up queries.

# CREATE TABLE

Creates a new table within a database.

CREATE  TABLE table\_name (  
   column\_name1 datatype,  
   column\_name2 datatype,  
   column\_name3…….  
);

# CREATE VIEW

Creates a virtual table based on a saved query.

# CROSS JOIN

Returns a table with all possible combinations of every row from the first table and every row from the second table.

Eg;

If we have 3 different shirts (white, grey, and olive) and 2 different pants (light denim and black), the results might look like this:

![][image1]

# DISTINCT

used to return unique values in the output. It filters out all duplicate values in the specified column(s).

Eg;

SELECT DISTINCT column\_header    
FROM table\_name ;

# DELETE

Removes existing row(s) from a table. If a WHERE statement is excluded, all rows in the table will be deleted.

# DROP TABLE

Deletes an existing table in a database.

# ELSE

Use in WHEN command 

# FULL OUTER JOIN

Combines rows from different tables even if the JOIN condition is not met.

# GROUP BY

Groups a result set based on an aggregate function (COUNT(), MIN(), MAX(), SUM(), AVG()). It lists the number in each group.

GROUP BY is a clause in SQL that is used with aggregate functions is used in collaboration with the SELECT statement to arrange identical data into groups.

Eg;

SELECT “column \- header1”, COUNT(\*/“column \- header”)   
FROM “table \- name”  
GROUP BY “column-header1”

# HAVING

A conditional statement similar to WHERE but used with aggregate functions (COUNT(), MIN(), MAX(), SUM(), AVG()).

Eg;

SELECT “column \- header1”,  
   “column \- header2”,  
   COUNT (“column \- header3”)  
FROM “table \- name”  
GROUP BY 1, 2  
HAVING COUNT(“column \- header3”) \>  value

# INNER JOIN

Returns all rows that have matching values in both tables and omits non-matching rows.

# INSERT INTO

Creates and places new rows into a table.

# JOIN

Use to combine tables. The different tables are connected on the reference “second” \-  data with the same information \= unique key/foreign key

SELECT \*  
FROM “table1 \- name”  
JOIN “table2 \- name”  
  ON “table1 \- name”. unique key \= “table2 \- name”.unique key;

Eg:  
Foreign key \= customer\_id  
2 tables with the present of unique  key  
Table 1 \- orders  
Table 2 \- customers

\*\*\*  
SELECT \*  
FROM orders  
JOIN customers  
  ON orders.customer\_id \= customers.customer\_id;

Or 

SELECT orders.order\_id,  
   customers.customer\_name  
FROM orders  
JOIN customers  
  ON orders.customer\_id \= customers.customer\_id;

\*\*\*also known as INNER JOIN

# LEFT JOIN

Combines matching rows with rows from the left-side table (table 1……..) regardless of whether  there is a matching row in the next sequence table

# LIKE\*\*

is a special operator used with the  WHERE  clause to search for a specific pattern in a column.

Eg;

In a column (name) there are 2 data that are spelled with “Se7en” and “Seven”

\~ 

SELECT \*/column\_header    
FROM table\_name  
WHERE condition LIKE 'Se\_en'; \# condition usually refers to column \_header 

‘Se\_en’ represents a pattern with a wildcard character

% is a wildcard character that matches zero or more missing characters in the pattern. For example:

A% matches all movies with names that begin with letter ‘A’  
%a matches all movies that end with ‘a’  
We can also use % both before and after a pattern:

SELECT \*/column\_header    
FROM table\_name  
WHERE condition LIKE '%man%'

\#put space within a wildcard will cause the selection to be only the selection without any possible combination /any location

SELECT \*/column\_header    
FROM table\_name  
WHERE condition LIKE 'the %'; 

 \# will select only the name starting with ‘the’ . No any other possibility like They or there will be selected due to the “space” before the wildcard.

Wildcard is case insensitive

# LIMIT

Specifies, or limits, the maximum number of rows the result set will have.

# IS NULL\*\*

NULL indicates unknown values. It is not possible to test for NULL values with comparison operators, such as \= and \!=.

Instead, use these operators:

* IS NULL  
* IS NOT NULL

Eg;

SELECT \*/column\_header    
FROM table\_name   
WHERE condition  IS NULL;  \# condition usually refers to column \_header 

# OR\*\*

operator displays a row if any condition is true.

# ORDER BY

Sorts the result set by a particular column either alphabetically or numerically.

Eg;

SELECT \*/column\_header    
FROM table\_name   
ORDER BY column\_header DESC;  \# DESC refer to the order of the data presented

\*  
DESC \- Descending order  
 ASC \- Ascending order

If no order is stated, it will always go for Ascending order, 0-10, then alphabets A-Z for text & number data

If using with WHERE command, it should every time after WHERE

Eg;

SELECT \*/column\_header    
FROM table\_name   
WHERE condition \> value ; \# condition usually refers to column \_header   
ORDER BY column\_header DESC \# DESC refer to the order of the data presented

# PIVOT

Transforms rows of a table into columns.

# REPLACE()

Replace all matching string parts with a specified new substring.

# RIGHT JOIN

Combines matching rows with rows from the right-side table.

# ROLLBACK

Undoes any work performed in the current transaction. It can also be used to undo work performed by in-doubt transactions.

# ROUND

Rounds a value to the nearest integer or to a specific number of decimals if an optional value is provided.

# SELECT

Every SQL query will begin with the SELECT command to fetch data from one or more tables.

SELECT \* FROM  “Table- name”     ( select all second / parameter /column available )

SELECT \* FROM “Table-name”  
WHERE “column \- header”  \= ‘data \- value1’ or /and  ‘data \- value2

Or 

SELECT \* FROM “Table-name”  
WHERE “column \- header”  in   
(data \- value1, data \- value2 , data \- value3….)

# SELECT DISTINCT

Returns unique values in the specified column(s).

# SELECT TOP

Returns a specified number of rows from the top of the result.

# THEN

Use together in the WHEN command to represent the condition return that met the criteria set

Eg;

SELECT column\_header ,  
 CASE  
  WHEN condition THEN 'value/text'  
  ELSE 'value/text'  
 END AS ‘‘name for the column\_header’’   
 FROM  table\_name;                               

# TRIM

Removes leading and trailing spaces or specified characters from a string.

# TRUNCATE

Deletes all rows from a table while retaining the table structure.

# UNION

Combines the results of two or more SELECT queries. Combine 2  tablet

# UPDATE

Edits row(s) in a table.

UPDATE table\_name  
SET column1 \= value1, column2 \= value2, ...  
WHERE condition;

# WHEN

Used in CASE command to define a condition \- if-then logic

# 

# 

# WHERE \*

Filters records (rows) that match a certain condition. (clause to obtain only the information we want.)

Operators create a condition that can be evaluated as either true or false.

Comparison operators used with the WHERE clause are:

\= equal to  
\!= not equal to  
\> greater than  
\< less than  
\>= greater than or equal to  
\<= less than or equal to

Eg;

SELECT \*/column\_header    
FROM table\_name   
WHERE condition \< value ; \# condition usually refers to column \_header 

# WITH

Stores the result of a query in a temporary table using an alias.

[image1]: <data:image/png;base64,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>