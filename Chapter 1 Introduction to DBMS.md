# 1.1 Concepts and Applications
## Introduction
1. Data: Raw facts and figures (e.g., numbers, text, images) that can be processed to produce meaningful information
2. Database (DB): A shared, integrated and organised collection of logically related data, designed to meet the information needs of an organisation.
3. Database Management System (DBMS): A software system that enables users to define, create, maintain and control access to the database.
	- acts as an intermediary between the database and the application programs/end-users
4. DB vs DBMS:
	1. DB is the actual collection of data (the "what").
	2. DBMS is the software that manages the data (the "how").
## File Processing System
**Concept**:
- Before DBMS came along, data was stored in individual files
- they were managed by application programs
**Problems**
1. Data Redundancy and Inconsistency
	1. Redundancy
		- The same data is stored in multiple files
		- e.g., a customer's address is stored in both the `Shipping` and `Billing` files
	2. Inconsistency
		- Redundant data can become inconsistent if updates are not applied to all files
		- e.g., the address is updated in `Shipping` but not in `Billing`.
2. Difficulty in Accessing Data
	- Generating ad-hoc queries (e.g., "find all customers with a balance > $5000") is difficult
	- A new program had to be written for every new need, which was not feasible.
	- File processors do not allow needed data to be retrieved in convenient and efficient manners
		- as they are limited by their responsiveness
3. Data Isolation
	- Data is scattered across various files with different formats and with programs of various language.
	- this makes it difficult to write programs that retrieve related data from multiple sources
4. integrity problems
	- it is hard to enforce integrity constraint
	- e.g., `account balance > 0`, or `employee age < 65`
	- Constraints were buried within applica
	- The problem is compounded when several data items are involved from different files
5. Atomicity Problems
	- FPS cannot guarantee that a series of operations in a transaction (e.g., transferring $50 from Account A to B) will complete as a single, individual unit (atomic operation)
	- A system failure could result in a partial update and an inconsistent state
	- e.g., money debited from account A but not credited to B.
6. Concurrent-Access Anomalies
	- Uncontrolled simultaneous access by multiple users could lead to incorrect data
	- e.g., two users reading and updating the same account balance at the same time might overwrite each other's changes
	- To guard against this possibility, the system must maintain some form of supervision
	- But that supervision is difficult to provide, because different programs access data that may or may not have coordinated previously.
7. Security Problems
	- Enforcing security constraints (e.g., "only HR can see salary data") is difficult to build and maintain across numerous application programs
	- Since application programs are often added in ad-hoc manner, enforcing such security constraints is difficult
## DBMS vs File Processing
**Advantages**:
1. Minimised Data Redundancy: DBMS integrates data into a single logical repository, reducing duplicate data
2. Improved Data Consistency & Integrity: By reducing redundancy and allowing DBMS to enforce integrity rules, data remains accurate and consistent.
3. Efficient Data Access & Sharing: Allows concurrent, controlled access for multiple users and applications.
4. Data abstraction and Program-Data Independence: The system hides the complexity of how data is stored and physically structured. This allows the physical storage to change without affecting the application programs.
5. Atomic Transaction Processing: A DBMS ensures transactions are "all or nothing" operations, maintaining database inconsistency even after failures.
6. Uniform Security Administration: Centralised control allows for easy implementation of security policies, backups and recovery procedures.
**Specific adv of Relational DBMS**
- Simple, Tabular Data Model: Data is organised in tables (relations) which are intuitive and easy to understand
- Powerful Querying (SQL): uses a standard, high-level language (SQL) for complex data manipulation and retrieval without needing to write complex programs.
- Physical Data Independence: Applications are insulated from changes in how data is physically stored on disk
**Differences**

