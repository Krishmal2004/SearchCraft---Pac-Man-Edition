# Part 1 — Uninformed Search (Q1, Q2, Q3) + Project Setup

**Owner:** Member 1 · **Autograder marks:** 12 (Q1 4 + Q2 4 + Q3 4)
**File edited:** `search/search.py`
**Shared duty:** create the Git repo, verify the environment, and provide the
shared graph-search skeleton that Part 2 reuses for A*.

---

## 1. Setup tasks (do these first, day 1)

1. Create a **public** GitHub repo and push the untouched `search/` folder plus `docs/`.
   Commit: `chore: import SE3062 search project skeleton`.
2. Add all members as collaborators and create the four feature branches (see [README](README.md#5-git-workflow-10-individual-marks)).
3. Environment (every member verifies on their own machine):
   ```bash
   conda create -n cs188 python=3.11
   conda activate cs188
   pip install numpy matplotlib
   cd search
   python pacman.py          # a playable game window should open
   ```
4. Add a `.gitignore` containing `__pycache__/` and `*.pyc`.

---

## 2. Background: the shared graph-search skeleton

The brief says every algorithm uses the same loop. **Only the fringe changes.**

1. Push the start state onto the fringe.
2. Pop a node. If it is a goal, return its action list.
3. If the state has not been expanded, mark it expanded and push every successor
   (extended path, accumulated cost).
4. Repeat until the fringe is empty, then return failure (`[]`).

| Algorithm | Fringe (`util`) | Pops | Priority |
|---|---|---|---|
| DFS | `Stack` | Newest node (LIFO) | — |
| BFS | `Queue` | Oldest node (FIFO) | — |
| UCS | `PriorityQueue` | Cheapest node | `g(n)` = path cost so far |
| A* (Part 2) | `PriorityQueue` | Lowest estimate | `g(n) + h(n)` |

### The `SearchProblem` interface (`search.py`)

- `problem.getStartState()` returns the start state.
- `problem.isGoalState(state)` returns `True` or `False`.
- `problem.getSuccessors(state)` returns a list of `(successor, action, stepCost)`.

### Reference implementation (verified: q1–q4 all pass)

Add this helper above `depthFirstSearch` in `search.py`. A fringe item is
`(state, actions, g)`.

```python
def _graphSearch(problem, fringe, usePriority=False, heuristic=None):
    """Generic graph search. The fringe type decides the strategy."""
    start = problem.getStartState()

    def push(item, g):
        if usePriority:
            h = heuristic(item[0], problem) if heuristic else 0
            fringe.push(item, g + h)
        else:
            fringe.push(item)

    push((start, [], 0), 0)
    expanded = set()
    while not fringe.isEmpty():
        state, actions, g = fringe.pop()
        if problem.isGoalState(state):          # goal test on POP, not on push
            return actions
        if state in expanded:                   # graph search: never expand twice
            continue
        expanded.add(state)
        for succ, action, cost in problem.getSuccessors(state):
            if succ not in expanded:
                push((succ, actions + [action], g + cost), g + cost)
    return []
```

Why each detail matters:

- **Goal test when popping.** UCS and A* are only optimal if the goal test runs
  when a node is *popped*. A cheaper path to the goal may still be on the fringe
  when the goal is first *pushed*.
- **Expanded check after popping.** A state can sit on the fringe several times.
  The stale copies are skipped when popped. This is also what gives DFS the
  exact expansion order the autograder expects.
- **`actions + [action]` builds a new list**, so sibling paths never share or
  mutate each other's list.
- **`expanded` is a `set`**, so states must be hashable. This is why Q5 uses tuples.

---

## 3. Q1 — Depth First Search (4 marks)

```python
def depthFirstSearch(problem: SearchProblem):
    return _graphSearch(problem, util.Stack())
```

- **Fringe:** `util.Stack`, LIFO, so the search always goes deeper first.
- **Complete?** Yes on finite graphs, because the expanded set prevents cycles.
- **Optimal?** No. It returns the first path found, not the shortest.
- **Complexity:** time O(b^m), space O(b·m), where m is the maximum depth.

Test:
```bash
python autograder.py -q q1
python pacman.py -l mediumMaze -p SearchAgent -a fn=dfs
```
`mediumMaze` with DFS gives cost 130 and roughly 146 expanded nodes. The path is
long and wanders, as expected for DFS.

## 4. Q2 — Breadth First Search (4 marks)

```python
def breadthFirstSearch(problem: SearchProblem):
    return _graphSearch(problem, util.Queue())
```

- **Fringe:** `util.Queue`, FIFO, so it expands shallowest nodes first.
- **Optimal** when every step costs the same (it minimises the number of actions).
- **Complexity:** time and space O(b^d), where d is the depth of the shallowest goal.
- BFS must be generic. Q5 reuses it on `CornersProblem` (q5 depends on q2), and
  the 8-puzzle also works: `python eightpuzzle.py`.

```bash
python autograder.py -q q2
python pacman.py -l mediumMaze -p SearchAgent -a fn=bfs     # cost 68
```

## 5. Q3 — Uniform Cost Search (4 marks)

```python
def uniformCostSearch(problem: SearchProblem):
    return _graphSearch(problem, util.PriorityQueue(), usePriority=True)
```

- **Fringe:** `util.PriorityQueue` with priority `g(n)`, the accumulated path cost.
- Generalises BFS: with unit costs it behaves like BFS.
- **Optimal** for any non-negative step costs.
- "Varying the cost function": `StayEastSearchAgent` makes west moves expensive,
  and `StayWestSearchAgent` does the reverse.

```bash
python autograder.py -q q3
python pacman.py -l mediumMaze -p SearchAgent -a fn=ucs
python pacman.py -l mediumDottedMaze -p StayEastSearchAgent   # cost 1
python pacman.py -l mediumScaryMaze -p StayWestSearchAgent    # cost 68719479864
```

---

## 6. Suggested commit sequence (Member 1)

1. `chore: import SE3062 search project skeleton`
2. `chore: add .gitignore and docs folder`
3. `feat(search): add generic _graphSearch helper using util fringes`
4. `feat(search): implement depthFirstSearch with util.Stack (q1)`
5. `feat(search): implement breadthFirstSearch with util.Queue (q2)`
6. `feat(search): implement uniformCostSearch with util.PriorityQueue (q3)`
7. `docs(report): add Q1–Q3 explanations and autograder screenshots`

## 7. Report contribution (≤200 words per question)

For each of Q1, Q2 and Q3 provide: the code block, an autograder screenshot, and
an explanation covering the fringe type, the expanded set, the goal test on pop,
and optimality. Put these in the matching sections of [report-template.md](report-template.md).

## 8. Viva focus for this part

- Why is DFS not optimal, and when is BFS optimal?
- What changes between BFS and UCS? What if all costs are 1?
- Why is the goal test done on pop rather than on push?
- What would happen without the `expanded` set (tree search)?
