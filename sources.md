# TCS Reduction Problem Sources for Benchmark

Collected from university TCS courses worldwide — real exam/homework reduction problems centered on Karp's 21 NP-complete problems and CSP decision problems.

Tag legend:
- `[Karp21]` — reductions directly involving Karp's 21 NP-complete problems
- `[CSP]` — reductions involving CSP variants (Schaefer's dichotomy: 1-in-3SAT, NAE-3SAT, MAX CUT via NAE, etc.)
- `[Adaptable]` — lecture/textbook chapters with detailed reduction constructions that can be adapted into exercises

---

## Directly Usable: Exam/Homework Problem Sets

### 1. [Karp21] UCC CS4407 (Ireland, 2020) — Problem Set: NP-Complete Reductions
- **URL:** https://www.cs.ucc.ie/~gprovan/CS4407/2020/Quiz/Problem%20Set-NPC-solutions.pdf
- **Karp21 problems:** CLIQUE (via Half-Clique), KNAPSACK (from Subset-Sum), k-Colouring
- **Non-Karp21:** Double-SAT (SAT variant reduction), Class Scheduling
- **Type:** Problem set with full solutions

### 2. [Karp21 + CSP] UCC CS4407 (Ireland, 2013) — NP-Complete Reductions (different set)
- **URL:** https://www.cs.ucc.ie/~gprovan/CS4407/2013/HW-NP-complete_2013-SOLUTIONS.pdf
- **Karp21 problems:** VERTEX COVER (to Set Cover)
- **CSP problems:** NAE-3-SAT (from 3-SAT — this is a Schaefer dichotomy reduction), Set Splitting (from NAE-3-SAT — NAE-3SAT in disguise)
- **Other:** Dense Subgraph
- **Type:** Problem set with full solutions

### 3. [Karp21] UIUC CS573 (USA, 2013) — NP Completeness Exercises
- **URL:** https://courses.grainger.illinois.edu/cs573/fa2013/hw/files/hw_1_pract.pdf
- **Karp21 problems:** SET COVER, HITTING SET, EXACT COVER (by 4-Sets, from 3-Sets), VERTEX COVER, SUBSET-SUM, Scheduling (JOB SEQUENCING variant)
- **Other:** Longest Path
- **Type:** Homework-style chapter exercises

### 4. [Karp21] Texas A&M CSCE 629 (USA) — NP-Completeness Sample Questions
- **URL:** https://people.engr.tamu.edu/friesen/sample-np-629-12b.pdf
- **Karp21 problems:** DIRECTED HAMILTONIAN CIRCUIT (from undirected), TSP (with restricted weights {1,2,3}), VERTEX COVER
- **Type:** Exam-style questions

### 5. [Karp21] LUMS CS310 (Pakistan) — Problem Set 10: Complexity Theory
- **URL:** https://web.lums.edu.pk/~imdad/pdfs/CS310_ProblemSets/ProblemSet_10_Complexity_Theory.pdf
- **Karp21 problems:** CLIQUE (from 3SAT), VERTEX COVER (to Dominating Set), SUBSET SUM, HAMILTONIAN CYCLE, 3-COLORING (from 3SAT)
- **Type:** Problem set with gadget diagrams

### 6. [Karp21 + CSP] HSE Moscow (Russia) — "Show that the following problems are NP-complete"
- **URL:** https://www.hse.ru/mirror/pubs/share/217960059
- **Karp21 problems:** PARTITION (via Contains Partition), SUBSET SUM, Path with Forbidden Pairs
- **CSP problems:** 1-in-3 SAT (Schaefer reduction exercises)
- **Other:** NFA automaton hardness, Different Finite Regular Languages, Winnable Solitaire
- **Type:** Extensive exercise set with solutions

### 7. [Karp21] University of Ottawa CSI 4105 (Canada) — Midterm
- **URL:** https://www.site.uottawa.ca/~lucia/courses/4105-12/midterm-sol.pdf
- **Karp21 problems:** INDEPENDENT SET (apply 3-SAT reduction to concrete instance), 3D-MATCHING
- **Type:** Timed midterm exam with solutions

---

## Partially Usable: Problems Embedded in Lecture Notes

### 8. [Karp21] UIUC CS473 (USA) — 21 NP-Hard Problems
- **URL:** https://courses.grainger.illinois.edu/cs473/sp2010/notes/21-nphard.pdf
- **Karp21 problems:** 3-COLORING variant (careful 5-coloring), SUBGRAPH ISOMORPHISM, spanning tree with bounded degree
- **Verdict:** Extractable problems, but not a standalone problem set

### 9. [Karp21] CMU CMSC 451 (USA) — Reductions & NP-Completeness
- **URL:** https://www.cs.cmu.edu/~ckingsf/bioinfo-lectures/npcomplete.pdf
- **Karp21 problems:** Vertex Cover -> Set Cover (classic Karp reduction chain)
- **Verdict:** Thin on problems, but the VC->SC reduction is clean

### 10. [Adaptable] Polytechnique INF412 (France) — Foundations of CS: Logic, Models, Computations
- **URL:** https://www.enseignement.polytechnique.fr/informatique/INF412/uploads/Main/chap13english-goodINF412ENGLISH.pdf
- **CSP problems:** NAE4SAT NP-completeness, NAE4SAT -> NAE3SAT (clause splitting gadget), NAE3SAT -> MAX CUT (variable edges + clause triangles)
- **Type:** Textbook chapter with theorem/proof structure; cleanly extractable as reduction exercises

### 11. [Adaptable] Penn CIS 2620 (USA) — Some NP-Complete Problems
- **URL:** https://www.engineering.upenn.edu/~cis2620/notes/cis262sl14.pdf
- **Karp21 problems:** EXACT COVER (from SAT — full construction), CLIQUE (from Independent Set via complement graph), NODE COVER (from Independent Set)
- **Type:** Textbook chapter with detailed reduction constructions

### 12. [Adaptable] Columbia CSOR4231 (USA) — NP-Completeness
- **URL:** https://www.columbia.edu/~cs2035/courses/csor4231.F15/np.pdf
- **Karp21 problems:** Vertex Cover -> Subset-Sum (base-4 construction with dummy rows), SAT -> 3SAT
- **Type:** Lecture notes with worked-out reduction constructions

### 13. [CSP] CMU 15-750 (USA) — Proving NP-Completeness via Reductions
- **URL:** https://www.cs.cmu.edu/afs/cs/academic/class/15750-s17/ScribeNotes/lecture37.pdf
- **CSP problems:** 3-SAT -> NAE-4-SAT -> NAE-3-SAT -> 3-COLOR gadget chain
- **Verdict:** Good for CSP reduction chain problems, but not a standalone problem set

### 14. [CSP] Cornell CS4820 (USA) — Reduction from 3-SAT to MAX CUT
- **URL:** https://www.cs.cornell.edu/courses/cs4820/2014sp/notes/reduction-maxcut.pdf
- **CSP problems:** 3SAT -> NAE-4SAT -> NAE-3SAT -> MAX CUT with clause-splitting gadget
- **Verdict:** Good CSP reduction material, problem-level detail

---

## CSP-Specific Reference (Few Exam Problems Exist)

CSP decision-problem reductions are mainly taught at the graduate seminar level, not as exam problems. The canonical exam-level CSP reductions are:

- **3SAT -> 1-in-3SAT** (Schaefer's gadget: 5 clauses per original clause, 6 new variables)
- **3SAT -> NAE-3SAT** (reference variable z + clause splitting)
- **NAE-4SAT -> NAE-3SAT** (clause splitting with auxiliary variable)
- **NAE-3SAT -> MAX CUT** (variable edges + clause triangles)
- **NAE-3SAT -> 3-COLOR** (ground vertex + variable triangles + clause gadgets)

### 15. Wikipedia — 1-in-3-SAT (Schaefer Reduction)
- **URL:** https://en.wikipedia.org/wiki/1-in-3-SAT
- **Verdict:** The reduction itself is a classic exam problem

### 16. Berkeley CS252 (USA) — CSP Lecture Notes
- **URL:** http://people.eecs.berkeley.edu/~venkatg/teaching/15252-sp20/notes/csp-notes.pdf
- **Verdict:** Reference for which CSPs make good reduction targets

### 17. Technion (Israel) — CSP Lecture Notes (Yuval Filmus)
- **URL:** https://yuvalfilmus.cs.technion.ac.il/Courses/CSP/2022/Notes.pdf
- **Verdict:** Graduate-level CSP theory; 1-in-3SAT gadget, pp-definability

---

## Quick Reference (Not Problem Sources)

### 18. Wikipedia — Karp's 21 NP-complete problems
- **URL:** https://en.wikipedia.org/wiki/Karp%27s_21_NP-complete_problems

### 19. Wikipedia — Schaefer's dichotomy theorem
- **URL:** https://en.wikipedia.org/wiki/Schaefer%27s_dichotomy_theorem

### 20. Princeton CS423 — Polynomial-Time Reductions (Karp tree)
- **URL:** https://www.cs.princeton.edu/~wayne/cs423/lectures/reductions-poly-4up.pdf

### 21. Stanford CS154 — NP-Complete Problems (Trevisan)
- **URL:** https://theory.stanford.edu/~trevisan/cs154-12/np-reductions.pdf

### 22. NTU Taiwan — NP-Complete Reductions
- **URL:** https://www.csie.ntu.edu.tw/~yvchen/f107-ada/doc/181220_NP3.pdf

### 23. Liverpool COMP202 — Annotated NP-Complete Problems
- **URL:** https://www.csc.liv.ac.uk/~ped/teachadmin/COMP202/annotated_np.html

### 24. arXiv 1908.04198 — Simplified NP-Complete Variants of NAE-3-Sat
- **URL:** https://arxiv.org/pdf/1908.04198

### 25. npcomplete.owu.edu — Not All Equal 3SAT
- **URL:** https://npcomplete.owu.edu/2014/07/22/not-all-equal-3sat

---

## Summary

| Tag | Directly Usable | Partially Usable | Adaptable | Total |
|-----|----------------|------------------|-----------|-------|
| `[Karp21]` only | 5 (#1,3,4,5,7) | 2 (#8,9) | 2 (#11,12) | 9 |
| `[CSP]` only | 0 | 2 (#13,14) | 1 (#10) | 3 |
| `[Karp21 + CSP]` | 2 (#2,6) | 0 | 0 | 2 |

**Karp21 coverage across sources:** SAT/3SAT, CLIQUE, VERTEX COVER, INDEPENDENT SET, SET COVER, HITTING SET, EXACT COVER, SUBSET SUM, PARTITION, KNAPSACK, HAMILTONIAN CYCLE, TSP, 3D-MATCHING, 3-COLORING, SUBGRAPH ISOMORPHISM, JOB SEQUENCING.
**CSP coverage across sources:** 3SAT -> 1-in-3SAT, 3SAT -> NAE-3SAT, NAE-4SAT -> NAE-3SAT, NAE-3SAT -> MAX CUT, NAE-3SAT -> 3-COLOR, Set Splitting.
