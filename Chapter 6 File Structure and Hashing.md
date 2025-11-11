(4 Hours / 8 Marks)
# 6.1 Records Organisations
- Record Organisation refers to how individual data records (rows) are structured, stored and managed within database files.
- It deals with the physical representation of data on storage media.
## A. Types of Records based on Size:
### A.1 Fixed-Length Records
- Every record in a file has the same exact predefined size.
- Structure:
	- Each field has a fixed size (e.g., `CHAR(50)`, `INT`)
	- Padding is added if the actual data is shorter than the allocated space.
	- May include a small record header for metadata (e.g., null flags, record type).
- Storage & Access:
	- The location of the i$^{th}$ record can be calculated directly:
	  Location = Start of file + (i * Record's size).
	- This allows for direct access to any record.
- Advantages:
	- Fast Access: Direct record lookup via simple arithmetic.
	- Simple Management: Easy to implement and manage.
- Disadvantages:
	- Wasted Space: Significant internal fragmentation if data doesn't use the full allocated space.
	- Inflexibility: Cannot accommodate data that naturally varies in size.
- Example:
	- An `Employee` record defined as `(ID INT, Name CHAR(30), Salary DECIMAL(10,2))` will always be the same size (e.g., 48 bytes), regardless of whether an employee's name is "Bob" or "Charlie Mc'lovin"
### A.2 Variable-Length Records
- Records can have different sizes based on the actual data stored.
- This is common with `VARCHAR`, `BLOB` fields, or multi-record type files.
- Advantages:
	- Efficient Space Usage: No wasted space for unused character allocations
	- Flexibility: Can handle records of vastly different sizes.
- Disadvantages:
	- Slower Access: Cannot calculate record position directly; must follow pointers or scan a directory
	- Fragmentation: Deletions and updates can lead to fragmented free space, requiring periodic reorganisation.
- Storage Methods: Storing variable-length records requires more complex techniques to track where each record begins and ends.
	1. Length-Prefix Method:
		- Each variable-length field is preceded by its length.
		- The record itself may have a header storing the total record length.
		- e.g., Storing "Hello" would be stored as `[5][H][e][l][l][o]`.
	2. Pointer-Based:
		- The record is structured  as a fixed-length portion (for fixed fields and pointers) and a variable-length portion (for the actual data).
		- Implementation:
			- The fixed part contains the stable data and pointers (offsets) to the start of each variable-length field.
			- The variable part holds the actual strings or data blocks.
		- Example:
			- A record for `(ID INT, Name VARCHAR, Dept VARCHAR)` could be stored as:
			  `Fixed Part: [ 101 ] -> [ Ptr1 ] -> [ Ptr2 ]`
			  `Variable Part: [ A ][ l ][ i ][ c ][ e ][ S ][ a ][ l ][ e ][ s ]`
			  `                   ^                   ^`
			  `                 Ptr1                Ptr2`
	3. Slotted-Page Structure:
		- Most common in modern DBMS
		- A disk block (page) has three main sections:
			- Header: Contains the number of records and the location of free space.
			- Slot Array/Directory: An array of entries, where each entry stores (offset, length) of a record within the page.
			- Free space: The area where records are stored, typically growing from the end of the page towards the beginning
		- How it Works:
			- To find record `i`, the system checks slot `i` in the directory to get its location and size within the page.
			- This allows records to be easily moved within the page to maintain free space (compaction) without changing external references.
## B. Types of File Organisation
- They define how complete records are arranged and accessed in a file.
### B.1 Heap (Pile) File Organisation
- Records are stored in no particular order (FCFS).
- Operations:
	- Insertion: Very fast, added at the end of the file
	- Searching: Slow, Requires a linear scan (O(n))
	- Deletion: Mark record as deleted; requires periodic reorganisation to reclaim space.
- Best for: Bulk loading data, or when queries require a full table scan.
### B.2 Sequential File Organisation
- Records are stored in a sorted order based on an ordering key field.
- Operations:
	- Searching: Efficient for range queries and retrieving sorted data. Can use binary search.
	- Insertion/Deletion: Costly. Often requires rewriting the entire file or using overflow blocks to maintain order, which can degrade performance over time.
- Best For: Applications that frequently require data in a sorted order or process large ranges of records (e.g., reporting).
### B.3 Hash File Organisation
- A hash function is applied to a key field to compute the physical address (bucket number) where the record should be stored.
- Operations:
	- Exact-match search: Extremely fast (O(1) on average). Directly computes the address
	- Range-search: Inefficient. Requires scanning all buckets.
- Best For: Applications primarily needing fast point queries (e.g., retrieving a product by its ID).
### B.4 Clustered File Organisation
- Records from two or more different tables are stored intermingled together in same file.
- Typically done for tables that are frequently joined.
- Types:
	- Single-Table Clustering: Records of one table are stored together, often sorted. (essentially Sequential Organisation)
	- Multi-Table Clustering: Records from related tables are stored together. e.g., Storing an `Employee` record followed by all their `Dependent` records in the same disk block
- Advantage: Dramatically improves the performance of join operations, as related data is retrieved with a single disk I/O.
- Disadvantage: Can slow down queries that access only one of the clustered tables
### B.5 Indexing
- Records are stored in indexes like B-trees, B+ trees and bitmap indices.
- Secondary Structure that can be applied to any of the above file organisations.
### B.6 Improving Pointer Method with Anchor and Overflow Blocks
- Technique used in sequential and indexed files to manage insertions efficiently.
- In a sequentially ordered file, inserting a new record in the correct order requires shifting all subsequent records, which is very expensive.
- Solution:
	- Anchor Block: The mail file is divided into blocks. Records within each block are sorted. These are the anchor blocks.
	- Overflow Block: A separate block (or chain of blocks) is associated with each anchor block.
- Working:
	- When a new record needs to be inserted into a full anchor block, it is placed in the corresponding overflow block instead of shifting all records.
	- The overflow block is itself sorted or maintains a pointer to the next overflow block.
	- To read the data in order, the system reads the anchor block and then merges in the records from the overflow chain.
- Benefit: It defers the expensive reorganisation operation. The file can continue to function with good performance for a while before a full reorganisation is needed.
----
# 6.2 RAID
- RAID (Redundant Array of Independent Disks) is a data storage technology that combines multiple physical disk drives into a single logical unit to improve performance, increase storage capacity, and provide redundancy.
- RAID is commonly used in DBMS to enhance data reliability and performance.
- Characteristics:
	- Redundancy: Provides fault tolerance by duplicating data across multiple disks.
	- Performance: Can
# 6.3 Remote Backup System
# 6.4 Hashing Concepts
# 6.5 Order Indices
# 6.6 B+ Tree index