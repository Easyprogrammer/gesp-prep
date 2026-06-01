# GESP-Prep

基于2023-2025年真题的 GESP C++ 1-8级智能备考助手。自动分析真题、统计知识点频率、生成结构化备考报告。

## 能做什么

- 逐套分析真题 PDF，提取知识点、题型、分值
- 按频率三级分类：必考（10/10套）→ 高频（8-9套）→ 偶考（≤7套）
- 生成 Excel 考纲对照矩阵（含颜色标记）和 Markdown 分析报告
- 生成备考知识点清单、30天复习计划
- 生成零基础知识点详解文档（逐行代码注释 + 生活类比 + 真题考法）
- 内置 1-8 级参考数据，无真题 PDF 也能输出备考指南

## 依赖

**Claude Code**（Anthropic 的 AI 编程工具）。 Skill 本质是规则文件 + 工作流提示词，模型调用由 Claude Code 订阅提供，不需要额外 API Key。

## 安装

```bash
mkdir -p ~/.claude/skills/GESP-Prep
cp SKILL.md reference.md reference-data.md ~/.claude/skills/GESP-Prep/
```

重启 Claude Code 即生效。对话中提到「GESP」「备考」「真题分析」「知识点统计」等关键词自动激活。

## 文件结构

```
GESP-Prep/
├── SKILL.md           # 核心技能定义，6阶段完整工作流
├── reference-data.md  # 内置1-8级真题频率统计数据（56KB）
└── reference.md       # 补充参考：降级策略、代码模板、Excel规范
```

## 生成产出示例

按流程跑完一个级别后，输出以下文件到用户 Documents 目录：

| 类型 | 文件名示例 |
|------|-----------|
| Excel 对照表 | `GESP_C++_4-6级_真题分析与考纲对照.xlsx` |
| Markdown 报告 | `GESP_C++_4-6级_真题分析报告.md` |
| 模拟试卷 | `GESP_C++五级_编程题模拟试卷.md` |
| 备考清单 | `GESP 五级备考知识点清单.pdf` |
| 知识点详解 | `GESP_C++六级知识点详解.md` |
| 递推/DP 汇总 | `CSP-J递推与递归相关题目汇总_详细解析版.md` |

## 其他 AI 工具能用吗

**不能直接安装使用**。`SKILL.md` 的格式是 Claude Code 专属的技能系统（YAML 头 + 触发关键词）。

但核心数据是通用的：
- `reference-data.md`（56KB 真题频率数据）任何模型都能读取
- `reference.md`（代码模板、分类标准）同样通用
- `SKILL.md` 中的工作流是纯提示词，可粘贴到 Codex、Cursor 等工具的自定义指令中

多平台适配版本后续考虑。

## 限制

- 真题 PDF 的质量直接影响分析准确度（扫描版 OCR 可能产生错误）
- 知识点分类依赖模型能力，建议人工复核关键结论
- 已内置 1-8 级完整频率数据

## 许可

MIT
