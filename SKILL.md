---
name: GESP-Prep
description: >
  GESP (CCF Grade Examination of Software Programming) C++ 1-8级 备考助手。
  Analyzes past exam papers, extracts knowledge points, statistics on frequency and scores,
  and cross-references with the official syllabus to generate structured preparation reports.
  Built-in frequency statistics for all 8 levels based on 2023-2025 real exam papers.
  Activates when user mentions: GESP, 备考, exam preparation, 知识点, past papers, syllabus.
---

# GESP Exam Analysis & Preparation Report

## Overview

This skill analyzes GESP C++ certification exam materials (past papers + syllabus) to produce:
1. A structured per-exam knowledge point breakdown
2. Frequency statistics across all papers (must-know / high-frequency / occasional)
3. Syllabus coverage matrix (what is tested vs what the syllabus requires)
4. Excel workbook with multiple sheets (overview, coverage matrix, programming problem summary, trends)
5. Markdown narrative report with preparation advice
6. Optional per-level study guide with code templates, judgment question cheatsheet, and quick-reference tables
7. Optional per-level **knowledge-point deep-dive document** — detailed explanations ordered by exam frequency, with real-life analogies, code templates, common traps, and self-test exercises

## Supported Levels

GESP C++ Levels 1–8. The analysis adapits depth and expected algorithms to the target level.

| Level | Typical Algorithms |
|-------|-------------------|
| 1–2 | Basic I/O, loops, conditionals |
| 3 | Arrays, strings, simulation, enumeration |
| 4 | Functions, 2D arrays, file I/O, simple sorting |
| 5 | Number theory, linked lists, divide & conquer, greedy, binary search, recursion, high-precision arithmetic |
| 6 | Dynamic programming, BFS/DFS, advanced data structures |
| 7–8 | Advanced graph algorithms, complex DP, segment trees, network flow |

## Workflow

### Phase 0: Material Availability Check

**Before starting analysis, verify the user has the required PDF files.**

Required files:
1. **Official syllabus** (`C++&Python 认证标准.pdf` or equivalent) — defines knowledge blocks per level
2. **Past exam papers** — ideally 6+ sets for meaningful frequency statistics

**If the user does not have these files locally:**

1. **Guide them to official sources**:
   - GESP official website publishes past papers: `https://gesp.ccf.org.cn/101/1010/index.html`
   - The official syllabus can be downloaded from the GESP certification portal
   - Sample questions are often included in registration materials

2. **Create a material checklist** for the user to gather before re-running the analysis:
   ```
   Required materials checklist:
   [ ] Official syllabus PDF (C++ & Python 认证标准)
   [ ] Past papers: at least 6 sets for statistical significance
       Recommended: 2023.09 – 2025.12 (10 sets for levels 4–6)
   [ ] Organize papers by level in sub-folders (e.g. 5级/, 6级/)
   ```

3. **Built-in fallback dataset (`reference-data.md`)**: This skill includes a pre-built reference dataset (`reference-data.md`) extracted from the official GESP syllabus and 2023–2025 past paper analysis (levels 4–6). If the user has **no local PDFs at all**, use this built-in dataset to generate a preparation guide. The dataset contains:
   - Official syllabus knowledge blocks for all levels 1–8
   - Frequency statistics for levels 4–6 (must-know / high-frequency / occasional)
   - Programming problem trends and beyond-syllabus annotations
   - Core code templates per level
   - High-frequency true/false question cheatsheets
   - When using built-in data, clearly state: *"This guide is based on the skill's built-in reference dataset. For personalized frequency analysis, please provide 6+ past papers."*

4. **Graceful degradation tiers**:
   - **No papers, only syllabus** → Generate a syllabus-only study guide + reference built-in frequency stats where available.
   - **No syllabus, only papers** → Use built-in syllabus structure from `reference-data.md` + analyze provided papers.
   - **Neither syllabus nor papers** → Generate a guide entirely from `reference-data.md`. Provide official URLs for the user to download materials for future re-analysis.

### Phase 1: Discover Source Files

1. Ask the user to confirm or specify the folder containing GESP materials.
2. Look for:
   - `**/*认证标准*.pdf` or `**/*syllabus*.pdf` → Official syllabus
   - `**/*样题*.pdf` or `**/*sample*.pdf` → Sample questions
   - `**/*历年真题*.pdf` or `**/*[0-9][0-9][0-9][0-9]*.pdf` → Past papers organized by year/month
   - Sub-folders often organized by level (e.g. `5级/`, `level-5/`)
