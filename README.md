

# mysql-tutorial
## Installation

	Get ubuntu 20.04 machine 		
		https://docs.rackspace.com/docs/install-mysql-server-on-the-ubuntu-operating-system
		UPDATE mysql.user SET authentication_string = PASSWORD('mypwd') WHERE User = 'root';
		
	In ubuntu 22.04
		update password was done using 
		ALTER USER 'root'@'localhost' IDENTIFIED BY 'Vilas123';
		

	In ubuntu 24.04
		update password was done using 
		sudo mysql --defaults-file=/etc/mysql/debian.cnf
		ALTER USER 'root'@'localhost' IDENTIFIED BY 'Vilas123';
		
		quit;
		systemctl restart mysql 
		mysql -u root -p 
		
		
```bash
#docker-compose up -d

```

## Databases

### Listing, Creating and Updating Databases

```sql
SHOW DATABASES;
```

```sql
CREATE DATABASE hello_world_db;
```

### Select Activate Database

```sql
USE hello_world_db
```

### Show Activate Database
```sql
SELECT database();
```


## Tables
A database is a a collection of tables this is true for relational databases. 

### Data types
```sql
VARCHAR(<maximum characters>)
```

### Creating Databases
```sql
CREATE TABLE cats 
( 
    name VARCHAR(50), 
    age INT 
);
```

### Listing Table Properties

```sql
DESCRIBE cats;
```

```sql
SHOW COLUMNS FROM cats;
```

### Delete Tables
```sql
DROP TABLE cats;
```

### Inserting Data into Table
```sql
INSERT INTO cats(name, age)
VALUES('Blue', 1);
```

and verify it, like so:

```sql
SELECT * from cats;
```

### Bulk Insertion
```sql
INSERT INTO cats(name, age)
VALUES ('Peanut', 2),
       ('Butter', 4),
       ('Jelly', 7);
```

### Warnings
If a warning is shown when a command is executed you can show it, like so:

```sql
SHOW WARNINGS;
```

Examples of warnings are inserting strings that are above the maximum limit for a VARCHAR

### NULL and NOT NULL

If not specified otherwise a column in a table is allays nullable meaning that the column value
doesn't have to be specified on insertion. To override this behaviour add NOT NULL to the column
declaration in the `CREATE TABLE` statement.

```sql
CREATE TABLE cats1
( 
    name VARCHAR(50) NOT NULL, 
    age INT NOT NULL 
);
```

This will result in a warning about the column not having a default value and MySQL will actually
pick one anyway. The default for an INT is 0.

### Settings Default Values

```sql
CREATE TABLE cats3( name VARCHAR(100) DEFAULT 'unnamed', age INT DEFAULT 99);
INSERT INTO cats3(age) VALUES(13);
```

This results in a table like:

```
select * from cats3;
+---------+-----+
| name    | age |
+---------+-----+
| unnamed |  13 |
+---------+-----+
```

You still might want to specify `NOT NULL` to prevent the operator to explicitly provide a `NULL`
value for a column, like so:

```sql
CREATE TABLE cats4( name VARCHAR(100) NOT NULL DEFAULT 'unnamed', age INT NOT NULL DEFAULT 99);
INSERT INTO cats4(age) VALUES(13);
```

Leading to this:
```
ERROR: 1048 (23000): Column 'age' cannot be null
```

### A Primer in Primary keys
A unique identifier for a row and thereby a unique entity of a collection.

```sql
CREATE TABLE unique_cats( 
  cat_id INT NOT NULL, 
  name VARCHAR(100), 
  age INT, 
  PRIMARY KEY (cat_id)
);
```

Results in this:
```
+--------+--------------+------+-----+---------+-------+
| Field  | Type         | Null | Key | Default | Extra |
+--------+--------------+------+-----+---------+-------+
| cat_id | int(11)      | NO   | PRI | NULL    |       |
| name   | varchar(100) | YES  |     | NULL    |       |
| age    | int(11)      | YES  |     | NULL    |       |
+--------+--------------+------+-----+---------+-------+
```

