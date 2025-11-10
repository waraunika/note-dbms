# 3.1 Introduction to SQL
- SQL which stands for Structured Query Language, is a domain-specific language used in programming and managing relational databases.
- Standard language for interacting with and managing data in a relational database management system (RDBMS).
- SQL is essential for tasks such as creating and modifying database schema, inserting, updating and deleting data and querying databases to retrieve information.
# 3.2 Features of SQL
1. **Declarative Nature**
	- SQL is a declarative language
	- user specifies what data they want, not how to get it
	- the database engine figures out the most efficient way to execute the query.
2. **Comprehensive Data Handling**
	- SQL provides a unified language for all core database operations:
		- DDL: to define and modify the database structure (e.g., `CREATE`, `Alter`, `DROP` tables)
		- DML: to manage data with the tables (e.g., `SELECT`, `INSERT`, `UPDATE`, `DELETE`)
		- DCL: to control access and permissions (e.g., `GRANT`, `REVOKE`)
3. **Portability and Standardisation**
	- SQL is an international standard (ANSI, ISO)
	- while vendors add extensions, core SQL is highly portable across different systems like MySQL, PostgreSQL, Oracle and SQL Server
4. **Powerful Data Querying**
	- The `SELECT` statement, with clauses like `WHERE`, `GROUP BY`, `HAVING`, `ORDER BY` allows for complex filtering, aggregation and sorting of data to extract meaningful information.
5. **Transactional Control**
	- SQL supports transactions with commands like `COMMIT` and `ROLLBACK`.
	- this ensures data integrity by allowing a set of operations to be treated as a single, atomic unit that either fully succeeds or fully fails
6. **Scalability and Performance**
	- While SQL itself is language, it is designed to work with RDBMS that can handle very large volumes of data efficiently.
	- Features like views, indexing and stored procedures further enhance performance and manageability
# 3.3 Queries and Sub-Queries
## A. Queries
- A query in SQL is a request for information from a database.
- The primary SQL command for querying the data is the `SELECT` statement.
- Here is a basic syntax:
```sql
SELECT column1, column2
	FROM table_name
	WHERE condition;	
```
- Explanation:
	- `SELECT`: specifies the columns to be retrieved.
	- `FROM`: specifies the table from which to retrieve the data.
	- `WHERE`: specifies the conditions that filter the rows to be retrieved (optional).
- Here is a basic example:
```sql
SELECT first_name, last_name
	FROM employees
	WHERE department='IT';	
```
## B. Sub-Queries
- A Sub-query (or an inner query) is a query nested within another SQL statement.
- Sub-queries can be used in various parts of a SQL statement, such as the `SELECT`, `FROM`, `WHERE`, `HAVING` clauses.
- they help to break down complex queries into smaller, more manageable parts
- example:
```sql
SELECT product_name, price
	FROM products
	WHERE category_id = (
		SELECT category_id FROM categories WHERE category_name = 'Electronics';
	)
```

```sql
SELECT p.product_name, avg_prices.average_price
FROM products p
JOIN (
	SELECT category_id, AVG(price) AS average_price
	FROM PRODUCTS
	GROUP BY category_id
) AS avg_prices ON p.category_id = avg_prices.category_id
```
# 3.4 Set Operations
- In SQL, set operations allow users to combine the results of two or more queries.
- There are 3 primary set operations: `UNION`, `INTERSECT` and `EXCEPT` (or `MINUS` depending on DBMS)
	1. `UNION`:
		- used to combine the results of two or more `SELECT` statements, removing duplicate rows from the final result set.
		- e.g., `SELECT product_name FROM category1 UNION SELECT product_name FROM category2`
	2. `INTERSECT`:
		- used to return the common rows that appear in the result sets of two `SELECT` statements.
		- e.g., `SELECT product_name FROM category1 INTERSECT SELECT product_name FROM category2`
	3. `EXCEPT`:
		- returns the distinct rows present in the result set of first `SELECT` statement but not in the result set of the second `SELECT` statement.
		- e.g., `SELECT product_name FROM category1 EXCEPT SELECT product_name FROM category2`
