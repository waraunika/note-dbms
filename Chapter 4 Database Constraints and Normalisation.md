(6 Hours / 12 Marks)
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
----
# 4.3 Functional Dependencies
### A. Concept
- A constraint that describes a relationship between attributes in a relation.
- It exists when the value of one set of attributes (the determinant) uniquely determines the values of another set of attributes (the dependent)
- Notation: X -> Y
	- X is the determinant
	- Y is the dependent
	- Read as: "X functionally determines Y" or "Y is functionally dependent on X"
### B. Rules for Finding them
- A Functional Dependency X -> Y holds in a relation if, for every unique combination of values in X, there is exactly one corresponding value in Y.
- i.e., cannot have two rows with the same X value but different Y values.
- Example: `Employees (EID, Name, Department, Salary`
	- Valid FD: `EID` -> `Name`
		- Each employee ID is associated with exactly one name.
	- Valid FD: `EID` -> `{Name, Department, Salary}`
		- the `EID` uniquely identifies the entire tuple.
	- Invalid FD: `Department` -> `Salary`
		- multiple employees in the same department can have different salaries
	- Invalid FD: `Name` -> `EID`
		- two employees can share a name, but not ID
### C. Benefits of Functional Dependency
- Data Integrity: ensures consistency by preventing the storage of contradictory data.
- Data Redundancy Reduction: By identifying dependencies, we can decompose tables to avoid storing the same information multiple times.
- Normalisation Foundation: FD is the fundamental tool for database normalisation, which is the process of organising tables to minimise redundancy and avoid data anomalies
### D. Armstrong's Axioms
Set of rules used to infer all the functional dependencies logically implied by a set of given FD's.
1. Reflexivity Rule:
	- If Y is a subset of X, then X -> Y
	- e.g., `{EID, Name}` -> `Name` is always true. 
2. Augmentation Rule:
	- If X -> Y is a valid FD, then XZ -> YZ is also valid.
	- e.g., `EID` -> `Name`, then `{EID, Department}` -> `{Name, Department}`.
3. Transitivity Rule
	- If X -> Y and Y -> Z are both valid FD's then X -> Z is also valid.
	- e.g., If `EID` -> Department and `Department` -> `Manager`, then `EID` -> `Manager`.
Additional Derived Rules:
- Union: If X -> Y and X -> Z, then X -> `YZ`.
- Decomposition: If X -> `YZ`, then X -> Y and X -> Z.
- Pseudo-transitivity: If X -> Y and `YW` -> Z, then `XW` -> Z.
### E. Types
1. Trivial Functional Dependency
	- An FD X -> Y, if Y is a subset of X.
	- holds for every possible relation instance, and does not represent a meaningful constraint.
	- e.g., `{EID, Name}` -> `Name`.
2. Non-Trivial Functional Dependency
	- An FD X -> Y, if Y is not subset of X.
	- represent genuine constraints on the data.
	- e.g., `EID` -> `Name`
3. Fully Functional Dependency
	- An FD X -> Y, if Y is fully functionally dependent on X and not dependent on any proper subset of X.
	- this is critical for defining candidate keys.
	- e.g., for `Enrollment(StudentID, CourseID, Grade)`, the primary key is `{StudentID, CourseID`. `{StudentID, CourseID}` -> `Grade` is a fully functional dependency as grade needs to be determined by both the ID's.
4. Partial Functional Dependency
	- An FD X -> Y, if Y is a partially dependent on X if it is functionally dependent on a proper subset of X.
	- indicates a normalisation problem (the relation is not in 2NF).
	- e.g., for `Enrollment(StudentID, CourseID, StudentName)`, `{StudentID, CourseID}`   -> `StudentName` holds true.
5. Transitive Functional Dependency
	- An FD X -> Z, if X -> Y and Y -> Z and Y does not determine X.
	- indicates a normalisation problem (the relation is not in 3NF).
	- Consider `Employee(EID, Department, DeptManager)`
		- `EID` -> `Department`
		- `Department` -> `DepartManager` (and `Department` does not determine `EID`)
		- Therefore `EID` -> `DeptManager`
