---

title: 大模型微调技术
date: 2025-04-13 12:00:00 +0800
categories: [AI]
tags: [微调, 大模型]

---

大模型微调（Fine-tuning）算法旨在高效调整预训练模型的参数以适应下游任务，同时避免过高的计算成本。以下是主流的微调方法分类及代表性算法：

---

### **1. 全参数微调（Full Fine-tuning）**
- **特点**：更新模型所有参数，适合数据充足、计算资源丰富的场景。
- **缺点**：计算成本高，易过拟合，需要保存多份模型副本。
- **代表场景**：BERT、GPT-2时代的经典微调方式。

---

### **2. 参数高效微调（Parameter-Efficient Fine-Tuning, PEFT）**
#### **（1）适配器（Adapter）**
- **原理**：在Transformer层中插入小型全连接网络，仅训练适配器参数。
- **结构**：  
  - 原始参数冻结，适配器通常放在FFN层后（如Houlsby Adapter）。  
  - 参数量约为原模型的0.5%~5%。  
- **优点**：模块化设计，支持多任务切换。  
- **缺点**：引入额外推理延迟。  
- **变体**：Parallel Adapter、Compacter（参数共享）。  

#### **（2）LoRA（Low-Rank Adaptation）**
- **原理**：通过低秩矩阵分解（\( \Delta W = BA \)）更新权重，而非直接调整原参数。  
- **优势**：  
  - 无推理延迟（可合并矩阵）。  
  - 参数量极少（如GPT-3仅需0.1%参数）。  
- **应用**：LLaMA、ChatGLM等开源模型的指令微调。  
- **扩展**：  
  - **QLoRA**：结合4-bit量化，进一步降低显存。  
  - **AdaLoRA**：动态调整秩（rank）分配。  

#### **（3）前缀微调（Prefix Tuning）**
- **原理**：在输入序列前添加可学习的“虚拟token”（前缀），通过注意力机制影响后续生成。  
- **特点**：  
  - 仅需调整前缀参数（如Prefix长度=10时，参数量极低）。  
  - 适合生成任务（如GPT类模型）。  
- **变体**：P-Tuning v2（将前缀扩展到每一层）。  

#### **（4）提示微调（Prompt Tuning）**
- **原理**：类似Prefix Tuning，但仅在最底层输入添加可学习提示（Prompt）。  
- **优势**：比Prefix Tuning更轻量，适合分类任务。  
- **代表**：Google的T5模型微调。  

#### **（5）其他PEFT方法**  
- **BitFit**：仅微调偏置项（Bias），参数量占比约0.1%。  
- **DiffPruning**：学习参数掩码，稀疏化更新。  
- **IA³**（Infused Adapter by Inhibiting and Amplifying Activations）：通过缩放激活值调整模型。  

---

### **3. 基于强化学习的微调（RL-based Fine-tuning）**
- **RLHF（Reinforcement Learning from Human Feedback）**：  
  - **步骤**：  
    1. 监督微调（SFT） → 2. 奖励模型训练 → 3. PPO优化策略。  
  - **应用**：ChatGPT、Claude等对话模型的对齐优化。  
- **DPO（Direct Preference Optimization）**：  
  - 直接优化人类偏好数据，避免复杂的RL流程。  

---

### **4. 高效训练技术**
#### **（1）混合专家（MoE）微调**
- **原理**：仅激活部分专家网络（如Switch Transformer），减少计算量。  
- **应用**：Google的GLaM模型。  

#### **（2）梯度检查点（Gradient Checkpointing）**
- 用时间换显存，支持更大批次微调。  

#### **（3）量化微调（Quantized Fine-tuning）**
- **QLoRA**：4-bit量化+LoRA，可在单卡上微调65B模型。  
- **LLM.int8()**：8-bit量化微调。  

---

### **5. 领域自适应微调**
- **持续学习（Continual Learning）**：防止微调新任务时遗忘旧知识。  
- **领域对抗训练（DANN）**：对齐源域和目标域特征分布。  

---

### **6. 开源工具库**
- **Hugging Face PEFT**：支持LoRA、Adapter、Prefix Tuning等（[GitHub](https://github.com/huggingface/peft)）。  
- **DeepSpeed**：微软的高效训练框架，支持ZeRO-3优化。  
- **ColossalAI**：集成LoRA、QLoRA等算法。  

---

### **方法选择建议**
| **场景**             | **推荐方法**          |
| -------------------- | --------------------- |
| 资源充足，高精度需求 | 全参数微调 + 早停法   |
| 单卡微调大模型       | LoRA/QLoRA            |
| 多任务快速切换       | Adapter/Prefix Tuning |
| 对齐人类偏好         | RLHF/DPO              |
| 超低资源（边缘设备） | BitFit + 量化         |

---

### **总结**
- **轻量级微调**：LoRA、Adapter、Prefix Tuning是当前主流，平衡效率与性能。  
- **对齐需求**：RLHF/DPO适合对话和生成任务。  
- **未来方向**：MoE微调、更高效的稀疏化方法（如DiffPruning）。  

如果需要具体方法的代码实现或某类任务的微调案例（如对话、分类），可以进一步探讨！