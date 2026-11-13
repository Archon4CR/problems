# 数据清洗方案

## 输入

`problems.json` — 从 01.md ~ 07.md 经 LLM 提取的结构化题目数据，102 条原始条目。

数据概况：

| 指标 | 数值 |
|---|---|
| 总条目 | 102 |
| 源文件数 | 7（01~07.md） |
| type=reduction | 58（顶层） + 26（子题） = 84 |
| type=meta | 25 |
| type=theory | 11 |
| type=algorithm | 7 |
| type=true_false | 1 |
| 有解答 | 36 |
| 难度非空 | 8 |

---

## 字段说明

| 字段 | 类型 | 说明 |
|---|---|---|
| `type` | enum | 题目类型：`reduction`（归约）、`meta`（父题容器）、`theory`（理论分析）、`algorithm`（算法设计）、`true_false`（判断） |
| `reduction_direction` | string | 归约方向，格式 `A -> B`，表示从问题 A 归约到问题 B |
| `problems_used` | list | 归约涉及的问题名称列表 |
| `cleaned_from` | enum | 条目来源：`top-level`（原始顶层条目）、`sub_problem`（从父题提取的子题）、`manual_fix`（人工修正） |

---

## 第 1 步：提取 reduction 类型

- 从 102 条中筛出 58 条顶层 `type=reduction`
- 遍历 25 条 `type=meta` 条目，从其 `sub_problems` 中提取 `type=reduction` 的子题（共 23 条），提平为顶层条目
- 遍历 `type=reduction` 但 `problems_used=[]` 的容器条目，从其 `sub_problems` 中提取 `type=reduction` 的子题（共 3 条：07-003a/b、07-004b），提平为顶层条目
- 提平的子题继承父题的 `source_file`、`source_name`，生成独立 id（如 `03-004a`）

**合并后：84 条**（58 顶层 + 23 meta 子题 + 3 容器子题）

---

## 第 2 步：剔除不合格条目

### 缺少 reduction_direction

| 条目 | 原因 | 处理 |
|---|---|---|
| 07-003 | 容器题，`problems_used=[]`，子题已提平 | 丢弃 |
| 07-004 | 容器题，`problems_used=[]`，子题已提平 | 丢弃 |
| 05-010 | theory 误标为 reduction，内容是"解释 Subset Sum 为什么是 NPC" | 丢弃 |
| 05-011 | theory 误标为 reduction，内容是"解释 Hamiltonian Cycle 为什么是 NPC" | 丢弃 |
| 03-005 | LONGEST-PATH，正经归约题但 LLM 漏抽方向 | 手工补方向，保留 |

### 06.md 末尾非归约条目

06-030 ~ 06-034（QUADRATIC DIOPHANTINE EQUATIONS、PROTEIN FOLDING 等）type=meta，不在 reduction 提取范围内，自动排除。

**此步后：80 条**（84 − 4 丢弃）

---

## 第 3 步：去重

### 跨源文件重复（保留有解答或更完整的版本）

| 归约方向 | 保留 | 丢弃 | 理由 |
|---|---|---|---|
| `HAM-CYCLE -> DIRECTED-HAM-CYCLE` | 02-002 | 04-002 | 02-002 有解答，04-002 无 |
| `INDEPENDENT-SET -> VERTEX-COVER` | 06-018 | 03-019a | 06-018 有解答，03-019a 无 |
| CLIQUE is NP-complete | 05-007 | 07-004b | 05-007 有明确方向，07-004b 无方向 |
| `HAMILTONIAN-CYCLE -> HAMILTONIAN-PATH` | 03-013c | 05-012 | 均无解答，03-013c statement 更完整 |
| `SUBSET-SUM -> KNAPSACK` | 01-005 | 03-018a | 01-005 有解答，03-018a 无 |
| `VERTEX-COVER -> SET-COVER` | 02-007 | 03-004a | 02-007 有解答，03-004a 无 |

### 03.md 内部重复

同一道题被原文的多个 "collection" 收录，只保留首次出现的版本：

| 问题 | 出现位置 | 出现次数 | 丢弃数 |
|---|---|---|---|
| MINIMUM SET COVER | 03-004a, 03-013a, 03-016a, 03-021a | 4 | 3 |
| HITTING SET | 03-004d, 03-013b, 03-016b, 03-021d | 4 | 3 |
| BIN PACKING | 03-004b, 03-017a, 03-020a | 3 | 2 |
| TILING | 03-004c, 03-017b, 03-020d | 3 | 2 |

### 非重复

同问题名但归约方向不同的条目（如 01-003 `HALF-CLIQUE -> CLIQUE` 与 01-004 `CLIQUE -> HALF-CLIQUE`）不视为重复，全部保留。

**此步后：64 条**（80 − 6 − 10）

---

## 第 4 步：补全短 statement

06.md 中部分条目的 statement 仅包含问题定义（Input/Question），缺少"证明 X 是 NP-complete"的任务描述。这是 LLM 抽取时的系统性遗漏——原文的任务描述在段落正文中，LLM 只提取了问题定义块。

**检测条件**：statement 不含以下任一关键词：`NP-complete`、`reduction`、`<=p`、`<=_P`、`reduces`、`NP-hard`

