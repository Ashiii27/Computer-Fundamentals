# DBMS — Curated Learning Resources

> Books → Courses → Interactive SQL → YouTube → Docs & Practice. Starred items = best value.

## Books
| Resource | Why | Link |
|---|---|---|
| **Database System Concepts** — Silberschatz, Korth, Sudarshan | THE standard DBMS text ("the dinosaur book"); free slides & practice on the site | https://www.db-book.com |
| **Fundamentals of Database Systems** — Elmasri & Navathe | Alternative classic; excellent normalization & transactions coverage | Print / Pearson |
| **Use The Index, Luke** — Markus Winand | **Free online**; indexing & SQL tuning explained properly — reads like interview prep | https://use-the-index-luke.com |
| **Designing Data-Intensive Applications** — Martin Kleppmann | The modern distributed-data bible (replication, sharding, consistency) — advanced but career-changing | Print / O'Reilly |

## Free Courses
| Course | Why | Link |
|---|---|---|
| **CMU 15-445: Intro to Database Systems** — Andy Pavlo | The best free DB internals course (b-trees, MVCC, joins, optimization) | https://15445.courses.cs.cmu.edu |
| **SQLBolt** | Interactive lessons from zero — finish in an afternoon | https://sqlbolt.com |
| **Select Star SQL** | Free interactive book — learn SQL by exploring real data | https://selectstarsql.com |

## Interactive SQL Practice
| Site | Why | Link |
|---|---|---|
| **SQLZoo** | Bite-size exercises per concept | https://sqlzoo.net |
| **Mode SQL Tutorial** | Beginner → advanced analytics SQL | https://mode.com/sql-tutorial |
| **LeetCode — SQL 50 study plan** | Interview-style SQL problems with a judge | https://leetcode.com/studyplan/top-sql-50/ |
| **HackerRank SQL track** | Graded practice from basic → advanced | https://www.hackerrank.com/domains/sql |
| **StrataScratch** | Real company SQL interview questions | https://www.stratascratch.com |
| **DB Fiddle** | Run/schema-test SQL in the browser | https://www.db-fiddle.com |

## YouTube
| Channel / Playlist | Best for | Link |
|---|---|---|
| **Gate Smashers — DBMS playlist** | Concept + GATE/interview coverage, short videos | https://www.youtube.com/results?search_query=gate+smashers+dbms+playlist |
| **Jenny's Lectures — DBMS** | Deep walkthroughs: normalization, serializability, locks | https://www.youtube.com/results?search_query=jenny+lectures+dbms |
| **freeCodeCamp — SQL courses** | Full-length SQL courses (MySQL/PostgreSQL) | https://www.youtube.com/results?search_query=freecodecamp+sql+course |
| **CMU Database Group** | Andy Pavlo's lectures on YouTube (15-445/799) | https://www.youtube.com/@CMUDatabaseGroup |

## Official Docs & Websites
| Resource | Why | Link |
|---|---|---|
| **PostgreSQL documentation** | The best-written DB docs anywhere | https://www.postgresql.org/docs/ |
| **MySQL Reference Manual** | The default-DB reference | https://dev.mysql.com/doc/ |
| **PostgreSQL Tutorial** | Guided, practical Postgres learning | https://www.postgresqltutorial.com |
| **GeeksforGeeks — DBMS** | Last-minute notes + MCQs | https://www.geeksforgeeks.org/dbms/ |
| **MongoDB Manual** | Document-store fundamentals | https://www.mongodb.com/docs/manual/ |

## Suggested path
1. **Concepts:** Gate Smashers/Jenny's Lectures + the notes in this repo (normalization, ACID, indexing).
2. **SQL muscle memory:** SQLBolt → SQLZoo → LeetCode SQL 50 (aim to write joins & GROUP BY without looking).
3. **Depth for product companies:** Use The Index, Luke (indexes) + CMU 15-445 lectures (internals).
4. **Polish:** this repo's 30 Q&A + write Nth-highest-salary and dedupe queries on paper.
