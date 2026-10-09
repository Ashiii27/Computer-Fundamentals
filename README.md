<div align="center">

# 🖥️ Computer Fundamentals — Interview Revision Hub

### 🎯 One repo. Every core subject. Notes + Interview Q&A + Curated resources.

![Subjects](https://img.shields.io/badge/subjects-9-blue?style=for-the-badge)
![Interview Questions](https://img.shields.io/badge/interview_Q%26A-240%2B-green?style=for-the-badge)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-ff69b4?style=for-the-badge)
![Made with Markdown](https://img.shields.io/badge/made_with-markdown-1f425f?style=for-the-badge)

*Built for placement season, job switches, and semester revision — the CS fundamentals that every technical interview quietly assumes you know.*

</div>

---

## 📌 What is this repo?

Technical interviews are decided less by frameworks and more by **fundamentals** — OS, Networks, DBMS, OOPS, and increasingly DevOps, Cloud, and Security. This repository is a **structured revision hub**: for every subject you get three things, always in the same place:

1. 📝 **Notes** — complete, condensed revision notes (read once before the interview)
2. 💬 **Interview Questions** — the most frequently asked questions **with model answers**
3. 🔗 **Resources** — hand-picked books, free courses, YouTube playlists, docs & practice sites

Everything is in Markdown, so it renders beautifully on GitHub and works offline too.

---

## 🗂️ Subjects

| # | Subject | 📝 Notes | 💬 Interview Q&A | 🔗 Resources |
|---|---|---|---|---|
| 01 | **Operating Systems** | [Notes](./01-Operating-Systems/notes/operating-systems.md) | [30 Q&A](./01-Operating-Systems/interview-questions/operating-systems-interview-questions.md) | [Resources](./01-Operating-Systems/resources/operating-systems-resources.md) |
| 02 | **Computer Networks** | [Notes](./02-Computer-Networks/notes/computer-networks.md) | [30 Q&A](./02-Computer-Networks/interview-questions/computer-networks-interview-questions.md) | [Resources](./02-Computer-Networks/resources/computer-networks-resources.md) |
| 03 | **DBMS** | [Notes](./03-DBMS/notes/dbms.md) | [30 Q&A](./03-DBMS/interview-questions/dbms-interview-questions.md) | [Resources](./03-DBMS/resources/dbms-resources.md) |
| 04 | **OOPS** | [Notes](./04-OOPS/notes/oops.md) | [30 Q&A](./04-OOPS/interview-questions/oops-interview-questions.md) | [Resources](./04-OOPS/resources/oops-resources.md) |
| 05 | **DevOps** | [Notes](./05-DevOps/notes/devops.md) | [30 Q&A](./05-DevOps/interview-questions/devops-interview-questions.md) | [Resources](./05-DevOps/resources/devops-resources.md) |
| 06 | **DSA** | [Notes](./06-DSA/notes/dsa.md) | [30 Q&A](./06-DSA/interview-questions/dsa-interview-questions.md) | [Resources](./06-DSA/resources/dsa-resources.md) |
| 07 | **Software Engineering** | [Notes](./07-Software-Engineering/notes/software-engineering.md) | [25 Q&A](./07-Software-Engineering/interview-questions/software-engineering-interview-questions.md) | [Resources](./07-Software-Engineering/resources/software-engineering-resources.md) |
| 08 | **Cloud Computing** | [Notes](./08-Cloud-Computing/notes/cloud-computing.md) | [25 Q&A](./08-Cloud-Computing/interview-questions/cloud-computing-interview-questions.md) | [Resources](./08-Cloud-Computing/resources/cloud-computing-resources.md) |
| 09 | **Cybersecurity** | [Notes](./09-Cybersecurity/notes/cybersecurity.md) | [30 Q&A](./09-Cybersecurity/interview-questions/cybersecurity-interview-questions.md) | [Resources](./09-Cybersecurity/resources/cybersecurity-resources.md) |

> Every subject folder also has its own `README.md` with a suggested revision order and the "must-know" shortlist.

---

## 🏗️ Repository Structure

```
Computer-Fundamentals/
│
├── 01-Operating-Systems/
│   ├── README.md                        # subject overview + revision order
│   ├── notes/                           # complete revision notes
│   ├── interview-questions/             # frequently asked questions WITH answers
│   └── resources/                       # books, courses, YouTube, docs, practice
│
├── 02-Computer-Networks/                # same structure
├── 03-DBMS/                             # same structure
├── 04-OOPS/                             # same structure
├── 05-DevOps/                           # same structure
├── 06-DSA/                              # same structure
├── 07-Software-Engineering/             # same structure
├── 08-Cloud-Computing/                  # same structure
├── 09-Cybersecurity/                    # same structure
│
└── README.md                            # ← you are here
```

---

## 🧭 Suggested Learning / Revision Order

**If you're preparing for placements, follow this priority:**

| Priority | Subject | Why |
|---|---|---|
| 🔴 Core (must-do) | **DSA** | Coding rounds are the first filter — 60–70% of the decision |
| 🔴 Core (must-do) | **Operating Systems** | The most-asked fundamentals subject everywhere |
| 🔴 Core (must-do) | **DBMS** | Almost always combined with live SQL writing |
| 🟠 High | **Computer Networks** | "What happens when you type a URL" is a rite of passage |
| 🟠 High | **OOPS** | Pillars + SOLID + patterns show up in every design conversation |
| 🟡 Role-based | **DevOps / Cloud** | SDE-DevOps, backend, platform, and support-engineering roles |
| 🟡 Role-based | **Software Engineering** | Service companies & QA roles love SDLC/testing theory |
| 🟢 Bonus | **Cybersecurity** | Security roles + general rounds (encryption, HTTPS, XSS) |

---

## 🚀 How to Use This Repo (the 3-pass method)

<details open>
<summary><b>Pass 1 — Learn (weeks, not days)</b></summary>

- Open a subject → `resources/` → follow the **suggested path** at the bottom (it sequences videos → books → practice).
- Read along with the `notes/` file; make the concepts yours, not memorized.
- DSA is different: concepts take days, **problems take months** — start a sheet (NeetCode 150 / Striver A2Z) today.

</details>

<details>
<summary><b>Pass 2 — Revise (1–2 days per subject before interviews)</b></summary>

- Read the subject `notes/` end-to-end (each file ends with a **cheat sheet table**).
- Answer the `interview-questions/` **out loud** before reading the answers — spoken recall is what interviews test.
- Write the classic outputs by hand: SQL queries (Nth salary), a semaphore solution, a subnetting sum.

</details>

<details>
<summary><b>Pass 3 — Simulate (the final 48 hours)</b></summary>

- Only cheat-sheet tables + the "⭐ Must-know" lists in each subject README.
- Do 1–2 mock interviews with a friend; explain "type a URL", ACID, deadlock, and the TLS handshake aloud.
- Sleep. A rested brain retrieves; a crammed one blanks.

</details>

### 💡 Golden rules
- 🗣️ **Answer out loud** — knowing ≠ communicating. Practice speaking in *definition → contrast → example* structure.
- ⏱️ **Spaced repetition beats marathons** — revisit a subject after 3 days, then 3 weeks.
- ✍️ **Whiteboard it** — scheduling numericals, Banker's algorithm, SQL, and LLD designs must be hand-practice.
- 🧪 **Do one lab per subject** — capture a packet in Wireshark, run a Docker container, solve 50 SQL problems. One real experience beats ten videos.

---

## ✅ Quick Revision Checklists

<details>
<summary><b>🖥️ OS — can you answer these right now?</b></summary>

- Process vs thread vs program? · What exactly happens in a context switch?
- The 4 deadlock conditions + how Banker's algorithm avoids deadlock?
- Mutex vs semaphore vs spinlock?
- Paging vs segmentation? What is thrashing and how do you fix it?
- FIFO vs LRU vs Optimal page replacement + Belady's anomaly?
- FCFS/SJF/RR — compute average waiting time for a given table?

</details>

<details>
<summary><b>🌐 CN — can you answer these right now?</b></summary>

- All 7 OSI layers with one function + one protocol each?
- TCP vs UDP? Why a 3-way handshake and not 2?
- Flow control vs congestion control? Slow start & AIMD?
- Explain "what happens when you type a URL" — layer by layer?
- Subnet 192.168.1.0/26 — how many usable hosts?
- HTTP vs HTTPS + the TLS handshake steps?

</details>

<details>
<summary><b>🗄️ DBMS — can you answer these right now?</b></summary>

- Primary vs unique vs foreign key? DELETE vs TRUNCATE vs DROP?
- 1NF → BCNF with one example each?
- ACID + the 4 isolation levels vs dirty/unrepeatable/phantom reads?
- Clustered vs non-clustered index? Why B+ trees?
- Write on paper: Nth highest salary + delete duplicates?
- CAP theorem and SQL vs NoSQL trade-offs?

</details>

<details>
<summary><b>🧱 OOPS — can you answer these right now?</b></summary>

- Encapsulation vs abstraction (the classic confusion)?
- Overloading vs overriding — full rules + the static-method trap?
- Abstract class vs interface + when each?
- Diamond problem and how Java/C++ solve it?
- SOLID — one line + one example each?
- Singleton (thread-safe), Factory, Observer, Strategy — from memory?

</details>

---

## 🤝 Contributing

Contributions make this better for everyone! 🎉

1. **Fork** → create a branch (`git checkout -b feature/dbms-questions`)
2. **Add** — new questions, clearer notes, better resources, typo fixes
3. **Follow the format** — one subject folder per subject; `notes/` + `interview-questions/` + `resources/`
4. **Open a PR** describing what you improved

Ideas welcome: new subjects (System Design, Linux, Git, Programming Languages, Agile), more questions per subject, GATE-focused notes, quiz files.

## 🗺️ Roadmap

- [x] Core structure: 9 subjects × notes / Q&A / resources
- [ ] Subject-specific numerical practice sheets (scheduling, subnetting, Banker's)
- [ ] Quiz/MCQ files per subject
- [ ] System Design & Low-Level Design folder
- [ ] Interview experience logs + company-wise question patterns

## ⭐ Support

If this repo helps you crack an interview, consider giving it a **star** ⭐ — it helps other students find it, and it keeps the motivation to expand it!

---

<div align="center">

**Happy revising — go get that offer letter! 🎉**

*Start here → [Operating Systems](./01-Operating-Systems/README.md) → [Computer Networks](./02-Computer-Networks/README.md) → [DBMS](./03-DBMS/README.md) → [OOPS](./04-OOPS/README.md)*

</div>
