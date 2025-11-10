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
# 4.2 Assertions and Triggering

2. define assertion with its SQL syntax
3. what is trigger
4. working of trigger, syntax and example
5. safe or risky to use triggers
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