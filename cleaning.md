# Data Cleaning Plan

## Input

`problems.json` — structured problem data extracted by an LLM from 01.md ~ 07.md, 102 raw entries.

Data overview:

| Metric | Value |
|---|---|
| Total entries | 102 |
| Source files | 7 (01~07.md) |
| type=reduction | 58 (top-level) + 26 (sub-problems) = 84 |
| type=meta | 25 |
| type=theory | 11 |
| type=algorithm | 7 |
| type=true_false | 1 |
| With solution | 36 |
| Non-null difficulty | 8 |

---

## Field Descriptions

| Field | Type | Description |
|---|---|---|
| `type` | enum | Problem type: `reduction`, `meta` (parent container), `theory` (theoretical analysis), `algorithm` (algorithm design), `true_false` (true/false) |
| `reduction_direction` | string | Reduction direction, in the format `A -> B`, meaning a reduction from problem A to problem B |
| `problems_used` | list | List of problem names involved in the reduction |
| `cleaned_from` | enum | Entry origin: `top-level` (original top-level entry), `sub_problem` (sub-problem extracted from a parent), `manual_fix` (manual correction) |

---

## Step 1: Extract reduction types

- Filter the 58 top-level `type=reduction` entries out of the 102
- Iterate over the 25 `type=meta` entries, extract the `type=reduction` sub-problems from their `sub_problems` (23 in total), and flatten them into top-level entries
- Iterate over the container entries with `type=reduction` but `problems_used=[]`, extract the `type=reduction` sub-problems from their `sub_problems` (3 in total: 07-003a/b, 07-004b), and flatten them into top-level entries
- Flattened sub-problems inherit the parent's `source_file` and `source_name`, and are given independent ids (e.g., `03-004a`)

**After merging: 84 entries** (58 top-level + 23 meta sub-problems + 3 container sub-problems)

---

## Step 2: Remove unqualified entries

### Missing reduction_direction

| Entry | Reason | Action |
|---|---|---|
| 07-003 | Container problem, `problems_used=[]`, sub-problems already flattened | Dropped |
| 07-004 | Container problem, `problems_used=[]`, sub-problems already flattened | Dropped |
| 05-010 | theory mislabeled as reduction; the content is "Explain why Subset Sum is NPC" | Dropped |
| 05-011 | theory mislabeled as reduction; the content is "Explain why Hamiltonian Cycle is NPC" | Dropped |
| 03-005 | LONGEST-PATH, a genuine reduction problem whose direction was missed by the LLM extraction | Direction added manually; kept |

### Non-reduction entries at the end of 06.md

06-030 ~ 06-034 (QUADRATIC DIOPHANTINE EQUATIONS, PROTEIN FOLDING, etc.) are type=meta, outside the scope of reduction extraction, and are automatically excluded.

**After this step: 80 entries** (84 − 4 dropped)

---

## Step 3: Deduplication

### Duplicates across source files (keep the version with a solution or the more complete one)

| Reduction direction | Kept | Dropped | Reason |
|---|---|---|---|
| `HAM-CYCLE -> DIRECTED-HAM-CYCLE` | 02-002 | 04-002 | 02-002 has a solution, 04-002 does not |
| `INDEPENDENT-SET -> VERTEX-COVER` | 06-018 | 03-019a | 06-018 has a solution, 03-019a does not |
| CLIQUE is NP-complete | 05-007 | 07-004b | 05-007 has an explicit direction, 07-004b has none |
| `HAMILTONIAN-CYCLE -> HAMILTONIAN-PATH` | 03-013c | 05-012 | Neither has a solution; 03-013c's statement is more complete |
| `SUBSET-SUM -> KNAPSACK` | 01-005 | 03-018a | 01-005 has a solution, 03-018a does not |
| `VERTEX-COVER -> SET-COVER` | 02-007 | 03-004a | 02-007 has a solution, 03-004a does not |

### Duplicates within 03.md

The same problem is included by multiple "collection" sections of the source text; only the first occurrence is kept:

| Problem | Locations | Occurrences | Dropped |
|---|---|---|---|
| MINIMUM SET COVER | 03-004a, 03-013a, 03-016a, 03-021a | 4 | 3 |
| HITTING SET | 03-004d, 03-013b, 03-016b, 03-021d | 4 | 3 |
| BIN PACKING | 03-004b, 03-017a, 03-020a | 3 | 2 |
| TILING | 03-004c, 03-017b, 03-020d | 3 | 2 |

### Non-duplicates

Entries with the same problem name but different reduction directions (e.g., 01-003 `HALF-CLIQUE -> CLIQUE` and 01-004 `CLIQUE -> HALF-CLIQUE`) are not considered duplicates; all are kept.

**After this step: 64 entries** (80 − 6 − 10)

---

## Step 4: Complete short statements

In 06.md, the statements of some entries contain only the problem definition (Input/Question) and lack the "prove that X is NP-complete" task description. This is a systematic omission during LLM extraction — the task description was in the body paragraph, and the LLM extracted only the problem-definition block.

**Detection condition**: the statement contains none of the following keywords: `NP-complete`, `reduction`, `<=p`, `<=_P`, `reduces`, `NP-hard`

**Completion rule**: automatically generate a task prefix based on `reduction_direction`. For example:

