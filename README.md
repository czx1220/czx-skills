# CZX Skills — 从想法到投稿，15 天落地一篇论文

一套用于 AI Coding Agent 的学术论文写作 Skills，覆盖从初步想法到最终投稿的完整流程。

## 为什么做这个

AI 大跃进的时代，做研究也需要提速增效。

在自己写论文的过程中，我发现有很多和 AI 讨论的环节——搭框架、理逻辑、写正文、调图表、整理代码——几乎每篇论文都要经历一遍。这些对话中沉淀了大量可复用的经验，但如果不刻意整理，每次都要从头来过。

于是我开始把这些经验提炼成 Skills。说白了就是在"蒸馏"自己——把我在科研中积累的思维方式、写作习惯、审稿视角，一点一点固化到可复用的流程里。每写完一篇论文，就回头看看哪些环节可以做得更好，然后修改现有的 Skill 或者添加新的。这个仓库会持续迭代，跟着我的科研节奏一起成长。

做这件事首先是方便自己，让每一篇新论文都能站在之前的经验之上，而不是每次重新摸索。同时也希望开源出来，帮到有同样需求的朋友。这套 Skills 远称不上完美，但确实是我自己在用、并且切实帮我提速的工具。

## 整体工作流

```mermaid
flowchart LR
    A["① 构思<br/>Superpowers"] --> B["② 框架<br/>paper-framework"] --> C["③ 可视化<br/>mechanism-analysis-plotting<br/>plot-adjustment<br/>image2plot"] --> D["④ 写作<br/>paper-writing"] --> E["⑤ 润色审查<br/>逻辑检查 / 表达润色<br/>AI Review"] --> F["⑥ 文献检查<br/>CiteLint"] --> G["⑦ 代码整理<br/>code-paper-alignment"] --> H["⑧ 投稿<br/>paper-to-arxiv"]
```

### 🔹 前期：构思与框架

