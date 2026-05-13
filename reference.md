# GESP Exam Analysis Reference

## When Users Lack Source PDFs

### Scenario A: No past papers, only syllabus
Generate a syllabus-only study guide ordered by official knowledge blocks. Include all code templates and concept explanations, but omit frequency statistics and trend analysis. Add a prominent note: "Frequency analysis requires past papers. Add 6+ exam papers to enable full statistical reporting."

### Scenario B: No syllabus, only past papers
Use the agent's built-in knowledge of GESP syllabus structure (levels 1–8) to create a makeshift syllabus. Mark knowledge blocks as "inferred from agent knowledge" and recommend the user obtain the official syllabus for accuracy.

### Scenario C: Neither syllabus nor past papers
Provide a generic preparation guide based on the agent's knowledge of GESP level requirements. Include the standard code templates and study plan. Clearly state that personalized frequency analysis and syllabus deviation detection are not possible without source materials.

### Official Sources for Materials

| Material | Source | URL Pattern |
|----------|--------|-------------|
| Past papers (published) | GESP official website | `https://gesp.ccf.org.cn/101/1010/index.html` |
| Official syllabus | GESP certification portal | Usually distributed with registration or available in the "认证指南" section |
| Sample questions | GESP official website | `https://gesp.ccf.org.cn/` → 认证指南 → 样题 |

## PDF Extraction Troubleshooting

### Text-based PDFs
Use `pdfplumber` for best table extraction. For pure text, `pypdf` is faster.

```python
import pdfplumber
with pdfplumber.open("paper.pdf") as pdf:
    text = "\n".join(page.extract_text() or "" for page in pdf.pages)
```

### Scanned/image-based PDFs
When `pdfplumber` returns empty text for all pages, the PDF is image-based.

```python
from pdf2image import convert_from_path
import pytesseract

images = convert_from_path("paper.pdf", dpi=200)
for i, img in enumerate(images):
    text = pytesseract.image_to_string(img, lang="chi_sim+eng")
```

**OCR quality issues**: Complex Chinese layouts with tables often produce garbled text. If OCR fails:
1. Try higher DPI (300)
2. If still unreadable, use the agent's existing knowledge of GESP syllabus structure to categorize questions manually

## Knowledge Point Classification (Level 5 Example)

### Must-know (10/10 papers)
- Recursion vs iteration (efficiency, stack overflow, termination)
- Sorting algorithms (quicksort, merge sort: stability, complexity, worst case)
- Linked lists (single/double/circular: insertion, deletion, traversal)
- Prime numbers (primality test, Sieve of Eratosthenes, linear sieve, prime factorization)
- Greedy algorithms (local vs global optimum)
- Binary search / binary answer (precondition: sorted, complexity O(logN))
- Divide and conquer (merge sort, quicksort)
- High-precision arithmetic (big integer add/sub/mul/div with carry/borrow)
- Time complexity analysis (O(1), O(logN), O(N), O(NlogN), O(N²), O(2^N))

### High-frequency (8–9/10)
- Euclidean algorithm (GCD, LCM)
- Fundamental theorem of arithmetic (unique factorization)
- Modular arithmetic / congruence

### Occasional
- Computer basics (I/O devices, network protocols)
- Bitwise operations
- Flowcharts

## Programming Problem Trends (Level 5, 2023–2025)

| Direction | Frequency (20 problems) | Representative Topics |
|-----------|------------------------|----------------------|
| Prime factorization / primes | 10 | Factorization, B-smooth numbers, lucky numbers, prime counting |
| Greedy | 5 | Task scheduling, allocation problems |
| Binary answer | 2 | Prize exchange, number moving |
| Sorting / multi-key | 1 | Grade ranking with tie handling |
| Prefix sum + enumeration | 1 | Grid sub-rectangle |
| Advanced number theory | 2 | Primitive root, GCD difference property |
| Bitwise / digit divide | 2 | AND maximization, binary digit parity sum |

## Syllabus Page Mapping

The official GESP C++ & Python certification standard PDF typically has:

- Level 1–3: Early pages (pages 2–10)
- Level 4: Pages 10–15
- Level 5: Pages 22–24
- Level 6: Pages 25–27
- Level 7–8: Pages 28+

When extracting syllabus text, search for `"X级"` or `"Level X"` to locate the correct section.

## Excel Sheet Specifications

### Sheet 1: 总览 (Overview)
- Paper count, date range
- Total questions analyzed
- Top 3 must-know knowledge points
- Key insight (one sentence)

### Sheet 2: 考纲对照矩阵 (Syllabus Coverage Matrix)
- Rows: knowledge blocks from syllabus
- Columns: each exam paper (date as header)
- Cells: checkmark or question count if tested
- Rightmost column: frequency count
- Color: green if 10/10, yellow if 8–9/10, red otherwise

### Sheet 3: 编程题汇总 (Programming Problem Summary)
- Columns: Date, Problem #, Topic, Algorithm, Key Technique, Difficulty
- Sort by date descending

### Sheet 4: 备考建议 (Preparation Advice)
- Free-form text based on analysis findings
- 30-day plan if user requests study guide

## Code Template Priority (Level 5)

Students should memorize these 10 templates:
1. `gcd(a, b)` — Euclidean algorithm
2. `isPrime(n)` — Trial division
3. `sieve(n)` — Sieve of Eratosthenes
4. `linearSieve(n)` — Euler/linear sieve
5. `factorize(n)` — Prime factorization
6. `partition(arr, l, r)` — Quicksort partition
7. `merge(arr, l, mid, r)` — Merge sort merge
8. `binarySearch(arr, target)` — Standard binary search
9. `removeElements(head, val)` — Linked list delete with dummy head
10. `add(a, b)` — High-precision addition