A Primary Key must be Unique so the this will lead to an error:

```sql
INSERT INTO unique_cats(cat_id, name, age) VALUES(1, 'Fred', 23);
INSERT INTO unique_cats(cat_id, name, age) VALUES(1, 'James', 3);

show warnings;
```
```
ERROR: 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
```
It is not common to use the username as the Primary Key


It is redundant to have to specify the id of the row manually for each insertion. The 
`AUTO_INCREMENT` column property solves this, like so:

```sql
CREATE TABLE unique_cats2( 
  cat_id INT NOT NULL AUTO_INCREMENT, 
  name VARCHAR(100), 
  age INT, 
  PRIMARY KEY (cat_id)
);
```

It is now possible to insert multiple entities, like so:
```sql
INSERT INTO unique_cats2 (name, age) VALUES('Skippy', 4) ;
INSERT INTO unique_cats2 (name, age) VALUES('Jiff', 3) ;
INSERT INTO unique_cats2 (name, age) VALUES('Jiff', 3) ;
```

And each of the inserts will be treated as a separate entity.



Not null and unique key constraints 
```sql
drop table unique_cats;
CREATE TABLE unique_cats(
  cat_id INT NOT NULL,
  name VARCHAR(100) NOT NULL,
  age INT,
  PRIMARY KEY (cat_id, name)
);

or 

ALTER TABLE unique_cats
ADD CONSTRAINT unique_name
UNIQUE (name);

or 


CREATE TABLE unique_cats(
  cat_id INT NOT NULL,
  name VARCHAR(100) NOT NULL,
  age INT,
  PRIMARY KEY (cat_id, name),
  UNIQUE (name)
);


Verify by executing 
 SHOW CREATE TABLE unique_cats;

```


Check key constraint 


```sql
drop table unique_cats;
CREATE TABLE unique_cats(
  cat_id INT NOT NULL,
  name VARCHAR(100) NOT NULL,
  age INT,
  PRIMARY KEY (cat_id, name),
  CHECK (age <= 20)  -- Add the check constraint here
);
or 

CREATE TABLE unique_cats(
  cat_id INT NOT NULL,
  name VARCHAR(100) NOT NULL,
  age INT,
  PRIMARY KEY (cat_id, name)
);


ALTER TABLE unique_cats
ADD CONSTRAINT check_age CHECK (age <= 20);

Verify
INSERT INTO unique_cats (cat_id, name, age) VALUES(1, 'Skippy', 4) ;
INSERT INTO unique_cats (cat_id, name, age) VALUES(2, 'Pippy', 24) ;

```

## CRUD

### Sample data


```sql
-- Let's drop the existing cats table:
DROP TABLE cats;
-- Recreate a new cats table
CREATE TABLE cats 
  ( 
     cat_id INT NOT NULL AUTO_INCREMENT, 
     name   VARCHAR(100), 
     breed  VARCHAR(100), 
     age    INT, 
     PRIMARY KEY (cat_id) 
  ); 

-- Show the created cats table
DESCRIBE cats;

-- Insert data
INSERT INTO cats(name, breed, age) 
VALUES ('Ringo', 'Tabby', 4),
       ('Cindy', 'Maine Coon', 10),
       ('Dumbledore', 'Maine Coon', 11),
       ('Egg', 'Persian', 4),
       ('Misty', 'Tabby', 13),
       ('George Michael', 'Ragdoll', 9),
       ('Jackson', 'Sphynx', 7);
```

## C(R)UD

```sql
SELECT * FROM cats;
```
The star means retrieve all columns from table cats.

## Select specific column(s) from table

```sql
SELECT name, age FROM cats;
```

## Filter the results by value using `WHERE`
```sql
SELECT * FROM cats WHERE age=4;
```

Will result in: 
```
+--------+-------+---------+-----+
| cat_id | name  | breed   | age |
+--------+-------+---------+-----+
|      1 | Ringo | Tabby   |   4 |
|      4 | Egg   | Persian |   4 |
+--------+-------+---------+-----+
```