### F. Closure of Functional Dependencies
- The closure of a set of functional dependencies `F` denotes as F$^+$ is the set of all functional dependencies that can be logically inferred from `F` using Armstrong's Axioms.
- Example:
	- Given: a relation `R(A, B, C, D, E)` and the set of FD's as:
	- `F` = {`A` -> `BC`, `CD` -> `E`, `B` -> `D`, `E` -> `A`}
	- To find, the closure of `{A, B}` i.e. {A, B}$^+$:
	- Start: result = {A, B}
	- Use `A` -> `BC`, so: result = {A, B, C}
	- Use `B` -> `D`, so: result = {A, B, C, D}
	- Use `CD` -> `E`, so: result = {A, B, C, D, E}
	- Use `E` -> `A`, so result = {A, B, C, D, E}
	- We have exhausted all FD's. and final result includes all attributes.
	- Hence, {A, B}  is a super key for R
# 4.4 Multi-valued and Joined Dependencies
## A. Multi-valued Dependency (MVD)
#### A.1 Concept:
- exists when a single value of determinant (X), there are multiple sets of values for two other attributes (Y and Z), and these sets are independent of each other.
- Notation: X ->> Y, read as "X multi-determines Y".
- Formal Definition: An MVD X ->>Y holds in a relation R, if for any two tuples t1 and t2 in R that have the same value for X (`t1[X] = t2[X]`), there must exist two other tuples `t3` and `t4` such that:
	- `t3[X] = t4[X] = t1[X] = t2[X]`
	- `t3[Y] = t1[Y]` and `t3[Z] = t2[Z]`
	- `t4[Y] = t2[Y]` and `t4[Z] = t1[Z]`
	- where Z represents all attributes in R not in X or Y
- MVD represents a situation where two independent multi-valued facts are associated with a single entity.
#### A.2 Example:
| Student | Project    | Hobby    |
| ------- | ---------- | -------- |
| Alice   | Database   | Chess    |
| Alice   | Database   | Painting |
| Alice   | Web Design | Chess    |
| Alice   | Web Design | Painting |
- Explanation:
	- MVD's: `Student` ->> `Project` and `Student` ->> `Hobby`.
	- A student works on multiple project and has multiple hobbies.
	- The projects a student works on are independent of the hobbies they have.
	- The table must contain all possible combinations of a student's projects and hobbies to correctly represent this independence.
	- If it didn't (e.g., the combination `(Alice, Web Design, Painting)` was missing), it would incorrectly imply a relationship between the specific project and hobby.
- Note:
	- Every FD is also a MVD. If X -> Y, then X ->> Y automatically holds
## B. Join Dependency
- Concept:
	- A JD exists when a relation can be recreated without loss of information by joining several of its projections (sub-relations).
	- it is a generalisation of MVD.
- A relation `R` with attributes `(A, B, C, D)` satisfies the join dependency `*(R1, R2, ..., Rn)` if `R` is equal to the natural join of its projects `R1, R2, R3, ..., Rn`.
#### B.1 Lossless Join Dependency
- A decomposition of a relation `R` into a set of relations `{R1, R2, ..., Rn}` is a lossless join decomposition if the natural join of `R1, R2, ..., Rn` produces the exact original relation R.
- No false/fake tuples are generated, and no original tuples are lost.
- it is a fundamental requirement for any database decomposition.
- it is the only way to recover original information from multiple relations.
##### B.1.a Example:
- Consider relation `Employee(EmpID, Name, DeptID, DeptName` with FD: `DeptId` -> `DeptName`
- This relation suffers from update anomalies due to the FD. We can losslessly decompose it into:
	- `R1(EmpID, Name, DeptId`
	- `R2(DeptID, DeptName)`
- The natural join between `R1` and `R2` will perfectly reconstruct the original `Employee` relation without any fake tuples. This is a lossless join.
##### B.1.b Conditions for Lossless Join Decomposition
- For a decomposition of `R` into `R1` and `R2` to be lossless, the intersection of `R1` and `R2` must be a super key for atleast one of the decomposed relations (`R1` or `R2`)
- Formal Condition:
	- The decomposition `{R1, R2}` is a lossless join decomposition if at least one of the following functional dependencies holds in the closure F$^+$ of the FD set F:
		- $(R1\ \cap\ R2)$ -> $(R1 - R2)$, or
		- $(R1\ \cap\ R2)$ -> $(R2 - R1)$.
	- In simpler terms, the common attributes must functionally determine all the attributes of atleast one of the tables involved in the decomposition.
