# Part 4 — Eating All the Dots: Food Heuristic (Q7) + Testing & Submission

**Owner:** Member 4 · **Autograder marks:** 8 (Q7)
**File edited:** `search/searchAgents.py` (`foodHeuristic`)
**Shared duty:** full integration testing, the individual contribution table,
the AI usage declaration, and packaging the final submission.

---

## 1. Q7 — `foodHeuristic` (8 marks)

`FoodSearchProblem` is already provided. Its state is `(pacmanPosition, foodGrid)`.
`foodGrid` is a `Grid` of booleans, and `foodGrid.asList()` returns the remaining food coordinates.

### Grading (nodes expanded on `trickySearch`)

| Nodes expanded | Marks |
|---|---|
| > 15000 | 2/8 |
| ≤ 15000 | 4/8 |
| ≤ 12000 | 6/8 |
| **≤ 9000** | **8/8** |

The heuristic must be **admissible and consistent**. A non-optimal path on any test means 0.

### Design: maze distance to the farthest remaining food

Pac-Man must eventually reach **every** food dot, including the one farthest
away. So the true remaining cost is at least the real maze distance (walls
included) to the farthest dot.

`mazeDistance` (already in `searchAgents.py`) runs BFS on the real maze, so it
accounts for walls. BFS is expensive, so results are cached in
`problem.heuristicInfo`, the dictionary the framework provides for this purpose.

```python
def foodHeuristic(state: Tuple[Tuple, List[List]], problem: FoodSearchProblem):
    position, foodGrid = state
    best = 0
    for food in foodGrid.asList():
        key = (position, food)
        if key not in problem.heuristicInfo:
            problem.heuristicInfo[key] = mazeDistance(position, food, problem.startingGameState)
        best = max(best, problem.heuristicInfo[key])
    return best
```

> `mazeDistance` needs a working BFS (Part 1, Q2).

### Measured result (verified)

| Heuristic on `trickySearch` | Nodes expanded | Marks |
|---|---|---|
| `0` (UCS) | ≈ 16,000 | 2/8 |
| Max **Manhattan** distance to food | ≈ 9,500 | 6/8 |
| Max **maze** distance to food (ours) | **4137** | **8/8** |

### Proof sketch (put this in the report)

- **Admissible.** Every remaining dot must be eaten, so the remaining path must
  reach the farthest dot `f`. Its length is at least the shortest-path distance
  `mazeDistance(pos, f)`. Taking the max over dots is therefore still a lower bound.
- **Consistent.** Moving one step changes the maze distance to any fixed dot by
  at most 1 (triangle inequality on shortest paths). The max over a set that can
  only shrink also drops by at most 1. So `h(s) ≤ 1 + h(s')`.
- **Goal gives 0.** No food remains, so the max over an empty set is 0.

### Ideas that fail

| Idea | Problem |
|---|---|
| Number of food dots left | Admissible but weak |
| Sum of distances to all food | **Inadmissible** |
| Nearest-food distance + food count − 1 | Admissible, but usually weaker than the maze-distance max |

```bash
python autograder.py -q q7
python pacman.py -l trickySearch -p AStarFoodSearchAgent
```

---

## 2. Shared duty — Testing & integration

1. After every merge to `main`, run the full autograder:
   ```bash
   cd search
   python autograder.py            # all questions
   python autograder.py --no-graphics
   ```
2. Keep a small log (`docs/test-log.md` is optional) noting the date, commit and score per question.
3. Take the **final** autograder screenshots from `main` (one per question plus a
   full-run summary) and give them to Member 3 for the report.
4. Check the Python version is between 3.9 and 3.11 before the final run.

## 3. Shared duty — Contribution table & AI declaration

Individual contribution table (fill in names and IDs):

| Member | Student ID | Part | Questions / tasks |
|---|---|---|---|
| Member 1 | ITxxxxxxxx | Part 1 | Q1, Q2, Q3, repo setup, graph-search skeleton |
| Member 2 | ITxxxxxxxx | Part 2 | Q4, Q6, heuristic proofs |
| Member 3 | ITxxxxxxxx | Part 3 | Q5, report assembly and formatting |
| Member 4 | ITxxxxxxxx | Part 4 | Q7, testing, submission packaging |

**AI usage declaration.** Collect from every member the tool used (for example
ChatGPT, Gemini or Claude) and the **exact prompts**. If no AI was used, write:
*"No AI tools were utilized."* This is mandatory.

## 4. Shared duty — Submission packaging

Upload **two separate items** (once per group):

1. `Group_XX_Report.pdf` (from Member 3).
2. `Group_XX_Code.zip` containing **only** `search.py` and `searchAgents.py`:
   ```powershell
   cd search
   Compress-Archive -Path search.py, searchAgents.py -DestinationPath ..\Group_XX_Code.zip
   ```
   ```bash
   cd search && zip ../Group_XX_Code.zip search.py searchAgents.py
   ```
3. Unzip the file into a fresh copy of the original `search/` folder and run
   `python autograder.py` again to confirm the submitted files alone reach full marks.

---

## 5. Suggested commit sequence (Member 4)

1. `feat(food): baseline foodHeuristic using remaining food count`
2. `feat(food): use max Manhattan distance to remaining food`
3. `perf(food): switch to cached mazeDistance to farthest food (q7, 4137 nodes)`
4. `test: record full autograder run on main`
5. `docs(report): add Q7 explanation, contribution table and AI declaration`

## 6. Viva focus for this part

- Why is maze distance better than Manhattan distance here, and why is it still admissible?
- Why is the max over food admissible but the sum is not?
- What is `problem.heuristicInfo` for? What would happen without the cache?
- Why must the heuristic return 0 at the goal?
