# ICAE - In-Context Autoencoder Project

这一个基于 ICAE (In-Context Autoencoder) 的改进项目。ICAE 旨在通过将长上下文压缩到有限的记忆槽 (Memory Slots) 中，使大语言模型能够处理超长输入。

## 📁 核心文件说明

- **`modeling_icae_multi_span.py`**
  核心模型定义。在 Mistral 等基座模型上扩展了词表，增加了记忆 Token，并实现了压缩（编码）和重建/生成（解码）的前向传播逻辑。支持 LoRA 微调。

- **`pretrain.py`**
  预训练脚本。执行自编码任务（压缩->重建）和语言建模任务。
  - **用法**: `python pretrain.py --train_file train.jsonl --validation_file eval.jsonl ...`

- **`instruction_finetune.py`**
  指令微调脚本。在预训练基础上，让模型学会根据 Input 和 Prompt 利用记忆 Token 回答问题。

- **`training_utils.py`**
  数据处理工具。包含针对预训练和微调的 Tokenization 逻辑（注意：包含针对特定 Token ID 的处理，如迁移模型需修改）。

## 🚀 快速开始

### 1. 环境准备
```bash
pip install torch transformers peft datasets
```

### 2. 数据准备
准备 JSONL 格式的数据：
- **预训练**: `{"text": "长文本..."}`
- **微调**: `{"input": "背景...", "prompt": "问题...", "answer": "答案..."}`

### 3. 运行预训练
```bash
python pretrain.py \
    --model_name_or_path mistralai/Mistral-7B-v0.1 \
    --output_dir ./output_pretrain \
    --train_file ./data/train.jsonl \
    --validation_file ./data/eval.jsonl \
    --fixed_mem_size 128 \
    --lora_r 128
```

## ⚠️ 兼容性说明 (Qwen/Llama)
本项目默认配置适配 **Mistral-7B-v0.1**。
若要迁移到 **Qwen** 或 **Llama-3**，请务必修改 `training_utils.py` 中的 Chat 模板特殊 Token ID，以及 `modeling_icae_multi_span.py` 中的 BOS/EOS ID 定义。

## 🔗 参考
原始 ICAE 项目: [GitHub Link]
