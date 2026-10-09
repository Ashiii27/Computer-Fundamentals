# 🗂️ DBMS (Database Management Systems)

> Asked in almost every SDE interview — often mixed with live SQL writing.

## 📚 What's inside

| Folder | Contents |
|---|---|
| [`notes/`](./notes/dbms.md) | Full revision notes — architecture, ER model, SQL & joins, normalization, ACID & transactions, isolation levels, indexing, NoSQL & CAP |
| [`interview-questions/`](./interview-questions/dbms-interview-questions.md) | 30 most frequently asked DBMS interview questions **with answers** (+ classic SQL queries to write) |
| [`resources/`](./resources/dbms-resources.md) | Books, free courses, YouTube playlists, interactive SQL practice sites |

## 🎯 Suggested revision order
1. DBMS basics, 3-level architecture, keys
2. SQL — SELECT, joins, group by/having (practice writing!)
3. Normalization (1NF → BCNF with examples)
4. Transactions: ACID, anomalies, isolation levels, locks
5. Indexing (B+ trees, clustered vs non-clustered)
6. Modern extras: CAP theorem, SQL vs NoSQL, sharding

## ⭐ Must-know for interviews
- Primary vs unique vs foreign key; candidate vs super key
- DELETE vs TRUNCATE vs DROP
- All join types + write them blindfolded
- Normal forms with one example each
- ACID + 4 isolation levels vs anomalies (dirty/phantom/unrepeatable read)
- Clustered vs non-clustered index; why B+ tree
- **Write on paper:** Nth highest salary, delete duplicates
