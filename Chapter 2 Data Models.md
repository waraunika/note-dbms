(7 Hours / 12 Marks)
- A Data Model is a collection of conceptual tools for describing:
	- the structure of data (entities, attributes, relationships).
	- the operations that can be performed on the data.
	- the constraints that the data must follow.
- It is the architectural blueprint for a database
- It provides a standardised way to design, communicate and implement how information is organised and connected
# 2.1 Logical, Physical and Conceptual
- To manage complexity, three distinct models, reach representing a different level of abstraction, are created
# A. Conceptual Model:
- Concept:
	- high level, business oriented view.
	- describes what information the system needs to store
	- it ignores how it will be implemented
	- the "what"-model
- Purpose:
	- to capture the essential data rules and concepts from the user's perspective.
	- used to communicate between clients and analysts and database engineer.
- Example:
	- defining Entities as: `Student`, `Course`, `Professor` and 
	- Relationships as: A `Professor` teaches a `Course` to a `Student`
## B. Logical Model:
- Concept:
	- translates the conceptual model into a specific, implementation-ready structure without being tied to a particular database technology.
	- defines how the data will be logically organised.
- Purpose:
	- To create a detailed blueprint of the database.
	- specifies tables, columns, data types, keys and relationships.
	- often following a specific data model like Relational Model.
- Example:
	- defining Table `Students`.
	- with attributes (columns): `StudentID` (`INT`, `Primary Key`), `FirstName` (`VARCHAR`), `LastName` (`VARCHAR`).
	- and similar for Courses as `CourseCode` and `Title`.
## C. Physical Model:
- Concept:
	- describes the actual implementation on the storage system.
	- deals with the technical details of where and how the data is physically stored and accessed for optimal performance
- Purpose:
	- To optimise the database for specific performance, storage, and security requirements of a chosen Database Management System (DBMS) like MySQL or Oracle.
- Example:
	- Create a `B-tree index` on the `LastName` Column in the `Students` table for fast searching
	- Store the `Courses` table on a specific, high-speed disk drive.
	- The `INT` datatype will be stored with 8-bit values.
