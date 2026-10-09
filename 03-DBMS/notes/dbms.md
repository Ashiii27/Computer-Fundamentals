# DBMS — Complete Revision Notes

> Covers: Basics & architecture → ER model → Relational model & SQL → Normalization → Transactions & ACID → Isolation & concurrency → Indexing → Query processing → NoSQL, CAP & scaling

## Table of Contents
1. [Introduction](#1-introduction)
2. [Architecture & Data Independence](#2-architecture--data-independence)
3. [ER Model & Keys](#3-er-model--keys)
4. [Relational Model & SQL](#4-relational-model--sql)
5. [Normalization](#5-normalization)
6. [Transactions & ACID](#6-transactions--acid)
7. [Concurrency & Isolation Levels](#7-concurrency--isolation-levels)
8. [Indexing](#8-indexing)
9. [Query Processing & Optimization](#9-query-processing--optimization)
10. [NoSQL, CAP & Modern Scaling](#10-nosql-cap--modern-scaling)
11. [Cheat Sheet](#11-cheat-sheet)

---

## 1. Introduction

- **Data vs Information**: raw facts vs processed, meaningful data.
- **DBMS**: software to define, create, store, query, and manage databases with integrity, security, and concurrent access (MySQL, PostgreSQL, Oracle, MongoDB).
- **File system vs DBMS**:

| File system | DBMS |
|---|---|
| Redundancy & inconsistency | Controlled redundancy (normalization, constraints) |
| No concurrent access control | Concurrency control (locks, MVCC) |
| Weak security/granular access | Fine-grained authorization |
| No crash recovery | ACID transactions, WAL/undo-redo logs |
| Ad-hoc retrieval hard | Declarative querying (SQL), indexes |

- **Users**: naive (apps), application programmers, sophisticated (SQL analysts), DBA (schema, authorization, tuning, backup).

---

## 2. Architecture & Data Independence

**Three-level (ANSI-SPARC) architecture**:
| Level | What it describes | Example |
|---|---|---|
| External (view) | Per-user slice of data | "Only name & marks" view for students |
| Conceptual (logical) | Whole DB structure: entities, relations, constraints | All tables + FKs |
| Internal (physical) | How stored: files, indexes, compression | B+ tree on roll_no |

- **Logical data independence**: change conceptual schema without changing views/apps (add a column). **Harder** to achieve.
- **Physical data independence**: change storage/indexes without changing the conceptual schema (add an index). **Easier**.
- **Schema** = blueprint (rarely changes). **Instance** = data at a moment (always changing).
- **Languages**: DDL (`CREATE/ALTER/DROP/TRUNCATE`), DML (`SELECT/INSERT/UPDATE/DELETE`), DCL (`GRANT/REVOKE`), TCL (`COMMIT/ROLLBACK/SAVEPOINT`).

---

## 3. ER Model & Keys

- **Entity** (rectangle) → table; **attribute** (oval) → column; **relationship** (diamond) → FK/join table.
- **Attribute types**: simple/composite (address → street, city), single/multivalued (phone numbers — double oval), derived (age from DOB — dashed), key.
- **Cardinality**: 1:1, 1:N, M:N (M:N → junction table with two FKs).
- **Participation**: total (double line — every entity participates) vs partial.
- **Generalization** (bottom-up: Car, Bike → Vehicle), **Specialization** (top-down), **Aggregation** (relationship treated as an entity).

### Keys
| Key | Meaning |
|---|---|
| Super key | Any attribute set that uniquely identifies a row |
| Candidate key | Minimal super key (no extra attributes) |
| **Primary key** | Chosen candidate key; **NOT NULL + UNIQUE**; one per table |
| Alternate key | Candidate keys not chosen |
| Composite key | Key made of ≥ 2 columns |
| **Foreign key** | References a PK/unique key in another table; enforces referential integrity (`ON DELETE CASCADE / SET NULL / RESTRICT`) |
| Unique key | Unique + (in most DBs) allows a NULL; table can have many |

---

## 4. Relational Model & SQL

**Constraints**: domain (valid values), entity integrity (PK not null), referential integrity (FK points to an existing row), user-defined (CHECK, UNIQUE).

### SELECT logical evaluation order (interviewers love this)
```sql
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT
```
This is why you can't use a SELECT alias in WHERE (but can in ORDER BY), and why WHERE filters rows while HAVING filters groups.

### Joins
```sql
-- INNER: only matching rows
SELECT e.name, d.dname
FROM emp e INNER JOIN dept d ON e.dept_id = d.id;

-- LEFT: all emp + matching dept (NULL if none)
... FROM emp e LEFT JOIN dept d ON e.dept_id = d.id;
```
| Join | Returns |
|---|---|
| INNER | Rows matching in both |
| LEFT / RIGHT OUTER | All rows of one side + matches (NULL-filled) |
| FULL OUTER | Union of both sides + matches |
| CROSS | Cartesian product (every × every) |
| SELF | Table joined with itself (e.g., employee→manager) |

### Set ops & aggregates
- `UNION` (dedupes) vs `UNION ALL` (keeps duplicates, faster); `INTERSECT`; `EXCEPT/MINUS`. Same column count/types required.
- Aggregates: COUNT, SUM, AVG, MIN, MAX. **NULL is ignored by aggregates**; `COUNT(*)` counts rows, `COUNT(col)` counts non-null.
- **NULL logic**: anything compared to NULL is UNKNOWN — use `IS NULL`, not `= NULL`.
- **Subqueries**: scalar, row, table; correlated subqueries run per outer row (slow — often rewritten as JOIN or window function).
- **Views**: stored named query. Simple views can be updatable; **materialized views** physically store results (fast reads, need refresh).
- Common types: `INT`, `DECIMAL(p,s)` (exact — money), `FLOAT` (approx), `CHAR(n)` fixed padded vs `VARCHAR(n)` variable, `TEXT`, `DATE/TIMESTAMP`, `BOOLEAN`, `JSONB` (Postgres).

---

## 5. Normalization

Organizing tables to remove redundancy and anomalies.

**Anomalies in unnormalized data**: insert (can't add a course without a student), update (change an instructor's name in 500 rows → inconsistency risk), delete (deleting the last enrollment loses the course itself).

**Functional dependency** X → Y: X's value determines Y's value. Armstrong's axioms: reflexivity, augmentation, transitivity.

| Form | Rule (one-liner) | Remove |
|---|---|---|
| **1NF** | Atomic values; no repeating groups/arrays | Multivalued cells |
| **2NF** | 1NF + no **partial dependency** (non-key column depending on *part* of a composite key) | Part-key dependencies |
| **3NF** | 2NF + no **transitive dependency** (non-key → non-key) | Key→non-key→non-key chains |
| **BCNF** | For every X → Y, X must be a **super key** | Odd determinants (3NF leftovers) |
| 4NF | No independent multivalued dependencies | MVDs |
| 5NF | No join dependency (can't be split further and rejoined losslessly) | JDs |

*Classic example*: `StudentGrades(student, course, instructor, dept)` — if instructor → dept but instructor isn't a key, 3NF is violated → split out `Instructor(instructor, dept)`.

- **Lossless-join decomposition** (mandatory) vs **dependency preservation** (desirable; BCNF may sacrifice it).
- **Denormalization**: deliberately re-add redundancy (caches, aggregates, read-heavy analytics) to avoid joins — a trade-off, not a sin. Know when: read-heavy workloads, precomputed counters, reporting tables.

---

## 6. Transactions & ACID

**Transaction**: a logical unit of work that must execute completely or not at all.
**States**: active → partially committed → committed (success), or failed → aborted (rollback).

| ACID | Meaning | Enforced by |
|---|---|---|
| **Atomicity** | All or nothing | Undo log / rollback segments |
| **Consistency** | DB moves from one valid state to another (constraints hold) | Constraints + both other properties |
| **Isolation** | Concurrent transactions don't see each other's partial work | Locks / MVCC, isolation levels |
| **Durability** | Once committed, survives crashes | Redo log / WAL, fsync |

### Concurrency anomalies
| Anomaly | Definition |
|---|---|
| **Dirty read** | Reading another transaction's *uncommitted* data |
| **Lost update** | Two writes overwrite each other; one vanishes |
| **Unrepeatable read** | Same row read twice gives different values (someone updated it) |
| **Phantom read** | Same *range query* returns new/missing rows (someone inserted) |

### Schedules & Serializability
- **Serial schedule**: transactions one after another (safe, slow). **Concurrent**: interleaved.
- A concurrent schedule is **(conflict) serializable** if it's equivalent to some serial schedule — test with a **precedence graph** (nodes = transactions; edge T1→T2 if they conflict on an item and T1 first). **Acyclic graph ⇔ serializable.**
- **Recoverable schedule**: T2 reads T1's data only if T1 commits first. **Cascadeless**: only read committed data. **Strict**: read/write only after the writer commits — safest.

### Locks & 2PL
- Shared (S) lock: read; eXclusive (X) lock: write. S-S compatible; X conflicts with everything.
- **Two-Phase Locking**: phase 1 *growing* (only acquire), phase 2 *shrinking* (only release). Guarantees serializability. Variants: **Strict 2PL** (hold X locks till commit — prevents cascading aborts; used in practice), Rigorous (hold all locks till commit).
- 2PL can still **deadlock** → DB detects (wait-for graph cycle) and aborts a victim, or prevents (wait-die/wound-wait timestamps). Lock tuning: lock escalation, granularity (row < page < table).

---

## 7. Concurrency & Isolation Levels

| Isolation level (lowest→highest) | Dirty read | Unrepeatable read | Phantom read |
|---|---|---|---|
| READ UNCOMMITTED | possible | possible | possible |
| READ COMMITTED | prevented | possible | possible |
| REPEATABLE READ | prevented | prevented | possible* |
| SERIALIZABLE | prevented | prevented | prevented |

*MySQL InnoDB's default REPEATABLE READ mostly blocks phantoms via next-key locks/gap locks; PostgreSQL's is snapshot-based.

- Implementations: pessimistic (locks) vs optimistic/**MVCC** — readers never block writers, writers never block readers; each transaction sees a snapshot (Postgres, Oracle, InnoDB). Write conflicts surface as serialization errors or last-write-wins.
- Set explicitly: `SET TRANSACTION ISOLATION LEVEL ...` (note: autocommit mode commits every single statement).

---

## 8. Indexing

- **Why**: avoid full table scans — find data in O(log n) instead of O(n). Trade-off: extra storage + **slower writes** (every insert/update must maintain indexes).
- **Clustered vs non-clustered**:

| Clustered | Non-clustered (secondary) |
|---|---|
| Table rows stored **in key order** — the table IS the index | Separate structure with pointers back to rows |
| One per table | Many per table |
| Fast range scans on the key | Lookup → then bookmark lookup to the row (unless covering) |
| Usually the PK (InnoDB, SQL Server default) | Composite, partial, covering indexes |

- **B+ tree** (why DBs love it): balanced, high fan-out (short height → few disk reads), data only in **leaf nodes linked together** → superb range scans & ordered traversal. B-trees store data in internal nodes too (fewer depth for point lookups but worse ranges).
- Dense (entry per row) vs sparse (entry per page — only for clustered). Multi-level indexes = B+ tree idea.
- **Composite index** (a, b, c): works for a, a+b, a+b+c — leftmost prefix rule; (b, c) alone can't use it.
- **Covering index**: contains all queried columns → no row lookups at all (`INCLUDE` columns).
- Index these: PKs/FKs, WHERE/JOIN/ORDER BY columns. Skip: tiny tables, write-hot tables, low-selectivity columns (gender flag) alone.

---

## 9. Query Processing & Optimization

Pipeline: **Parser → (rewrite/normalize) → Optimizer → Execution plan → Executor**.
- Join algorithms:
  | Algorithm | When it wins |
  |---|---|
  | Nested loop | Small outer table + indexed inner (OLTP point joins) |
  | Hash join | Large unsorted inputs, equality joins (build hash on smaller side) |
  | Sort-merge join | Inputs already sorted on the key; range joins |
- Optimizer chooses **join order, algorithms, and index usage** based on statistics (row counts, histograms) and cost estimation.
- Use `EXPLAIN` / `EXPLAIN ANALYZE` to read plans: spot full scans, wrong join types, missing indexes, huge intermediate row counts.

---

## 10. NoSQL, CAP & Modern Scaling

### SQL vs NoSQL
| Relational | NoSQL |
|---|---|
| Schema, joins, ACID | Flexible schema, denormalized, tunable consistency |
| Vertical scaling first | Horizontal scaling by design |
| Types: — | **Document** (MongoDB), **key-value** (Redis, DynamoDB), **column-family** (Cassandra, HBase), **graph** (Neo4j) |
| Use: transactions, reporting, integrity | Use: huge scale, caching, feeds, flexible/rapid iteration |

### CAP theorem
In a distributed store, when a **network partition (P)** occurs you must choose **Consistency (C)** or **Availability (A)** — you can't have all three. CP: HBase, MongoDB (default), Spanner. AP: Cassandra, DynamoDB, CouchDB. Partition tolerance isn't optional in distributed systems, so the real choice is C vs A during partitions. Related: **BASE** (Basically Available, Soft state, Eventually consistent) vs ACID.

### Scaling & distribution vocabulary
- **Replication**: copies of data (primary–replica with failover; multi-master) — availability + read scale; introduces lag & consistency issues.
- **Partitioning vs sharding**: partitioning = splitting a table (often within one server: by range/hash); **sharding** = splitting data *across machines* by shard key. Vertical (by columns) vs horizontal (by rows).
- Consistent hashing: how shards get redistributed without moving everything.
- **OLTP vs OLAP**: OLTP = many short read/write transactions (orders, banking); OLAP = complex analytics over historical data (warehouse, star schema, columnar stores). **Data lake**: raw schema-on-read storage.

---

## 11. Cheat Sheet

| Question | Answer |
|---|---|
| DELETE vs TRUNCATE vs DROP | DELETE: DML, row-by-row logged, WHERE allowed, triggers fire. TRUNCATE: DDL, deallocates pages (fast), no WHERE, resets identity, most DBs can't easily roll back. DROP: removes table + schema entirely. |
| WHERE vs HAVING | WHERE filters rows before grouping (no aggregates); HAVING filters groups after. |
| UNION vs UNION ALL | UNION dedupes (sort/hash cost); UNION ALL keeps duplicates, faster. |
| CHAR vs VARCHAR | CHAR(n) fixed-length space-padded; VARCHAR(n) variable + length prefix. |
| Primary vs unique key | PK: one per table, NOT NULL. Unique: many allowed, usually one NULL allowed. |
| DROP vs TRUNCATE | TRUNCATE keeps structure; DROP deletes the table itself. |
| Fastest way to find Nth highest salary | Window function: `DENSE_RANK() OVER (ORDER BY salary DESC)` — or `LIMIT 1 OFFSET n-1`. |
| Kill duplicates | `DELETE FROM t WHERE id NOT IN (SELECT MIN(id) FROM t GROUP BY a, b);` |
| Why B+ tree not binary tree | High fan-out ⇒ tiny height ⇒ few disk I/Os; linked leaves ⇒ fast ranges. |
| MVCC in one line | Readers see a snapshot; writers create new versions — no read locks. |
| CAP in one line | Under a partition choose C or A; you never sacrifice P. |
