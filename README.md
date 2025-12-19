# Hierarchical Compression (Context-Hierarchical-Compression)

## 📌 项目愿景 (Project Vision)

本项目旨在基于 **ICAE (In-Context Autoencoder)** 的思想，探索和实现**层级化上下文压缩 (Hierarchical Context Compression)**。

我们的核心目标是通过多级记忆压缩机制，打破当前大语言模型的上下文长度限制，实现更高效的超长文本理解与生成。

### 核心变更 (Key Changes)
- **基座模型**: 全面迁移至 **Qwen (千问)** 系列 (如 Qwen2.5/Qwen3)，替代原论文中的 Mistral/Llama。选择 Qwen 是因其在长文本和中文理解上的卓越性能。
- **技术路线**:
  1.  **Phase 1**: 复现并适配 ICAE 到 Qwen 模型。
  2.  **Phase 2**: 实现**层级压缩**架构 (Hierarchical Compression)，支持递归式的上下文编码。
  3.  **Phase 3**: 针对超长文本任务进行微调和优化。

---

## 📂 项目结构 (Structure)

目前代码库处于 **Adaptation Phase**（适配阶段），主要包含从 ICAE v2 迁移来的核心逻辑。

- **`modeling_icae_multi_span.py`**: 
  - 核心模型定义。
  - **TODO**: 需要适配 Qwen 的词表和特殊 Token (BOS/EOS/Chat Templates)。
  - **Goal**: 实现支持层级输入的 `HierarchicalICAE` 类。

- **`training_utils.py`**:
  - 数据处理与 Tokenization。
  - **TODO**: 移除针对 Mistral 的硬编码 Token ID，改为 Qwen 的动态各类 Chat Template 处理。

- **`pretrain.py` & `instruction_finetune.py`**:
  - 预训练与指令微调脚本。
  - 支持自定义数据路径 (JSONL)。

---

## 🚀 快速开始 (Quick Start)

### 1. 环境准备
```bash
pip install torch transformers peft datasets
```

### 2. 预训练 (Pre-training)
目前脚本支持自定义数据路径，但请注意**基座模型适配**尚未完全完成。

```bash
# 示例命令 (迁移完成后)
python pretrain.py \
    --model_name_or_path Qwen/Qwen2.5-7B-Instruct \
    --output_dir ./output_hierarchical_qwen \
    --train_file ./data/train.jsonl \
    --validation_file ./data/eval.jsonl \
    --fixed_mem_size 128 \
    --lora_r 128
```

## ⚠️ 迁移指南 (Migration Guide)

如果您正在参与开发，请重点关注以下迁移工作：

1.  **Token ID 修复**: 检查 `training_utils.py`，将 `[1, 733...]` 等 Mistral 特定 ID 替换为 Qwen 的 `tokenizer.apply_chat_template` 逻辑。
2.  **特殊 Token**: 确认 `modeling_icae_multi_span.py` 中的 `bos_id`, `eos_id` 是否自动获取自 Qwen Tokenizer。

---
*Based on ICAE, evolving towards Infinite Context with Qwen.*
