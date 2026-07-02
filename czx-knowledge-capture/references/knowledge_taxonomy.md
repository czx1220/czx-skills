# 知识分类体系

## 主题文件与覆盖范围

| 文件名 | 覆盖内容 |
|:--|:--|
| `model_architecture.md` | LLM / VLM / Omni 模型架构设计（Transformer 变体、编码器-解码器设计、模态融合架构、模型创新点等） |
| `training_framework.md` | 训练框架原理与使用（Swift 训练流程/内部机制、DeepSpeed、Megatron-LM、HuggingFace Trainer、PyTorch FSDP 等框架的核心概念与差异） |
| `training_technique.md` | 训练技术与策略（SFT/RLHF/DPO/GRPO、LoRA/QLoRA、ZeRO stages、混合精度、梯度累积、数据并行/张量并行/流水线并行等） |
| `training_detail.md` | 训练实践细节（loss 设计与变体、学习率调度策略、数据配比与采样、收敛技巧、显存优化、训练稳定性等） |
| `inference_deployment.md` | 推理优化与部署（vLLM PagedAttention、TensorRT-LLM、量化方法 GPTQ/AWQ/GGUF、KV Cache 管理、Speculative Decoding、Continuous Batching、Serving 架构等） |
| `attention_mechanism.md` | 注意力机制专题（MHA/MQA/GQA 对比、Flash Attention 原理、Sliding Window、Sparse/Linear Attention、Cross Attention 等） |
| `position_encoding.md` | 位置编码专题（RoPE 原理与推导、ALiBi、M-RoPE/3D-RoPE、NTK-aware 外推、长度外推方法等） |
| `multimodal.md` | 多模态专题（视觉编码器 ViT/SigLIP、音频编码 Whisper、视频采样策略、跨模态对齐、多模态 token 设计等） |
| `tokenizer_data.md` | Tokenizer 与数据工程（BPE/SentencePiece/Tiktoken、数据清洗流程、多模态数据格式与处理等） |
| `evaluation.md` | 评测方法论（主流 benchmark 分类与公信力、TVG/DVC/VQA 等任务 metrics、评测流程设计等） |
| `misc.md` | 其他知识点（不好归类的跨领域知识、工程实践等） |

## 知识筛选标准

**应沉淀**:
- 主流模型的核心架构设计与创新点（LLM/VLM/Omni 均可）
- 面试高频技术问题（ZeRO-1/2/3 区别、LoRA 原理、KV Cache、Flash Attention 等）
- 框架核心概念与内部机制（Swift trainer 流程、vLLM 调度逻辑等）
- 训练/推理的关键技术细节（loss 设计思路、显存优化方法等）
- 关键技术的原理对比（如 MHA vs GQA、LoRA vs Full Finetune）

**不应沉淀**:
- 某篇 paper 的具体 benchmark 数字
- 项目特定的实验参数、配置（这些属于 memory）
- 代码级实现细节（通用调参技巧除外）
- 极其小众、无面试价值的技术
