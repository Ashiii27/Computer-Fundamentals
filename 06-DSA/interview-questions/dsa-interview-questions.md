# DSA — Most-Frequently-Asked Concept Questions (with Answers)

> Coding rounds are ~70% actually *solving* problems (use the pattern table in the notes) — these concept questions are the other 30%, often asked while reviewing your solution.

---

### 1. Array vs linked list — when does each win?
Array: contiguous memory, O(1) index access, cache-friendly — win when reads dominate and size is predictable. Linked list: O(1) insert/delete at a known node, no resizing/moves — win when insertion/deletion dominates or you need a deque/queue with stable node pointers. Costs flipped: arrays pay O(n) for mid-inserts; lists pay O(n) to even *reach* a node (plus per-node pointer memory).

### 2. Time complexities of the common sorting algorithms?
Bubble/Selection/Insertion: O(n²) (insertion O(n) best — nearly sorted). Merge: O(n log n) all cases, O(n) space, stable. Quick: O(n log n) average, O(n²) worst (bad pivots), in-place-ish, not stable. Heap: O(n log n) guaranteed, O(1) space, not stable. Counting/radix: O(n+k)/O(d(n+k)) for restricted inputs.

### 3. What is a stable sort and why does it matter?
Equal keys keep their original relative order. Matters for multi-key sorting (sort by name, then stably by department ⇒ grouped by dept, alphabetical within), and for algorithms building on order (radix sort needs stable digit passes). Stable: merge, insertion, bubble, counting. Unstable: quick, heap, selection.

### 4. Quicksort vs mergesort — which and why?
Quicksort: in-place, excellent cache behavior, fastest in practice — but O(n²) worst case (mitigated by random/median-of-3 pivots) and unstable. Mergesort: guaranteed O(n log n), stable, but O(n) extra space. Default for arrays: quicksort (introsort guards its worst case). For linked lists (no random access, no shifting cost): mergesort. External sorting of disk data: mergesort.

### 5. Binary search requirements + why is it O(log n)?
Requires sorted (or monotonic) order and random access. Each comparison halves the search space → log₂n comparisons. Know the variants: first/last occurrence (lower/upper bound), rotated array, peak element, and **binary search on the answer** ("minimize the maximum…" → binary search the answer, greedy-check feasibility).

### 6. How does a hash table work? How are collisions handled?
Hash function maps key → bucket index → O(1) average access. Collisions: **chaining** (list/tree per bucket — Java turns long chains into red-black trees) or **open addressing** (probe linearly/quadratically/double-hash to the next free slot). Resize when load factor crosses a threshold (≈0.75) so buckets stay sparse — rehashing is O(n) but amortized O(1) per insert.

### 7. Hash map vs tree map?
Hash map: O(1) average ops, unordered — default choice. Tree map (red-black tree): O(log n) but **sorted order**, range queries, floor/ceiling — use when you need ordering or worst-case guarantees. (C++: unordered_map vs map; Java: HashMap vs TreeMap; Python dict is ordered by insertion but not sorted.)

### 8. Stack vs queue — real use cases?
Stack (LIFO): function call stack, undo/redo, balanced parentheses, expression evaluation, DFS, monotonic-stack problems. Queue (FIFO): scheduling, BFS, rate limiting/buffering, task queues. Deque adds both-end operations — sliding window maximum.

### 9. Detect a cycle in a linked list? Find where it starts?
Floyd's tortoise & hare: slow moves 1, fast moves 2; if they meet, a cycle exists (O(n) time, O(1) space). For the start node: after meeting, move one pointer to head; advance both by 1 — they meet at the cycle entry (the head→entry distance equals the meeting→entry distance). Alternative: hash set of visited nodes (O(n) space).

### 10. How do you reverse a linked list iteratively (and recursively)?
Iterative: three pointers — `prev=null`, walk `cur`, save `next`, flip `cur.next=prev`, shift forward; O(n)/O(1). Recursive: reverse the rest, then attach `head.next.next = head; head.next = null`; O(n)/O(n) stack. Know both; interviewers often ask to switch mid-problem.

