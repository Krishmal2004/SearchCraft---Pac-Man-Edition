# SE3062 Group Assignment: Search Algorithms in Pac-Man

> **Course:** SE3062 Intelligent Systems (Year 3 · 2026) · **Lecturer:** Mr. Jeewaka Perera
> **Weight:** 10% of final grade · **Group size:** 4 members
> **Base project:** UC Berkeley CS188 Pac-Man search project (`search.zip`)

This repository holds our group's solution. We implement the classic graph-search algorithms (DFS, BFS, UCS, A*), model the "visit all four corners" problem, and design **admissible and consistent** heuristics for the corners and food problems. Every member must understand the **whole** solution, because the viva tests each of us on every question.

---

## 1. What the assignment asks for

| Part | Task |
|---|---|
| Setup | Python 3.9–3.11 environment, install `numpy` and `matplotlib`, extract `search.zip`, verify with `python pacman.py` |
| Code | Edit **only** `search.py` (Q1–Q4) and `searchAgents.py` (Q5–Q7) |
| Verify | `python autograder.py` must reach full marks on Q1–Q7 |
| Git | Public repository with regular, descriptive commits from **all 4 members** |
| Report | One PDF: code, autograder screenshots and an explanation (max 200 words) for each of Q1–Q7, plus Git evidence, contribution table and AI declaration |
| Viva | Strict 6-minute individual oral exam per team on algorithms, heuristic proofs and line-by-line code tracing |

### Marking scheme (100 marks)

| Component | Marks | Graded as |
|---|---|---|
| Autograder Q1–Q7 | 40 | Group |
| Group report (PDF) | 10 | Group |
| Git contribution | 10 | Individual |
| Viva: algorithm knowledge | 20 | Individual |
| Viva: code comprehension and defence | 20 | Individual |

Half of the grade (Git 10 + Viva 40) is **individual**, so each member must commit code and master the entire codebase.

### Autograder allocation (40 marks)

| Q | Task | File | Command | Marks |
|---|---|---|---|---|
| Q1 | Depth First Search | `search.py` | `python autograder.py -q q1` | 4 |
| Q2 | Breadth First Search | `search.py` | `python autograder.py -q q2` | 4 |
| Q3 | Uniform Cost Search | `search.py` | `python autograder.py -q q3` | 4 |
| Q4 | A* Search | `search.py` | `python autograder.py -q q4` | 4 |
| Q5 | CornersProblem (representation) | `searchAgents.py` | `python autograder.py -q q5` | 8 |
| Q6 | cornersHeuristic | `searchAgents.py` | `python autograder.py -q q6` | 8 |
| Q7 | foodHeuristic | `searchAgents.py` | `python autograder.py -q q7` | 8 |

### Heuristic grading

**Q6: nodes expanded on `mediumCorners`**

| Nodes expanded | Marks |
|---|---|
| more than 2000 | 0 / 8 |
| at most 2000 | 4 / 8 |
| at most 1600 | 6 / 8 |
| at most 1200 | 8 / 8 |

**Q7: nodes expanded on `trickySearch`**

| Nodes expanded | Marks |
|---|---|
| more than 15000 | 2 / 8 |
| at most 15000 | 4 / 8 |
| at most 12000 | 6 / 8 |
| at most 9000 | 8 / 8 |

> **Inadmissible = 0.** If A* with our heuristic returns a non-optimal path on any test, that question scores **0** regardless of node count. Both heuristics must be admissible, consistent, return `0` at goal states and never return a negative value.

---

## 2. Team and work split

| Member | Implementation tasks | Documentation and shared tasks | Viva defence focus |
|---|---|---|---|
| **Member 1** | **Q1 DFS** and **Q2 BFS** (`search.py`) | Write report sections for Q1 and Q2; capture their autograder screenshots; push the initial repo and environment setup | Stack vs Queue, fringe data structures, cycle prevention (expanded set) |
| **Member 2** | **Q3 UCS** and **Q4 A\*** (`search.py`) | Write report sections for Q3 and Q4; capture their autograder screenshots | Priority-queue ordering, g(n) vs g(n)+h(n), conditions for optimality |
| **Member 3** | **Q5 CornersProblem** and **Q6 cornersHeuristic** (`searchAgents.py`) | Write report sections for Q5 and Q6; structure the contribution matrix and the report template | State-space formulation, hashability in Python, corner-tracking logic |
| **Member 4** | **Q7 foodHeuristic** and optimisation of the Q6 heuristic (`searchAgents.py`) | Write report section for Q7; compile the final PDF, handle Git branch merges, write the AI declaration, package the ZIP | Admissibility and consistency proofs, relaxation techniques (Manhattan distance, MST, maze distance) |

