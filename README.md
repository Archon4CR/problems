# Problems Data Directory

## Overview

This directory holds the raw materials, structured extraction data, and cleaned data for the Karp21 and CSP reduction problems, ultimately used to build an NP-completeness reduction benchmark. The problem set was compiled and curated entirely by the project author, based on publicly available university course materials.

## Data Pipeline

```
Raw PDFs (01.pdf ~ 14.pdf)
    ↓  transcription extraction
Markdown transcripts (01.md ~ 07.md, 7 in total)
    ↓  structured extraction
problems.json (102 raw entries)
    ↓  cleaning
problems_clean.json (64 reduction problems)
```

## File Descriptions

### Raw Materials

| File | Description |
|---|---|
| `sources.md` | Metadata such as source file provenance, course/institution, and year — 25 sources in total |
| `01.pdf` ~ `14.pdf` | Original PDFs; 01~07 have been transcribed to md, while 08~14 are of lower availability and have not yet been transcribed |
| `01.md` ~ `07.md` | Problem texts transcribed from the PDFs (Markdown), containing statements, hints, and solutions |

### Structured Data

| File | Description | Entries |
|---|---|---|
| `problems.json` | Raw structured data extracted by an LLM (all types) | 102 |
| `problems_clean.json` | **Final cleaned data**, containing only reduction problems, deduplicated and completed | 64 |

### Documentation

| File | Description |
|---|---|
| `cleaning.md` | The complete cleaning plan, including per-step operations, deduplication decisions, and the rationale for the manual supplementation of reduction_direction |
| `README.md` | This file |

## Final Dataset Overview

`problems_clean.json` contains **64 reduction problems**, with the following field structure:

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier, in the format `XX-NNN` or `XX-NNNa` |
| `source_file` | string | Source file name (01.md ~ 07.md) |
| `source_name` | string | Source course/institution name |
| `title` | string | Problem title |
| `difficulty` | string\|null | Difficulty annotation (null for the vast majority; the raw materials rarely annotate it) |
| `tags` | list | Tags; all tagged `Karp21`, some also tagged `CSP` |
| `type` | string | Fixed to `"reduction"` |
| `statement` | string | Problem statement (with LaTeX formulas) |
| `hint` | string\|null | Hint |
| `solution` | string\|null | Solution |
| `has_solution` | bool | Whether a solution exists |
| `reduction_source` | string | Normalized source problem name |
| `reduction_target` | string | Normalized target problem name |
| `cleaned_from` | enum | Origin marker: `top-level` / `sub_problem` / `manual_fix` |

`reduction_source` and `reduction_target` unify variant names of the same problem across different sources (e.g., `VERTEX-COVER` and `VERTEX COVER`, `NAE-3-SAT` and `NOT-ALL-EQUAL 3SAT`, `TRAVELLING SALESMAN` and `TSP`) into the same canonical name; the 63 problems cover 57 distinct problem names.

### Basic Statistics

| Metric | Value |
|---|---|
| Total problems | 63 |
| With solution | 37 |
| With hint | 16 |
| With reduction direction | 63 (all) |
| Canonical directions after deduplication | 60 |
| Non-null difficulty | 8 |

Tag distribution: all 63 problems are tagged `Karp21`, of which 36 are also tagged `CSP`. Entry marker distribution: `top-level` 50 entries, `sub_problem` 2 entries, `manual_fix` 11 entries.

### Source Distribution

| Source file | Problems | Source |
|---|---|---|
| 01.md | 7 | UCC CS4407 (2020) Problem Set |
| 02.md | 8 | UCC CS4407 (2013) Problem Set |
| 03.md | 15 | Multi-source collection (including problem sets, exam questions, etc.) |
| 05.md | 3 | Algorithm course exercises from a certain university |
| 06.md | 28 | HSE Moscow NP-complete problem set |
| 07.md | 2 | University of Ottawa CSI 4105 Midterm |

### Retained Duplicate Reduction Directions

The following 3 reduction directions appear twice in the dataset (from different sources, with a complete solution on both sides); they are retained to provide the benchmark with different problem phrasings of the same reduction:

| Canonical direction | Entries | Source |
|---|---|---|
| `3SAT -> NAE-3SAT` | 02-004, 06-006 | UCC 2013 / HSE Moscow |
| `HAMILTONIAN CYCLE -> TSP` | 02-008, 06-026 | UCC 2013 / HSE Moscow |
| `VERTEX COVER -> SET COVER` | 02-007, 06-019 | UCC 2013 / HSE Moscow |


## Cleaning Operations Summary

1. **Extract reduction types**: filter the reduction entries out of the 102, and flatten the sub-problems of meta parent problems
2. **Remove unqualified entries**: container problems and theory problems mislabeled as reduction
3. **Deduplicate**: detect cross-file duplicates by reduction direction, keeping the version with a solution or the more complete one; remove duplicate inclusions within 03.md
4. **Complete statements**: automatically generate a task-description prefix for short statements that contain only the problem definition
5. **Complete reduction_direction**: manually complete the directions of the 11 problems whose directions were missed during extraction, based on standard NPC reduction paths (Garey & Johnson)

For detailed steps and decision rationale, see [cleaning.md](cleaning.md).