## D. Figure:
![[models.png | 720]]
****
# 2.2 E-R Model
- The Entity Relationship (E-R) Model is a conceptual data model that provides a visual representation of the logical structure of a database.
- It uses diagrams (ERD's) to illustrate **entities**, their **attributes** and their **relationships**.
- primary tool for translating business requirements into a clear database design blueprint.
## A. Core Terminology
1. Entity:
	- A real-world object or concept that can be distinctly identified.
	- like a noun.
	- e.g., `Student`, `Course`, `Professor`.
2. Attribute
	- A property or characteristic that describes an entity.
	- like, an adjective describing the noun.
	- e.g., for a `Student` entity: `StudentID`, `FirstName`, `LastName`.
3. Relationship
	- An association between two or more entities.
	- like, a verb connecting the nouns.
	- e.g., a `Student` **`Enrolls`** in a `Course`.
4. Cardinality
	- Defines the numerical relationship between entities in a relationship.
	- describes "how many?".
	- Types:
		- One-to-one (1:1):
			- One instance of Entity A is associated with at most one instance of Entity B.
			- E.g., One `Country` has one `CapitalCity`.
		- One-to-Many (1:N):
			- One instance of Entity A is associated with many instances of Entity B.
			- e.g., One `Department` employs many `Professors`.
		- Many-to-Many (M:N):
			- Many instances of Entity A are associated with many instances of Entity B.
			- e.g., Many `Students` enroll in many `Courses`.
5. Keys
	- Primary Key: A unique identifier for an entity instance (e.g., `StudentID`).
	- Foreign Key: An attribute in one table that references the Primary Key of another table to create a link.
## B. E-R Notations
| Component              | Symbol                              |
| ---------------------- | ----------------------------------- |
| Entity                 | ![[rectangle.png\| 100]]            |
| Weak Entity            | ![[c_rectangle.png \| 100]]         |
| Attribute              | ![[ellipse.png\| 100]]              |
| Key Attribute          | ![[key_attribute.png \| 100]]       |
| Composite Attribute    | ![[composite_attribute.png \| 100]] |
| Multi-valued Attribute | ![[c_ellipse.png \| 100]]           |
| Derived Attribute      | ![[derived_attribute.png \| 100]]   |
| Relationship           | ![[diamond.png \| 100]]             |
| Link (Cardinality)     | ![[line.png \| 100]]                |
****
# 2.3 Entities and Entities Sets
## A. Entity
- An entity is a single, distinguishable object or "thing" from the real world that we want to store data about.
- represented by a single record (or row) in a table.
- e.g., in a university database, a specific student named 'Alice Chain' with `StudentId = 101` is an entity.
## B. Entity Set
- An entity set is a collection of similar-type entities.
- defines the "type" or "blueprint" for the entities it contains.
- represented by a table.
- the table's schema (column headers) define the attributes common to all entities in the set.
- e.g., the `Students` table, which contains all individual student records, is an `entity` set. Every entity (row) in this set shares the same attributes (`StudentID`, `Name`, `DOB`), but each has its own unique values.
----
# 2.4 Strong and Weak Entity Sets
This concept defines whether an entity can be identified on its own or it depends on another entity.
## A. Strong Entity Set
- Concept:
	- An entity set that has its own unique key (Primary Key) and does not depend on other entity for its identification.
	- it can exist independently.
- Characteristics:
	- Has a primary key
	- Represented by a single rectangle in ERD.
- Example:
	- in `Students` entity set, a student can be identified by their `StudentID` alone.
## B. Weak Entity Set
- Concept:
	- An entity set that cannot be uniquely identified by its own attributes alone.
	- depends on a Strong Entity Set for its existence and identity.
	- this relationship is also called identifying relationship.
- Characteristics:
	- No primary key of its won. It uses a partial key (or discriminator) which, when combined with the primary key of its owner, forms a unique identifier.
	- Represented by a double rectangle in ERD.
	- its identifying relationship is represented by a double diamond.
- Example:
	- a `Dependents` entity set in a company database.
	- the `Dependent` (e.g., spouse/child) cannot be uniquely identified by just their name. One needs to know which employee they belong to.
	- the `Dependents` table would have a partial key like `DependentName`.
	- Its full primary key would be a combination: `(EmployeeID (FK), DependentName)`.
###### Differences:
| Feature      | Strong Entity Set              | Weak Entity Set                                          |
| ------------ | ------------------------------ | -------------------------------------------------------- |
| Existence    | Independent                    | Depends on a Strong Entity                               |
| PK           | Has its own primary key        | Has a partial key; full key includes owner's primary key |
| ERD Symbol   | Rectangle                      | Double Rectangle                                         |
| Relationship | Regular relationship (Diamond) | Identifying relationship (Double Diamond)                |
| Example      | `Students (StudentsID, ...)`   | `Dependents (EmployeeID (FK), DependentName, ...)`       |
****
# 2.5 Attributes and Keys
## A. Attribute:
- Concept:
	- An attribute is a characteristic or property that describes an entity.
	- In the context of database table, an attribute represents a column table.
- Example:
	- For a `Student` entity, attributes include `StudentID`, `FirstName`, `LastName`, `DOB`, etc.
##### Types:
| SN  | Types of Attribute | Definition                                                                              | Example                               |
| --- | ------------------ | --------------------------------------------------------------------------------------- | ------------------------------------- |
| 1   | Simple             | Atomic and cannot be divided into smaller parts.                                        | `StudentID`, `Age`                    |
| 2   | Composite          | Can be divided into smaller sub-pars, each with its own meaning.                        | `FullName` -> `FirstName`, `LastName` |
| 3   | Derived            | Its value can be calculated or derived from another attribute.                          | `DOB` -> `Age`                        |
| 4   | Single-Valued      | Holds a single value for a single entity instance.                                      | `StudentID`, `DOB`                    |
| 5   | Multi-Valued       | Holds multiple values for a single entity instance.                                     | `PhoneNumbers`, `Emails`              |
| 6   | Key Attribute      | An attribute used to uniquely identify an entity instance.<br>It forms the primary key. | `StudentID`, `ProductCode`            |
##### Explanations:
- Composite vs Multi-valued:
	- A composite attribute is about structure: one value, made of parts.
	- multi-valued attribute is about cardinality: one entity has many values for that attribute.
- Null Attribute:
	- Not a formal type.
	- Any attribute that isn't mandatory can have a `NULL` value
	- e.g., `MiddleName`
## B. Keys
- Key is an attribute (or a set of attributes) used to uniquely identify and relate records in a database table.
- fundamental to data integrity and establishing relationships
- Types:
	- Super Key:
		- A set of one or more attributes that can uniquely identify a record
		- it may have extra attributes that are not necessary for unique identification
		- e.g., a `Students` table super keys could be: `{StudentID}`, `{StudentID, FirstName}`, `{FirstName, LastName, DOB}`.
	- Candidate Key:
		- A minimal super key without any unnecessary attributes.
		- A table could have multiple candidate keys.
		- Must be unique and non-redundant
		- From above, the candidate keys are: `{StudentID}`, `{FirstName, LastName, DOB}` (assuming it is truly minimal and unique).
	- Primary Key:
		- The chosen candidate key that the database designer selects to be the main unique identifier for the table.
		- cannot be null and must be unique.
		- e.g., `{Student Key}`.
	- Alternate Key:
		- The candidate keys that were not chosen to be the primary key.
		- e.g., `{FirstName, LastName, DOB}`
	- Foreign Key
		- An attribute (or set of attributes) in one table that reference the primary key of another table.
		- used to create a link between two tables and enforce referential integrity.
		- e.g., an `Enrollments` table has a `StudentID` column which is a Foreign Key referencing the `StudentID` (Primary Key) in the `Students` table.
	- Composite Key
		- a key that consists of more than one attribute.
		- this can be a composite primary key, composite candidate key, etc.
		- e.g., In an `Enrollments` table,  a single attribute cannot uniquely identify a record (a student enrolls in many courses). The combination of `{StudentID, CourseID}` can be used as Composite Primary Key.
## C. Miscellaneous
#### C.1 Represent composite and multi-valued attributes in a table
1. Composite Attribute: Flatten it. Create a separate column in the table for each of its sub-parts
	- ERD: `Employee` with attribute `Name(FirstName, LastName)`
	- Table: `Employees(EmpID, FirstName, LastName, ...)`
2. Multi-valued Attribute: Create a new table. This table will have a foreign key referencing the parent entity's primary key and a column for the multi-valued attribute
	- ERD: `Employee` with multi-valued attribute `PhoneNumbers`
	- Tables:
		- `Employees(EmpID (PK), Name, ...)`
		- `EmployeePhones(EmpID (FK), PhoneNumber)` - this is weak entity set
----
# 2.6 E-R Diagram
- ER Diagram is a graphical representation of the entities, attributes, and relationships within a database
- visual tool used in database design to model the structure of a database system
- based on E-R Model
- main components are: entities, attributes, relationships and cardinality.
## A. Degree and Cardinality of a Relationship
| Aspect     | Degree                                                                                                                                                                               | Cardinality                                                                                   |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------- |
| Definition | The number of entity sets that participate in a relationship                                                                                                                         | Number of instances of one entity that can be associated with instances of the related entity |
| It Answers | "How many different types of entities are connected?"                                                                                                                                | "How many of one entity can link to how many of the other?"                                   |
| Focus      | On the structure of the relationship                                                                                                                                                 | On the business rules and quantitative ratio of the relationship                              |
| Types      | - Unary (1$^{st}$ degree): relationship with 1 entity<br>- Binary (2$^{nd}$ degree): relationship between 2 entities<br>- Ternary (3$^{rd}$ degree): relationship between 3 entities | - One-to-one (1:1)<br>- One-to-many (1:N)<br>- Many-to-one (N:1)<br>- Many-to-many (M:N)      |
## B. Generalisation
- Concept:
	- bottom-up process in which multiple lower-level entity sets (sub-classes) are combined based on their common features to form a single higher-level entity set (a super-class).
	- it highlights an "is-a" relationship.
- Purpose:
	- To minimise redundancy by abstracting shared attributes and relationships into a more general entity.
	- the super-class contains common features while sub-classes contain unique feature.
- Notation:
	- a triangle symbol connects the super-class to the sub-classes.
- Example:
	- In a university database, we might have `Student` and `Professor` entities.
	- Both share common attributes like `PersonId`, `Name` and `Email`.
	- Through generalisation, we create a super-class `Person` to hold these common attributes.
	- the `Student` and `Professor` sub-classes inherit from `Person` and add their specific attributes, e.g., `Major`, `GPA`, `Department`, `Salary`, etc.
``` .txt
Student (▭)  -----|
                  |---- △ ---- Person
Professor (▭) ----|
```
## C. Discriminator
- Concept:
	- an attribute (or set of attributes) in a weak entity set that, when combined with the primary key of its owner, strong entity, uniquely identifies a weak entity instance.
- Purpose:
	- to serve as the partial key for a weak entity.
	- since a weak entity does not have a primary key of its own, the discriminator provides the final piece of unique identification.
----
# 2.7 Alternate Data Model (hierarchical, network, graph)
## A. Hierarchical Model
- Structure: organised in a tree-like structure with a single root. Each parent record can have multiple children, but each child has only one parent (1:N relationships)
- Data Access: Uses pointers to navigate from parent to child records. Accessing data often requires traversing from the root
- Strength: Simple and fast for data that naturally fits a hierarchy (e.g., a file system)
- Weakness: **inflexible**: representing complex many-to-many (M:N) relationships is awkward and leads to data redundancy
- Best for: early mainframe systems, suited  for well-defined static hierarchies
## B. Network Model
- Structure: an enhancement of the hierarchical model, forming a graph where a record can have multiple parents. This allows for M:N relationships
- Data Access: Also uses pointers (physical address links) to connect records. The paths between records are defined as "sets".
- Strength: More flexible than the hierarchical model. Efficient for complex queries with pre-defined access paths
- Weakness: **Extremely complex**: database structure is rigid; modifying schema requires major effort
- Best for: Complex, pre-defined relationships like telecommunications and banking
## C. Graph Model
- Structure: The most flexible, consisting of **nodes** (vertices), **relationships** (edges) and **properties**. Directly mirrors graph theory.
- Data Access: Relationships are first-class citizens, stored natively. Uses graph traversal and pattern-matching queries.
- Strength: Excellent for **interconnected data**. Highly agile and intuitive for modelling complex relationships
- Weakness: Can be less efficient for traditional, row-based aggregate operations common in business reporting
- Best for: Social networks, fraud detection, knowledge graphs and any domain where relationships are as important as the data itself.

[[Chapter 3 Relational Languages and Relational Model]]
