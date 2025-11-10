# 4.1 Integrity Constraints and Domain Constraints
- Rules that ensure the accuracy, consistency, and reliability of data in a relational database.
- They act as safeguards to prevent invalid data entry and maintain data quality.
## A. Integrity Constraints:
1. Entity Integrity Constraint
	- Concept:
		- Ensures each row in a table is uniquely identifiable.
		- The primary key must be unique and cannot contain NULL values.
	- Guarantees that every entity can be uniquely retrieved and referenced
	- e.g., In a `Students` table, `StudentID` as a primary key must be unique for each student and cannot be empty.
2. Referential Integrity Constraint
	- Concept:
		- Ensures that a foreign key value in one table must either match a primary key value in another table or be NULL.
	- Maintains consistency between related tables and prevents orphaned records.
	- e.g., In an `Orders` table, the `CustomerID` (foreign Key) must exist in the `Customers` table's `CustomerID` (primary key).
	- Cascading Actions:
		- When a referenced primary key is updated or deleted, cascading actions define what happens to the matching foreign key records:
		- `CASCADE`:
			- `ON DELETE CASCADE`: If a parent record is deleted, all related child records are automatically deleted.
			- `ON UPDATE CASCADE`: If a parent primary key is updated, all foreign keys are automatically updated
			- e.g., Delete a `Department` record -> all `Employees` in that department are also deleted.
		- `SET NULL`:
			- When a parent record is deleted/updated, the foreign key in child records is set to NULL.
			- e.g., Delete a `Manger` -> The `manager_id` in the `Employees` table is set to NULL
		- `SET DEFAULT`:
			- The foreign key is set to a predefined default value when the parent is deleted/updated
		- No actions/`RESTRICT`:
			- Prevents the operation if child record exist (default behaviour).
3. Domain Integrity Constraint
	- Concept:
		- Ensures that all values in a column are valid according to defined criteria for that data domain.
	- Validates data at the column level before it is entered into the database.
4. Key Constraint
	- Primary Key: Unique + NOT NULL identifier for a table
	- Unique Constraint: Ensures uniqueness but allows NULL values
	- Foreign Key: Establishes relationships between tables.
5. Other Important ones
	- Default Constraint: Provides a default value when no value is specified
	- Check Constraint: Custom validation rules for column values
## B. Domain Constraints
- Specifically focus on what values are permissible in individual columns.
- Types:
1. Data Type Constraints
```sql
Age INT          -- Only integers allowed
Name VARCHAR(50) -- Only strings up to 50 characters allowed
```
2. Range Constraints
```sql
CHECK (Salary BETWEEN 30000 AND 150000)
CHECK (Age >= 0 AND Age <= 120)
```
3. Format Constraints
```sql
CHECK (EMAIL LIKE '%_@_%._%') -- regex for email format
CHECK (Phone LIKE '[0-9][0-9][0-9][0-9][0-9][0-9][0-9]') -- landline no. of 7 digits
```
4. Enumerated Constraints
```sql
CHECK (Status IN ('Active', 'Inactive', 'Pending'))
CHECK (Grade IN ('A', 'B', 'C', 'D','F'))
```
5. NOT NULL Constraints
```sql
StudentName VARCHAR(100) NOT NULL
```
## C. Example:
```sql
CREATE TABLE Employees (
    -- Entity Integrity
    EmployeeID INT PRIMARY KEY,
    
    -- Domain Constraints
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE,
    Age INT CHECK (Age >= 18 AND Age <= 65),
    Salary DECIMAL(10,2) CHECK (Salary BETWEEN 30000 AND 150000),
    DepartmentID INT,
    Status VARCHAR(20) DEFAULT 'Active' CHECK (Status IN ('Active', 'Inactive', 'Leave')),
    JoinDate DATE DEFAULT GETDATE(),
    
    -- Referential Integrity with Cascading
    FOREIGN KEY (DepartmentID) REFERENCES Departments(DepartmentID)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);
```
----
# 4.2 Assertions and Triggering
## A. Assertions
- Concept:
	- Declarative conditions that define a state that the entire database must always satisfy.
	- They are used to enforce complex business rules that span multiple tables or involve aggregate conditions.
- Characteristics:
	- Expected to be true at all times.
	- The DBMS automatically checks them whenever a relevant transaction occurs.
	- A violation indicates a fundamental data integrity issue.
