<!-- solve-problem:fee15eab-48d0-46c8-95d8-d5d5863f52ce -->
# Work-efficient polylog-depth directed reachability

A [Tangle Solve problem](https://solve.tangle.tools/problems/fee15eab-48d0-46c8-95d8-d5d5863f52ce).

## What is the problem?

Given a directed graph \(G=(V,E)\) with \(n\) vertices and \(m\) edges, decide whether there is a directed path from a source \(s\) to a target \(t\) (or compute the transitive closure), on a parallel machine model such as CRCW PRAM or the modern multicore/BSP-like setting. The goal is an algorithm simultaneously achieving:

- **Work** (total processor-time product): \(W = O(m \cdot \mathrm{polylog}(n))\) — i.e., within polylogarithmic factors of the optimal sequential \(O(m+n)\) BFS/DFS.
- **Depth** (parallel time / critical path length): \(T = \mathrm{polylog}(n)\).

By Brent's theorem, work and depth together determine processors: \(P = W/T\). Known results achieve one property but not both. Note that s-t reachability is complete for NL under logspace reductions, so any resolution also bears on the parallel complexity of logspace (fine, since Reingold's \(O(\log n)\)-space undirected algorithm shows undirected reachability is in L and parallelizes cleanly). The obstruction is specific to *directedness*. A reasonable formalization: prove existence or non-existence of a randomized (or deterministic) PRAM algorithm with \(W = m\,\mathrm{polylog}(n)\) and \(T = \mathrm{polylog}(n)\) for s-t reachability; alternatively for full transitive closure, where the natural work target becomes \(O(n^2 \mathrm{polylog}(n))\) since the output can be that large.

## Why does it matters?

Work-efficient polylog-depth reachability would make parallel processing of massive sparse directed graphs (web graphs, IRCs in verification, state-space exploration in model checking, dependency resolution) actually cost-effective — near-sequential total work with exponential wall-clock speedup. It is the canonical open question bridging parallel complexity theory and practical multicore/BSP graph computation, and it gates analogous results for parallel algorithms on PDDL-like directed structures, strongly connected components, and shortest-path approximations.

## What's already known?

- **Polylog depth, excessive work:** Ullman–Yannakakis (1991) compute transitive closure in \(O(\log^2 n)\)-ish depth but with roughly \(O(n^{\omega})\)- or \(n^2\)-type work via repeated squaring / matrix multiplication — far from \(m\,\mathrm{polylog}\) for sparse graphs. Their key technique is "path doubling" over shortcut/braiding structures, which shrinks diameter but inflates edge count (and hence work) by polynomial factors.
- **Near-linear work, excessive depth:** Repeated-squaring-free approaches — parallel BFS, frontier-based methods — achieve \(O(m)\)-ish work but depth \(\Theta(\text{directed diameter})\), which is \(\Omega(n)\) in general. Random shortcutting (adding random shortcut edges à la Ullman–Yannakakis sampling) reduces diameter to \(\tilde{O}(\sqrt{n})\) or \(\tilde{O}(n/\log n)\) with near-linear added edges, but never to polylog.
- **Undirected case: solved.** Reingold's logspace undirected reachability (2005, JACM) yields work-efficient \(O(\log n)\)-depth algorithms for undirected connectivity. The entire difficulty is directionality.
- **Tradeoff lower bounds:** Spencer (1997) formalizes time–work tradeoffs for reachability/transitive closure, showing known points on the tradeoff curve and that the polylog×near-linear corner is not achieved by existing techniques. Randomized "random-mate" / pointer-jumping methods (e.g., for connectivity, Shiloach–Vishkin) do not extend to directed reachability because directed contraction loses path information.

The known barrier: any doubling/shortcut scheme that kills diameter also multiplies edges; any work-conserving scheme leaves long directed chains (diameter) unbroken.

## What would count as solving it?

1. **Positive result:** a (randomized) PRAM algorithm for directed s-t reachability with \(W = O(m\,\mathrm{polylog}(n))\) and depth \(O(\mathrm{polylog}(n))\), with success probability \(1 - 1/\mathrm{poly}(n)\). Deterministic variant is stronger.
2. **Negative/barrier result:** e.g., evidence within a concrete model (restricted classes of algorithms, or conditional on complexity assumptions like NC ≠ NL / no work-efficient NC¹ algorithms for reachability) that the two targets cannot be met simultaneously.
3. **Intermediate:** a genuine improved tradeoff point, e.g., \(W = m\, n^{o(1)}\) with depth \(n^{o(1)}\), or \(W = m\,\mathrm{polylog}\) with depth \(n^{1/2-\epsilon}\), would be significant.