- Two conditions to be satisfied by any two sets for set operations are:
	1. Same number of columns: Both tables must have the same number of columns in their `SELECT` statements. The column names need not be the same. The result takes the column name from the first `SELECT` statement
	2. Compatible Data Types: The data types of corresponding columns must be compatible. i.e. They must be the same or implicitly convertible.
# 3.5 Relations (Joined, Derived)
- in SQL, relations refer to how data from multiple tables is combined or derived to create a meaningful result set
- two types of relations: joined relations and derived relations
## A. Joined Relation (`JOIN`)
- A `JOIN` clause is used to combine rows from two or more tables, based on a related column between them.
- Figure:
  ![[joins.png | 300]]
- Types:
1. **`INNER JOIN`**:
	- returns only the rows where there is a match in both tables based on the specified condition.
	- e.g.:
```sql
SELECT *
FROM table1
INNER JOIN table2 ON table1.column_name = table2.column_name;
```
2. **`FULL JOIN`**:
	- returns all rows when there is a match in either left or right table, if there is no match, `NULL` values are returned for columns from the table without a match.
3. **`LEFT JOIN`**:
	- returns all rows from the left table and the matching rows from the right table.
	- If there is no match, `NULL` values are returned for columns from the right table.
```SQL
SELECT *
FROM table1
LEFT JOIN table2 ON table1.column_name = table2.column_name;
```
4. **`RIGHT JOIN`**:
	- returns all rows from the right table and the matching rows from the left table.
	- If there is no match, `NULL` values are returned for columns from the right table.
```SQL
SELECT *
FROM table1
RIGHT JOIN table2 ON table1.column_name = table2.column_name;
```
# 3.6 Queries under DDL and DML Commands
```sql
CREATE TABLE table_name(
	attribute1 INT PRIMARY KEY,
	attribute2 VARCHAR(50),
	attribute3 DECIMAL(10,2)
	attribute4 DATE
);

ALTER TABLE table_name
ADD COLUMN attribute4 INT

DROP TABLE table_name

INSERT INTO table_name (attribute1, attribute2, attribute3, attribute 4)
VALUES (1, '2', '3.4', '2021-04-07')

SELECT * FROM table_name WHERE attribute1 = 1;

UPDATE table_name
SET attribute2 = 'value' WHERE attribute1 = 1;

DELETE FROM table_name
WHERE attribute1= 1;

GRANT SELECT, INSERT ON table_name
TO user1, user2

REVOKE UPDATE, DELETE ON table_name
FROM user3;
```
# 3.7 Embedded SQL
- Concept:
	- Embedded SQL is a method of integrating standard SQL statements directly within a general-purpose programming language like C, C++, or COBOL.
- Working:
	- the SQL code is embedded within the host programming language.
	- A special precompiler (or preprocessor) scans the source file.
	- It identifies the embedded SQL statements (usually prefixed with `EXEC SQL`) and converts them into appropriate function calls in the host language.
	- The resulting pure host language code is then compiled normally.
- Key Components:
	- `EXEC SQL`: Prefaces every SQL statement to distinguish it from host language code
	- `HOST Variables`: Program variables used to transfer data between the application and the database. They are prefixed with a colon (`:`) within SQL statements.
	- SQLCA (SQL Communication Area): A data structure used for error handling and obtaining status information after SQL statement execution.