> **Critical rule:** never work in isolation. After finishing Q1–Q7, hold a **team mock viva** where each member explains the entire code line by line. The rubric penalises students who only know their own portion.

### Names and IDs (fill in)

| Member | Name | Student ID | GitHub username |
|---|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |

---

## 3. Project structure

| File | Role | Description |
|---|---|---|
| `search.py` | **Edit** | DFS, BFS, UCS, A* (Q1–Q4) |
| `searchAgents.py` | **Edit** | `CornersProblem`, `cornersHeuristic`, `foodHeuristic` (Q5–Q7) |
| `util.py` | Read only | Provides `Stack`, `Queue`, `PriorityQueue` |
| `pacman.py` | Reference | Runs the game; describes `GameState` |
| `game.py` | Reference | `AgentState`, `Agent`, `Directions`, `Grid` |
| `autograder.py` | Run | Automated grading script |
| `test_cases/` | Auto-used | Test cases for each question |

---

## 4. Environment setup

```bash
conda create -n cs188 python=3.11
conda activate cs188
pip install numpy matplotlib

# verify: a playable Pac-Man window should open
python pacman.py
```

Run the whole autograder at any time with `python autograder.py`, or one question with `python autograder.py -q q1`.

---

## 5. How graph search works here

Every algorithm uses the same skeleton. **Only the fringe changes.**

1. Put the start state on the fringe.
2. Pop a node. If its state is the goal, return the path of actions.
3. If the state has not been expanded before, mark it expanded and push each successor (with the extended path, and accumulated cost where relevant).
4. Repeat until the goal is found or the fringe is empty (return failure).

| Algorithm | Fringe | Priority |
|---|---|---|
| DFS | `util.Stack` (LIFO) | none |
| BFS | `util.Queue` (FIFO) | none |
| UCS | `util.PriorityQueue` | g(n) |
| A* | `util.PriorityQueue` | f(n) = g(n) + h(n) |

`aStarSearch` takes `heuristic(state, problem)`. The default `nullHeuristic` returns 0, which makes A* behave like UCS.

---

## 6. Question details and agreed interfaces

### Q1 and Q2: DFS and BFS (Member 1)
Graph-search versions: never expand the same state twice. DFS uses `util.Stack`, BFS uses `util.Queue`. BFS is optimal in number of steps.

### Q3 and Q4: UCS and A* (Member 2)
UCS orders by accumulated cost g(n). A* orders by g(n) + h(n). Remember that the fringe may hold the same state more than once with different costs, so check the expanded set when popping.

### Q5: CornersProblem (Member 3)
Implement `getStartState`, `isGoalState`, `getSuccessors` and the successor bookkeeping.

**Agreed state format (fixed so Q6 and Q7 work can start before Q5 is merged):**

```
state = (position, visited)
position = (x, y)
visited  = tuple of 4 booleans, in the same order as problem.corners
```

Keep the state small and hashable. Never store the wall grid or the `GameState` in the state.

### Q6: cornersHeuristic (Member 3, optimised by Member 4)
Admissible and consistent heuristic for `CornersProblem`, targeting at most **1200** expanded nodes on `mediumCorners`. A common relaxation: from the current position, repeatedly move to the nearest unvisited corner by Manhattan distance and sum the legs. This ignores walls, so it never overestimates.

### Q7: foodHeuristic (Member 4)
Admissible and consistent heuristic for `FoodSearchProblem`, targeting at most **9000** expanded nodes on `trickySearch`. Use the provided `problem.heuristicInfo` dictionary to cache expensive values such as maze distances.

---

## 7. Dependencies between members

```
Member 1 (Q1, Q2 + repo setup) ──► Member 2 (reuses the graph-search skeleton for Q3, Q4)
Member 2 (Q4 A*)               ──► Member 3 (Q6) and Member 4 (Q7) need A* to run
Member 3 (Q5 state format)     ──► Member 3 (Q6 reads the Q5 state)
```

Autograder dependencies (from `test_cases/*/CONFIG`): **q5 depends on q2**, and **q6 and q7 depend on q4**.

To avoid blocking each other:

1. **Day 1:** Member 1 pushes the repo, the base files and the shared graph-search skeleton.
2. Members 3 and 4 write heuristics against the agreed state format and test with the reference A* until Q4 is merged.
3. Members 3 and 4 coordinate on Q6 so Member 4's optimisation does not conflict with Member 3's code (use PRs, not direct edits).

---

## 8. Timeline (suggested)