3. If the user does not specify a target level, default to analyzing levels 4–6 (the most common certification range).

### Phase 2: Extract PDF Content

Use the following toolchain in order of preference:

1. **Text-based PDFs**: `pdfplumber` or `pypdf` for direct text extraction.
2. **Scanned/image-based PDFs**: Convert pages to images (`pdf2image`), then OCR with `pytesseract` using `chi_sim+eng` language pack.
3. If extraction yields garbled text due to complex layouts, fall back to manual categorization based on known GESP question patterns and the agent's existing knowledge of the syllabus.

**Tool installation** (run once if missing):
```bash
pip3 install pdfplumber pypdf --quiet
pip3 install pytesseract pdf2image --quiet
```

### Phase 3: Analyze Individual Papers (Parallelize)

For each past paper PDF, launch a sub-agent or process sequentially to extract:

- **Exam date** (year and month)
- **Question type**: single-choice (15×2pts), true/false (10×2pts), programming (2×25pts)
- **Per-question knowledge point** (e.g. "linked list insertion", "merge sort stability", "prime factorization")
- **Programming problem summary**: problem statement, algorithm used, key technique

Use parallel sub-agents when analyzing more than 6 papers. Group papers by time period (e.g. 2023, 2024, 2025) to maximize parallelism.

### Phase 4: Aggregate Statistics

After all papers are analyzed, compute:

1. **Knowledge point frequency table**:
   - Must-know (10/10 papers)
   - High-frequency (8–9/10)
   - Occasional (≤7/10)
2. **Score distribution per knowledge point**: total points accumulated across all papers
3. **Programming problem trend analysis**: which algorithms dominate the 2 programming questions per paper
4. **Syllabus deviation**: topics tested frequently but not explicitly in the syllabus (mark as "beyond syllabus")

### Phase 5: Generate Outputs

#### Output A: Excel Workbook (`GESP_C++_X级_真题分析与考纲对照.xlsx`)

Create using `openpyxl`. Include these sheets:

| Sheet Name | Content |
|-----------|---------|
| 总览 | Summary: total papers, date range, question structure, key findings |
| 考纲对照矩阵 | Matrix: syllabus knowledge blocks vs exam frequency, color-coded (green=10/10, yellow=8-9/10, red=rare) |
| 编程题汇总 | Table: each programming problem with date, topic, algorithm, difficulty |
| 备考建议 | Bullet-point preparation advice based on frequency analysis |

Formatting rules:
- Header row: dark blue fill (`366092`), white bold font
- Color coding: green `#C6EFCE` for must-know, yellow `#FFD93D` for high-frequency, red `#FFC7CE` for rare
- Auto-fit column widths where possible; set minimum widths for readability

#### Output B: Markdown Report (`GESP_C++_X级_真题分析报告.md`)

Structure:
1. Executive summary (2–3 paragraphs)
2. Exam structure overview
3. Knowledge point frequency list (must-know / high-frequency / occasional)
4. Detailed findings per knowledge block
5. Programming problem trend analysis
6. Syllabus deviation analysis
7. Preparation recommendations

#### Output C: Study Guide (optional, when user asks)

If the user asks for a study guide or checklist for a specific level and exam date:

1. Generate a detailed knowledge-point checklist with explanations, common traps, and code templates.
2. Order knowledge points by **exam frequency** (must-know first), not by syllabus order.
3. Include:
   - One-page quick-reference cheat sheet
   - Core code templates that the student should memorize
   - High-frequency true/false question cheatsheet (with answers)
   - 30-day study plan
4. Clearly mark which topics the student has not yet learned and note when they will be covered.

#### Output D: Knowledge-Point Deep-Dive（知识点详解文档）

**文档定位**：供学生查找不理解的知识点的详细参考资料。假设学生**完全没有学过**这些内容，需要从零开始讲解。

**核心原则**：
- 每个知识点都假设学生是**零基础**，从最基础的概念讲起
- **讲清楚"为什么"**，不只是"是什么"
- **类比生活场景**，让抽象概念具象化
- **超纲内容也详细讲**，因为学生可能没学过

**文档结构**：