#### B.2 Lossy Join Dependency
- A decomposition is lossy if the natural join of the resulting relations produce extra, fake tuples that were not in the original relation, or fails to recreate original tuples.
- A lossy join leads to incorrect and misleading query results, as it creates information that never existed.
#####  Example:
- Consider the same relation `Employee(EmpID, Name, DeptID, DeptName` but decomposed incorrectly as:
	- `R1(EmpID, Name)`
	- `R2(DeptID, DeptName)`
- The natural join of `R1` and `R2` is a Cartesian product because there is no common attribute to join on.
- It will pair every employee with every department creating a huge number of fake and incorrect tuples.
- This is a lossy join.
# 4.5 Different Normal Forms
- Purpose: A systematic process of organising data in a database to:
	- Eliminate Data Redundancy: Avoid storing the same data in multiple places.
	- Minimise Data Anomalies: Prevent Insertion, Deletion and Update anomalies.
	- Ensure Data Integrity and Dependency.
## A. First Normal Form (1NF)
- A table is in 1NF if it contains only atomic (indivisible) values and each column contains values of a single type.
- There should be no repeating groups or array.
- **Rule**: Every attribute must contain only a single value from its domain.
#### Example:
| StudentID | Name  | Courses                 |
| --------- | ----- | ----------------------- |
| 101       | Alice | Maths, Physics          |
| 102       | Bob   | Chemistry               |
| 103       | Carol | Biology, Maths, English |
**Violation**: The `Courses` Column contains multiple values (a repeating group).
**Solution**: Flatten the table so each course is in its own row.

| StudentID | Name  | Course    |
| --------- | ----- | --------- |
| 101       | Alice | Maths     |
| 101       | Alice | Physics   |
| 102       | Bob   | Chemistry |
| 103       | Carol | Biology   |
| 103       | Carol | Maths     |
| 103       | Carol | English   |
## B. Second Normal Form (2NF)
- A table is in 2NF if it is in 1NF and every non-prime attribute is fully functionally dependent on the entire primary key.
- **Rule**: There should be no Partial Dependency (a non-key attribute depending on only part of a composite).
#### Example:
| StudentID | CourseID | CourseName | Instructor | Grade |
| --------- | -------- | ---------- | ---------- | ----- |
| 101       | CSE101   | Databases  | Dr. Smith  | A     |
| 101       | MTH202   | Calculus   | Dr. Jones  | B+    |
| 102       | CSE101   | Databases  | Dr. Smith  | A-    |
- Primary Key (Composite): `{StudentID, CourseID}`
- Functional Dependencies:
	- `{StudentID, CourseID}` -> `Grade` (Full dependency ✅)
	- `CourseID` -> `{CourseName, Instructor}` (Partial dependency ❌)
- **Violation**: `CourseName` and `Instructor` are dependent only on `CourseID`, not on the full key. This causes redundancy and update anomalies
- **Solution**: Remove the partially dependent attributes into a new table.

| StudentID | CourseID | Grade |
| --------- | -------- | ----- |
| 101       | CSE101   | A     |
| 101       | MTH202   | B+    |
| 102       | CSE101   | A-    |
and

| CourseID | CourseName | Instructor |
| -------- | ---------- | ---------- |
| CSE101   | Databases  | Dr. Smith  |
| MTH202   | Caclulus   | Dr. Jones  |
## C. Third Normal Form (3NF)
- A table is in 3NF if it is in 2NF and no non-prime attribute is transitively dependent on the primary key.
- **Rule**: There should be no transitive dependence (a non-key attribute depending on another non-key attribute).
#### Example:
| StudentID | Name  | DeptID | DeptName    | DeptBuilding |
| --------- | ----- | ------ | ----------- | ------------ |
| 101       | Alice | D01    | Computer    | Tech center  |
| 102       | Bob   | D01    | Computer    | Tech Center  |
| 103       | Carol | D02    | Mathematics | Science Hall |
- Primary Key: `StudentID`
- Functional Dependencies:
	- `StudentID` -> `{Name, DeptID}`
	- `DeptID` -> `{DeptName, DeptBuilding` (Transitive Dependency occurs)
- **Violation**: `DeptName` and `DeptBuilding` depend on `DeptID`, not directly on `StudentID`. This causes redundancy and update anomalies (if we rename Computer department, we must update multiple rows to avoid data conflict)
- **Solution**: Remove the transitively dependent attribute into a new table.
Students table and Departments table