`WHERE` statements matching strings are case insensitive by default.

`WHERE` statements can match either user input or values of other columns, like so:
```sql
SELECT * FROM cats WHERE cat_id=age;
```
This will retrieve a list of all cats that has a `cat_id` that is equal to their `age`

### Aliases

It is possible to rename the output of some fields using an alias, like so:
```sql
SELECT cat_id as id, name FROM cats;
```

## CR(U)D Update


How do we alter existing database

Change all cats that are of `breed` 'Tabby' to `breed` 'Shorthair'
```sql
	UPDATE cats SET breed='Shorthair' WHERE breed='Tabby';
```

*Always do a `SELECT` statement to test the `UPDATE` statement to make sure that
the correct data was targeted*  

### Samples

```sql
SELECT * FROM cats WHERE name='Jackson';
 
UPDATE cats SET name='Jack' WHERE name='Jackson';
 
SELECT * FROM cats WHERE name='Jackson';
 
SELECT * FROM cats WHERE name='Jack';
 
SELECT * FROM cats WHERE name='Ringo';
 
UPDATE cats SET breed='British Shorthair' WHERE name='Ringo';
 
SELECT * FROM cats WHERE name='Ringo';
 
SELECT * FROM cats;
 
SELECT * FROM cats WHERE breed='Maine Coon';
 
UPDATE cats SET age=12 WHERE breed='Maine Coon';
 
SELECT * FROM cats WHERE breed='Maine Coon';
```

## CRU(D) Delete
```sql
-- Delete all cats named 'Egg' from the table `cats`
DELETE FROM cats WHERE name='Egg';
-- Delete all cats from the table `cats`
DELETE FROM cats;
```

*Always do a `SELECT` statement to test the `DELETE` statement to make sure that
the correct data was targeted*  


## Running SQL Files


1. Start the CLI by
   ```bash
      mysql -u root -p

      mysqlsh --sql -P3306 -h127.0.0.1 -u root -p password
   ```
2. Source the file within the CLI, like so:
   ```
   \source ./cats.sql
   ```

## Insert Book Sample data

1. Start the CLI by
   ```bash
   mysqlsh --sql -P3306 -h127.0.0.1 -uroot -ppassword
   ```
2. Create book_shop database
   ```sql
   CREATE DATABASE book_shop;
   USE book_shop;
   ```
2. Source the file within the CLI, like so:
   ```
   \source ./books/book-data.sql
   ```