```
Before: "Input: A formula phi in 3CNF. Question: Is phi satisfiable?"
After:  "Show that 3SAT is NP-complete by reduction from CIRCUIT SAT.\n\nInput: A formula phi in 3CNF. Question: Is phi satisfiable?"
```

**Number completed**: 35 entries.

**Entry count unchanged: 64**

---

## Step 5: Complete missing reduction_direction and problems_used

The LLM extraction missed the `reduction_direction` of 12 sub-problems from 03.md plus 1 top-level problem (03-005). The statements of these problems are themselves complete reduction problems; the direction and source problem were added manually based on standard NPC reduction paths.

### Supplementation Rationale

| Entry | Direction | Rationale |
|---|---|---|
| 03-003a SUBGRAPH ISOMORPHISM | `CLIQUE -> SUBGRAPH-ISOMORPHISM` | CLIQUE is a special case of subgraph isomorphism: let H = K_k, and finding a k-clique is equivalent to finding a K_k subgraph |
| 03-003b LARGEST COMMON SUBGRAPH | `SUBGRAPH-ISOMORPHISM -> LARGEST-COMMON-SUBGRAPH` | Subgraph isomorphism is a special case of largest common subgraph (one of the two graphs is exactly a subgraph of the other) |
| 03-004a MINIMUM SET COVER | `VERTEX-COVER -> SET-COVER` | Vertices become elements, and each edge becomes a set containing its two endpoints |
| 03-004b BIN PACKING | `PARTITION -> BIN-PACKING` | Items are the individual numbers, and the bin capacity = half of the total sum |
| 03-004c TILING | `3SAT -> TILING` | Use an arrangement of rectangles to encode the truth assignment of the 3SAT variables and the satisfiability of the clauses |
| 03-004d HITTING SET | `VERTEX-COVER -> HITTING-SET` | Vertex Cover is the special case of Hitting Set with the hyperedge size restricted to 2 |
| 03-005 LONGEST-PATH | `HAMILTONIAN-PATH -> LONGEST-PATH` | Set k = n−1, and longest path degenerates to Hamiltonian Path |
| 03-013c Hamiltonian Path | `HAMILTONIAN-CYCLE -> HAMILTONIAN-PATH` | Split one point on the cycle into the start and end points |
| 03-013d Max Degree Spanning Tree | `HAMILTONIAN-PATH -> MAX-DEGREE-SPANNING-TREE` | Equivalent to Hamiltonian Path when k=2 |
| 03-018a KNAPSACK | `SUBSET-SUM -> KNAPSACK` | For each item, weight=value=s_i, capacity=target value |
| 03-018b MULTIPROCESSOR SCHEDULING | `PARTITION -> MULTIPROCESSOR-SCHEDULING` | m=2 processors, deadline = total sum/2 |
| 03-022e r-DISK-COVER | `DOMINATING-SET -> r-DISK-COVER` | Dominating set on unit disk graphs reduces to disk cover (the dual problem of k-Center) |
| 03-023a MAX SAT | `3SAT -> MAX-SAT` | Set k = the total number of clauses, and MAX-SAT degenerates to SAT |

After completion, `cleaned_from` is marked as `manual_fix`. The `problems_used` of the corresponding entries is completed as well (adding the source problem name).

**Entry count unchanged: 64**. All entries have `reduction_direction`.

### Known special cases

06-001 `any NP language -> ACCEPTS SOMETHING FAST`: the source text asks to prove that "ACCEPTS SOMETHING FAST is NP-complete" without specifying from which concrete problem to reduce. The "any NP language" in the direction reflects the definition of NP-hardness (any NP language can be reduced to this problem) rather than a named problem. `problems_used` contains only `["ACCEPTS SOMETHING FAST"]`. This entry is kept as-is, and its reduction direction format is not compared against other entries.

---

## Step 6: Output

Generate `problems_clean.json`. Remove the `sub_problems` field, which is empty in all entries; the final field structure:

```
id, source_file, source_name, title, difficulty, tags,
problems_used, reduction_direction, type, statement,
hint, solution, has_solution, cleaned_from
```

---

## Expected Results

| Stage | Entries | Description |
|---|---|---|
| Original problems.json | 102 | — |
| Extract reduction | 84 | 58 top-level + 23 meta sub-problems + 3 container sub-problems |
| Remove unqualified | 80 | Dropped 07-003/07-004/05-010/05-011 |
| Deduplication | 64 | Dropped 6 cross-file duplicates + 10 internal duplicates |
| Complete statements | 64 | 35 entries completed with a prefix |
| Complete directions | 64 | 11 entries completed manually (marked as manual_fix) |

The final result is 64 reduction problems with explicit directions, no duplicates, and complete statements.

Types of entries that did not enter the final set:

| Category | Count | Disposition |
|---|---|---|
| theory / algorithm / true_false | 19 | Not within the reduction scope; not extracted |
| meta parent problems (sub-problems flattened) | 25 | Parents are not kept once their sub-problems are flattened |
| Missing direction (containers/mislabeled) | 4 | Dropped |
| Duplicates across source files | 6 | Dropped |
| Duplicates within 03.md | 10 | Dropped |
| Non-reduction entries at the end of 06.md | 5 | type=meta; automatically excluded |