|                       | File System                                                                                 | Databases                                                                        |
| --------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Definition            | A process that manages how and where data on a storage disk is stored, accessed and managed | An organised collection of data that can easily be accessed, managed nad updated |
| Consistency           | has high data inconsistency                                                                 | maintains data consistency                                                       |
| Structure             | is simple                                                                                   | complex                                                                          |
| Data Sharing          | is hard                                                                                     | easy                                                                             |
| Redundancy            | high redundancy                                                                             | low                                                                              |
| Security              | not very secure                                                                             | more secure                                                                      |
| Backup-Recovery       | no process for it                                                                           | can be done easily                                                               |
| Concurrent Access     | Prone to anamolies                                                                          | Managed and controlled                                                           |
| Integrity Constraints | Hard to implement and change                                                                | Easily defined and enforced                                                      |
## DBMS and DBA
**Components**:
1. Hardware: The physical devices (computers, storage disks, etc) on which the database resides
2. Software: The DBMS program itself, along with the Operating System and network software
3. Data: The most important component; the actual collection of facts stored in the database
4. Users:
	1. End Users: People who interact with the database through applications or queries
	2. Application Programmers: Write programs that use the database
	3. Database Administrators (DBA): Responsible for managing and maintaining the database system
**Roles and Responsibilities of DBA**
- DBA is the central coordinator of database system
- Schema Definition: Defining the logical structure of the database
- Storage Structure and Access Method Definition: Deciding how the data is physically stored and accessed
- Granting Authorisation and Managing Security: Defining user accounts and granting permissions to access data
- Routine Maintenance:
	- Backup and Recovery: Ensuring regular backups are taken and the database can be restored after a failure
	- Performance Tuning: Monitoring and optimising system performance
	- Ensuring Data Availability: Ensuring the database is up and running for users
- System Selection and Installation: Evaluating and installing the DBMS software
# 1.2 Objective and Evolution
Latest Trends in DBMS:
1. Rise of Cloud-Native Databases (DBaaS)
	- Concept: Databases are no longer just software to be installed; they are services provided by cloud vendors (e.g., Amazon Aurora, Google Cloud Spanner, Snowflake)
	- Features:
		- Managed Service: The cloud provider handles maintenance, patching, backups and scaling
		- Scalability: Easy to scale up/down (vertical) or in/out (horizontal) on-demand
		- pay-as-you-go: Cost is based on usage (storage and compute), not fixed licenses.
2. Dominance of NoSQL and Specialised Databases
	- concept: Moving away from 'one-size-fits-all' to using different database models for different tasks (Polyglot Persistence).
	- common types:
		- Document DBs (e.g., MongoDB): For semi-structured JSON data
		- Key-value DBs (e.g., Redis): For simple, fast lookups (caching, sessions)
		- Graph DBs (e.g., Neo4i): For analysing relationships (social networks, fraud detection)
		- Wide-column DBs (e.g., Cassandra): For massive, scalable storage
3. AI and Machine Learning Integration
	- concept: Embedding AI/ML directly into the database to automate tasks and provide insights
	- Applications:
		- Automated Optimisation: Self-tuning databases that optimise queries and indexes
		- Predictive Queries: Using in-database ML models to forecast trends
		- Enhanced Security: AI-driven anomaly detection to identify potential threats
4. Focus on Data Governance and Security
	- concept: As data privacy laws (like GDPR, CCPA) get stricter, tools for managing data security and compliance are becoming core features.
	- key aspects:
		- Data Masking and Obfuscation: Hiding sensitive data from unauthorised users.
		- Unified Data Catalogues: Providing a single view of all data assets, their lineage and ownership
		- Fine-Grained Access Control: Precisely defining who can see and use what data
# 1.3 Data Abstraction and Data Independence
## Data Abstraction
- definition: The process of hiding the complex physical storage details from users, providing them with a simplified, conceptual view of the database
- significance/purpose:
	- simplifies user interaction: Users (including application programmers) don't need to understand complex low-level data structures.
	- Improves Usability: Different users can interact with the same database at a level of detail that is relevant to them
	- Enhances Security: Users can only see the data they are authorised to see the data they are authorised to see
