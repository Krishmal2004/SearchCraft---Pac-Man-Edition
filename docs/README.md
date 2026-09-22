# SE3062 Group Assignment — Search Algorithms in Pac-Man

> **Course:** SE3062 Intelligent Systems (Year 3 · 2026) · **Lecturer:** Mr. Jeewaka Perera
> **Weight:** 10% of final grade · **Group size:** 4 members

This folder is the group's working documentation. It breaks the assignment into
**four parts of similar weight**, one per member, and gives each member a
self-contained guide for their part. Everyone must still understand the
**whole** solution: the viva tests every member on every question.

---

## 1. What the assignment asks for

Implement DFS, BFS, UCS and A* (`search/search.py`), model the "visit all four
corners" problem, and design admissible, consistent heuristics for the corners
and food problems (`search/searchAgents.py`).

### Marking scheme (100 marks)

| Component | Marks | Graded as |
|---|---|---|
| Autograder Q1–Q7 | 40 | Group |
| Group report (PDF) | 10 | Group |
| Git contribution | 10 | Individual |
| Viva: algorithm knowledge | 20 | Individual |
| Viva: code comprehension | 20 | Individual |

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

> The local autograder prints its own points (3/3, 4/4 and so on). The course
> converts these to the marks above. What matters is that every question
> reaches its maximum score.

---

## 2. Work split: four parts of similar weight

The questions carry 4, 4, 4, 4, 8, 8 and 8 marks, so no split gives exactly 10
autograder marks each. We balance the parts by pairing lighter coding work with
the shared deliverables (repo setup, report, testing, submission). Each part is
then roughly **25% of the group's total effort**.

| Part | Owner | Autograder questions | Shared deliverable | Guide |
|---|---|---|---|---|
| **Part 1 — Uninformed Search** | Member 1 | Q1 DFS, Q2 BFS, Q3 UCS (**12**) | Git repo, environment setup, shared graph-search skeleton | [part-1-uninformed-search.md](part-1-uninformed-search.md) |
| **Part 2 — Informed Search** | Member 2 | Q4 A*, Q6 cornersHeuristic (**12**) | Heuristic admissibility/consistency proofs for the report | [part-2-astar-and-corners-heuristic.md](part-2-astar-and-corners-heuristic.md) |
| **Part 3 — Problem Modelling & Report** | Member 3 | Q5 CornersProblem (**8**) | Assembling, formatting and exporting the PDF report | [part-3-corners-problem-and-report.md](part-3-corners-problem-and-report.md) |
| **Part 4 — Food Heuristic & Submission** | Member 4 | Q7 foodHeuristic (**8**) | Full test runs, screenshots, contribution table, AI declaration, ZIP packaging | [part-4-food-heuristic-and-submission.md](part-4-food-heuristic-and-submission.md) |

Shared docs used by everyone:

- [report-template.md](report-template.md): the skeleton of the final PDF report.
- [viva-preparation.md](viva-preparation.md): questions **every** member must be able to answer.

### Effort balance

| Part | Autograder marks | Coding effort | Extra duties | Approx. share |
|---|---|---|---|---|
| 1 | 12 | Medium: three algorithms on one skeleton | Repo + env setup | 25% |
| 2 | 12 | Medium–High: A* plus heuristic design and proof | Proof write-up | 25% |
| 3 | 8 | Medium: state-space design | Whole report assembly | 25% |
| 4 | 8 | High: hardest heuristic (trickySearch) | Testing + submission | 25% |

---

## 3. Dependencies between parts

```
Part 1 (Q1–Q3 + skeleton) ──► Part 2 (Q4 A*) ──► Q6 and Q7 need A* to run
Part 3 (Q5 state format)  ──► Part 2 (Q6 reads the Q5 state)
                              Part 4 (Q7 needs A*; independent of Q5)
```

To avoid blocking each other:

1. **Day 1:** Member 1 pushes the repo and the shared `_graphSearch` helper (see Part 1).
2. **Agreed interface (fix it now):** the `CornersProblem` state is
   `(position, visited)` where `position = (x, y)` and `visited` is a
   **tuple of 4 booleans** in the same order as `problem.corners`.
   Member 2 can write `cornersHeuristic` against this contract before Q5 is merged.
3. Members 2 and 4 can test their heuristics with the reference A* until Q4 is merged.

Autograder dependencies (from `test_cases/*/CONFIG`): q5 depends on q2, and q6 and q7 depend on q4.

---

## 4. Timeline (suggested)

| Phase | Work | Owner |
|---|---|---|
| Week 1 · early | Repo, branches, env check (`python pacman.py`) | M1 (all verify) |
| Week 1 | Q1–Q3, Q5 in parallel | M1, M3 |
| Week 1 · late | Q4 | M2 |
| Week 2 | Q6, Q7 | M2, M4 |
| Week 2 | Autograder screenshots, per-question explanations (each member writes their own) | All |
| Week 2 · late | Full `python autograder.py` run, report assembly, ZIP | M4, M3 |
| Before viva | Cross-teaching session (each member explains their part to the others) | All |

---

## 5. Git workflow (10 individual marks)

The rubric rewards **frequent, meaningful commits spread across the timeline**
from **each** member. Large end-of-project dumps score 4–5/10 at most.

- One public repo. Each member works on a feature branch:
  `feature/q1-q3-uninformed`, `feature/q4-q6-astar-corners-heuristic`,
  `feature/q5-corners-problem`, `feature/q7-food-heuristic`.
- Merge through Pull Requests, and have another member review each one.
- Commit small steps with descriptive messages, for example:
  - `feat(search): add generic graph-search helper using util fringe`
  - `feat(search): implement DFS with util.Stack (q1 passing)`
  - `fix(corners): make visited-corners state hashable (tuple)`
  - `perf(food): cache mazeDistance results in heuristicInfo`
  - `docs(report): add Q7 explanation and autograder screenshot`
- Avoid messages like `update`, `final`, `asdf` or `fix`.
- Aim for **at least 5–8 commits per member** spread over several days.
- Commit the report sources (`docs/`) too. Documentation commits count.

---

## 6. Hard rules (from the brief)

- **Do not rename** any file, function or class. The autograder imports them by name, and a rename scores zero for that question.
- Use `util.Stack`, `util.Queue` and `util.PriorityQueue`, **not** Python built-ins.
- Graph search: never expand the same state twice.
- Keep search states **small and hashable**. Never put the wall grid or `GameState` inside a state.
- Q6 and Q7 heuristics must be **admissible and consistent**. A single non-optimal path gives **0** for the question.
- Python **3.9–3.11** is the official range (`conda create -n cs188 python=3.11`).

---

## 7. Submission checklist (one submission per group)

- [ ] `Group_XX_Report.pdf`: includes, for Q1–Q7, the edited code, an autograder screenshot and an explanation of 200 words or fewer
- [ ] Report ends with: public repo link, Git evidence screenshots, individual contribution table, AI usage declaration
- [ ] `Group_XX_Code.zip` containing **only** `search.py` and `searchAgents.py`
- [ ] Full `python autograder.py` run shows every question at maximum
