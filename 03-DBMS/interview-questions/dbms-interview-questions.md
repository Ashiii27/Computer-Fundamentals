# DBMS — Top 30 Interview Questions (with Answers)

> Concepts first, then the classic "write this SQL" round at the end. These cover ~90% of what gets asked.

---

### 1. What is a DBMS? Advantages over a file system?
Software that manages databases — storing, querying, updating data with integrity, security, and concurrency. Over files it gives: controlled redundancy, consistency constraints, concurrent multi-user access with locking, crash recovery (ACID), fine-grained authorization, and declarative querying (SQL) with indexes.

### 2. Explain the 3-level architecture and data independence.
External (user views), conceptual (whole logical schema), internal (physical storage). **Logical data independence**: change the conceptual schema without touching apps/views (harder). **Physical data independence**: change storage/indexes without touching the logical schema (easier). Separating levels is exactly what makes both possible.

### 3. Explain all the key types. Primary key vs unique key?
Super key (any unique set) → candidate key (minimal super key) → primary key (the chosen candidate; one per table; NOT NULL + UNIQUE) → alternate keys (the rest) → foreign key (references another table's PK/unique). **Primary vs unique**: a table has one PK which can never be NULL; it can have many unique keys which (in most DBs) allow a NULL.

### 4. DELETE vs TRUNCATE vs DROP?
**DELETE** — DML; removes rows one by one, fully logged, supports WHERE, fires triggers, can roll back. **TRUNCATE** — DDL; deallocates data pages (very fast), no WHERE, resets auto-increment, triggers don't fire, often not rollback-able (DB-dependent). **DROP** — removes the table *and its schema* entirely.

### 5. What are DDL, DML, DCL, TCL?
DDL defines structure (CREATE, ALTER, DROP, TRUNCATE); DML manipulates data (SELECT, INSERT, UPDATE, DELETE); DCL controls access (GRANT, REVOKE); TCL manages transactions (COMMIT, ROLLBACK, SAVEPOINT).

### 6. WHERE vs HAVING?
WHERE filters individual rows **before** grouping and can't use aggregates; HAVING filters **groups after** GROUP BY and can. Example: `WHERE country='IN' ... HAVING COUNT(*) > 5`.

### 7. Explain all SQL join types.
INNER: only matching rows. LEFT/RIGHT OUTER: all rows from one side + matches (NULL-filled). FULL OUTER: everything from both sides. CROSS: Cartesian product. SELF: table joined to itself (employee→manager). Know how to turn a LEFT JOIN + `WHERE right IS NULL` into an "anti-join" (rows with no match).

### 8. UNION vs UNION ALL?
Both stack result sets of the same shape. UNION removes duplicates (extra sort/hash work); UNION ALL keeps everything and is faster. Use UNION ALL unless you truly need dedup.

### 9. What is normalization? Why do we need it?
Organizing tables to eliminate redundancy and the insert/update/delete anomalies it causes. E.g., storing an instructor's department in every enrollment row risks inconsistency; normalization splits data into clean, related tables.

### 10. Explain 1NF, 2NF, 3NF, BCNF with an example.
**1NF**: atomic values, no repeating groups (split "phones: 98, 91" into rows/table). **2NF**: 1NF + no non-key column depends on *part* of a composite key (in Grades(student, course, studentName), studentName depends only on student → split). **3NF**: no transitive dependencies (in Emp(id, dept, deptHq), dept→deptHq is non-key→non-key → separate Dept table). **BCNF**: every determinant must be a super key — fixes 3NF leftovers like (student, subject) → teacher with teacher → subject.

### 11. When would you denormalize?
Read-heavy systems where joins are too costly: precomputed aggregates/counters, reporting tables, caches, feed timelines. Deliberate redundancy traded for write cost and consistency discipline — a scale decision, not laziness.

### 12. Explain ACID properties.
**Atomicity** — transaction is all-or-nothing (undo log). **Consistency** — constraints remain valid before/after. **Isolation** — concurrent transactions don't see each other's partial work (locks/MVCC). **Durability** — committed data survives crashes (write-ahead log, fsync).

### 13. What concurrency anomalies exist? Define each.
Dirty read (read uncommitted data), lost update (two writes clobber each other), unrepeatable read (re-reading a row gives a different value), phantom read (re-running a range query returns new rows).

### 14. Explain the four isolation levels and what each prevents.
READ UNCOMMITTED (prevents nothing), READ COMMITTED (no dirty reads), REPEATABLE READ (no unrepeatable reads; phantoms mostly possible), SERIALIZABLE (full — behaves as if transactions ran one-by-one). Higher isolation = more safety, less concurrency. Defaults: Postgres READ COMMITTED, MySQL InnoDB REPEATABLE READ.

### 15. What is two-phase locking (2PL)?
Every transaction acquires locks (growing phase) and only then releases them (shrinking phase) — never reacquire. Guarantees conflict-serializability. **Strict 2PL** holds exclusive locks until commit to avoid cascading aborts — what real databases do. 2PL doesn't prevent deadlocks, so DBs detect wait-for cycles and abort a victim.

### 16. What is a deadlock in DBMS and how is it handled?
Two transactions each hold a lock the other needs. Databases run deadlock **detection** (wait-for graph cycle) periodically and roll back the cheaper victim; some use prevention via timestamps (wait-die, wound-wait). Application fixes: consistent lock ordering, short transactions, good indexing.

### 17. What is serializability? How do you test it?
A concurrent schedule is correct if it's equivalent to some serial order. Conflict-serializability test: build a **precedence graph** — edge Ti→Tj when Ti performs the earlier conflicting operation (r/w on the same item) — schedule is serializable iff the graph is **acyclic**.

### 18. What is a view? Materialized view?
A view is a stored query that behaves like a virtual table — simplifies access, adds a security layer (hide columns), insulates apps from schema change. A **materialized view** physically stores the result — fast reads, but must be refreshed; used for expensive aggregates.

### 19. What is an index? When do indexes hurt?
A data structure (B+ tree) that finds rows without full scans — O(log n) lookups, ordered access. Costs: extra storage and **slower INSERT/UPDATE/DELETE** (each must update every index). Avoid over-indexing write-hot tables, low-selectivity columns alone, and unused indexes.

### 20. Clustered vs non-clustered index?
Clustered: the table's rows are physically stored in key order — the index *is* the table (one per table, usually the PK). Non-clustered: a separate structure whose leaves point back to rows (many allowed). Clustered wins for range scans on its key; non-clustered needs an extra lookup unless the index is *covering*.

### 21. Why do databases use B+ trees (and not binary trees or hash tables)?
B+ trees have huge fan-out → very small height → few disk reads per lookup; leaves are **linked**, giving excellent range scans and ordered iteration. Binary trees are too deep (one disk read per level); hash indexes do O(1) point lookups but can't do ranges/sorting at all.

### 22. What is the leftmost prefix rule for composite indexes?
A composite index on (a, b, c) serves filters on a, a+b, a+b+c — leading columns must be constrained. Queries on b or c alone can't use it efficiently. Order columns by (equality filters first, then range, then order-by needs).

### 23. How does a query get executed? What is query optimization?
Parser → rewrite/normalize → optimizer picks a plan (join order, join algorithm: nested-loop/hash/sort-merge, index choice) using table statistics and cost estimates → executor runs it. Use `EXPLAIN ANALYZE` to see actual plans; typical fixes: add the right index, cut functions off indexed columns, shrink the row count early.

### 24. Stored procedure vs function? Trigger? Cursor?
Procedure: callable routine, may return many result sets, called with CALL/EXEC. Function: must return a value, usable inside SQL expressions (restrictions apply). **Trigger**: code auto-fired on INSERT/UPDATE/DELETE (audit trails, denormalized counters — but hidden magic, avoid hot paths). **Cursor**: row-by-row iterator — slow; prefer set-based SQL.

### 25. SQL vs NoSQL — how do you choose?
SQL when you need integrity, complex joins/transactions, and stable schema (money, inventory). NoSQL when you need horizontal scale, flexible schema, or specific access patterns: documents (content/catalogs), key-value (caching, sessions), wide-column (time series, write-heavy), graph (relationships). Many systems use both.

### 26. Explain the CAP theorem.
A distributed system during a network partition can guarantee only **Consistency** or **Availability** — not both; partition tolerance is non-negotiable. CP (block until consistent): Spanner, HBase. AP (serve possibly-stale data): Cassandra, DynamoDB. Related: BASE — eventually consistent instead of ACID.

### 27. Sharding vs partitioning vs replication?
Replication: copies of the same data for availability/read scale. Partitioning: splitting one table by key/columns. **Sharding**: horizontal partitioning *across machines* — each shard owns a key range/hash; gives write scale but complicates cross-shard queries/transactions; rebalanced via consistent hashing.

### 28. OLTP vs OLAP?
OLTP: many short concurrent transactions, current data, row stores, normalized (banking, orders). OLAP: few complex analytical queries, historical data, columnar stores, denormalized star/snowflake schemas (dashboards, BI). They're separated because their access patterns fight each other.

### 29. Write a query for the Nth highest salary.
```sql
-- Method 1: window function (N = 2 shown)
SELECT salary FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
  FROM employee
) t WHERE rnk = 2;

-- Method 2: MySQL
SELECT DISTINCT salary FROM employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1; -- OFFSET = N-1
```
Know why DENSE_RANK is safer than ROW_NUMBER (handles ties).

### 30. Write a query to delete duplicate rows (keep the lowest id).
```sql
DELETE FROM employees
WHERE id NOT IN (
  SELECT MIN(id) FROM employees GROUP BY email
);
```
`GROUP BY` the columns that define a duplicate. (In some DBs you can't re-select the target table in a DELETE directly — use a CTE/temp table there.)

---

## How to answer DBMS questions well
- Contrast answers as mini-tables (DELETE/TRUNCATE/DROP, clustered/non-clustered).
- Always attach a **trade-off** ("indexes speed reads but slow writes…", "CAP forces C vs A…").
- Practice writing SQL by hand: joins, GROUP BY+HAVING, Nth max, dedupe — you *will* be asked to write.