A referee would want full proofs of work/depth bounds; heuristics or empirical speedups alone do not resolve it.

## How to start

1. Read Ullman–Yannakakis (1991) carefully — understand exactly why random braiding/shortcutting costs \(\omega(m)\) work, and where the edge blowup is forced.
2. Read Spencer (1997) for the formal tradeoff framework and to calibrate what "known" tradeoff points exist; identify precisely which corner is empty.
3. Consider hybrid schemes: apply shortcuts only to a sampled subgraph or to long chains detected by parallel pointer jumping; recent work on parallel low-diameter decomposition is for undirected graphs — check whether any directed analogue (e.g., via random vertex sampling plus interval/shortcut structures à la Fineman (2018?) "Ladder" for single-source shortest paths, (verify)) can be bootstrapped.
4. Main obstacle: directed diameter with near-linear edge budget — any local shortcut operation either preserves a long path or duplicates it many times. A useful exercise is proving a lower bound for "shortcut-graph + BFS" style algorithms to understand whether new techniques are mandatory.

## Key references

- **Ullman, J. D., Yannakakis, M. (1991), "High-Probability Parallel Transitive-Closure Algorithms," SIAM J. Comput. 20(1).** Gives randomized PRAM transitive-closure algorithms with polylog depth via path-doubling over random shortcut graphs, at the cost of superlinear work on sparse graphs. (verify exact volume/pages)
- **Spencer, J. H. (1997), "Time–Work Tradeoffs for Parallel Algorithms."** Formalizes and probes the achievable (time, work) pairs for reachability-type problems, delimiting known tradeoff curves. (verify venue/details — citation as given in census record)
- **Reingold, O. (2008), "Undirected Connectivity in Log-Space," JACM 55(4).** Solves undirected reachability in logspace, implying work-efficient polylog-depth undirected connectivity — the contrast case showing directedness is the blocker.
- **(Background) Chazelle, B. (?), or Fineman, J. (2018+), work on parallel shortcutting / ladders for directed reachability in the FG (families-of-graphs) model.** Modern treatment of shortcut-edge tradeoffs for parallel directed reachability. (verify — active research area; search "parallel shortcutting directed reachability Fineman")

## Warnings

- Distinguish **s-t reachability**, **transitive closure**, and **single-source reachability**; work targets differ (\(m\,\mathrm{polylog}\) vs. potentially \(n^2\)). The census statement most plausibly means s-t reachability.
- Don't confuse with **undirected** connectivity — that is fully solved (Reingold/Shiloach–Vishkin); naive adaptations fail on directed graphs.
- Beware the trap of "shortcutting" results that assume bounded graph diameter or a family-of-graphs model; those don't resolve the general problem.
- The census record is machine-generated (source: "gpt6pro"); the Spencer 1997 citation in particular may be imprecise — verify before relying on it.

---
*`ALGO-000123` · unverified*

## Acceptance guidance

1. **Positive result:** a (randomized) PRAM algorithm for directed s-t reachability with \(W = O(m\,\mathrm{polylog}(n))\) and depth \(O(\mathrm{polylog}(n))\), with success probability \(1 - 1/\mathrm{poly}(n)\). Deterministic variant is stronger.
2. **Negative/barrier result:** e.g., evidence within a concrete model (restricted classes of algorithms, or conditional on complexity assumptions like NC ≠ NL / no work-efficient NC¹ algorithms for reachability) that the two targets cannot be met simultaneously.
3. **Intermediate:** a genuine improved tradeoff point, e.g., \(W = m\, n^{o(1)}\) with depth \(n^{o(1)}\), or \(W = m\,\mathrm{polylog}\) with depth \(n^{1/2-\epsilon}\), would be significant.

A referee would want full proofs of work/depth bounds; heuristics or empirical speedups alone do not resolve it.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/fee15eab-48d0-46c8-95d8-d5d5863f52ce?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000123.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000123)
