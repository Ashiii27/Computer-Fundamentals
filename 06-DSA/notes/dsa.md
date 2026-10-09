# DSA — Complete Revision Notes

> Covers: Complexity → Arrays & Strings → Linked Lists → Stacks & Queues → Hashing → Searching & Sorting → Trees → Heaps → Graphs → Recursion & Backtracking → Greedy → Dynamic Programming → Patterns cheat sheet

## Table of Contents
1. [Complexity Analysis](#1-complexity-analysis)
2. [Arrays & Strings](#2-arrays--strings)
3. [Linked Lists](#3-linked-lists)
4. [Stacks & Queues](#4-stacks--queues)
5. [Hashing](#5-hashing)
6. [Searching & Sorting](#6-searching--sorting)
7. [Trees](#7-trees)
8. [Heaps / Priority Queues](#8-heaps--priority-queues)
9. [Graphs](#9-graphs)
10. [Recursion & Backtracking](#10-recursion--backtracking)
11. [Greedy](#11-greedy)
12. [Dynamic Programming](#12-dynamic-programming)
13. [Problem-Solving Patterns Cheat Sheet](#13-problem-solving-patterns-cheat-sheet)

---

## 1. Complexity Analysis

- **Big-O** (upper bound), **Ω** (lower), **Θ** (tight). Interviewers want Big-O of *time* and *space*, plus the reason.
- Common classes: O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ) < O(n!).
- Rules: drop constants, keep the dominant term, complexity of **nested loops multiply**, sequential blocks add.

### Operation complexities (memorize)
| Structure | Access | Search | Insert | Delete | Notes |
|---|---|---|---|---|---|
| Array | O(1) | O(n) | O(n) | O(n) | Insert/delete shift elements |
| Dynamic array (ArrayList/vector) | O(1) | O(n) | O(1)* amortized push | O(n) | Doubling growth ⇒ amortized O(1) |
| Linked list | O(n) | O(n) | O(1) at head/known node | O(1) at known node | No random access |
| Stack / Queue | O(n) search | — | O(1) push/enq | O(1) pop/deq | LIFO / FIFO |
| Hash map/set | — | O(1) avg, O(n) worst | O(1) avg | O(1) avg | Worst case all-collisions |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) | Skewed → O(n) |
| Heap | peek O(1) | O(n) | O(log n) | O(log n) pop-min | |

- **Amortized analysis**: average over a sequence of ops — dynamic array doubling, hash resizing.
- **Space**: count recursion depth (call stack) too — DFS on a list is O(n) stack space.

---

## 2. Arrays & Strings

Core techniques (with classic problem tags):
- **Two pointers**: pair-sum in sorted array, remove duplicates, palindrome check, container-with-most-water.
- **Fast & slow pointers**: cycle detection, middle of list (see §3).
- **Sliding window**: longest substring without repeating chars, min-window-substring, max-sum subarray of size k — window grows/shrinks to keep invariant; O(n).
- **Prefix sums**: range-sum queries O(1) after O(n) build; subarray-sum-equals-k with hash map.
- **Kadane's algorithm**: max subarray sum — `cur = max(x, cur + x)`, track best.
- **Dutch national flag**: sort 0/1/2 in one pass with 3 pointers.
- **Rotation trick**: reverse whole array, then reverse parts.
- Strings: char-count arrays/maps, anagram signatures (sorted or count), string builder over concatenation (avoid O(n²)).
- Matrix: transpose, rotate 90° (transpose + reverse rows), spiral traversal, set-matrix-zeroes with O(1) markers.

---

## 3. Linked Lists

- Singly / doubly / circular. Node = value + next.
- **Reverse a linked list** (must be able to code cold):
```java
ListNode prev = null, cur = head;
while (cur != null) {
    ListNode nxt = cur.next;
    cur.next = prev;
    prev = cur; cur = nxt;
}
return prev; // new head
```
- **Cycle detection (Floyd's tortoise & hare)**: slow +1, fast +2; they meet iff a cycle exists. To find cycle *start*: after meeting, move one pointer to head, advance both by 1 — they meet at the entry (math: distance head→entry = distance meeting→entry). O(1) space.
- **Middle node**: fast/slow again — when fast ends, slow is at middle.
- **Merge two sorted lists**: dummy head + tail pointer.
- Remove nth-from-end: two pointers n apart, or two passes.
- Intersection of two lists: switch pointers at ends (equalizes path lengths).
- LRU cache = hashmap + doubly linked list (O(1) get/put) — top interview design.

---

## 4. Stacks & Queues

- **Stack** (LIFO): push/pop/peek O(1). Uses: balanced parentheses, undo, expression evaluation, DFS, monotonic patterns.
- **Monotonic stack**: keep stack sorted (increasing/decreasing); pop when violated — solves *next greater element*, daily temperatures, largest rectangle in histogram in O(n).
- Expression conversion: infix → postfix via stack (operator precedence); postfix evaluation.
- **Queue** (FIFO): circular array implementation; enqueue/dequeue O(1).
- **Deque**: push/pop both ends — sliding-window maximum.
- **Queue via 2 stacks / stack via 2 queues** — classic amorphized-analysis question.
- **Priority queue** → see heaps (§8).

---

## 5. Hashing

- **Hash function**: maps keys → bucket indices. Good = fast, uniform, deterministic.
- **Collision resolution**:
  - **Chaining**: each bucket is a linked list/tree (Java 8+ converts long chains to red-black trees). Simple, load factor can exceed 1.
  - **Open addressing**: probe for next slot — linear probing (clustering), quadratic probing, **double hashing**. Better cache locality; needs load factor < 1 and deletion markers.
- **Load factor** α = n/buckets; resize (rehash) when α crosses threshold (Java default 0.75) — amortized O(1).
- Java: HashMap vs TreeMap (red-black tree — sorted, O(log n)) vs LinkedHashMap (insertion order). C++: unordered_map vs map. Python: dict (insertion-ordered since 3.7).
- Patterns: frequency counting, seen-set for duplicates/pairs, index-map for two-sum-style lookups, grouping by canonical key (group anagrams).

---

## 6. Searching & Sorting

### Binary search (know the template + variants)
```java
int lo = 0, hi = n - 1;
while (lo <= hi) {
    int mid = lo + (hi - lo) / 2;      // overflow-safe
    if (a[mid] == target) return mid;
    if (a[mid] < target) lo = mid + 1; else hi = mid - 1;
}
```
Variants: first/last occurrence (lower/upper bound), rotated sorted array, search on answer space (min pages, Koko bananas), infinite array, peak element.

### Sorting
| Algorithm | Best | Avg | Worst | Space | Stable? | Idea |
|---|---|---|---|---|---|---|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | ✅ | Swap adjacent |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | ❌ | Min each pass |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | ✅ | Insert into sorted prefix (great for nearly-sorted) |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | ✅ | Divide & merge |
| **Quick** | O(n log n) | O(n log n) | **O(n²)** | O(log n) | ❌ | Partition around pivot; in-place; worst on bad pivots/sorted input |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | ❌ | Build heap, extract max n times |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | ✅ | Small integer range k |
| Radix | O(d·(n+k)) | — | — | O(n+k) | ✅ | Digit-by-digit (d digits) |

- **Stability**: equal elements keep original order (matters when sorting by secondary keys). Merge/insertion/bubble/counting stable; quick/heap/selection not.
- **Quickselect**: kth smallest in avg O(n) — partial quicksort.
- Interview favorites: why quicksort despite O(n²) worst (cache-friendly, in-place, rare with random pivots), when mergesort (linked lists, external sort, stability), sort 1M integers 1–1000 (counting).

---

## 7. Trees

- Terminology: root, leaf, height (edges on longest root→leaf path), depth, level, balanced factor.
- **Traversals** (must code all cold):
  - DFS: **preorder** (root-L-R — copy tree, serialize), **inorder** (L-root-R — sorted output for BST), **postorder** (L-R-root — delete tree, evaluate expression).
  - BFS: **level order** (queue) — right side view, zigzag, min depth.
- **BST**: left < root < right ⇒ search/insert/delete O(h). Delete node: 0/1 child easy; 2 children → replace with inorder successor (min of right subtree). Skewed insertion → O(n) → **balanced trees**: AVL (strict, faster lookups) & Red-Black (looser, faster inserts — Java TreeMap/HashMap bins).
- Classic problems: validate BST (pass min/max bounds, not just parent), LCA (BST: walk between p,q; binary tree: recursive both-sides), diameter (height + combine), max path sum, symmetric tree, flatten to list, build tree from preorder+inorder.
- **Trie (prefix tree)**: autocomplete, word search, prefix counting — insert/search O(L).
- B-tree/B+ tree: multi-way balanced trees for disk-based stores (DB indexes — see DBMS notes).

---

## 8. Heaps / Priority Queues

- **Binary heap**: complete binary tree in an array; parent ≤ children (min-heap); children of i = 2i+1, 2i+2; parent = (i−1)/2.
- **Build heap in O(n)** (heapify from last parent); push/pop O(log n); peek O(1).
- **Heapsort**: build max-heap, repeatedly swap root with last, shrink — O(n log n), in-place, not stable.
- **Top-K pattern**: k largest → min-heap of size k (O(n log k)); k smallest → max-heap. Stream median → two heaps (max-heap low half + min-heap high half). Merge k sorted lists → heap of k heads.
- When heap vs sorted array vs BST: need repeated min/max access with insertions → heap; need ordered iteration/range → BST.

---

## 9. Graphs

- Representations: **adjacency list** O(V+E) space (default), adjacency matrix O(V²) (dense/fast edge checks), edge list.
- **BFS**: queue, level-by-level, shortest path in **unweighted** graphs — word ladder, rotting oranges (multi-source BFS).
- **DFS**: stack/recursion — connected components, cycle detection (visited + recursion stack for directed / parent-tracking for undirected), flood fill, island counting.
- **Topological sort** (DAG only, directed): Kahn's BFS with indegrees, or DFS + reversed finish order. Uses: course schedule, build order, task dependency.
- **Cycle detection**: undirected (DSU or DFS-parent), directed (3-color / recursion stack).
- **Bipartite check**: 2-coloring via BFS/DFS.
- **Shortest paths**:
  | Algorithm | Weights | Complexity | Notes |
  |---|---|---|---|
  | BFS | unweighted | O(V+E) | Also unit weights |
  | **Dijkstra** | non-negative | O((V+E) log V) with heap | Greedy; fails on negatives |
  | **Bellman-Ford** | negatives OK | O(V·E) | Detects negative cycles |
  | Floyd-Warshall | all pairs | O(V³) | DP over intermediate nodes |
- **MST**: Kruskal (sort edges + **DSU/Union-Find** with path compression & union by rank — near O(1) per op) vs Prim (heap, like Dijkstra).
- Grid problems are graphs in disguise: cells = nodes, neighbors = edges.

---

## 10. Recursion & Backtracking

- Every recursion needs: **base case** + recursive case that shrinks the problem. Complexity often O(branch^depth).
- **Backtracking template** — choose → explore → un-choose:
```java
void backtrack(State s) {
    if (isGoal(s)) { record(s); return; }
    for (choice c : choices(s)) {
        apply(s, c);
        backtrack(s);
        undo(s, c);          // restore state
    }
}
```
- Classics: subsets (2ⁿ), permutations (n·n!), combinations, N-Queens (row-by-row + column/diagonal sets), Sudoku, word search, palindrome partitioning.
- Prune early (bounds, feasibility checks) — that's what makes backtracking viable.
- Convert recursion→iteration with explicit stack; memoize overlapping calls (→ DP).

---

## 11. Greedy

- Make the locally optimal choice, prove it's globally safe — **exchange argument**: swapping any other choice with the greedy one never hurts.
- Works when a problem has the **greedy-choice property + optimal substructure**; fails otherwise (know a counterexample, e.g., coin change for {1,3,4} target 6: greedy 4+1+1=3 coins vs optimal 3+3=2).
- Classics: activity/interval scheduling (sort by end time), non-overlapping intervals, minimum platforms, jump game (reachability), fractional knapsack (0/1 needs DP!), Huffman coding, merge intervals.

---

## 12. Dynamic Programming

- **When**: overlapping subproblems + optimal substructure. Signs: "count ways", "min/max value", "can you reach/make".
- **Memoization** (top-down: recursion + cache) vs **tabulation** (bottom-up: loops filling a table) — same complexity; tabulation avoids recursion depth, memo is easier to derive.
- The 4-step recipe: 1) define state `dp[i]`/`dp[i][j]` precisely, 2) write the recurrence, 3) identify base cases, 4) compute order (and note space optimization: often only the previous row/col is needed).

| Classic | State / recurrence | Complexity |
|---|---|---|
| Fibonacci / climbing stairs | `dp[i] = dp[i-1] + dp[i-2]` | O(n) |
| House robber | `dp[i] = max(dp[i-1], dp[i-2] + nums[i])` | O(n) |
| 0/1 Knapsack | `dp[i][w] = max(dp[i-1][w], val[i] + dp[i-1][w-wt[i]])` | O(n·W) |
| Unbounded knapsack / coin change (min coins) | `dp[a] = min(dp[a - c] + 1)` over coins | O(n·A) |
| Coin change (count ways) | iterate coins outer → combinations; amount outer → permutations | O(n·A) |
| Longest Common Subsequence | `dp[i][j] = match ? 1+dp[i-1][j-1] : max(dp[i-1][j], dp[i][j-1])` | O(m·n) |
| Longest Increasing Subsequence | DP O(n²), or patience/binary-search O(n log n) | O(n log n) |
| Edit distance | insert/delete/replace min | O(m·n) |
| Unique paths / min path sum (grid) | `dp[i][j] = dp[i-1][j] + dp[i][j-1]` | O(m·n) |
| Partition equal subset | subset-sum to total/2 | O(n·sum) |

- **DP vs divide & conquer**: D&C subproblems are independent (mergesort); DP subproblems overlap (fib). **DP vs greedy**: greedy commits irrevocably; DP explores all.
- knapsack family: 0/1 (each item once — iterate capacity *downward* in 1-D), unbounded (upward), fractional (greedy).

---

## 13. Problem-Solving Patterns Cheat Sheet

| If the problem says… | Reach for |
|---|---|
| Sorted array, pairs, in-place removal | **Two pointers** |
| Contiguous subarray/substring with constraint | **Sliding window** |
| Range sums, subarray sum = k | **Prefix sum (+ hashmap)** |
| Fast lookup, counts, duplicates | **Hash map/set** |
| Sorted/nearly sorted, "find in rotated" | **Binary search (or on answer)** |
| k largest/smallest/closest, streaming | **Heap** |
| Next greater/smaller, span | **Monotonic stack** |
| Level-by-level, shortest path unweighted | **BFS** |
| Explore all paths, islands, components | **DFS / backtracking** |
| Dependencies, ordering | **Topological sort** |
| Connected?, groups, cycle (undirected) | **Union-Find (DSU)** |
| Shortest path weighted | **Dijkstra** (negatives → Bellman-Ford) |
| Count ways / min cost / can-we | **DP** |
| Intervals (merge, overlap, rooms) | **Sort + sweep/greedy** |
| Prefixes of strings | **Trie** |
| LRU / O(1) get+put | **Hashmap + doubly linked list** |
| Linked list cycle/middle/kth-from-end | **Fast & slow pointers** |

## 💡 Interview execution framework (memorize)
1. **Clarify** — inputs, sizes (n up to 10⁵ ⇒ O(n log n) max), edge cases, duplicates.
2. **Brute force out loud** with complexity — gives you a floor.
3. **Optimize** — walk the pattern table above; state the bottleneck you're removing.
4. **Agree on approach with interviewer before coding.**
5. **Code clean** — meaningful names, guard clauses, helper decomposition.
6. **Dry-run** on an example + an edge case; then state final time/space.
