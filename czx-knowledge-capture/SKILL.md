---
name: czx-knowledge-capture
description: 对话中自动或手动沉淀大模型技术知识为面经风格 Q&A 文档。触发条件：(1) 用户说「记一下」「沉淀」「加到知识库」「更新到knowledge」等；(2) 讨论完技术话题后用户发出完成信号（「ok」「明白了」「懂了」「下一个」、转换话题）时自动触发；(3) 用户询问知识库状态（「有没有更新」「记了吗」）时立即触发。不触发于项目特定实验参数、paper benchmark 数字、代码 debug 过程。
---

# 知识沉淀 Skill

将对话中产生的大模型技术知识，以面经 Q&A 格式持久化到知识库目录 `$KNOWLEDGE_DIR`（环境变量配置；未设置时默认 `~/knowledge/`）。

## 知识库位置与结构

```
$KNOWLEDGE_DIR/
├── merge_knowledge.py        # 合并脚本：分散文件 → 单文件 knowledge.md
├── knowledge.md              # 【自动生成】完整知识库单文件，复制即用
├── model_architecture.md     # 模型架构（LLM/VLM/Omni 架构设计）
├── training_framework.md     # 训练框架（Swift、DeepSpeed、Megatron、HF Trainer、FSDP 等）
├── training_technique.md     # 训练技术（SFT/RLHF/DPO、LoRA、混合精度、梯度策略等）
├── training_detail.md        # 训练细节（loss 设计、数据配比、学习率调度、收敛技巧等）
├── inference_deployment.md   # 推理与部署（vLLM、量化、KV Cache、Serving 等）
├── attention_mechanism.md    # 注意力机制（MHA/MQA/GQA、Flash Attention 等）
├── position_encoding.md      # 位置编码（RoPE、ALiBi、M-RoPE、外推等）
├── tokenizer_data.md         # Tokenizer 与数据工程
├── evaluation.md             # 评测方法（benchmark 分类、metrics 含义等）
├── multimodal.md             # 多模态专题（视觉编码、音频处理、模态融合、跨模态对齐等）
└── misc.md                   # 其他
```

编辑时写分散文件（增量修改快），写完后运行合并脚本自动生成完整单文件。

分类详情见 [references/knowledge_taxonomy.md](references/knowledge_taxonomy.md)。

## 触发判断

### 自动触发（讨论完成时 invoke Skill tool）

对话中讨论了可沉淀的技术知识后，**当用户发出"讨论完成"信号时，必须 invoke 本 skill 执行写入**：

可沉淀的知识类型：
- 解释了某个模型的架构设计或创新点（LLM/VLM/Omni 均算）
- 对比了技术方案（如 ZeRO-1 vs 2 vs 3、LoRA vs QLoRA）
- 澄清了框架的核心概念或内部机制（Swift trainer 流程、vLLM PagedAttention 等）
- 讨论了训练/推理的具体技术细节（loss 设计、数据处理流程、显存优化等）
- 回答了一个面试级别的技术问题

"讨论完成"的信号（出现即 invoke）：
- 用户确认理解：「ok」「明白了」「懂了」「理解了」「好的」
- 用户转换话题：开始问一个不同的技术问题，或切换到其他任务
- 用户开始下一个任务：「接下来」「然后」「下一个问题」
- 用户主动询问知识库：「有没有更新到knowledge」「记了吗」「沉淀了吗」

重要：不要在讨论进行中途 invoke（避免打断思路），但一旦看到完成信号就**立即 invoke**，不要等用户追问。

### 不触发

- 项目特定的实验参数、配置、评测结果 → 用 memory
- 某篇 paper 的具体 benchmark 数字
- 代码调试、bug 修复过程
- 极小众的技术
- 讨论尚在进行中（用户还在追问同一话题的细节）

### 手动触发（立即 invoke）

用户说「记一下」「沉淀一下」「加到知识库」「更新到knowledge」时立即触发，无需等待讨论完成。

## 写入流程

讨论完整后批量写入，不要每个问答立即更新。

**重要：整个写入过程完全自主执行，不向用户确认任何中间步骤**（不问"放到哪个文件？""要不要写入？""这样可以吗？"）。自行判断分类、位置、合并策略，写完后只报一句结果（如"已沉淀 N 条到 xxx.md"）。

1. **汇总知识点**：回顾整段讨论，提炼所有值得沉淀的知识点，去除项目特定细节
2. **查 INDEX.md 定位**：Read INDEX.md 了解已有知识点全貌，确定各新知识点归属文件
3. **Read 目标文件全文**：逐个读取要写入的分散文件，理解已有内容的组织结构和知识递进关系
4. **判断合并还是新增**：
   - **已有相同知识点** → 合并补充：将新信息融入已有 Q&A 的要点或补充中，不新建条目
   - **已有相关但不同角度的知识点** → 在其后新增，保持知识递进（如先原理后对比后实践）
   - **全新知识点** → 找到文件中逻辑上合适的位置插入，而非简单追加到末尾
5. **编排检查**：写入后确认文件内的 Q&A 顺序合理——同一主题从基础到深入、从概念到对比到实践
6. **更新 INDEX.md**：仅对新增的知识点添加条目，合并更新的不重复添加
7. **运行合并脚本**：`python "$KNOWLEDGE_DIR/merge_knowledge.py"` 生成完整单文件

### 编排原则

- 同一文件内知识点按递进关系排列：概念介绍 → 内部原理 → 对比辨析 → 实践要点
- 相关知识点紧邻放置（如 LoRA 紧跟在 ZeRO 后面，都属于训练显存优化范畴）
- 禁止重复：如果新内容和已有条目有 >50% 重叠，必须合并而非新建
- Q 的粒度适中：一个 Q&A 聚焦一个可独立回答的问题，不要把多个问题塞进一个 Q

## Q&A 格式

```markdown
### N. [简洁的技术问题，面试问法]

[1-3 句概括性回答]

- 要点1
- 要点2
- ...

> 补充：[可选，延伸知识、对比、易混淆点]
```

其中 N 为该文件内的连续编号（从 1 开始），每个文件独立编号。

### 格式要求

- 问题用面试提问的口吻，简洁精准
- A 先给出概括，再用要点展开
- 要点 3-7 个，每个一句话说清
- 补充部分可选，放对比、易错点、延伸
- 不放代码块，除非极简关键代码（< 5 行）
- 不放参考链接
- 自包含：每个 Q&A 独立可读，不依赖对话上下文

## INDEX.md 格式

```markdown
# 知识库索引

## 模型架构
- Qwen2.5-Omni 整体架构 → model_architecture.md
- LLaMA 3 架构改进 → model_architecture.md

## 训练框架
- Swift SFT 训练流程 → training_framework.md
- DeepSpeed ZeRO 配置 → training_framework.md

## 训练技术
- ZeRO-1/2/3 区别 → training_technique.md

## 训练细节
- Cycle loss 设计思路 → training_detail.md

## 推理与部署
- vLLM PagedAttention 原理 → inference_deployment.md

## 注意力机制
- MHA vs MQA vs GQA → attention_mechanism.md

## 位置编码
- RoPE 原理 → position_encoding.md

## 多模态
- 视觉编码器选型 → multimodal.md

## 评测方法
- TVG 评测指标 → evaluation.md
```

每条格式：`- [知识点标题] → [文件名]`

## 质量标准

- **面试导向**：内容可直接用于技术面试复习
- **自包含**：每个 Q&A 独立可读，去掉项目细节
- **准确**：技术描述必须准确，不确定处标注
- **全面覆盖**：LLM、VLM、Omni、训练、推理、框架细节均在范围内