- Note: 
	- While part of SQL standard, assertions are not widely supported by modern database management systems (e.g., not in MySQL or PostgreSQL).
	- Their functionality is often implemented using triggers.
- Syntax:
```sql
CREATE ASSERTION <assertion_name>
CHECK (<condition>);
```
- `<condition>`: This is a search condition that can involve sub-queries, aggregates, and reference to multiple tables.
- The entire condition must evaluate to `TRUE` for the assertion to be satisfied.
- Example: Ensuring total project budget never exceeds allocated amount of department.
```sql
CREATE ASSERTION BudgetCheck
CHECK (
	(SELECT SUM(budget) FROM Project) <= (SELECT total_budget FROM Department)
);
```
## B. Triggers
- Concept:
	- A trigger is a special type of stored procedure that is automatically executed (i.e. "triggered") in response to specific data modification events (`INSERT`, `UPDATE`, `DELETE`) on specified table or view.
- Purpose:
	- Used to enforce complex business rules, maintain derived data (audit trails).
	- To implement referential integrity actions beyond standard cascading and send notifications.
- Trigger Components:
	- The core component of a trigger are defined using the `CREATE TRIGGER` statement.
	- While syntax varies by DBMS, the logical structure is consistent.
- Syntax Outline:
```sql
CREATE TRIGGER <trigger_name>
{BEFORE | AFTER | INSTEAD OF} {INSERT | UPDATE | DELETE}
ON <table_name>
[FOR EACH ROW | FOR EACH STATEMENT]
[WHEN (<condition>)]
<trigger_body>;
```
- Components:
	- Timing: `BEFORE | AFTER | INSTEAD OF`
		- `BEFORE`: Executed before the triggering event. Often used for data validation, modification or to prevent an invalid operation
		- `AFTER`: Executed after the triggering event. Commonly used for logging, updating summary tables or complex cascading actions
		- `INSTEAD OF`: Primarily used on views to allow updates to non-updatable views. The trigger code runs instead of the original DML operation.
	- Granularity: `FOR EACH ROW | FOR EACH STATEMENT`
		- Row-level Trigger (`FOR EACH ROW`): The trigger body executes once for every affected row.
		- Statement-level Trigger (`FOR EACH STATEMENT`): The trigger body executes only once for the entire DML statement, regardless of how many rows are affected.
- Example: trigger for logging any change in salary.
```sql
CREATE TRIGGER log_salary_change
	AFTER UPDATE OF salary ON Employee
	FOR EACH ROW
	WHEN (OLD.salary IS DISTINCT FROM NEW.salary)
	EXECUTE FUNCTION log_salary_audit();
    
-- here, we need to create log_salary_audit() as the trigger function
CREATE OR REPLACE FUNCTION log_salary_audit()
RETURNS TRIGGER AS $$
BEGIN
	INSERT INTO SalaryAudit (empid, old_salary, new_salary, changed_by)
	VALUES (NEW.empid, OLD.salary, NEW.salary, CURRENT_USER);
	RETURN NEW; -- for an AFTER trigger, the returned value is usually ignored.
END;
$$ LANGUAGE plpgsql;
```
## C. Risk of Triggers
When Triggers are Risky:
1. Hidden Logic and Complexity: Triggers execute implicitly. A developer performing a simple `UPDATE` might not be aware of a trigger that fires and performs other operations, making debugging difficult
2. Performance Overload: Poorly designed triggers, especially row-level triggers on tables with high transaction volumes, can significantly slow down DML operations.
3. Cascading Triggers: A trigger on Table A can update Table B, which has a trigger that updates Table C, which might even update Table A again. This can lead to infinite loops and unpredictable behaviour if not carefully managed.
4. Maintenance Challenger: The business logic is scattered between application code and database triggers, making the system harder to understand and maintain.
# 4.3 Functional Dependencies



1. define functional dependence
2. What is decomposition
3. desirable properties of decomposition
4. partial dependency definition
5. transitive functional dependency definition + example
6. what is closure of functional dependency + example
# 4.4 Multi-valued and Joined Dependencies


1. lossless decomposition and dependency preservation definitions
2. lossless join decomposition definition
3. Explain the conditions to be satisfied for lossless decomposition using FD sets
4. define multi-valued dependency
5. Differentiate primary key and foreign key
6. Explain trivial and non-trivial dependencies. Explain BCNF
7. Explain the necessary condition for decomposing a relational database table into two tables.
# 4.5 Different Normal Forms