| Phase | Work | Owner |
|---|---|---|
| Week 1 · early | Repo, branches, environment check (`python pacman.py`) | M1 (all verify) |
| Week 1 | Q1, Q2, Q3, Q5 in parallel | M1, M2, M3 |
| Week 1 · late | Q4 | M2 |
| Week 2 | Q6, Q7, Q6 optimisation | M3, M4 |
| Week 2 | Autograder screenshots and per-question explanations (each member writes their own) | All |
| Week 2 · late | Full `python autograder.py` run, report assembly, ZIP | M4, M3 |
| Before viva | Team mock viva (each member explains the whole code) | All |

---

## 9. Git workflow (10 individual marks)

The rubric rewards **frequent, meaningful commits spread across the whole timeline** from **each** member. Large end-of-project dumps score 4–5/10 at most; 1–2 commits total score 2–3.

- One **public** repository. Each member works on a feature branch:
  - `feature/q1-q2-dfs-bfs`
  - `feature/q3-q4-ucs-astar`
  - `feature/q5-q6-corners`
  - `feature/q7-food-heuristic`
- Merge through Pull Requests and have another member review each one.
- Commit small steps with descriptive messages, for example:
  - `feat(search): add generic graph-search helper using util fringe`
  - `feat(search): implement DFS with util.Stack (q1 passing)`
  - `feat(search): implement UCS with PriorityQueue ordered by g(n)`
  - `fix(corners): make visited-corners state hashable (tuple)`
  - `perf(food): cache mazeDistance results in heuristicInfo`
  - `docs(report): add Q7 explanation and autograder screenshot`
- Avoid messages like `update`, `final`, `asdf` or `fix`.
- Aim for **at least 5–8 commits per member**, spread over several days.
- Commit the report sources (`docs/`) too. Documentation commits count.

---

## 10. Hard rules (from the brief)

- **Do not rename** any file, function or class. The autograder imports them by name; a rename scores zero for that question.
- Do **not** edit `util.py`, `game.py` or `pacman.py`.
- Use `util.Stack`, `util.Queue` and `util.PriorityQueue`, **not** Python built-ins.
- Graph search: never expand the same state twice.
- Keep search states **small and hashable**.
- Q6 and Q7 heuristics must be **admissible and consistent**; a single non-optimal path gives **0** for the question.
- Python **3.9–3.11** only.

---

## 11. Report requirements (10 marks)

For **each** of Q1–Q7 the report must contain:

1. The functions or code blocks we edited or added.
2. A clear screenshot of the autograder output proving the score.
3. An explanation of **at most 200 words** covering logic, data structures and heuristic design.

The **end** of the report must contain:

1. **Public Git repository link.**
2. **Git contribution evidence:** screenshots of commit history and contribution graphs showing frequency, meaningful messages and activity from every member throughout the project.
3. **Individual contribution table:** names, student IDs and the tasks or questions each member contributed to.
4. **AI usage declaration:** if any AI tool was used, name the tool and include the **exact prompts**. If none, state "No AI tools were utilized."

---

## 12. Submission checklist (one submission per group, two separate files)

- [ ] `Group_XX_Report.pdf`: standalone file named after our Group ID
- [ ] Report covers Q1–Q7 (code, autograder screenshot, explanation of 200 words or fewer each)
- [ ] Report ends with: public repo link, Git evidence screenshots, contribution table, AI declaration
- [ ] `Group_XX_Code.zip` containing **only** `search.py` and `searchAgents.py`
- [ ] Full `python autograder.py` run shows every question at maximum
- [ ] Team mock viva completed; every member can explain all seven questions

---

## 13. Viva preparation (6 minutes, individual)

Scored out of 50: Git 10, Algorithm knowledge 20, Code comprehension 20. Every member should be able to answer:

**Algorithms**
- Why does DFS use a stack and BFS a queue? What changes in UCS and A*?
- Is BFS optimal? When? Is DFS? Is UCS?
- What is the difference between g(n) and g(n)+h(n)?
- When is A* optimal? (Admissible for tree search, consistent for graph search.)
- Why does the expanded set stop infinite loops on cyclic graphs?

**Heuristics**
- Define admissible and consistent. Why must a consistent heuristic be admissible?
- Prove your corners heuristic never overestimates. What relaxation does it use?
- Prove your food heuristic is admissible and consistent. Why is it better than `nullHeuristic`?
- What does a heuristic return at a goal state, and why?

**Code tracing**
- Walk through `getSuccessors` in `CornersProblem`: how is the visited tuple updated?
- Why must the state be hashable, and why not store the wall grid in it?
- Trace one iteration of the search loop on a small maze.
