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
# 2.5 Attributes and Keys



1. Define Attributes and explain its type with example.
2. Define attribute and its types, entity, participation constraint, weak entity set.
3. How do you represent composite and multivalued attributes of ERD in tables? Explain with example.
4. What is keys and explain different types of keys.
5. Explain different keys used in database design.
6. What is key attribute? List out the types and explain them briefly.
# 2.6 E-R Diagram
1. differentiate between degree with cardinality of a relationship in an ER diagram
2. Explain briefly about generalisation in context to ER diagram with an example
3. Define discriminator in ER Diagram
# 2.7 Alternate Data Model (hierarchical, network, graph)