**补全规则**：根据 `reduction_direction` 自动生成任务前缀。例如：

```
原: "Input: A formula phi in 3CNF. Question: Is phi satisfiable?"
补: "Show that 3SAT is NP-complete by reduction from CIRCUIT SAT.\n\nInput: A formula phi in 3CNF. Question: Is phi satisfiable?"
```

**补全数量**：35 条。

**条目数不变：64 条**

---

## 第 5 步：补全缺失的 reduction_direction 和 problems_used

LLM 抽取时漏掉了 12 道 03.md 子题 + 1 道顶层题目（03-005）的 `reduction_direction`。这些题目的 statement 本身是完整的归约题，根据标准 NPC 归约路径手工补上方向和源问题。

### 补充依据

| 条目 | 方向 | 依据 |
|---|---|---|
| 03-003a SUBGRAPH ISOMORPHISM | `CLIQUE -> SUBGRAPH-ISOMORPHISM` | CLIQUE 是子图同构的特例：令 H = K_k，找 k-clique 等价于找 K_k 子图 |
| 03-003b LARGEST COMMON SUBGRAPH | `SUBGRAPH-ISOMORPHISM -> LARGEST-COMMON-SUBGRAPH` | 子图同构是最大公共子图的特例（两个图中一个恰好是另一个的子图） |
| 03-004a MINIMUM SET COVER | `VERTEX-COVER -> SET-COVER` | 顶点变为元素，每条边变为包含两端点的集合 |
| 03-004b BIN PACKING | `PARTITION -> BIN-PACKING` | 物品为各数，箱容量 = 总和的一半 |
| 03-004c TILING | `3SAT -> TILING` | 用矩形排列编码 3SAT 变量的真值赋值和子句满足性 |
| 03-004d HITTING SET | `VERTEX-COVER -> HITTING-SET` | Vertex Cover 是 Hitting Set 限定超边大小 = 2 的特例 |
| 03-005 LONGEST-PATH | `HAMILTONIAN-PATH -> LONGEST-PATH` | 设 k = n−1，最长路径退化为 Hamiltonian Path |
| 03-013c Hamiltonian Path | `HAMILTONIAN-CYCLE -> HAMILTONIAN-PATH` | 将环上一点拆成首尾两点 |
| 03-013d Max Degree Spanning Tree | `HAMILTONIAN-PATH -> MAX-DEGREE-SPANNING-TREE` | k=2 时等价于 Hamiltonian Path |
| 03-018a KNAPSACK | `SUBSET-SUM -> KNAPSACK` | 每项 weight=value=s_i，容量=目标值 |
| 03-018b MULTIPROCESSOR SCHEDULING | `PARTITION -> MULTIPROCESSOR-SCHEDULING` | m=2 处理器，deadline = 总和/2 |
| 03-022e r-DISK-COVER | `DOMINATING-SET -> r-DISK-COVER` | 单位圆图上的支配集归约到圆盘覆盖（k-Center 的对偶问题） |
| 03-023a MAX SAT | `3SAT -> MAX-SAT` | 设 k = 总子句数，MAX-SAT 退化为 SAT |

补全后 `cleaned_from` 标记为 `manual_fix`。同时补全对应条目的 `problems_used`（添加源问题名）。

**条目数不变：64 条**。全部有 `reduction_direction`。

### 已知特殊情况

06-001 `any NP language -> ACCEPTS SOMETHING FAST`：原文要求证明"ACCEPTS SOMETHING FAST 是 NP-complete"，没有指定从哪个具体问题归约。方向中的 "any NP language" 反映的是 NP-hardness 的定义（任意 NP 语言可归约到该问题），而非命名问题。`problems_used` 仅含 `["ACCEPTS SOMETHING FAST"]`。此条目保留原样，不与其他条目比较归约方向格式。

---

## 第 6 步：输出

生成 `problems_clean.json`。移除已全部为空的 `sub_problems` 字段，最终字段结构：

```
id, source_file, source_name, title, difficulty, tags,
problems_used, reduction_direction, type, statement,
hint, solution, has_solution, cleaned_from
```

---

## 预期结果

| 阶段 | 条目数 | 说明 |
|---|---|---|
| 原始 problems.json | 102 | — |
| 提取 reduction | 84 | 58 顶层 + 23 meta 子题 + 3 容器子题 |
| 剔除不合格 | 80 | 丢弃 07-003/07-004/05-010/05-011 |
| 去重 | 64 | 丢弃跨文件重复 6 + 内部重复 10 |
| 补全 statement | 64 | 前缀补全 35 条 |
| 补全 direction | 64 | 手工补 11 条（mark 为 manual_fix） |

最终 64 道归约题，方向明确，无重复，statement 完整。

未进入最终集合的条目类型：

| 类别 | 数量 | 去向 |
|---|---|---|
| theory / algorithm / true_false | 19 | 不在 reduction 范围内，不提取 |
| meta 父题（已提平子题） | 25 | 子题提平后父题不再保留 |
| 缺少方向（容器/误标） | 4 | 丢弃 |
| 跨源文件重复 | 6 | 丢弃 |
| 03.md 内部重复 | 10 | 丢弃 |
| 06.md 末尾非归约 | 5 | type=meta，自动排除 |