1. **开篇引导**：本章学什么、为什么重要、在考试中怎么考
2. **前置知识**：学这节之前需要懂什么
3. **生活类比**：用生活中的例子引入概念
4. **概念定义**：什么是XXX（通俗易懂）
5. **原理解释**：为什么是这样（讲清楚原理）
6. **实现思路**：怎么用代码实现（伪代码→真实代码）
7. **代码详解**：逐行注释，每行干什么
8. **执行过程**：用具体数字模拟代码执行，展示变量变化
9. **图解说明**：用图或表格直观展示（树结构、数组存储等）
10. **变形考法**：这个知识点在考试中会怎么变形考
11. **易错提醒**：学生常犯的错误和正确理解
12. **知识回顾**：学完后的快速回顾
13. **历年真题考法及策略**：总结该知识点在历年真题中的考察方式和对应的解题策略

**13. 历年真题考法及策略（必须包含）**：

这是文档中最重要的实用部分！基于历年真题，总结该知识点的：
- 考察题型分布（选择题/判断题/填空题/编程题）
- 每种题型的典型问法
- 对应的解题策略和步骤
- 答题技巧和注意事项

**格式示例**：

```markdown
## 历年真题考法及策略

### 一、选择题常见考法

**考法1：考察XXX概念**
- 典型问法："下列关于XXX的说法正确的是/错误的是"
- 解题策略：先判断题干考什么，再逐一分析选项
- 答题技巧：注意关键词，如"一定"、"必须"等

**考法2：考察代码输出**
- 典型问法："执行上述代码后，输出结果是什么"
- 解题策略：手算执行一遍代码，记录关键变量变化
- 答题技巧：遇到循环先找规律，不必完整模拟

### 二、判断题常见考法

**考法1：直接判断正误**
- 典型问法："下列说法正确/错误"
- 解题策略：找出说法中的绝对词（"都"、"一定"、"必须"），判断是否成立
- 答题技巧：举反例是判断错误的有效方法

### 三、编程题常见考法

**考法1：实现XXX算法**
- 典型问法："请用XXX方法解决XXX问题"
- 解题策略：套用模板，注意边界条件
- 答题技巧：先写框架，再填细节

### 四、复习优先级

| 优先级 | 考法 | 出现频率 |
|--------|------|---------|
| ⭐⭐⭐⭐⭐ | 代码输出 | 10/10套 |
| ⭐⭐⭐⭐ | 概念判断 | 8/10套 |
| ⭐⭐⭐ | 综合应用 | 5/10套 |
```

**写作要求**：
- 假设学生完全没学过，要讲到他能理解
- 公式要推导，不能只给结论
- 代码要逐行解释，不能省略
- 例子要用具体的数字，不要用抽象符号

**示例风格**：

```markdown
## 3.2 什么是指针？

### 为什么要学指针？
在内存中，每个变量都有一个"门牌号"——这就是地址。
指针就是存储"门牌号"的变量。

### 生活中的指针
想象一下：
- 变量是房间
- 指针是写着房间号的便签
- *指针就是打开那个房间*

### 代码解释
```cpp
int x = 10;      // 变量x，值是10
int *p = &x;     // p这个指针，存了x的地址
cout << *p;       // *p打开p指向的房间，得到10
```

### 逐行执行
1. `int x = 10;` → 在内存0x1000开了一个房间，存了10
2. `int *p = &x;` → p存了0x1000
3. `cout << *p;` → p指向0x1000，打开看是10，输出10
```

**必须包含的内容**：
- 每个知识点都有生活类比
- 每个代码模板都有逐行注释
- 每个核心概念都有具体数值的执行示例
- 重点处有图解说明ASCII art或表格
- 变形考法要标注在考试中出现的位置

**超纲内容处理**：
- 详细讲解，因为学生很可能真的没学过
- 说明这是"进阶内容"，考试可能用更简单方法
- 提供模板代码，学生可以直接背

### Phase 6: Deliver & Copy

1. Save all outputs to the agent workspace first (`~/.qoderwork/workspace/.../outputs/`).
2. Copy final deliverables to the user's selected Documents folder.
3. Present files with `file://` links so the user can open them directly.

## Quality Checklist

Before finishing, verify:

- [ ] All requested levels and date ranges are covered
- [ ] Syllabus pages are correctly matched to the target level
- [ ] Programming problem statistics reflect actual paper counts
- [ ] Color coding in Excel matches the frequency thresholds
- [ ] Markdown report includes at least one insight not obvious from raw data (e.g. "binary answer is a rising trend in 2025")
- [ ] Files are copied to the user's Documents folder and links are provided