- Example:
```c
#include <stdio.h>
#include <stdlib.h>
#include <sqlca.h>

EXEC SQL BEGIN DECLARE SECTION;
	int student_id;
	char student_name[50];
	float student_gpa;
EXEC SQL END DECLARE SECTION;

int main() {
	EXEC SQL CONNECT :username IDENTIFIED BY :password;
	
	if (sqlca.sqlcode != 0) {
		printf("Connection failed!\n");
		exit(1);
	}
	printf("Connected to database.\n");
	
	EXEC SQL DECLARE student_cursor CURSOR FOR
		SELECT id, name, gpa FROM students WHERE gpa > 3.5;
	
	EXECT SQL OPEN student_cursor;
	
	if (sqlca.sqlcode != 0) {
		printf("Failed to open cursor.\n");
		exit(1);
	}
	
	printf("High-performing students:\n");
	printf("ID\t Name\t GPA\n");
	
	while (1) {
		EXEC SQL FETCH student_cursor INTO: student_id, :student_name, :student_gpa;
		
		if (sqlca.sqlcode == 1403) { // Oracle's no data found code
			break;
		}
		
		printf("%d\t %s\t %.2f\n", student_id, student_name, student_gpa);
	}
	
	EXEC SQL CLOSE student_cursor;
	EXEC SQL COMMIT WORK RELEASE;
	
	printf("Done.\n");
	return 0;
}
```
# 3.8 Views
- A view is a virtual table that is based on the result of a `SELECT` query.
- unlike a physical table, a view does not store the actual data itself.
- instead it is a saved query that can be referenced as if it were a table.
- useful for simplifying complex queries, encapsulating business logic, providing a layer of security by restricting access to specific columns or rows or table.
## A. Updateable and Non-updatable View
- Updateable views allow modifications (insert, updates, deletes) to the underlying base tables through the view, while non-updatable views restrict such operations.
- To be updatable, a view must:
	- be based on only one table (no joins).
	- include all required columns (especially NOT NULL columns with no default).
	- Avoid aggregations (`SUM()`, `AVG()`, grouping, `DISTINCT`, or window functions).
	- avoid set operations (`UNION`, `INTERSECT`, etc).
	- Not include sub-queries in the `SELECT` list
## B. Operations
### B.1 Create
```sql
CREATE VIEW view_name AS
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```
### B.2 Use
```sql
SELECT * from view_name
```
### B.3 Drop
```sql
DROP VIEW view_name
```
# 3.9 Relational Algebra
- theoretical query language used to query and manipulate data in a relational database.
- uses mathematical set operations to operate on relations (tables).
### Basic Operations in Relational Algebra
| Operation         | Symbol   | Description                                    |
| ----------------- | -------- | ---------------------------------------------- |
| Selection         | $\sigma$ | Filters row (horizontal)                       |
| Projection        | $\pi$    | Selects columns (vertical)                     |
| Union             | $\cup$   | Combines two relations                         |
| Set Difference    | -        | Returns tuples in one relation but not another |
| Cartesian Product | x        | Combines all tuples (cross product)            |
| Rename            | $\rho$   | Renames relation or attributes                 |
### Derived Operations
| Operation    | Symbol    | Description                                |
| ------------ | --------- | ------------------------------------------ |
| Join         | $\bowtie$ | combines related tuples from two relations |
| Intersection | $\cap$    | Common tuples in both relations            |
| Division     | $\div$    | Special case query like "for all"          |
# 3.11 Numerical Q/A
### 1. (`81 Bh`)
#### Given:
`Employee(empid, ename, address, title, headid)`
`Project(pid, pname, budget, location)`
`Work(empid, pid, responsibility, duration)`
`Payment(title, salary)`
#### Q/A.
1. Write SQL Code to create the above relational schema.
```sql
CREATE TABLE Employee (
	empid INT PRIMARY KEY,
	ename VARCHAR(50),
	address VARCHAR(100),
	title VARCHAR(50),
	headid INT,
	FOREIGN KEY (headid) REFERENCES Employee(empid)
);

CREATE TABLE Project (
	pid INT PRIMARY KEY,
	pname VARCHAR(50),
	budget DECIMAL(12,2),
	location VARCHAR(50)
);

CREATE TABLE Work (
	empid INT
	pid INT
	responsibility VARCHAR(50),
	duration INT, -- in years
	PRIMARY KEY (empid, pid),
	FOREIGN KEY (empid) references Employee(empid),
	FOREIGN KEY (pid) references Project(pid)
);

CREATE TABLE Payment (
	title VARCHAR(50) PRIMARY KEY,
	salary DECIMAL(10,2)
)
```
2.  Write SQL to count the number of project with duration more than 2 years.
```sql
SELECT COUNT(*)
FROM Work
WHERE duration > 2;
```
3. Write SQL query to find the name of engineers working in ICT project and earning more than 20K.
```sql
SELECT DISTINCT e.ename
FROM Employee e
JOIN Work w ON e.empid = w.empid
JOIN Project p ON w.pid = p.pid
JOIN Payment pm on e.title = pm.title
WHERE p.pname = 'ICT'
	AND pm.salary > 20000
	AND e.title = 'Engineer';
```
4. Write SQL to update the salary of employees by 15\% if salary is less than 50K, by 10\% if salary is in between 50K and 100K and by 5\% if greater than 100K.
```sql
UPDATE Payment
SET salary = CASE
	WHEN salary < 50000 THEN salary * 1.15
	WEHN salary BETWEEN 50000 AND 100000 THEN salary * 1.10
	ELSE salary * 1.05
END;
```  
### 2. (81 Bh, 80 Ch)
#### Given:
`Employee(empid, ename, address, title, headid)`
`Project(pid, ename, budget, location)`
`Work(empid, pid, responsibility, duration)`
#### Q/A:
1. Write relational algebra to find the name and salary of employees working in `kathmandu`
$$
\pi\ ename,\ salary (\sigma\ location\ =\ 'kathmandu' (Project \bowtie Work \bowtie Employee \bowtie Payment ))
$$
2. Write the relational algebra to show the employee name along with their head name
$$
\pi\ e1.ename,\ e2.ename (\rho\ e1(Employee) \bowtie e1.headid = e2.empid\ \rho\ e2(Employee))
$$
3. Write relational algebra to display the details of those employees who live in the same location of their project.
$$
\pi\ Employee.*\ (\sigma\ Employee.address\ =\ Project.location\ (Employee\ \bowtie\ Work\ \bowtie\ Project))
$$
 4. Write relational algebra to display employee's title, name along with their project name, salary if the project duration is more than 5 years.