### 11. BFS vs DFS — when to use which?
BFS: queue, explores by distance → **shortest path in unweighted graphs**, level-order, nearest-neighbor problems; memory O(width). DFS: stack/recursion, dives deep → cycle detection, topological sort, connected components, backtracking paths; memory O(depth). Grid problems: BFS for "fewest steps", DFS for "count islands".

### 12. What is topological sorting and where is it used?
A linear ordering of a **DAG** where every edge points forward. Kahn's algorithm: repeatedly remove nodes with indegree 0 (BFS flavor), or DFS + push on finish (reversed). Uses: course prerequisites, build/dependency order, task scheduling. If at some point no indegree-0 node exists → cycle → no valid order (that's the cycle-detection trick too).

### 13. Dijkstra vs BFS vs Bellman-Ford?
BFS: unweighted (or unit weights) shortest path, O(V+E). Dijkstra: non-negative weights, greedy with a min-heap, O((V+E) log V) — fails with negative edges. Bellman-Ford: handles negative weights, O(V·E), and detects negative cycles (relax V−1 times; a V-th improvement ⇒ cycle). All-pairs small graphs: Floyd-Warshall O(V³).

### 14. Kruskal vs Prim (MST)?
Both build minimum spanning trees. Kruskal: sort all edges, add cheapest that doesn't form a cycle (Union-Find checks) — great for sparse graphs/edge lists. Prim: grow one tree from a start node, always add the cheapest edge leaving it (min-heap) — great for dense graphs with adjacency lists. Both greedy; both O(E log V) with proper structures.

### 15. What is Union-Find (DSU) and why is it so fast?
Disjoint Set Union tracks connected components under two ops: find (which set?) and union (merge sets). With **path compression** + **union by rank/size**, both are near O(1) — inverse Ackermann α(n) in practice. Uses: cycle detection in undirected graphs, Kruskal's, connected components, accounts-merge style problems.

### 16. Recursion vs iteration — trade-offs?
Recursion: matches divide-and-conquer/tree structure, cleaner code; costs call-stack memory (depth d ⇒ O(d) space; deep recursion overflows — convert to explicit stack/iteration). Iteration: constant space, faster (no call overhead), but can get gnarly for tree-shaped problems. Tail-recursive forms can be optimized (not guaranteed in Java/Python).

### 17. What is backtracking? Give the template.
DFS over the space of choices with undo: choose → explore → un-choose. Prune branches that can't lead to a solution (feasibility/bounds). Covers subsets, permutations, N-Queens, Sudoku, word search. Complexity O(b^d) worst case, but pruning makes it practical.

### 18. Memoization vs tabulation?
Both cache overlapping subproblems. Memoization = top-down recursion + cache (write the brute force, add a map); tabulation = bottom-up loops filling a table (better constant factors, no stack depth, easy space optimization). Same asymptotics; choose by comfort — then often optimize space (only previous row needed for many 2-D DPs).

### 19. DP vs greedy vs divide & conquer?
D&C: subproblems are **independent** (mergesort). DP: subproblems **overlap** → cache them. Greedy: make the irrevocably-best local choice — only correct with the greedy-choice property (prove via exchange argument; counterexample: coins {1,3,4}, amount 6 → greedy gives 3 coins, optimal is 3+3). When greedy fails or is hard to prove, reach for DP.

### 20. Explain 0/1 knapsack.
n items, weights wt[i], values val[i], capacity W — maximize value, each item used at most once. `dp[i][w] = max(dp[i-1][w], val[i] + dp[i-1][w-wt[i]])`; answer dp[n][W]; O(nW) pseudo-polynomial. 1-D optimization: iterate capacity **downward** so each item is used once (upward ⇒ unbounded knapsack). Variants: subset-sum, partition-equal-subset (target = total/2).

