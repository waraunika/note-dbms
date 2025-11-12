# 8.1 Failure Classification
### A. Transaction Failures
- Transaction failures occur when a transaction cannot complete successfully.
- This can happen for various reasons:
	- **Logical Errors**: These errors within the transaction's logic, such as division by zero, insert a duplicate key in a unique column, etc.
	- **System Errors**: These occur when the database system detects a problem with the transaction, such as deadlock detection and aborting one of the transactions to resolve the deadlock.
### B. System Failures
- System failures, also known as soft failures, happen when the database system crashes or shuts down unexpectedly.
- System failures affect the DBMS but not damage the database stored on disk.
- The database can typically be recovered from a system failure using transaction logs and checkpoints.
- The happen due to reasons such as
	- **Hardware Failures**: These include issues like CPU failures, power outages, or memory failures.
	- **Software Failures**: These include bugs in DBMS software, operating system crashes or unexpected reboots.
### C. Media Failures
- Media Failures, aka hard failures, occur when the storage media holding the database becomes damaged or corrupted.
- Media Failures can result in loss of data unless proper backup and recovery mechanisms are in place.
- Examples include:
	- **Disk Crashes**: Physical damage to the disk that stores the database.
	- **File System Corruption**: Issues with the file system that lead to the loss or corruption of database files.
