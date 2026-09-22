# Part 2 — Informed Search: A* (Q4) + Corners Heuristic (Q6)

**Owner:** Member 2 · **Autograder marks:** 12 (Q4 4 + Q6 8)
**Files edited:** `search/search.py` (`aStarSearch`), `search/searchAgents.py` (`cornersHeuristic`)
**Shared duty:** write the admissibility and consistency arguments for both
heuristics (Q6, and Q7 together with Member 4) in the report.

---

## 1. Q4 — A* Search (4 marks)

A* expands the node with the lowest `f(n) = g(n) + h(n)`:
- `g(n)` is the cost already paid to reach `n`.
- `h(n)` is the heuristic's estimate of the remaining cost to a goal.

It reuses Part 1's `_graphSearch` helper, adding the heuristic to the priority:

```python
def aStarSearch(problem: SearchProblem, heuristic=nullHeuristic):
    return _graphSearch(problem, util.PriorityQueue(), usePriority=True, heuristic=heuristic)
```

- `heuristic(state, problem)` is called with **two** arguments.
- With `nullHeuristic` (always 0), A* is exactly UCS.
- **Optimality (graph search) requires a consistent heuristic.** Admissible alone
  is enough only for tree search.

```bash
python autograder.py -q q4
python pacman.py -l bigMaze -z .5 -p SearchAgent -a fn=astar,heuristic=manhattanHeuristic
```
On `bigMaze`, A* with Manhattan distance finds the optimal cost of 210 while
expanding fewer nodes than UCS (about 549 compared with about 620).

---

## 2. Key theory you must be able to defend

| Property | Definition | Why it matters |
|---|---|---|
| **Admissible** | `0 ≤ h(n) ≤ h*(n)` (never overestimates the true remaining cost) | A* tree search is optimal |
| **Consistent** | `h(n) ≤ c(n, n') + h(n')` for every edge, and `h(goal) = 0` | A* graph search is optimal, because f never decreases along a path |
| Consistent ⇒ admissible | By induction along the optimal path to a goal | — |
| Dominance | If `h1(n) ≥ h2(n)` for all n (both admissible), h1 expands no more nodes | A bigger admissible h means fewer expansions |

A standard way to get an admissible heuristic is to **relax the problem**. For
example, ignoring walls gives Manhattan distance. The exact cost of a relaxed
problem is always admissible.

---

## 3. Q6 — `cornersHeuristic` (8 marks)

### State contract (agreed with Member 3, Q5)

`state = (position, visited)`, where `visited` is a tuple of 4 booleans aligned
with `problem.corners`.

### Grading (nodes expanded on `mediumCorners`)

| Nodes expanded | Marks |
|---|---|
| > 2000 | 0/8 |
| ≤ 2000 | 4/8 |
| ≤ 1600 | 6/8 |
| **≤ 1200** | **8/8** |

Inadmissible means **0** for the whole question.

### Design: shortest Manhattan tour through the remaining corners

Relax the problem by removing the walls. Without walls, the cheapest way to visit
the remaining corners is the best **order** of visiting them, with Manhattan
distance between each consecutive pair. There are at most 4 corners, so we try
all orders (4! = 24 permutations), which is cheap.

```python
def cornersHeuristic(state: Any, problem: CornersProblem):
    corners = problem.corners # These are the corner coordinates
    walls = problem.walls # These are the walls of the maze, as a Grid (game.py)

    import itertools
    position, visited = state
    remaining = [c for c, v in zip(corners, visited) if not v]
    best = 0
    if remaining:
        best = min(sum(util.manhattanDistance(a, b)
                       for a, b in zip((position,) + order, order))
                   for order in itertools.permutations(remaining))
    return best
```

### Measured result (verified)

| Search on `mediumCorners` | Nodes expanded | Path cost |
|---|---|---|
| BFS (no heuristic) | 1966 | 106 |
| A* + this heuristic | **741** | 106 (optimal) |

741 is at most 1200, so this scores **8/8**, and q6 passes every test.

### Proof sketch (put this in the report)

- **Admissible.** Any real path that visits the remaining corners visits them in
  some order. Each leg of that path is at least as long as the Manhattan distance
  of the leg, because walls only make paths longer. So the real cost is at least
  the Manhattan length of *that* order, which is at least the *minimum* over all
  orders, which is `h`.
- **Consistent.** Take one step (cost 1) from `s` to `s'`. Manhattan distance
  satisfies the triangle inequality, so the best tour from the old position is at
  most 1 plus the best tour from the new position. This also holds when the step
  lands on a corner: that corner becomes the tour's free first stop. So
  `h(s) ≤ 1 + h(s')`.
- **Goal gives 0.** No corners remain, so `h = 0`. The value is never negative.

### Heuristics that fail (good viva material)

| Idea | Problem |
|---|---|
| Sum of Manhattan distances to every unvisited corner | Overestimates, because one path serves several corners. **Inadmissible, so 0 marks** |
| Manhattan distance to the nearest corner only | Admissible but weak, so too many expansions |
| Max Manhattan distance to any unvisited corner | Admissible and consistent, but weaker than the tour |
| Greedy nearest-neighbour chain | Not guaranteed to be a lower bound, so it can be inadmissible |

```bash
python autograder.py -q q6
python pacman.py -l mediumCorners -p AStarCornersAgent -z 0.5
```

---

## 4. Suggested commit sequence (Member 2)

1. `feat(search): implement aStarSearch using f = g + h (q4)`
2. `test: verify A* with manhattanHeuristic on bigMaze`
3. `feat(corners): add cornersHeuristic stub against agreed state contract`
4. `feat(corners): heuristic = min Manhattan tour over remaining corners (q6)`
5. `docs(report): admissibility and consistency proof for cornersHeuristic`
6. `docs(report): add Q4 and Q6 explanations and autograder screenshots`

## 5. Viva focus for this part

- What is the difference between admissible and consistent? Give an example that is admissible but not consistent.
- Why does graph-search A* need consistency?
- Why is A* with `h = 0` the same as UCS?
- Prove that your corners heuristic never overestimates.
- Why is "sum of distances to corners" inadmissible?