$$
\pi\ title,\ ename,\ pname,\ salary\ (\sigma\ duration>5\ (Employee\ \bowtie\ Work\ \bowtie\ Project\ \bowtie\ Payment))
$$
### 3. (80 Ch)
#### Given:
`PERSON(licenseNo, name, address)`
`CAR(modelNo, brand, year)`
`ACCIDENT(reportNo, date, location)`
`OWNS(licenseNo, modelNo)`
`PARTICIPATED(licenseNo, reportNo, damageAmount)`
#### Q/A:
1. Display all the detail of a Person whose name ends with `ta` and is involved in some accident.
```sql
SELECT DISTINCT p.*
FROM PERSON p
JOIN PARTICIPATED pt ON p.licenseNo = pt.licenseNo
WHERE p.name LIKE '%ta';
```
2. Display the license numbers and location where the accident took place on Jan 20, 2020.
```sql
SELECT pt.licenseNo, a.location
FROM ACCIDENT a
JOIN PARTICIPATED pt ON a.reportNo = p.reportNo
WHERE a.date = '2020-01-20'
```
3. Update the brand name "BMW" to "BMW-X" for car manufactured in year 2020.
```sql
UPDATE CAR
SET brand = 'BMW-X'
WHERE brand = 'BMW'
	AND year = 2020;
```
   4. Create a view named PERSON\_REPORT which contains licenseNo, Name and reportNo as its members where the damage amount is less than or equal to 100,000.
```sql
CREATE VIEW PERSON_REPORT AS
SELECT p.licenseNo, p.name, pt.reportNo
FROM PERSON p
JOIN PARTICIPATED pt ON p.licenseNo = pt.licenseNo
WHERE pt.damageAmount <= 100000;
```
### 4.  (79 Ch)
#### Given:
`Doctor(name, age, address)`
`Works (name, dept_no)`
`Department (dept_no, floor, room)`
#### Q/A
1. To display the records of doctor with their department information.
```sql
SELECT d.*, w.dept_no, dt.floor, dt.room
FROM Doctor d
JOIN Works w ON d.name = w.name
JOIN Department dt ON w.dept_no = dt.dept_no
```
2. To find total number of rooms assigned in each floor.
```sql
SELECT floor, COUNT(room) as total_rooms
FROM Department
GROUP By Floor;
```
3. To display the name of doctor with maximum age.
```sql
SELECT name
FROM Doctor
WHERE age = (
	SELECT MAX(age) FROM Doctor
);
```
 4. To delete the records of doctors whose name start with 'M' and works in 10$^{th}$ floor.