### D. Application Failures
- Application Failures occur due to errors in application programs that interact with the database. These errors occur due to:
	- **Application Logic Errors**: Bugs or flaws in the application code that lead to incorrect database operations.
	- **Misuse of Database API's**: Incorrect usage of database's application programming interfaces (API's), leading to unintended results or failures.
### E. Concurrency Control Failures
- Concurrency Control Failures happen when multiple transactions interfere with each other, leading to problems such as:
	- **Deadlocks**: Two or more transactions waiting indefinitely for resources held by each other.
	- **Lost updates**: When two transactions simultaneously update the same data, leading to one update being lost.
	- **Inconsistent Retrievals**: When a transaction reads data that is being modified by another transaction, leading to inconsistent or incorrect data retrieval. 

---
# 8.2 Recovery and Atomicity
### A. Concept
1. Atomicity: (All or nothing)
	- Atomicity guarantees that a transaction is treated as a single, indivisible unit of work.
	- Either all operations of the transaction are executed, or none of them are.
	- This is implemented using two key operations:
		- `COMMIT`: Finalises the transaction, making all changes permanent.
		- `ROLLBACK`: Aborts the transaction, undoing all changes made so far.
2. Recovery: (Safety net system)
	- Recovery is the process of restring all the database to a consistent state after a software or hardware failure.
### B. Relationship: How Recovery Enforces Atomicity
Recovery is the mechanism that implements the promise of Atomicity.
- Atomicity is the property that we need to guarantee
- Recovery is how the system and procedures that make this guarantee possible, especially when things go wrong.
When a system crashes, the recovery system must answer two critical questions to enforce atomicity.
1. Which transactions need to be undone (rolled back) because they didn't complete?
2. Which transactions need to be redone (re-applied) because they committed but their changes were lost?
The entire architecture of the recovery system is built to answer these questions reliably.
### D. Recovery Algorithm:
1. Analysis:
	- Scan the log backwards from the latest checkpoint to determine:
		- `UNDO-LIST`: Transactions that were active at the checkpoint or started later but did not commit.
		- `REDO-LIST`: Transactions that did commit after the checkpoint.
- Redo (Repeat History):
	- Replay all updates from the `REDO-LIST` to ensure all committed changes are durable, even if they were lost in the crash.
- Undo (Rollback History):
	- Roll back all transactions in the `UNDO-LIST` by using the log to restore old values, ensuring atomicity for incomplete transactions.

---
# 8.3 Log Based Recovery
### A. Concept:
- Log-Based Recovery is a method that uses a chronological record (the log) of all transactions and the changes they made to ensure database consistency and atomicity after a failure.
- By keeping a detailed "diary" of everything that happens, the database can replay or reverse transactions to restore itself to a correct state.
- Key component: The transaction Log
	- A sequential, append only file stored on stable storage (disk).
	- Each record has a unique Log Sequence Number (LSN).
### B. Working:
The log contains critical records for each transaction
- `[START T]`: Transaction T has begun
- `[UPDATE T, X, old_value, new_value]`: Transaction T changed data item X from old_value to new_value.
- `[COMMIT T]`: Transaction T has completed successfully.
- `[ABORT T]`: Transaction T is being rolled back.
A standard in log keeping: **Write-Ahead Logging** (WAL):
- Rule 1: Before any data page is written to disk, all log records pertaining to the changes on that page must be flushed to the log on disk.
- Rule 2: Before a transaction issues a `COMMIT`, all its log records must be flushed to the disk.
- Why WAL is non-negotiable:
	- It guarantees that we always have a record of changes before the changes themselves are made permanent.
	- This allows us to REDO changes (if the log says `COMMIT`, but the data is missing) and UNDO changes (if the log has no `COMMIT`).
### C. Recovery Techniques & The Undo/Redo Logic
- The techniques used determines what the recovery manager must do after a crash.
1. **Deferred Database Modification** (No UNDO)
	- Concept:
		- Transactions do not write any changes directly to the database until after they commit.
		- All modifications are only recorded in the log.
	- Process:
		- Transaction executes and writes all `[UPDATE...]` records to the log
		- On `COMMIT`, the log is flushed.
		- Only after the commit are the actual data pages in the database updated.
	- Recovery Logic (After a Crash):
		- No UNDO needed: Since no uncommitted transaction ever wrote to the database, there is nothing to roll back.
		- REDO all committed transactions: For every transaction with a `[COMMIT T]` record in the log, re-apply all its updates to the database.
2. **Immediate Database Modification** (UNDO/REDO)
	- Transactions can write changes to the database before they commit, but they must follow the Write-Ahead Logging protocol.
	- Process:
		1. Transaction writes an `[UPDATE T, X, old_value, new_value` record to the log.
		2. The log record is flushed to the disk (WAL).
		3. The transaction can now write the new value of X to the database.
		4. This can happen many times before the transaction commits or aborts.
	- Recovery Logic (After a crash):
		- REDO all updates from transactions that committed
		- UNDO all updates from transactions that did not commit.
	- Example of Immediate Modification:
```txt
T1: BEGIN
	LOG: [START T1]
	Writes: LOG: [UPDATE T1, A, 100, 50] -> Flush Log -> Write A=50 to DB
	Writes: LOG: [UPDATE T1, B, 200, 250] -> Flush Log -> Write B=250 to DB
	LOG: [COMMIT T1] -> Flush Log
T1: COMMIT
```
### D. Checkpoints
- A checkpoint is a point of synchronisation between the log and the database.
- It is a periodic operation where the DBMS:
	- Stops accepting new transactions momentarily.
	- Writes all modified data pages in memory to disk.
	- Writes a `[CHECKPOINT]` record to the log, listing all the active transactions at that time.
- **Purpose/Significance**: 
	- To dramatically reduce recovery time after a crash.
	- Without checkpoints, the system would have to read the entire log after every crash.
	- With checkpoints, recovery only needs to process the log back to the last checkpoint.
- **Working**:
	- The system crashes.
	- The recovery manager finds the last checkpoint record in the log.
	- It builds two list:
		- UNDO-LIST: All transactions that are active at the time of the checkpoint, plus any that started after but did not commit.
		- REDO-LIST: All transactions that committed after the checkpoint.
	- Initialise `UNDO-LIST = {T2, T3}` from checkpoint, `REDO-LIST = {}`
	- Scan forward from the checkpoint.
		- If you find `[COMMIT T]`, move T from `UNDO-LIST` to `REDO-LIST`.
		- If you find `[START T]`, add T to `UNDO-LIST`.
	- REDO Phase: Re-do all updates for every transaction in the REDO-LIST.
	- UNDO Phase: Un-do  all updates for every transaction still in the UNDO-LIST.
- Example:
```txt
Log:
...
[START T1]
[START T2]
[UPDATE T1, A, 10, 20]
[CHECKPOINT {T2}]    <-- Last Checkpoint. T1? It committed/was not active.
[START T3]
[UPDATE T2, B, 5, 15]
[COMMIT T1]
[UPDATE T3, C, 30, 10]
...CRASH...
```
- Recovery:
	- Scan the transactions
	- T1 was committed, but T2 and T3 were left uncommitted.
	- Here, `REDO-LIST = {T1}`, `UNDO-LIST = {T2, T3}`
	- Action: REDO all of T1's updates and UNDO all of T2's and T3's updates.
- How often should checkpoints be performed?
	- This is tradeoff between Recovery Time and Run Time Performance.
	- If frequent checkpoints, then recovery is fast, but runtime overhead is quite large
	- If infrequent checkpoints, then runtime overhead is low, but the recovery is quite slow.
	- Best practice: Checkpoint frequency is configurable and should be tuned based on the application's tolerance for downtime vs. its need for high throughput. A common balance is every 5-10 minutes for many systems.
---
# 8.4 Shadow Paging
#### A. Concept
- Shadow paging is a recovery technique that maintains atomicity by using page-level copy-on-write mechanisms.
- It ensures database consistency by preserving two versions of data pages during transaction.
#### B. Page Table
- Current Page Table: Points to the latest versions of data pages.
- Shadow Page Table: Preserves the pre-transaction state as a backup.
- Each entry contains pointers to physical locations of data pages on disk.
#### C. Transaction Processing
- Start Transaction:
	- Create a shadow copy of the current page table.
	- Both tables initially point to identical data pages.
- Modify Data Pages
	- Copy-on-Write: Modified pages are written to new disk locations.
	- Current Table Updated: Points to new page versions
	- Shadow Table Preserved: Maintains original page pointers
- Commit Operation
	- Atomically replace the shadow page table with the current page table.
	- Discard old shadow pages that are no longer referenced.
	- Changes become permanent and visible to other transactions.
- Rollback Operation
	- Discard the current page table.
	- Restore the shadow page table as the active table
	- Database reverts to pre-transaction state instantly
#### D. Adv/Disadv
1. **Adv**:
	- Simplicity: Straightforward implementation
	- Immediate Recovery: No complex log analysis required
	- Atomicity Guarantees: All changes apply or none do
	- Fast Rollback: Instant recovery using shadow copies
2. **Disadv**:
	- Memory Overhead: Duplicate page tables and copied pages
	- Concurrency Challenges: Complex multi-transaction management
	- Storage Fragmentation: Frequent copying causes disk fragmentation
	- Performance Bottlenecks: Resource-intensive for large transactions
### E. Differences
| Aspect            | Shadow Paging                                     | Log-Based Recovery                                      |
| ----------------- | ------------------------------------------------- | ------------------------------------------------------- |
| Core Mechanism    | Page-level copy-on-write                          | Sequential log records                                  |
| Performance       | Fast rollback, slower commits                     | Consistent performance                                  |
| Storage Overhead  | High (duplicate pages)                            | Moderate (log files)                                    |
| Concurrency       | Complex to implement                              | Well-established protocols                              |
| Recovery Speed    | Instant rollback                                  | Depends on log size                                     |
| Implementation    | Simpler conceptually                              | More complex infrastructure                             |
| Disk Usage        | Fragmentation issues                              | Sequential log writing                                  |
| Atomicity         | Natural through page swapping                     | Achieved via log rules                                  |
| Durability        | Page table atomic updates                         | Log forces operations                                   |
| Data Management   | Works at page granularity, maintains two versions | Records byte-level changes, supports logical operations |
| Recovery Approach | Discard current page, use shadow copies           | Log-based: Replay log records or reverse changes        |
| Scalability       | Limited by copy overhead                          | better suited for high-concurrency systems              |
| Industry Adoption | Rare in modern databases                          | Industry standard                                       |

---
# 8.5 Advanced Recovery Techniques
### A. ARIES (Algorithm for Recovery and Isolation Exploiting Semantics)
- Log-based recovery algorithm using write-ahead logging (WAL)
- Supports repeating history after crashes for redo, then undo uncommitted transactions
- Uses LSN's on pages of idempotence
- Maintains transaction table and dirty page table during checkpointing
- Allows fine-grained, physiological logging for efficiency.
### B. Time-Travel Queries:
- Query past states of data using temporal tables.
- DBMS maintains history of row version with timestamps
- Uses system-time or application-time periods
- Enables "AS OF" queries to see data at a certain time
- Requires automatic versioning and retention policies.

---