# Part 3 — Problem Modelling: CornersProblem (Q5) + Report Assembly

**Owner:** Member 3 · **Autograder marks:** 8 (Q5)
**File edited:** `search/searchAgents.py` (`CornersProblem`)
**Shared duty:** own the final report. Collect every section, format it, and export `Group_XX_Report.pdf`.

---

## 1. Q5 — Finding All the Corners (8 marks)

The goal is a path that touches **all four corners** of the maze. Positions alone
are not enough as a state, because the same square means different things
depending on which corners have already been visited.

### State design

```
state = (position, visited)
  position : (x, y)                      Pac-Man's square
  visited  : (bool, bool, bool, bool)    one flag per corner, same order as self.corners
```

Why this design:

- **Minimal.** It holds only what affects the future: where Pac-Man is and which corners remain.
- **Hashable.** Tuples of ints and bools can go into the `expanded` set used by graph search.
  A `list` or `set` would crash (`unhashable type`).
- **No walls or `GameState` inside the state.** The walls live on `self.walls`.
  Copying them into every state wastes memory, and the brief forbids it.
- **Size of the state space:** positions × 2⁴, so at most 16 copies of the maze.

This format is the **contract with Member 2** (`cornersHeuristic` reads it). Agree on it on day 1.

### Reference implementation (verified: q5 passes)

```python
def getStartState(self):
    # The start square may itself be a corner
    return (self.startingPosition,
            tuple(corner == self.startingPosition for corner in self.corners))

def isGoalState(self, state: Any):
    return all(state[1])

def getSuccessors(self, state: Any):
    successors = []
    for action in [Directions.NORTH, Directions.SOUTH, Directions.EAST, Directions.WEST]:
        (x, y), visited = state
        dx, dy = Actions.directionToVector(action)
        nextx, nexty = int(x + dx), int(y + dy)
        if not self.walls[nextx][nexty]:
            nextPos = (nextx, nexty)
            nextVisited = tuple(v or corner == nextPos
                                for corner, v in zip(self.corners, visited))
            successors.append(((nextPos, nextVisited), action, 1))

    self._expanded += 1 # DO NOT CHANGE
    return successors
```

Details to get right:

- Keep `self._expanded += 1` exactly as provided. The autograder counts nodes with it.
- Leave `__init__` and `getCostOfActions` unchanged.
- The step cost is always `1`.
- Build `nextVisited` as a **new tuple**. Tuples are immutable, so states never share mutable data.

### Test

```bash
python autograder.py -q q5
python pacman.py -l tinyCorners  -p SearchAgent -a fn=bfs,prob=CornersProblem
python pacman.py -l mediumCorners -p SearchAgent -a fn=bfs,prob=CornersProblem
```
Measured: BFS on `mediumCorners` finds cost **106** with **1966** expanded nodes.
The A* heuristic in Part 2 cuts this to 741.

---

## 2. Shared duty — Report assembly (10 group marks)

The report is graded on **structure, formatting, clarity and completeness**. Use
[report-template.md](report-template.md) as the skeleton.

### Responsibilities

1. Create the report document from the template on day 1 and share it with the group.
2. Set internal deadlines: each owner delivers their question sections (code, screenshot, explanation).
3. Check that **every** Q1–Q7 section has all three required items:
   - the functions or code blocks that were edited,
   - a **clear** autograder screenshot for that question,
   - an explanation of **200 words or fewer** (count them).
4. Add the closing sections: repo link, Git evidence, contribution table, AI
   declaration (Member 4 supplies the last two).
5. Apply consistent formatting: a cover page with Group ID and members, headings,
   monospaced code, numbered figures, and page numbers.
6. Export as `Group_XX_Report.pdf` (replace XX with the Group ID).

### Formatting tips

- Use syntax-highlighted code (for example, Markdown to PDF with VS Code "Markdown PDF", or pandoc).
- Crop screenshots to the relevant autograder output: the `### Question qN: X/X ###` line and the node counts.
- Give each question section the same layout: *Code → Screenshot → Explanation*.

---

## 3. Suggested commit sequence (Member 3)

1. `feat(corners): define (position, visitedCorners) state and getStartState`
2. `feat(corners): implement isGoalState for all-corners-visited`
3. `feat(corners): implement getSuccessors with wall check and corner tracking (q5)`
4. `docs(report): create report skeleton from template`
5. `docs(report): add Q5 explanation and screenshot`
6. `docs(report): integrate all sections and format final report`

## 4. Viva focus for this part

- Why can't the state be just `(x, y)`?
- Why must the state be hashable? What would break with a list?
- How large is the state space?
- What happens if Pac-Man starts on a corner?
- Why is the wall grid kept out of the state?