| StudentID | Name  | DeptID |
| --------- | ----- | ------ |
| 101       | Alice | D01    |
| 102       | Bob   | D01    |
| 103       | Carol | D02    |

| DeptID | DeptName    | DeptBuilding |
| ------ | ----------- | ------------ |
| D01    | Computer    | Tech Center  |
| D02    | Mathematics | Science Hall |

## D. Boyce-Codd Normal Form (BCNF)
- A table is in BCNF if it is in 3NF and for every non-trivial functional dependency X -> Y, X must be a super key.
- **Rule**: Every determinant must be a candidate key. BCNF is a stronger version of 3NF.
#### Example:
| StudentID | Course    | Instructor   |
| --------- | --------- | ------------ |
| 101       | Databases | Dr. Smith    |
| 101       | Calculus  | Dr. Jones    |
| 102       | Databases | Dr. Smith    |
| 103       | Calculus  | Dr. Williams |
- Assumptions:
	- A student can enroll in multiple Courses
	- A Course can be taught by only one instructor
- Functional Dependencies:
	- `{StudentID, Course}` -> `Instructor` (The full key determines the instructor)
	- `Course` -> `Instructor` (Course determines the instructor)
	- `Instructor` -> `Course` (Instructor determines the course)
- **Violation**: While the table is in 3NF (no transitive dependencies for non-prime attributes), the FD `Course` -> `Instructor` is a problem. Here, `Course` is a determinant but it is not a super key (it doesn't identify a row by itself). This can still cause anomalies.
- **Solution**: Decompose based on the problematic FD: `Course` -> `Instruction`. So we have two tables: `StudentInstructor` and `InstructorCourse`

| StudentID | Instructor   |
| --------- | ------------ |
| 101       | Dr. Smith    |
| 101       | Dr. Jones    |
| 102       | Dr. Smith    |
| 103       | Dr. Williams |

| Instructor   | Course    |
| ------------ | --------- |
| Dr. Smith    | Databases |
| Dr. Jones    | Calculus  |
| Dr. Williams | Calculus  |
- Now, both tables are in BCNF.
- In `StudentInstructor`, the key is `{StudentID, Instructor}`.
- In `InstructorCourse`, both `Instructor` and `Course` are candidate keys.
- Every determinant is a super key.
## E. Fourth Normal Form (4NF)
- A table is in 4NF if it is in BCNF and contains no non-trivial multi-valued dependencies (MVD) other than those arising from candidate keys.
- **Rule**: For every non-trivial MVD X ->> Y, X must be a super key.
#### Example:
| Student | Course    | Hobby    |
| ------- | --------- | -------- |
| Alice   | Databases | Chess    |
| Alice   | Databases | Painting |
| Alice   | Calculus  | Chess    |
| Alice   | Calculus  | Painting |
| Bob     | Physics   | Reading  |
- MVD's:
	- `Student` ->> `Course`
	- `Student` ->> `Hobby`
- A student's courses and hobbies are independent.
- The table stores all possible combinations, leading to redundancy.
- **Violation**: The MVD `Student` ->> `Course` exists, but `Student` alone is not a super key (it cannot uniquely identify a row). This independent multi-valued information should be separated.
- **Solution**: Decompose into two tables, each representing one of the independent multi-valued facts. So we get 2 tables: `StudentCourse` and `StudentHobbies`


| Student | Course    |
| ------- | --------- |
| Alice   | Databases |
| Alice   | Calculus  |
| Bob     | Physics   |

| Student | Hobby    |
| ------- | -------- |
| Alice   | Chess    |
| Alice   | Painting |
| Bob     | Reading  |
## F. Summary:
| Form | Solves                     | Rule Focus                     | Simple Check                                              |
| ---- | -------------------------- | ------------------------------ | --------------------------------------------------------- |
| 1NF  | Repeating<br>Groups        | Atomic Values                  | Are all values in each column single?                     |
| 2NF  | Partial<br>Dependency      | Full Functional Dependency     | Does every non-key field depend on the whole primary key? |
| 3NF  | Transitive<br>Dependency   | Non-key to Key Dependency only | Does every non-key field depend only on the primary key?  |
| BCNF | Key-based<br>Dependency    | All Determinants are Keys      | Is every "thing-that-determines" a key?                   |
| 4NF  | Multi-valued<br>Dependency | Independent MVD's.             | Are independent multi-valued facts in separate tables?    |