### Levels of Data abstraction
1. Physical Level:
	- Internal
	- The lowest level 
	- describes how the data are actually stored on the storage medium
	- data may be files, indices, storage structures
	- relevant audience is DBMS Developers
	- main concept is "how the data is stored"
2. Logical Level:
	- Conceptual
	- Describes what data are stored and what the relationships among that data are.
	- hides the physical storage details
	- relevant audience is Database Administrators (DBA), Application Developers
	- main concept is "which data are stored with what relationships"
3. View Level:
	- External
	- The highest level 
	- describes only a part of the entire database, tailored to a user group
	- multiple views can exist for the same database
	- relevant audience is End Users, Application Programs
	- main concept is "what the users see"
## Data Independence
- definition: The immunity of user applications to make changes in the definition or organisation of data at one level of the database system
- significance (in data modelling):
	- crucial because it allows the database to evolve and improve
	- doesn't disrupt existing applications
	- makes system more maintainable, scalable and cost-effective.
- kind of security for user applications that adjust correctly and does not change address, imparting the class of data and their order.
### Types of Data Independence
1. Logical Data Independence
	- definition: the ability to change the logical (conceptual) schema without requiring changes to the external schema (user views).
	- application programs/ user views are protected from changes in the logical structure
	- independence against changes at the conceptual level
	- difficult to achieve
	- example: adding new entities, attributes, or relationships without affecting existing views
2. Physical Data Independence
	- definition: the ability to change the physical (internal) schema without requiring to change the logical schema
	- logical schema and application programs are protected from changes in physical storage
	- independence against changes at the internal level
	- easier to achieve
	- example: changing storage devices, compression algorithms, or file organisations (e.g., from sequential to hash)
#### Differences

|               | Physical                                                                 | Logical                                                               |
| ------------- | ------------------------------------------------------------------------ | --------------------------------------------------------------------- |
| concerns      | how data is stored in system                                             | changes to the structure or data definition                           |
| difficulty    | easier to achieve                                                        | difficult                                                             |
| requirement 1 | to change physical level, no need to change at application program level | to change at logical level, need to make change at application level  |
| requirement 2 | may or may not need to change internal level to improve structure        | need to change logical level if logical structure of DB has to change |
| describes     | internal schema                                                          | conceptual schema                                                     |
| e.g.,         | change in compression technology                                         | adding/modifying new attribute                                        |
#### Advantage of separating:
- Program-Data Independence: Application programs are insulated from how the data is stored. one can change the physical storage without rewriting the application
- Improved Maintainability: Database tuning and physical reorganisation can be performed without halting system operations or altering logical designs
- focused development: DBAs can work on physical optimisation while application developers work against a stable logical schema
#### Aspects of Physical Storage that can be changed
- storage media/device: moving data from an HDD to SSD or to a different server
- file organisation: changing from a heap file to a sorted file or a hash-based structure
- indexing: creating, modifying or dropping indexes to improve query performance
- data compression & encoding: implementing new compression techniques to save space
- partitioning & fragmentation: splitting a table across different physical locations
- storage structures: changing the way records are stored (e..g, from row-based to columnar storage)
# 1.4 Schema and Instances
- DB changes overtime as information is inserted and deleted.
- the collection of information stored in DB at a particular moment is called an instance of DB
- the overall design of DB is called DB schema. 
- Analogy of schema/instance to programming:
	- A DB schema corresponds to variable declarations in a program.
	- each variable has a particular value at a given instant
	- the values of variables in a program at a point in time correspond to an instance of a database schema
- schema is the structure, instance is the current snapshot of the data in that structure
## Differences