配合 [Superpowers](https://github.com/obra/superpowers) 的 brainstorming 能力，快速把模糊的想法变成可讨论的具体方向。想法成型后，用 `czx-paper-framework` 和 AI 深度讨论，把想法变成一个逻辑完整的论文骨架。

### 🔹 中期：实验可视化 → 正文写作

框架确定后，先做实验和可视化。`czx-mechanism-analysis-plotting`、`czx-plot-adjustment`、`czx-image2plot` 负责实验分析和图表的制作与统一。实验结果和图表都准备好之后，再用 `czx-paper-writing` 逐章节把骨架变成正文——先有图表数据，写起来才有底气。

### 🔹 后期：质量把关 → 代码整理 → 投稿

正文写完后，进入质量把关阶段。我们提供了三个 Prompt 模板（见下方 [Prompt 模板](#prompt-模板) 章节），分别用于：

- **逻辑检查**：红线审查，只抓致命问题——前后矛盾、术语混乱、严重语病，不做无意义的挑刺
- **表达润色**：深度打磨英文表达，对标顶会写作规范，消除中式英语，提升学术语感
- **AI 模拟审稿**：模拟真实的顶会审稿流程，提前暴露实验设计、论证逻辑和表述上的问题

润色和审查通过后，用我们的另一个开源工具 [CiteLint](https://github.com/haolin3/citelint) 对参考文献进行可视化检查——确认引用的准确性和完整性，避免投稿前的低级错误。然后用 `czx-code-paper-alignment` 整理代码。最后用 `czx-paper-to-arxiv` 转换格式，完成投稿。

## Skills 一览

| Skill | 用途 |
| :--- | :--- |
| **czx-paper-framework** | 通过和作者的深度讨论，把一个粗略想法变成逻辑完整的论文骨架。输出带占位符的 LaTeX 框架，把写论文变成"填空题"。 |
| **czx-paper-writing** | 在框架确定、实验做完之后，逐章节将占位符变成投稿级的正文，同步附录内容，完成最终润色。 |
| **czx-mechanism-analysis-plotting** | 分析模型内部机制（attention、hidden states、logits、PPL、entropy 等），从对比实验数据中探索规律、生成假设、输出出版级图表。 |
| **czx-plot-adjustment** | 统一一批已有图表的风格（配色、字号、线宽、图例等），确保一篇论文里的所有图看起来属于同一个项目。 |
| **czx-image2plot** | 根据代码或讨论内容，生成用于 OpenAI Image2 的结构化 prompt（如 intro figure、method pipeline 等示意图）。前面的 Skills 解决科研绘图问题，这个用来辅助 PPT 风格的展示图制作。 |
| **czx-code-paper-alignment** | 整理项目代码用于论文提交或开源发布。确保代码和论文描述一致，清理敏感信息，生成 README。 |
| **czx-paper-to-arxiv** | 自动识别原论文项目的格式（ACL、EMNLP、NeurIPS 等），转为 arxiv 提交格式。 |

## 通用研究辅助 Skills

除了论文写作 pipeline，这里还有几个项目无关的科研日常辅助 Skill。**注意**：与论文 Skills 不同，其中部分支持「讨论完成时自动触发」，并非全部显式调用。

| Skill | 用途 | 触发方式 |
| :--- | :--- | :--- |
| **czx-daily-summary** | 扫描当天实验/工作产出，与作者确认结论后生成日报（产出目录、关键指标、baseline 等通过配置占位符适配各项目）。 | 显式调用 / 触发词 |
| **czx-knowledge-capture** | 把对话中沉淀的大模型技术知识整理成面经风格 Q&A 知识库（位置由环境变量 `$KNOWLEDGE_DIR` 配置）。 | 手动 + 讨论完成时自动 |
| **czx-memory-update** | 整理/更新项目 memory，记录关键改动、新实验、新发现。 | 手动 + 关键任务完成后主动 |

## Prompt 模板

除了 Claude Code Skills，我们还整理了三个在投稿前质量把关阶段常用的 Prompt 模板。这些 Prompt 不依赖 Claude Code，可以直接在任何 LLM 对话中使用。

完整的 Prompt 文本见 [`prompts/`](prompts/) 目录。

| Prompt | 用途 | 使用方式 |
| :--- | :--- | :--- |
| **逻辑检查** | 红线审查：只抓致命的逻辑矛盾、术语混乱、严重语病，不做风格优化 | 粘贴 LaTeX 代码片段 |
| **表达润色** | 深度打磨英文表达，对标顶会写作规范，消除中式英语，输出润色后的 LaTeX + 中文直译 + 修改说明 | 粘贴 LaTeX 代码片段 |
| **AI 模拟审稿** | 模拟顶会审稿流程，输出审稿报告（strengths/weaknesses/rating）+ 针对性改稿建议 | 上传论文 PDF |

## 安装

将需要的 Skill 目录复制到你使用的 AI Coding Agent 的 skills 文件夹中。以 Claude Code 为例：

```bash
# 安装全部 skills
cp -r czx-* ~/.claude/skills/

# 或者只安装某个 skill
cp -r czx-paper-writing ~/.claude/skills/
```

其他支持自定义 skill/prompt 的 Agent 也可以直接使用这些文件。

## 使用

每个 Skill 通过对应的命令显式调用：

```
/czx-paper-framework
/czx-paper-writing
/czx-paper-to-arxiv
/czx-code-paper-alignment
/czx-mechanism-analysis-plotting
/czx-plot-adjustment
/czx-image2plot
```

所有 Skill 与用户的交流语言为中文，LaTeX 和代码输出保持英文。

## 设计原则

- **作者主导**：所有决策都由作者做出。Agent 提供建议、选项和质疑，但最终拍板权在作者手里。
- **显式调用**：论文写作 pipeline 的 Skill 不会自动触发，必须由用户主动调用（[通用研究辅助 Skills](#通用研究辅助-skills) 中部分支持自动触发，见该节说明）。
- **迭代沉淀**：每个 Skill 都来自真实的论文写作经验，在实际使用中不断打磨。

## 用这套 Skills 产出的论文

**Knowing but Not Correcting: Routine Task Requests Suppress Factual Correction in LLMs**
<p align="left">
  <a href='https://arxiv.org/abs/2605.05957'>
    <img src='https://img.shields.io/badge/Arxiv-2605.05957-A42C25?style=flat&logo=arXiv&logoColor=A42C25'>
  </a>
</p>

**AVID: A Benchmark for Omni-Modal Audio-Visual Inconsistency Understanding via Agent-Driven Construction**
<p align="left">
  <a href='https://arxiv.org/abs/2604.13593'>
    <img src='https://img.shields.io/badge/Arxiv-2604.13593-A42C25?style=flat&logo=arXiv&logoColor=A42C25'>
  </a>
</p>

**GhostPrompt: Jailbreaking Text-to-image Generative Models based on Dynamic Optimization**
<p align="left">
  <a href='https://arxiv.org/abs/2505.18979'>
    <img src='https://img.shields.io/badge/Arxiv-2505.18979-A42C25?style=flat&logo=arXiv&logoColor=A42C25'>
  </a>
</p>

## 致谢

- [Superpowers](https://github.com/obra/superpowers) — 初期构思阶段的灵感来源和得力工具
- [CiteLint](https://github.com/haolin3/citelint) — 投稿前参考文献检查的好帮手

## 作者

**Zixuan Chen (陈梓轩)**

<a href='https://scholar.google.com/citations?user=gcb-zkkAAAAJ'>
  <img src='https://img.shields.io/badge/Google%20Scholar-4285F4?style=flat&logo=google-scholar&logoColor=white'>
</a>
