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
# 2.2 E-R Model
1. Define unary relationship along with example
2. How do you convert an ER relationship into relation schema? Explain with examples of different cardinalities.
3. Describe what is total participation using an ERD example.
4. Differentiate total and partial participation with suitable example.
5. What is importance of aggregation in ER design? Discuss with an example.
6. Define generalization and specialization with its notation and examples.
7. Mention the distinctions among the terms Generalization and Specification with appropriate symbolic representation.
# 2.3 Entities and Entities Sets
1. Explain strong and weak entity sets along with example.
# 2.4 Strong and Weak Entity Sets
1. What is the difference between strong and weak entity sets
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