| Aspect            | Schema                                                                                     | Instance                                                                     |
| ----------------- | ------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- |
| Definition        | Overall design or blueprint of the database. defines the structure                         | The actual data stored in the database at a specific moment in time          |
| Analogy           | the variable declaration (int stdnt_id;)                                                   | the current value (stdnt_id = 101;)                                          |
| Nature            | static.<br>remains constant unless structure is altered                                    | dynamic.<br>changes frequently with every INSERT, UPDATE or DELETE operation |
| Change Frequency  | Changes rarely                                                                             | changes very frequently                                                      |
| What it describes | what data is stored, including tables, columns, data types, constraints, and relationships | what data is present right now                                               |
## Three-schema architecture
- this architecture is a framework for achieving data abstraction and independence by separating the user applications from the physical database
- uses 3 levels of schemas:
1. External Schema (or Subschema or View Schema)
	- external level
	- defines multiple views of the database
	- each view is tailored to a specific user or group
	- it hides irrelevant data from uers
	- end users, application programs are privy to it
2. Conceptual Schema (or Logical Schema)
	- conceptual level
	- describes the entire database structure for all users
	- defines entities, attributes, relationships, and constraints
	- hides physical storage details
	- DBA, Application Developers are privy to it
3. Internal Schema (or Physical Schema)
	- Internal level
	- describes the physical storage of the database
	- defines how data is stored, indexed and accessed on the storage medium
	- DBMS Developers are privy to it
### Significance
1. Logical Data Independence
	- The conceptual schema can be changed without affecting the external schemas
	- e.g., adding a new column doesn't break existing views that don't use it
2. Physical Data Independence
	- the internal schema can be changed without affecting the conceptual schema
	- e.g., changing a file storage format doesn't change the logical table structure
3. Security
	- users only have access to the data defined in their specific external schema
# 1.5 Concepts of DDL, DML and DCL
## DDL (Data Definition Language)
- definition: commands used to define the database schema
- used to create, modify, and delete the structure of database objects,
	- but not the data within them
- purpose: to define the structure and the rules of the data
- characteristics:
	- auto-committed (changes are saved permanently immediately upon execution)
	- affects the entire schema/structure
- common commands and examples:
	- create, alter, drop, truncate (to delete all records from a table, but not the table itself)
```sql
CREATE TABLE Students (
	student_id INT PRIMARY KEY,
	name VARCHAR(100),
	age INT
);
	
ALTER TABLE Students ADD COLUMN email varchar(255);
	  
DROP TABLE Students;
TRUNCATE TABLE Students; 	  
```
## DML (Data Manipulation Language)
- definition: Commands used to manipulate the data stored within the database objects
- they are used for retrieving, inserting, updating, and deleting data
- purpose: to handle the content of the database
- key characteristics:
	- not auto-committed (changes can be rolled back unless committed explicitly)
	- affects the data/records in tables
- common commands and examples:
	- select, insert, update, delete
```sql
SELECT name, age FROM Students WHERE age > 20;

INSERT INTO Students (student_id, name, age)
VALUES (101, 'Alice', 25);

UPDATE Students SET age = 23 WHERE name = 'Alice';

DELETE FROM Students WHERE student_id = 101;
```
## DCL (Data Control Language)
- definition: Commands used to control access to the data in the database.
- used to grant and revoke user permissions
- purpose: to enforce data security and access control
- key characteristics:
	- deals with user privileges and permissions
- common commands and examples:
	- grant, revoke
```sql
GRANT SELECT, INSERT ON Students TO user_alice;
REVOKE INSERT ON Students FROM user_alice;
```
## Differences

| Feature         | DDL                           | DML                                      | DCL                                 |
| --------------- | ----------------------------- | ---------------------------------------- | ----------------------------------- |
| Purpose         | Define the database structure | Manipulate the data within the structure | Control access to the data          |
| What it affects | Schema/Structure              | Actual Data/Records (Rows)               | user permissions and privileges     |
| Commands        | CREATE, ALTER, DROP, TRUNCATE | SELECT, INSERT, UPDATE, DELETE           | GRANT, REVOKE                       |
| Committing      | auto-committed                | Not auto-committed                       | requires a commit to make permanent |
| Audience        | DBA                           | Application Developers, End Users        | DBA                                 |
