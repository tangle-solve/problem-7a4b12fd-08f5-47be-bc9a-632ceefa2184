<!-- solve-problem:7a4b12fd-08f5-47be-bc9a-632ceefa2184 -->
# Logarithmic dynamic planar nearest neighbors

A [Tangle Solve problem](https://solve.tangle.tools/problems/7a4b12fd-08f5-47be-bc9a-632ceefa2184).

## What is the problem?

Design a data structure maintaining a set \(S\) of \(n\) points in the plane under the Euclidean metric that supports:

- **Insert(\(p\))**: add point \(p\) to \(S\).
- **Delete(\(p\))**: remove point \(p\) from \(S\).
- **Query(\(q\))**: return \(\arg\min_{p \in S} |pq|\), the exact nearest neighbor of query point \(q\).

The target complexity is \(O(\log n)\) **amortized cost per operation** (each of insert, delete, and query), using \(n \log^{O(1)} n\) (ideally \(O(n)\)) space. This is the dynamic planar Euclidean nearest-neighbor problem. Note carefully: the goal is *exact* nearest neighbors (not approximate, as in the well-solved LSH/dynamic approximate literature), in the plane, with a *logarithmic* — not merely polylogarithmic — bound. The static problem is classically solved at \(O(\log n)\) query time with \(O(n)\) space via Delaunay triangulation + point location; the open question is matching that while supporting updates. Ambiguity: it is not specified whether randomized (expected) bounds or worst-case vs. amortized are acceptable; the natural reading is amortized expected cost is fine, matching Chan's structure.

## Why does it matter?

A logarithmic dynamic NN structure would be the definitive answer to a long-standing entry in the Open Problems Project (TOPP 63), settling the true complexity of a core geometric primitive. It directly improves kinetic/dynamic proximity maintenance used in robotics, simulation, and VLSI/physical design, where continuous motion means constant updates. It would also likely require new machinery for dynamic lower envelopes / dynamic Delaunay triangulations, which would spill over into dynamic convex hulls and dynamic planar point location.

## What's already known?

- The static problem is solved: \(O(n)\) space, \(O(\log n)\) query via Delaunay triangulation and point location, built in \(O(n \log n)\) time.
- Chan (SODA 2006 / journal version, verify exact venue) gave the landmark dynamic structure: \(O(\log^2 n)\) amortized **expected** update and query time with \(O(n)\)-ish space, by reducing planar NN to dynamic 3D convex hull / dynamic lower envelopes of pseudo-lines and applying his dynamic planar convex hull machinery with logarithmic factors composing. This collapsed the prior polylog exponents and remains essentially the best known general bound.
- Subsequent and related work on dynamic Voronoi diagrams (e.g., Kaplan–Mulzer–Roditty–Seifer–Sharir on dynamic nearest-neighbor in the plane, via randomized incremental machinery) gives other polylogarithmic bounds but none reaching \(O(\log n)\) for updates (verify current best exponent; treat any claimed improvement below \(\log^2\) skeptically).
- For the *approximate* version, \(O(\log n)\) (even better) bounds are known and not relevant to this problem.
- No superlogarithmic lower bound is known beyond trivial cell-probe/certainty-type arguments; the \(\log^2\) vs. \(\log\) gap is unexplained by lower bounds.

## What would count as solving it?

1. **Upper bound (main target):** a randomized or deterministic structure achieving \(O(\log n)\) amortized cost per insert, delete, and query with near-linear space, with a full proof of correctness and running time. Referee criterion: a complete complexity analysis, not just heuristics.
2. **Lower bound variant:** a cell-probe or pointer-machine lower bound of \(\omega(\log n)\) (e.g., \(\Omega(\log^2 n / \log\log n)\)) for exact dynamic planar NN, proving the \(O(\log n)\) target unattainable — this would *close* the problem in the other direction.
3. **Intermediate credit:** shaving the exponent (e.g., \(O(\log^{3/2} n)\) or \(O(\log^2 n / \log\log n)\) updates) is a publishable step.

## How to start

1. Read Chan's dynamic NN paper first — understand exactly where the second log factor comes from (it comes from a reduction to dynamic lower-envelope queries whose structures each cost \(O(\log n)\), composed across a recursion/hierarchy). The whole game is eliminating one composed log.
2. Identify the bottleneck: is it the reduction NN → dynamic 3D hull / vertical decomposition, or the dynamic hull structure itself? If the reduction composes two logarithmic structures, look for a direct dynamic Delaunay/Voronoi approach that pays only one log.
3. Consider the randomized-incremental "history graph" approach: static Delaunay has a clean randomized incremental construction; the obstacle is that deletions corrupt the history DAG, and known fixes (backtracking, rebuild schedules) cost an extra log. Look for weight-balanced or entropy-based rebuild schemes.
4. As a sanity oracle, build a small experimental prototype to test whether structural changes under updates are local (they are not always — Delaunay flips can cascade), which tells you where amortization must come from.

## Key references

- T. M. Chan, "A dynamic data structure for 3-dimensional convex hulls and 2-dimensional nearest neighbor queries," SODA 2006 (journal version later, verify). Achieves \(O(\log^2 n)\) amortized expected update/query for planar NN via dynamic 3D convex hull techniques; the benchmark to beat.
- The Open Problems Project, Problem 63 ("Dynamic nearest neighbor queries" or similar, verify exact numbering/title), https://topp.openproblem.net/ — states the open problem and its history.
- Kaplan, Mulzer, Roditty, Seifer, Sharir, work on dynamic Voronoi diagrams and nearest neighbors in the plane (SODA ~2017; verify exact citation) — alternative randomized-incremental machinery with polylog bounds.
- Chan's dynamic planar convex hull papers (e.g., "Dynamic planar convex hull," ~2001; verify) — the underlying substructure; its log factor is inherited.

## Warnings

- Do not confuse this with the *approximate* dynamic NN problem or high-dimensional NN — both have richer, different literature with much better bounds that do not apply here.
- The reduction chain in Chan's paper (NN → 3D hull → lower envelopes → dynamic halfplane/point location) composes logarithmic factors; a common mistake is "solving" one level and forgetting the others.
- Delaunay triangulations can change by \(\Theta(n)\) edges after a single update in contrived point sets, so worst-case per-operation \(O(\log n)\) is implausible; amortization is essential — don't attempt a worst-case-bound structure first.
- TOPP numbering and the exact current best bound should be verified against the live TOPP page; the census record may lag.

---
*`ALGO-000150` · unverified*

## Acceptance guidance

1. **Upper bound (main target):** a randomized or deterministic structure achieving \(O(\log n)\) amortized cost per insert, delete, and query with near-linear space, with a full proof of correctness and running time. Referee criterion: a complete complexity analysis, not just heuristics.
2. **Lower bound variant:** a cell-probe or pointer-machine lower bound of \(\omega(\log n)\) (e.g., \(\Omega(\log^2 n / \log\log n)\)) for exact dynamic planar NN, proving the \(O(\log n)\) target unattainable — this would *close* the problem in the other direction.
3. **Intermediate credit:** shaving the exponent (e.g., \(O(\log^{3/2} n)\) or \(O(\log^2 n / \log\log n)\) updates) is a publishable step.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/7a4b12fd-08f5-47be-bc9a-632ceefa2184?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000150.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000150)