## Strings Functions
[MySQL Reference Manual - String Functions](https://dev.mysql.com/doc/refman/en/string-functions.html)


### CONCAT
[MySQL Reference Manual - CONCAT](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_concat)
Combine data for cleaner output

#### Combine author first name and last name into one column called `Name`
```sql
SELECT CONCAT(author_fname,' ', author_lname) as 'Name' from books;
```
Results in:
```
+----------------------+
| Name                 |
+----------------------+
| Jhumpa Lahiri        |
| Neil Gaiman          |
| Neil Gaiman          |
| Jhumpa Lahiri        |
| Dave Eggers          |
| Dave Eggers          |
| Michael Chabon       |
| Patti Smith          |
| Dave Eggers          |
| Neil Gaiman          |
| Raymond Carver       |
| Raymond Carver       |
| Don DeLillo          |
| John Steinbeck       |
| David Foster Wallace |
| David Foster Wallace |
+----------------------+
```

### Joining column with separator
```sql
SELECT CONCAT_WS(' - ', title, author_fname, author_lname) FROM books;
```

Results in:
```
+------------------------------------------------------------------------+
| CONCAT_WS(' - ', title, author_fname, author_lname)                    |
+------------------------------------------------------------------------+
| The Namesake - Jhumpa - Lahiri                                         |
| Norse Mythology - Neil - Gaiman                                        |
| American Gods - Neil - Gaiman                                          |
| Interpreter of Maladies - Jhumpa - Lahiri                              |
| A Hologram for the King: A Novel - Dave - Eggers                       |
| The Circle - Dave - Eggers                                             |
| The Amazing Adventures of Kavalier & Clay - Michael - Chabon           |
| Just Kids - Patti - Smith                                              |
| A Heartbreaking Work of Staggering Genius - Dave - Eggers              |
| Coraline - Neil - Gaiman                                               |
| What We Talk About When We Talk About Love: Stories - Raymond - Carver |
| Where I'm Calling From: Selected Stories - Raymond - Carver            |
| White Noise - Don - DeLillo                                            |
| Cannery Row - John - Steinbeck                                         |
| Oblivion: Stories - David - Foster Wallace                             |
| Consider the Lobster - David - Foster Wallace                          |
+------------------------------------------------------------------------+
```
## SUBSTRING
* [MySQL Reference Manual - SUBSTRING](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring)

```sql
SELECT SUBSTRING('Hello World', 1, 2);
```

Results in:
```
+-------------------------------+
| SUBSTRING('Hello World', 1, 4) |
+-------------------------------+
| He                          	 |
+-------------------------------+
```

Get position 7 to end of string
```sql
SELECT SUBSTRING('Hello World', 7);
```

Results in:
```
+-----------------------------+
| SUBSTRING('Hello World', 7) |
+-----------------------------+
| World                       |
+-----------------------------+
```

Get five characters starting from end of string

```sql
SELECT SUBSTRING('Hello World', -5);
```

Select the first ten characters of the title of each book in table `books` with column name 
'Short Title'

```sql
SELECT SUBSTRING(title, 1,10) as 'Short Title' from books;
```

To add an extra '...' at the end of the 'Short Title' the `SUBSTRING` command can be used in
conjunction with `CONCAT`, like so:

```sql
SELECT CONCAT(SUBSTRING(title, 1,10), '...') as 'Short Title' from books;
```

Results in:
```
+---------------+
| Short Title   |
+---------------+
| The Namesa... |
| Norse Myth... |
| American G... |
| Interprete... |
| A Hologram... |
| The Circle... |
| The Amazin... |
| Just Kids...  |
| A Heartbre... |
| Coraline...   |
| What We Ta... |
| Where I'm ... |
| White Nois... |
| Cannery Ro... |
| Oblivion: ... |
| Consider t... |
+---------------+
```

Looks nice doesn't it 

### REPLACE
* [MySQL Reference Manual - REPLACE](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_replace)


Replace all e's in all book titles with the number 3
```sql
SELECT REPLACE(title, 'e', '3') from books;
```

Nest multiple string functions
```sql
SELECT SUBSTRING(REPLACE(title, 'e', '3'), 1, 20) as 'Weird String' from books;
```

### REVERSE
* [MySQL Reference Manual - REVERSE](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_reverse)

```sql
SELECT REVERSE('Hello World');
```

Results in:
```
+------------------------+
| REVERSE('Hello World') |
+------------------------+
| dlroW olleH            |
+------------------------+
```

### CHAR_LENGTH
* [MySQL Reference Manual - CHAR_LENGTH](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_char-length)

```sql
SELECT CHAR_LENGTH('Hello World');
```

Results in:
```
+----------------------------+
| CHAR_LENGTH('Hello World') |
+----------------------------+
|                         11 |
+----------------------------+
```

Show a human readable string like 'Eggers is 6 characters long' for each author in table `books`

```sql
SELECT CONCAT(author_lname, ' is ', CHAR_LENGTH(author_lname), ' characters long.') FROM books;
```

### UPPER AND LOWER

* [MySQL Reference Manual - Upper](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_upper)
* [MySQL Reference Manual - Lower](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_lower)

```sql
SELECT UPPER('Hello World');
```

```sql
SELECT LOWER('Hello World');
```

## Refining our Selections

### More sample data into `books`

```sql
INSERT INTO books (title, author_fname, author_lname, released_year, stock_quantity, pages)
  VALUES ('10% Happier', 'Dan', 'Harris', 2014, 29, 256),
         ('fake_book', 'Freida', 'Harris', 2001, 287, 428),
         ('Lincoln In The Bardo', 'George', 'Saunders', 2017, 1000, 367);
```

## DISTINCT

The `DISTINCT` keyword comes straight after `SELECT` and only retrieves the unique entries of values
in a column. 

Get distinct author last names

```sql
SELECT DISTINCT author_lname FROM books;
```

Results in:
```
+----------------+
| author_lname   |
+----------------+
| Lahiri         |
| Gaiman         |
| Eggers         |
| Chabon         |
| Smith          |
| Carver         |
| DeLillo        |
| Steinbeck      |
| Foster Wallace |
| Harris         |
| Saunders       |
+----------------+
```

Get distinct pairs of author first and last names
```sql
SELECT DISTINCT author_fname, author_lname FROM books;
```

## ORDER BY


Orders results by the values in a column.

```sql
SELECT DISTINCT author_fname, author_lname FROM books ORDER BY author_lname;
```

* The default order is ASCENDING
* The default order can be reversed by adding the keyword `DESC` after `ORDER BY`

## LIMIT



Get all columns of the first record from the table `books`
```sql
SELECT * FROM books LIMIT 1;
```

Get all columns of the second to fifth record from the table `books`
```sql
SELECT * FROM books LIMIT 2,5;
```

## LIKE



Matches parts of column values

* The % sign are wildcards and works like a asterisk
* The _ sign are wildcards matches exactly one character
* The `%` and `_` can be escaped by `\`

* The patterns are case insensitive by default.

Get `title` and `author_fname` column from table `books` where `author_fname` contains the string
'da'.
```sql
SELECT title, author_fname from books WHERE author_fname LIKE '%da%';
```

Get `title` and `author_fname` column from table `books` where `author_fname` starts with the 
string 'da'.
```sql
SELECT title, author_fname from books WHERE author_fname LIKE 'da%';
```

Get `title` and `stock_quantity` from table `books` where `stock_quantity` us exactly to 
characters or numbers long.

```sql
SELECT title, stock_quantity FROM books WHERE stock_quantity LIKE '__';
```

## Selecting titles with `%` and `_` in them

Get `title` from table books where `title` has a percentage sign in it.
```sql
SELECT title FROM books WHERE title LIKE '%\%%' ;
```

Get `title` from table books where `title` has a underscore sign in it.
```sql
SELECT title FROM books WHERE title LIKE '%\_%' ;
```

## Aggregate functions

### COUNT


How many books are in our database?
```sql
SELECT COUNT(*) as 'Amount of Books' FROM books;
```

Results in:
```
+-----------------------------+
| Amount of Author First Name |
+-----------------------------+
|                          19 |
+-----------------------------+

```

How many distinct author first name are there in our `books` table

```sql
SELECT COUNT(DISTINCT author_fname) as 'Amount of Distinct Author First Name' FROM books;
```

Results in:
```
+--------------------------------------+
| Amount of Distinct Author First Name |
+--------------------------------------+
|                                   12 |
+--------------------------------------+

```

How many distinct authors are there in our `books` table

```sql
SELECT COUNT(DISTINCT author_fname, author_lname) as 'Amount of Distinct Author First Name' FROM books;
```


How many titles contains the string 'the'

```sql
SELECT COUNT(*) FROM books WHERE title LIKE '%the%';
```

### GROUP BY


`GROUP BY` shows records based on groups in a column. 

The output of this query is a bit confusing since it only shows the first book of each group.
```sql
SELECT count(title), author_lname FROM books GROUP BY author_lname;
```

By combining the `GROUP BY` with a `COUNT` it is easier to understand what is going on.

```sql
SELECT author_lname, COUNT(*) FROM books GROUP BY author_lname;
```

Results in:

```
+----------------+----------+
| author_lname   | COUNT(*) |
+----------------+----------+
| Carver         |        2 |
| Chabon         |        1 |
| DeLillo        |        1 |
| Eggers         |        3 |
| Foster Wallace |        2 |
| Gaiman         |        3 |
| Harris         |        2 |
| Lahiri         |        2 |
| Saunders       |        1 |
| Smith          |        1 |
| Steinbeck      |        1 |
+----------------+----------+
``` 
This shows the count the number of books each author has written.

It is possible to group by multiple fields, like so:
```sql
SELECT author_fname, author_lname, COUNT(*) FROM books GROUP BY author_lname, author_fname;
```
Which will avoid duplicated `author_lname`

### MIN and MAX


Get the earliest released book from table `books`

```sql
SELECT MIN(released_year) FROM books;
```

Get the latest released book from table `books`
```sql
SELECT MAX(released_year) FROM books;
```

Get the book with the minimum pages in the `books` table
```sql
SELECT * FROM books WHERE pages = (SELECT Min(pages) FROM books);
```
MySQL executes the subquery first and then uses the result as input for the outer query.

This is not effective since two queries needs to be executed. As an alternative `ORDER BY` can
be used to achieve the same thing, like so:

```sql
SELECT title, pages FROM books ORDER BY pages LIMIT 1;
```

### MIN and MAX in combination with GROUP BY

Get the first released book for all authors
```sql
SELECT author_fname, author_lname, MIN(released_year) 
FROM books 
GROUP BY author_lname, author_fname;
```

### SUM

Calculate the total amount of pages from all books in the `books` tables
```sql
SELECT SUM(pages) from books;
```

Calculate the total amount an author has produced in total in the `books` tables
```sql
SELECT author_fname, author_lname, SUM(pages) from books GROUP BY author_fname, author_lname;
```

### AVG


Calculate the average release year of all books in the `books` tables
```sql
SELECT AVG(released_year) from books;
```

Calculate the average `stock quantity` per `released_year` the `books` tables
```sql
SELECT released_year, AVG(stock_quantity) from books GROUP BY released_year;
```

Calculate the average `pages` per author in table `books`
```sql
SELECT author_fname, author_lname, AVG(PAGES) FROM books GROUP BY author_lname, author_fname;
```


### Emp and Dept Table

```sql
CREATE TABLE dept (
  dept_id INT PRIMARY KEY,
  dept_name VARCHAR(50)
);

CREATE TABLE emp (
  emp_id INT PRIMARY KEY,
  emp_name VARCHAR(50),
  emp_job VARCHAR(50),
  emp_salary DECIMAL(10,2),
  dept_id INT,
  FOREIGN KEY (dept_id) REFERENCES dept(dept_id)
);


INSERT INTO dept (dept_id, dept_name)
VALUES
  (1, 'IT'),
  (2, 'Development'),
  (3, 'Support'),
  (4, 'Marketing'),
  (5, 'Sales'),
  (6, 'Human Resources'),
  (7, 'Finance'),
  (8, 'Operations'),
  (9, 'Research and Development'),
  (10, 'Quality Assurance');


INSERT INTO emp (emp_id, emp_name, emp_job, emp_salary, dept_id)
VALUES
  (1, 'John', 'Manager', 5000, 1),
  (2, 'Lisa', 'Developer', 4000, 2),
  (3, 'David', 'Analyst', 3500, 1),
  (4, 'Sarah', 'Designer', 3000, 2),
  (5, 'Mark', 'Technician', 2500, 3),
  (50, 'Emily', 'Administrator', 2800, 10);
```



### Examples of Join queries

```sql
   
```
Retrieve employee details with their corresponding department names:

```sql

SELECT 
	emp.emp_id, 
	emp.emp_name, 
	emp.emp_job, 
	emp.emp_salary, 
	dept.dept_name
FROM emp
JOIN dept 
ON 
	emp.dept_id = dept.dept_id;
```
    Get a list of employees along with their department names, sorted by department:

```sql

SELECT emp.emp_id, emp.emp_name, emp.emp_job, emp.emp_salary, dept.dept_name
FROM emp
JOIN dept ON emp.dept_id = dept.dept_id
ORDER BY dept.dept_name;
```
    Find employees in the "IT" department:

```sql

SELECT emp.emp_id, emp.emp_name, emp.emp_job, emp.emp_salary
FROM emp
JOIN dept ON emp.dept_id = dept.dept_id
WHERE dept.dept_name = 'IT';
```
    Calculate the average salary for each department:

```sql

SELECT dept.dept_name, AVG(emp.emp_salary) AS avg_salary
FROM emp
JOIN dept ON emp.dept_id = dept.dept_id
GROUP BY dept.dept_name;
```
    Retrieve employee details along with their department name and total salary, sorted by department:

```sql

SELECT emp.emp_id, emp.emp_name, emp.emp_job, dept.dept_name, SUM(emp.emp_salary) AS total_salary
FROM emp
JOIN dept ON emp.dept_id = dept.dept_id
GROUP BY emp.emp_id, emp.emp_name, emp.emp_job, dept.dept_name
ORDER BY dept.dept_name;
```

	Example of having query

```sql
SELECT dept.dept_name, AVG(emp.emp_salary) AS avg_salary
FROM emp
JOIN dept ON emp.dept_id = dept.dept_id
GROUP BY dept.dept_name
HAVING AVG(emp.emp_salary) >= 4000;
```

	Example of sub query

```sql
SELECT emp_id, emp_name, emp_salary, dept_id
FROM emp
WHERE emp_salary > (
  SELECT AVG(emp_salary)
  FROM emp
  WHERE dept_id = emp.dept_id
);
```


###

Find all departments with no employees:
```sql

SELECT d.dept_name
FROM dept d
LEFT JOIN emp e ON d.dept_id = e.dept_id
WHERE e.dept_id IS NULL;
```

 Find the average salary for each department:

```sql

SELECT d.dept_name, AVG(e.emp_salary) AS avg_salary
FROM emp e
INNER JOIN dept d ON e.dept_id = d.dept_id
GROUP BY d.dept_name;

```

 Find the total number of employees in each department:

```sql
SELECT d.dept_name, COUNT(*) AS num_employees
FROM emp e
INNER JOIN dept d ON e.dept_id = d.dept_id
GROUP BY d.dept_name;
```
 Find the department with the highest average salary:


```sql
SELECT d.dept_name, AVG(e.emp_salary) AS avg_salary
FROM emp e
INNER JOIN dept d ON e.dept_id = d.dept_id
GROUP BY d.dept_name
ORDER BY avg_salary DESC
LIMIT 1;
```


Find the total salary paid to all employees in the company:
```sql
SELECT SUM(emp_salary) AS total_payroll
FROM emp;
```
Find employees who don't belong to any department (assuming dept_id can be NULL in emp table):


```sql
SELECT *
FROM emp
WHERE dept_id IS NULL;
```


Find the department names and the number of employees in each department with at least 2 employees:
```sql
SELECT d.dept_name, COUNT(*) AS num_employees
FROM emp e
INNER JOIN dept d ON e.dept_id = d.dept_id
GROUP BY d.dept_name
HAVING COUNT(*) >= 2;
```

Following query doesn't work as manager_id column is missing.
Find employees who earn more than their manager (assuming there's a manager_id column in emp table):
```sql
SELECT e1.emp_name, e1.emp_salary, m.emp_name AS manager_name, m.emp_salary AS manager_salary
FROM emp e1
INNER JOIN emp m ON e1.manager_id = m.emp_id
WHERE e1.emp_salary > m.emp_salary;
```

Find the department with the most employees who have the job title 'Developer':

```sql
SELECT d.dept_name, COUNT(*) AS num_developers
FROM emp e
INNER JOIN dept d ON e.dept_id = d.dept_id
WHERE emp_job = 'Developer'
GROUP BY d.dept_name
ORDER BY num_developers DESC
LIMIT 1;
```



```sql

```

```sql

```



```sql

```


```sql

```



```sql

```


```sql

```


```sql

```



### Alter table Drop Tables

Example of alter table command

```sql
ALTER TABLE emp
ADD emp_email VARCHAR(100);
```

Example of drop table command
```sql
DROP TABLE emp;
```


