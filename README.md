

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



#To add auto_increment latter 
ALTER TABLE unique_cats MODIFY COLUMN cat_id INT auto_increment;
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


#INDEXES
#-------
References: 
https://dev.mysql.com/doc/refman/8.0/en/mysql-indexes.html
https://www.youtube.com/watch?v=kv3jC0P4gOc
https://www.tutorialspoint.com/mysql/mysql-unique-index.htm
https://www.digitalocean.com/community/tutorials/how-to-use-indexes-in-mysql

```sql

CREATE DATABASE indexes;
USE indexes;

CREATE TABLE employees (
    employee_id int,
    first_name varchar(50),
    last_name varchar(50),
    device_serial varchar(15),
    salary int
);

INSERT INTO employees VALUES
    (1, 'John', 'Smith', 'ABC123', 60000),
    (2, 'Jane', 'Doe', 'DEF456', 65000),
    (3, 'Bob', 'Johnson', 'GHI789', 70000),
    (4, 'Sally', 'Fields', 'JKL012', 75000),
    (5, 'Michael', 'Smith', 'MNO345', 80000),
    (6, 'Emily', 'Jones', 'PQR678', 85000),
    (7, 'David', 'Williams', 'STU901', 90000),
    (8, 'Sarah', 'Johnson', 'VWX234', 95000),
    (9, 'James', 'Brown', 'YZA567', 100000),
    (10, 'Emma', 'Miller', 'BCD890', 105000),
    (11, 'William', 'Davis', 'EFG123', 110000),
    (12, 'Olivia', 'Garcia', 'HIJ456', 115000),
    (13, 'Christopher', 'Rodriguez', 'KLM789', 120000),
    (14, 'Isabella', 'Wilson', 'NOP012', 125000),
    (15, 'Matthew', 'Martinez', 'QRS345', 130000),
    (16, 'Sophia', 'Anderson', 'TUV678', 135000),
    (17, 'Daniel', 'Smith', 'WXY901', 140000),
    (18, 'Mia', 'Thomas', 'ZAB234', 145000),
    (19, 'Joseph', 'Hernandez', 'CDE567', 150000),
    (20, 'Abigail', 'Smith', 'FGH890', 155000);
	

EXPLAIN SELECT * FROM employees WHERE salary = 100000;	

Description of each field 
	id: 
		unique identifier to each row in the output
		typically 
			in the order the operations are considered 
				in the query plan.
	select_type: 
		type of join or operation being performed in the query. 
		SIMPLE
			straightforward table access without joins.
	table: 
		table(s) involved in the query. 
		employees
			query selects data only from that table.
	partitions: 
		if table partitioning is being used. 
		NULL
			partitioning is not relevant to this query.
	type: 
		type of table access used. 
		ALL
			engine will scan all rows in the employees table. 
				an index could potentially be used to optimize the query.
	possible_keys: 
		lists any indexes that could potentially be used 
			to speed up the query. 
		NULL
			there are no relevant indexes for this specific query.
	key: 
		actual index used in the query plan. 
		NULL
			indicating no index is being used.
	key_len: 
		length of the index used, if any. 
		Since no index is used, it's NULL.
	ref: 
		table referenced in a join (if applicable). 
		As this is a simple table access without joins, it's NULL.
	rows: 
		estimation of the number of rows the engine expects to examine 
			based on the chosen plan. 
		20
			indicating the engine anticipates looking at 20 rows from the employees table.
	filtered: 
		estimated percentage of rows that will pass the WHERE clause condition. 
		10.00
			engine expects roughly 10% of the 20 rows (around 2 rows) 
				to meet the salary = 100000 condition.
	Extra: 
		This column provides additional information about the query execution plan. 
		Here, it mentions "Using where", signifying that a table scan will be used with a filter based on the WHERE clause condition.




EXPLAIN analyze SELECT * FROM employees WHERE salary = 100000;	
	Filter: (employees.salary = 100000)
		Cost: measure of i/o operation 

		: This indicates a filtering operation will be applied based on the condition employees.salary = 100000.
			cost=2.25 rows=2: This is the estimated cost (usually in units of I/O operations) of applying the filter. It's estimated to process 2 rows.
			(actual time=0.0486..0.0726 rows=1 loops=1): This section from ANALYZE shows the actual execution details.
				actual time=0.0486..0.0726: The actual time taken to execute the filter, ranging from 0.0486 to 0.0726 seconds.
				rows=1: The actual number of rows that passed the filter (1 in this case).
				loops=1: The number of times the filter was applied (usually 1 for single-pass filtering).

	ANALYZE Section:

		-> Table scan on employees (cost=2.25 rows=20) (actual time=0.026..0.0551 rows=20 loops=1): 
			This part details the table access method used.
			Table scan on employees: This signifies the engine will scan all rows in the employees table.
			(cost=2.25 rows=20): 
				Similar to the filter section
					estimated cost and 
					number of rows to be scanned 
					(20 in this case).
			(actual time=0.026..0.0551 rows=20 loops=1): The actual execution details from ANALYZE.
				actual time=0.026..0.0551: The actual time taken to scan the table, ranging from 0.026 to 0.0551 seconds.
				rows=20: The actual number of rows scanned from the employees table (20 in this case).
				loops=1: The number of times the table scan was performed (typically 1).


EXPLAIN SELECT * FROM employees WHERE salary < 70000;

EXPLAIN analyze SELECT * FROM employees WHERE salary < 70000;

CREATE INDEX salary ON employees(salary);
GRANT INDEX on *.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
EXPLAIN SELECT * FROM employees WHERE salary = 100000;
EXPLAIN analyze SELECT * FROM employees WHERE salary = 100000;

EXPLAIN SELECT * FROM employees WHERE salary < 70000;
EXPLAIN analyze SELECT * FROM employees WHERE salary < 70000;

EXPLAIN SELECT * FROM employees WHERE salary < 140000;
EXPLAIN analyze SELECT * FROM employees WHERE salary < 140000;


CREATE UNIQUE INDEX device_serial ON employees(device_serial);

INSERT INTO employees VALUES (21, 'Sammy', 'Smith', 'ABC123', 65000);
	error if there is already same data 
	EXPLAIN SELECT * FROM employees WHERE device_serial = 'ABC123';
	
	
	
EXPLAIN SELECT * FROM employees WHERE last_name = 'Smith' AND first_name = 'John';
EXPLAIN analyze SELECT * FROM employees WHERE last_name = 'Smith' AND first_name = 'John';
	
CREATE INDEX names ON employees(last_name, first_name);
EXPLAIN SELECT * FROM employees WHERE last_name = 'Smith' AND first_name = 'John';
EXPLAIN analyze SELECT * FROM employees WHERE last_name = 'Smith' AND first_name = 'John';

EXPLAIN SELECT * FROM employees WHERE last_name = 'Smith';
EXPLAIN analyze SELECT * FROM employees WHERE last_name = 'Smith';

EXPLAIN SELECT * FROM employees WHERE first_name = 'John';	
EXPLAIN analyze SELECT * FROM employees WHERE first_name = 'John';	

SHOW INDEXES FROM employees;
DROP INDEX device_serial ON employees;


The output you provided is the result of the SHOW INDEXES FROM employees statement in MySQL. It displays information about all the indexes that exist on the table named 

employees. Let's break down each column and explain its significance:

    Table: This column shows the name of the table to which the index belongs (in this case, employees).
    Non_unique: This indicates whether the index allows duplicate values in the indexed columns.
        0 signifies a unique index, meaning no rows can have the same combination of values in the indexed columns.
        1 indicates a non-unique index, which allows duplicate values.
    Key_name: This column displays the name assigned to the index.
    Seq_in_index: This column specifies the order of the columns within a composite index. For single-column indexes, it's always 1.
    Column_name: This column shows the name of the table column that is part of the index.
    Collation: This column indicates the character set and sorting order used for the indexed column.
    Cardinality: This is an estimated number of distinct values in the indexed column(s). A lower cardinality suggests fewer unique values, potentially leading to more efficient lookups using the index.
    Sub_part: This column can be NULL or a value between 1 and the column length, indicating if only a portion of the column is included in the index.
    Packed: This column is usually NULL but can be YES for certain storage formats that compress data within the index.
    Null: This indicates whether the indexed column allows null values.
        YES signifies null values are allowed in the indexed column.
        NO means null values are not allowed.
    Index_type: This column shows the type of index used. In this case, all indexes are of type BTREE (B-Tree), a common and efficient indexing structure.
    Comment: This column is usually empty but can contain any comments associated with the index.
    Index_comment: This column can contain comments about the purpose of the index.
    Visible: This column indicates whether the index is visible to the optimizer for query planning.
        YES signifies the index is considered by the optimizer when choosing an execution plan.
        NO means the index is not currently being considered.
    Expression: This column can be NULL or contain an expression used in the index definition, if applicable.

In your specific output:

    The table employees has four indexes:
        device_serial (single-column unique index)
        salary (single-column non-unique index)
        names (composite non-unique index on last_name and then first_name)
    The cardinality for salary and last_name is relatively low (20 and 16 respectively), suggesting these indexes could be helpful for filtering or sorting based on those columns.	
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





Write a transaction that adheres to ACID properties and ensure database integrity.

```sql
START TRANSACTION;

-- 1. Update customer balance
UPDATE employees 
SET last_name = 'Vilas Varghese' 
WHERE employee_id = 1;

-- 2. Insert transaction record
INSERT INTO employees (employee_id, first_name, last_name, device_serial,salary) 
VALUES (201, 'Dilmon','Thomas',12, 987654);

-- 3. If any of the above operations fail, rollback the transaction
COMMIT; 

START TRANSACTION;

-- 1. Update customer balance
UPDATE employees 
SET last_name = 'Jansi ki Rani' 
WHERE employee_id = 1;

-- 2. Insert transaction record
INSERT INTO employees (employee_id, first_name, last_name, device_serial,salary) 
VALUES (202, 'Lakshmi','Bahi',15, 987654);

-- 3. If any of the above operations fail, rollback the transaction
ROLLBACK; 


```
