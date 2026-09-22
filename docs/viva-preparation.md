# Viva Preparation (Every Member)

The viva is a strict **6-minute session per team**, and each student is scored
out of 50 (Git 10 + algorithm knowledge 20 + code comprehension 20).
**You are expected to understand the entire solution, not just the parts you
wrote.** Scoring "Average" (8–11) means knowing only your own portion.

Run a cross-teaching session before the viva. Each owner explains their part
(Parts 1–4) to the other three, and then members quiz each other with the
questions below.

---

## 1. Algorithm knowledge (20 marks)

| # | Question | Short answer |
|---|---|---|
| 1 | What fringe does each algorithm use? | DFS: Stack. BFS: Queue. UCS: PQ on g. A*: PQ on g+h |
| 2 | Is DFS complete or optimal? | Complete on finite graphs with the expanded set. Not optimal |
| 3 | When is BFS optimal? | When every step has the same cost |
| 4 | BFS vs UCS? | UCS orders by path cost. With unit costs they behave the same |
| 5 | A* with h = 0? | It becomes UCS |
| 6 | Admissible? | h(n) ≤ true remaining cost, and h ≥ 0 |
| 7 | Consistent? | h(n) ≤ c(n, n') + h(n'), and h(goal) = 0 |
| 8 | Which one does graph-search A* need, and why? | Consistency. f never decreases, so the first expansion of a state is via its optimal path |
| 9 | Does consistent imply admissible? | Yes (the reverse is usually but not always true) |
| 10 | How do you invent an admissible heuristic? | Relax the problem (for example, remove walls). The relaxed optimum is a lower bound |
| 11 | Dominance? | If h1 ≥ h2 and both are admissible, h1 expands no more nodes |
| 12 | Graph vs tree search? | Graph search keeps an expanded set, so states are never re-expanded |
| 13 | Why goal-test on pop? | A cheaper path may still be on the fringe, and testing on push breaks UCS/A* optimality |
| 14 | Time and space of BFS vs DFS? | BFS O(b^d) in both. DFS time O(b^m), space O(bm) |

## 2. Code comprehension (20 marks)

Be able to point to the exact lines for each of these:

- **`_graphSearch`**: where the fringe is pushed and popped, where the goal is tested, and where `expanded` is checked. Why is it a `set`?
- **Fringe item** `(state, actions, g)`: why store the whole action list? Why `actions + [action]` rather than `append`?
- **`aStarSearch`**: where `h` is added and what arguments `heuristic` receives.
- **`CornersProblem` state** `(position, visitedTuple)`: why a tuple, why not store walls, and the start-on-a-corner case.
- **`getSuccessors`**: the wall check `self.walls[x][y]`, and why `self._expanded += 1` must stay.
- **`cornersHeuristic`**: permutations of the remaining corners, Manhattan tour length, proofs of admissibility and consistency. Result: 741 nodes on mediumCorners.
- **`foodHeuristic`**: max `mazeDistance` to the remaining food, caching in `problem.heuristicInfo`, proofs. Result: 4137 nodes on trickySearch.
- Why a *sum* of distances would be inadmissible in both heuristic questions.

## 3. Git (10 marks)

- Know your own commits: what each one changed and why.
- Be ready to show your contribution graph and PRs, and to explain the branching workflow.

## 4. Quick demo commands

```bash
cd search
python pacman.py -l mediumMaze -p SearchAgent -a fn=dfs
python pacman.py -l mediumMaze -p SearchAgent -a fn=bfs
python pacman.py -l mediumMaze -p SearchAgent -a fn=ucs
python pacman.py -l bigMaze -z .5 -p SearchAgent -a fn=astar,heuristic=manhattanHeuristic
python pacman.py -l mediumCorners -p AStarCornersAgent -z 0.5
python pacman.py -l trickySearch -p AStarFoodSearchAgent
python autograder.py
```