### 21. LCS / LIS — states and recurrences?
**LCS** of X,Y: `dp[i][j] = X[i]==Y[j] ? dp[i-1][j-1]+1 : max(dp[i-1][j], dp[i][j-1])` — O(mn); backbone of diff tools. **LIS**: `dp[i] = 1 + max(dp[j])` for j<i with a[j]<a[i] (O(n²)), or patience sorting with binary search (O(n log n)). Both are the classic "define the state, write the transition" interview checks.

### 22. What is a heap? Heap vs BST for priority queues?
Complete binary tree stored in an array where each parent ≤ (min-heap) its children: peek O(1), push/pop O(log n), build O(n). Heaps only give fast **extremes** — no ordered iteration/range/search. BST gives O(log n) for everything including ordered traversal. Priority-queue semantics (repeatedly take min/max) → heap; need sorted structure too → BST/tree-map.

### 23. Why is building a heap O(n) when n inserts would be O(n log n)?
`heapify` builds bottom-up: nodes at height h cost O(h) to sift down; summing over the tree, most nodes sit near the leaves (cheap) → total is linear. Inserting one-by-one pays the full log n depth per element — the top-down cost. Classic amortized-analysis talking point.

### 24. What is a trie? When do you use one?
Prefix tree storing strings character-by-character with shared prefixes; insert/search/prefix all O(L) (word length, independent of dictionary size!). Use for autocomplete, spell-check, prefix counting, word-search boards. Cost: memory (mitigate with maps/arrays per node, compressed tries).

### 25. Amortized analysis — explain with an example.
Average cost per operation over a worst-case *sequence*. Dynamic array: most pushes are O(1); occasional doubling is O(n) — but doubling happens after Ω(n) cheap pushes, so amortized push is O(1). Same logic for hash-table rehashing, and two-stack queue (each element moves ≤ twice).

### 26. What is a sliding window and when does it apply?
Maintain a contiguous window with two pointers; expand right, shrink left while a constraint is violated — each pointer moves ≤ n times ⇒ O(n). Applies to contiguous subarray/substring problems with a monotone-feasible property: longest substring without repeats, min window covering chars, max sum subarray of size k, at-most-k-distinct. (Not for "subsequence" or non-monotone constraints.)

### 27. In-place vs extra-space algorithms — give examples.
In-place: quicksort partition, heapify, array reversal/rotation tricks, dutch-flag — O(1) aux. Extra space: mergesort O(n), counting sort O(k), hashing patterns O(n), recursion stack. Interviewers often push "can you do it with O(1) space?" — know the pointer tricks (two pointers, cyclic sort, marking within the array).

### 28. How would you approach a completely unseen problem? (The meta-question.)
Clarify constraints and sizes (they hint the allowed complexity) → state a brute force with complexity → identify the bottleneck → map to a pattern (two pointers/hashing/heap/DP…) from the cheat sheet → dry-run on a small example → code → test edges (empty, single, duplicates, extremes) → give final time/space. Thinking aloud through this process is itself being graded.

### 29. Pseudo-polynomial vs polynomial complexity?
0/1 knapsack is O(nW) — polynomial in the *numeric value* of W, but exponential in its *bit length* (input size), hence pseudo-polynomial. Contrast with true polynomial algorithms (O(n log n) sorting). It's why knapsack is "hard-ish" but practical for bounded W — and why subset-sum is NP-complete yet solvable by DP.

### 30. What's the difference between time complexity and the actual runtime you'd measure?
Complexity is asymptotic growth ignoring constants/hardware; real time = complexity × constant factors × cache behavior × I/O. That's why O(n²) with tiny constants can beat O(n log n) for small n (insertion sort in hybrids like Timsort/introsort), and why cache-friendliness makes arrays beat linked lists even at equal complexity. Answer both axes when asked "which is faster?"

---

## The 150-problem rule
Concepts get you through the Q&A; **muscle memory gets you through coding rounds**. Work NeetCode 150 or Striver's A2Z sheet (links in resources) with spaced repetition, and re-solve every failure from a blank editor 3 days later.
