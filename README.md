# Problems 数据目录

## 概述

本目录存放 Karp21 和 CSP 归约试题的原始资料、结构化抽取数据和清洗后数据，最终用于构建 NP-完全性归约 benchmark。

## 数据流水线

```
原始 PDF (01.pdf ~ 14.pdf)
    ↓  提取转写
Markdown 原文 (01.md ~ 07.md，共 7 篇)
    ↓  结构化抽取
problems.json (102 条原始条目)
    ↓  清洗
problems_clean.json (64 道归约题)
```

## 文件说明

### 原始资料

| 文件 | 说明 |
|---|---|
| `sources.md` | 源文件出处、课程/机构、年份等元信息，共 25 个来源 |
| `01.pdf` ~ `14.pdf` | 原始 PDF，其中 01~07 已转写为 md，08~14 可用性较低尚未转写 |
| `01.md` ~ `07.md` | 从 PDF 转写的题目原文（Markdown），含陈述、提示、解答 |

### 结构化数据

| 文件 | 说明 | 条目数 |
|---|---|---|
| `problems.json` | LLM 抽取的原始结构化数据（全部类型） | 102 |
| `problems_clean.json` | **最终清洗后数据**，仅含归约题，已去重补全 | 64 |

### 文档

| 文件 | 说明 |
|---|---|
| `cleaning.md` | 完整清洗方案，含每步操作、去重决策、reduction_direction 手工补注依据 |
| `README.md` | 本文件 |

## 最终数据集概况

`problems_clean.json` 包含 **64 道归约题**，字段结构如下：

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一标识，格式 `XX-NNN` 或 `XX-NNNa` |
| `source_file` | string | 来源文件名（01.md ~ 07.md） |
| `source_name` | string | 来源课程/机构名称 |
| `title` | string | 题目标题 |
| `difficulty` | string\|null | 难度标注（绝大部分为 null，原始资料极少标注） |
| `tags` | list | 标签，全部标 `Karp21`，部分同时标 `CSP` |
| `type` | string | 固定为 `"reduction"` |
| `statement` | string | 题目陈述（含 LaTeX 公式） |
| `hint` | string\|null | 提示 |
| `solution` | string\|null | 解答 |
| `has_solution` | bool | 是否有解答 |
| `reduction_source` | string | 归一化源问题名 |
| `reduction_target` | string | 归一化目标问题名 |
| `cleaned_from` | enum | 来源标记：`top-level` / `sub_problem` / `manual_fix` |

`reduction_source`、`reduction_target` 将不同来源中的同问题异名（如 `VERTEX-COVER` 与 `VERTEX COVER`、`NAE-3-SAT` 与 `NOT-ALL-EQUAL 3SAT`、`TRAVELLING SALESMAN` 与 `TSP`）统一为同一规范名称，63 道题涵盖 57 个不同问题名。

### 基本统计

| 指标 | 数值 |
|---|---|
| 总题数 | 63 |
| 有解答 | 37 |
| 有提示 | 16 |
| 有归约方向 | 63（全部） |
| 去重规范方向数 | 60 |
| 难度非空 | 8 |

标签分布：全部 63 道均标 `Karp21`，其中 36 道同时标 `CSP`。条目标记分布：`top-level` 50 条、`sub_problem` 2 条、`manual_fix` 11 条。

### 来源分布

| 源文件 | 题数 | 来源 |
|---|---|---|
| 01.md | 7 | UCC CS4407 (2020) Problem Set |
| 02.md | 8 | UCC CS4407 (2013) Problem Set |
| 03.md | 15 | 多来源合集（含习题集、考试题等） |
| 05.md | 3 | 某大学算法课习题 |
| 06.md | 28 | HSE Moscow NP-complete 问题集 |
| 07.md | 2 | University of Ottawa CSI 4105 Midterm |

### 保留的重复归约方向

以下 3 个归约方向在数据集中出现两次（来自不同来源，且两边均有完整解答），保留原因是为 benchmark 提供同一归约的不同题目表述：

| 规范方向 | 条目 | 来源 |
|---|---|---|
| `3SAT -> NAE-3SAT` | 02-004, 06-006 | UCC 2013 / HSE Moscow |
| `HAMILTONIAN CYCLE -> TSP` | 02-008, 06-026 | UCC 2013 / HSE Moscow |
| `VERTEX COVER -> SET COVER` | 02-007, 06-019 | UCC 2013 / HSE Moscow |


## 清洗操作摘要

1. **提取 reduction 类型**：从 102 条中筛出 reduction 条目，并提平 meta 父题的子题
2. **剔除不合格条目**：容器题、误标为 reduction 的 theory 题
3. **去重**：按归约方向检测跨文件重复，保留有解答或更完整的版本；移除 03.md 内部重复收录
4. **补全 statement**：为仅含问题定义的短 statement 自动生成任务描述前缀
5. **补全 reduction_direction**：根据标准 NPC 归约路径（Garey & Johnson）手工补全 11 道漏抽方向的题目

详细步骤和决策依据见 [cleaning.md](cleaning.md)。