```sql
DELETE FROM Doctor
WHERE name LIKE 'M%'
AND name IN (
	SELECT d.name
	FROM Doctor d
	JOIN Works w ON d.name = w.name
	JOIN Department dep ON w.dept_no = dep.dept_no
	WHERE dep.floor = 10
);
```
### 5. (79 Ch)
#### Given:
`Client (Cid, Lid, Cname, birthYear, caseField)`
`Lawyer (Lid, Fname, Lname, Specialty, Salary, startingYear)`
`Firm (Fname, City, managerName)`
#### Q/A:
1. Write relational algebra expression to find names of clients that were born after 1980 and their case field was "Traffic"
$$
\pi\ Cname\ (\sigma\ birthYear > 1980\ \wedge\ caseField=``Traffic" (Client))
$$
2. Write relational algebra expression to find names of clients who were presented only by lawyers whose salary is at 8000.
$$
\pi\ Cname (Client) - \pi\ Cname\ ((\pi\ Cid\ (Client)\ \times\ \pi\ Lid\ ( (\sigma\ Salary=8000 (Lawyer))) -\pi\ Cid,\ Lid\ (Client))
$$
3. Write relational algebra expression to find all pairs of client id and firm name such that no lawyer of that law firm presented this client and this client had some case in a field other than "Divorce".
$$
\pi\ Cid,\ Fname\ ((\pi\ Cid\ (\sigma\ caseFiled\ ≠\ ``Divorce"\ (Client))\ \times\ \pi\ Fname\ (Firm)) - \pi\ Cid,\ Fname\ (Client\ \bowtie\ Lawyer\ \bowtie\ Firm))
$$
### 6. (76 Ba)
#### Given:
`Emp (Eid, Ename, Address, Salary, Dptid)`
`Depart (Dptid, Dname)`
#### Q/A:
1. Insert a single record in Emp table (100, `Ram', `Balaju', 10000, 5)
$$
Emp \leftarrow\ Emp\ \cup\ \{(100,\ 'RAM',\ 'Balaju',\ 10000,\ 5) \}
$$
2. Retrieve the record of employee who earns more than 10000 in computer department.
$$
\pi\ Emp.*\ (\sigma\ Salary > 10000\ \wedge\ Dname =\ 'Computer',\ (Emp \bowtie\ Department))
$$
3. Increase the salary of all employee by 10 percent.
$$
Emp \leftarrow\ \pi\ Eid,\ Ename,\ Address,\ Salary*1.10,\ Dptid (Emp)
$$
 4. Delete all the record of employee who are from ELX department. (Dptid = ``ELX")
 $$
Emp \leftarrow\ Emp\ - \sigma\ Dptid\ =\ 'ELX'\ (Emp)
$$
### 7. (75 Bh)
#### Given:
`Student (crn, name, address, phone, dob)`
`Course (courseid, crn, duration, fee)`
`Enroll (enrollid, cname, courseid, enrolldate, completedate)`
#### Q/A:
1. Create the above relations, including appropriate versions of all primary and foreign key integrity constraints
```sql
CREATE TABLE Student (
	crn INT PRIMARY KEY,
	name VARCHAR(50),
	address VARCHAR(50),
	phone VARCHAR(15),
	dob DATE
);

CREATE TABLE Course (
	courseid INT PRIMARY KEY,
	crn INT,
	duration INT, -- in months
	fee DECIMAL(8,2)
	FOREIGN KEy crn REFERENCES Student(crn)
);

CREATE TABLE Enroll (
	enrollid INT PRIMARY KEY,
	cname VARCHAR(50),
	courseid INT,
	enrolldate DATE,
	completedate DATE
);
```
2. to find crn, names and enroll date of all students who have taken the course `java' 
```sql
SELECT s.crn, s.name, e.enrolldate
FROM Student s
JOIN Course c ON s.crn = c.crn
JOIN Enroll e ON c.courseid = e.courseid
WHERE e.cname = 'java';
```
  3. find the names and address of all the students who have taken both course java and linux.
```sql
SELECT s.name, s.address
FROM Student s
WHERE s.crn IN (
	SELECT c.crn
	FROM Course c
	JOIN Enroll e ON c.courseid = e.courseid
	WHERE e.cname = 'java'
		AND e.cname = 'linux'
);
```
   4. create a view 'student\_course' having the attributes crn, name, phone, coursename, enrolldate.
```sql
CREATE VIEW student_course AS
SELECT s.crn, s.name, s.phone, e.coursename, e.enrolldate
FROM Student s
JOIN Course c ON s.crn = c.crn
JOIN Enroll e ON c.courseid = e.courseid;
```
### 8. (75 Bh)
#### Given:
`sailor (sailorid, sname, rating, age)`
`boat (boatid, boatname, color)`
`reserves (sailorid, boatid, date)`
#### Q/A:
1. find the names of sailor who has reserved boat number 105.
$$
\pi\ sname\ (\sigma\ boatid=105\ (sailor \bowtie\ reserves))
$$
2. find the names of sailors who have reserved a red boat.
$$
\pi\ sname\ (\sigma\ color\ =\ 'red'\ (sailor\ \bowtie\ reserves \bowtie\ boat))
$$
3. find the names of all sailor who have reserved either a red boat or a green boat.
$$
\pi\ sname\ (\sigma\ color\ =\ 'red'\ (sailor\ \bowtie\ reservers\ \bowtie\ boat))
$$
$$
\cup
$$
$$
\pi\ sname\ (\sigma\ color\ =\ 'green'\ (sailor\ \bowtie\ reserves\ \bowtie\ boat))
$$
### 9. (74 Bh)
#### Given:
`Employee (empid, ename, age, salary)`
`Department (deptid, dname, budget, managerid)`
`Works (empid, deptid, hours)`
#### Q/A:
1. to create the above relations, including appropriate versions of all primary and foreign key integrity constraints.
```sql
CREATE TABLE Employee (
	empid INT PRIMARY KEY,
	ename VARCHAR(50),
	age INT,
	salary DECIMAL(8,2),
);

CREATE TABLE Department (
	deptid INT PRIMARY KEY,
	dname VARCHAR(50),
	budget DECIMAL(10,2),
	managerid INT,
	FOREIGN KEY managerid REFERENCES Employee(empid),
);

CREATE TABLE Works (
	empid INT,
	deptid INT,
	hours INT,
	FOREIGN KEY deptid REFERENCES Department(deptid),
	FOREIGN KEY empid REFERENCES Employee(empid),
	PRIMARY KEY (empid, deptid)
);
```
2. to find the name of department whose employee earns the maximum salary.
```sql
SELECT d.name
FROM Department d
JOIN Works w ON d.deptid = w.deptid
JOIN Employee e ON w.empid = e.empid
WHERE e.salaray = (
	SELECT MAX(salary) from Employee
);
```
3. to find the name of the employee, department name and the number of hours, they work.
```sql
SELECT e.name, d.dname, w.hours
FROM Employee e
JOIN Works ON e.empid = w.empid
JOIN Department d ON w.deptid = d.deptid;
```
4. Write an expression in SQL to give every employee a 20\% raise in salary whose age is in between 45 to 50 years
```sql
UPDATE Employee
SET salary = salary * 1.20
WHERE age BETWEEN 45 AND 50;
```
### 10. (74 Bh) 
#### Given:
`Account (account-number, branch-name, balance)`
`Branch (branch-name, branch-city, assets)`
`Customer (cust-name, cust-street, cust-city)`
`Loan (loan-number, branch-name, amount)`
`Depositor(cust-name, account-number)`
#### Q/A:
1. find the names of customers who has loan at ``Koteshwor" branch.
$$
\pi\ cust-name\ (\sigma\ branch-name\ =\ 'Koteshwor'\ (Customer\ \bowtie\ Loan))
$$
2. find the largest account balance.
$$
\pi\ balance\ (Account)\ -\ \pi\ A1.balance\ (\sigma\ A1.balance < A2.balance\ (\rho\ A1\ (Account)\ \times\ \rho\ A2\ (Account)))
$$
3. find the names of all depositers along with their account number, street and city address.
$$
\pi\ \text{cust-name, account-number, cust-street, cust-city}\ (Depositor \bowtie\ Customer)
$$
### 11. (81 Ba)
#### Given:
`department(dept name, building, budget)`
`Course(course id, title, dept_name, credits)`
`instructor(ID, name, dept_name, salary)`
`section(course id, sec id, semester, year, building, room_number, time_slot_id)`
`teaches(ID, name, dept_name, total_credit)`
#### Q/A:
1. Add a check constraint in semester attribute. [winter, fall, spring, summer] of table "section".
```sql
ALTER TABLE section
ADD CONSTRAINT semester
CHECK (semester IN ('winter', 'fall', 'spring', 'summer'));
```
2. Find the names of all instructors that have a salary value greater than that of each instructor in the Computer department.
```sql
SELECT name 
FROM instructor 
WHERE salary > ALL (
    SELECT salary 
    FROM instructor 
    WHERE dept_name = 'Computer'
);
```
3. Find the list of the entire department relation in descending order of budget. If several departments have the same budget, order them in ascending order by department name.
```sql
SELECT * 
FROM department 
ORDER BY budget DESC, dept_name ASC;
```
4. Delete the records of all instructors with salary below the average at the university.
```sql
DELETE FROM instructor 
WHERE salary < (SELECT AVG(salary) FROM instructor);
```
5. Update all instructors with salary over 10,00,000 receive a 5 percent raise, whereas all other receive a 10 percent raise.
```sql
UPDATE instructor 
SET salary = CASE 
    WHEN salary > 1000000 THEN salary * 1.05
    ELSE salary * 1.10
END;
```
### 12.  (80 Bh, 68 Bh)
#### Given:
`employee(employee name, street, city)`
`works(employee name, company_name, salary)`
`company(company name, city)`
`manages(employee name, manager name`
#### Q/A:
##### SQL:  
1. Find the names and cities of residence of all employees who work for the First Bank Corporation. 
```sql
SELECT e.employee_name, e.city
FROM employee e
JOIN works w ON e.employee_name = w.employee_name
WHERE w.company_name = 'First Bank Corporation';
```
2. Find the names, street addresses, and cities of residence of all employees who work for First Bank Corporation and earn more than \$10,000 and employee name and must not start with the letter "z".
```sql
SELECT e.employee_name, e.street, e.city
FROM employee e
JOIN works w ON e.employee_name = w.employee_name
WHERE w.company_name = 'First Bank Corporation'
  AND w.salary > 10000
  AND e.employee_name NOT LIKE 'z%';
```
3. Find all employees in the database who do not work for First Bank Corporation.
```sql
SELECT employee_name
FROM employee
WHERE employee_name NOT IN (
    SELECT employee_name 
    FROM works 
    WHERE company_name = 'First Bank Corporation'
);
```
4. Find all employees in the database who earn more than each employee of Small bank Corporation.
```sql
SELECT employee_name
FROM works
WHERE salary > ALL (
    SELECT salary 
    FROM works 
    WHERE company_name = 'Small Bank Corporation'
);
```
5. Assume that the companies may be located in several cities. Find all companies located in every city in which Small Bank Corporation is located.
```sql
SELECT company_name
FROM company c1
WHERE NOT EXISTS (
    SELECT city 
    FROM company 
    WHERE company_name = 'Small Bank Corporation'
    EXCEPT
    SELECT city 
    FROM company c2 
    WHERE c2.company_name = c1.company_name
);
```
6. Find the company that has the most employees.
```sql
SELECT company_name
FROM company c1
WHERE NOT EXISTS (
    SELECT city 
    FROM company 
    WHERE company_name = 'Small Bank Corporation'
    EXCEPT
    SELECT city 
    FROM company c2 
    WHERE c2.company_name = c1.company_name
);
```
##### RAE:
1. Find the names and street address of all employees who work for First Bank Corp. and earn more than \$10,000 per annum.
$$
\pi\ employee\_name,\ street\ (\sigma\ company\_name\ =\ 'First Bank Corporation' \wedge\ salary>10000\ (employee\ \bowtie\ works))
$$
2. Find the names of all employees who do not work for First Bank Corp.
$$
\pi\ employee\_name\ (\sigma\ company\_name\ ≠\ '\text{First\ Bank\ Corporation}'\ (employee\ \bowtie\ works) )
$$
3. Give all employees working at First Bank Corp. a 10\% salary raise.
$$
works ← (π\ employee\_name,\ company\_name,\ salary×1.10\ (σ\ company\_name='First Bank Corporation' (works)))
$$
$$
∪
$$
$$
(π\ employee\_name,\ company\_name,\ salary\ (σ\ company\_name\ ≠\ \text{'First Bank Corporation'}\ (works)))
$$
4. Count the number of employees in each company.
$$
γ\ company\_name;\ COUNT(employee_name)\ →\ employee\_count (works)
$$