# SFT · 2026-03-02 ~ 2026-03-06

**论文数**: 12 篇

---

## 📊 趋势分析

### 研究全貌

本批次SFT领域论文的研究方向主要集中在**模型编辑与知识更新**、**参数高效微调（PEFT）的优化与扩展**、**微调策略的泛化与稳定性**以及**特定任务（如分类、知识图谱）的微调增强**。当前的热点问题是如何在高效、低成本地适应新任务或知识的同时，最大限度地保留模型的通用能力和先验知识，并避免灾难性遗忘或性能退化。整体研究趋势呈现出从简单的启发式方法向更具理论指导、更精细化的设计转变，强调在微调过程中对模型内部表示和动态的深入理解与控制。

### 重点方法深度解析

从这批论文中，以下几个工作因其创新性、实用性和深刻的洞察而尤为突出：

**1. 《Energy-Regularized Sequential Model Editing on Hyperspheres》 [URL](https://arxiv.org/abs/2510.01172)**
*   **核心创新点**：针对大语言模型序列化知识编辑中的性能退化问题，提出了一种基于超球面能量（HE）正则化的编辑策略SPHERE。其核心洞察是，神经元权重在超球面上的分布均匀性（HE稳定性）与模型的知识保留能力直接相关。
*   **技术细节**：SPHERE首先利用HE量化编辑过程中的神经元分布变化。为了稳定HE，它识别出预训练权重矩阵主超球面方向的互补稀疏空间，并将新知识（编辑更新）投影到这个稀疏空间上。这种方法最大限度地减少了对主导模型原有知识的核心权重方向的扰动，从而在吸收新知识的同时保护了先验知识。
*   **效果验证**：在LLaMA3-8B和Qwen2.5-7B模型上的实验表明，SPHERE在编辑成功率上平均优于最佳基线方法16.41%，同时在通用任务（如MMLU）上的性能保留也最为忠实。
*   **适用场景**：适用于需要持续、可靠地更新大模型知识库的场景，例如修正事实错误、注入领域知识，是实现模型“终身学习”的关键技术路径。

**2. 《Why Reinforcement Fine-Tuning Enables MLLMs Preserve Prior Knowledge Better: A Data Perspective》 [URL](https://arxiv.org/abs/2506.23508)**
*   **核心创新点**：从数据分布和学习动态的视角，揭示了强化微调（RFT）相比监督微调（SFT）能更好保留先验知识的内在机制，挑战了“算法差异主导遗忘”的常见观点。
*   **技术细节**：研究通过引入“拼图”等新颖任务，分析了SFT和RFT训练数据对模型参数的影响。研究发现，RFT（或基于RFT模拟的数据）产生的梯度更新，其**幅度更小**且**方向与基础模型的概率分布更对齐**。这意味着RFT主要是在强化模型已有能力中正确的部分，而非强行引入可能冲突的新模式，因此对先验知识的干扰更弱。
*   **效果验证**：在Qwen2.5-VL系列模型上的实验证实，SFT学习快但遗忘严重，RFT学习慢但知识保留好。进一步，使用RFT模拟数据训练的SFT也能在快速学习新任务的同时更好地保留知识。
*   **适用场景**：为任何需要在新任务学习和知识保留间取得平衡的微调场景提供了重要启示。建议在构建微调数据时，应注重其与基础模型分布的“对齐度”，而不仅仅是算法选择。

**3. 《Stable-LoRA: Stabilizing Feature Learning of Low-Rank Adaptation》 [URL](https://arxiv.org/abs/2603.05204) 与 《DiaBlo: Diagonal Blocks Are Sufficient For Finetuning》 [URL](https://arxiv.org/abs/2506.03230)**
*   **核心创新点对比**：这两篇工作都致力于改进PEFT方法。Stable-LoRA聚焦于解决LoRA因非零初始化导致的特征学习不稳定问题；DiaBlo则提出了一种完全不同的PEFT范式，即仅更新权重矩阵的对角块，以追求更稳定的收敛和理论上的表达力保证。
*   **技术细节**：
    *   **Stable-LoRA**：通过理论分析指出，在训练早期动态地收缩可训练低秩矩阵 `A` 的权重，可以消除不稳定性，同时保留非零初始化的好处。这是一种轻量的优化策略。
    *   **DiaBlo**：直接放弃低秩分解，选择更新原始权重矩阵中连续的对角块区域。该方法无需低秩乘积，避免了复杂的初始化或优化器调整，理论上在温和条件下比LoRA表达力更强，且能收敛到全量微调的一个驻点。
*   **效果验证**：两者在多个推理、代码生成等任务上均表现出稳定且优于或媲美标准LoRA的性能。Stable-LoRA几乎没有额外开销；DiaBlo在内存和速度上与LoRA相当。
*   **适用场景**：均为追求更高稳定性和可靠性的LoRA替代方案。Stable-LoRA是对现有LoRA用户的“即插即用”式增强；DiaBlo则为寻求更简洁、理论更扎实的PEFT方法提供了新选择。

### 实践启示

这些研究为大模型应用开发提供了清晰的路径：**在追求高效适配的同时，必须将“稳定性”和“知识保留”作为核心设计原则**。对于需要频繁进行知识更新的应用（如客服知识库、事实核查系统），应优先关注**SPHERE**这类模型编辑方法。对于常见的领域适配或任务微调，在采用LoRA等PEFT技术时，可考虑集成**Stable-LoRA**的稳定化策略，或尝试**DiaBlo**这类新架构。更重要的是，从**RFT**的研究中获得启示：在构造SFT数据时，应有意识地筛选或设计那些与基座模型原始分布更为协调的样本，这可能是低成本提升微调效果的关键。

**具体建议**：1) 实施模型编辑前，评估编辑的序列性和对通用能力的影响，优先采用带正则化的编辑方法；2) 微调时，除了算法，应投入资源分析训练数据与基座模型的分布关系；3) 在新项目中选择PEFT方法时，可将Stable-LoRA或DiaBlo纳入对比测试。

**关键注意事项**：理论创新方法（如基于流形的模型合并）虽优雅，但实现复杂度较高，需权衡收益与工程成本。此外，“窄域微调”会在激活中留下强烈痕迹，若涉及敏感领域微调，需评估其可解释性带来的潜在风险。

---

## 📄 论文列表（12 篇）

### [Energy-Regularized Sequential Model Editing on Hyperspheres](https://arxiv.org/abs/2510.01172)

**作者**: Liu, Gu, Yao, Wang, Peng  
**链接**: [arXiv](https://arxiv.org/abs/2510.01172) · [PDF](https://arxiv.org/pdf/2510.01172)  \| [📖 全文分析](paper_2510.01172.md)  
**评分**: 8.71  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.5）

> 本文提出了一种名为SPHERE（Sparse Projection for Hyperspherical Energy-Regularized Editing）的模型编辑正则化策略，旨在解决大语言模型（LLM）在序列化知识编辑过程中出现的性能退化问题。该方法创新性地将超球面能量（HE）作为衡量神经元权重分布均匀性的指标，并证明其稳定性与知识保留能力直接相关。基于此理论洞察，SPHERE通过将新知识投影到与预训练权重主方向互补的稀疏空间，有效稳定了权重分布，从而在LLaMA3和Qwen2.5模型上实现了编辑能力（平均提升16.41%）与通用性能保留的最佳平衡。实验设计全面，理论分析扎实，为可靠的大规模知识编辑提供了新思路。


### [Spectrum Tuning: Post-Training for Distributional Coverage and In-Context Steerability](https://arxiv.org/abs/2510.06084)

**作者**: Sorensen, Newman, Moore, Park, Fisher 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.06084) · [PDF](https://arxiv.org/pdf/2510.06084)  \| [📖 全文分析](paper_2510.06084.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由华盛顿大学、艾伦人工智能研究所、加州大学圣地亚哥分校等知名机构的研究团队合作完成，提出了一种名为Spectrum Tuning的后训练方法，旨在解决当前语言模型后训练在需要覆盖多样化输出分布的任务（如创意写作、合成数据生成）上存在的局限性。论文系统性地定义了分布建模的三个理想特性，并构建了大规模评估套件Spectrum Suite，实验表明该方法能有效提升模型的上下文可操控性和分布覆盖能力。


### [Why Reinforcement Fine-Tuning Enables MLLMs Preserve Prior Knowledge Better: A Data Perspective](https://arxiv.org/abs/2506.23508)

**作者**: Zhang, Dong, Zhang, Zhao, Zhou 等 15 人  
**链接**: [arXiv](https://arxiv.org/abs/2506.23508) · [PDF](https://arxiv.org/pdf/2506.23508)  \| [📖 全文分析](paper_2506.23508.md)  
**评分**: 8.57  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文系统研究了监督微调（SFT）与强化微调（RFT）对多模态大语言模型（MLLMs）在适应新任务时保留先验知识能力的影响。研究通过引入拼图任务这一新颖评估方式，并结合学习动态分析，揭示了两种微调方法在知识保留与任务学习之间的权衡机制。研究发现RFT通过强化与基础模型概率分布自然对齐的正确样本，对先验知识干扰更小，从而能更好地保留知识。这一发现为稳定持续的模型后训练提供了重要见解。


### [Stable-LoRA: Stabilizing Feature Learning of Low-Rank Adaptation](https://arxiv.org/abs/2603.05204)

**作者**: Wu, Gao, Li, Wu  
**链接**: [arXiv](https://arxiv.org/abs/2603.05204) · [PDF](https://arxiv.org/pdf/2603.05204)  \| [📖 全文分析](paper_2603.05204.md)  
**评分**: 8.57  （novelty: 8.5 | method: 9.0 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Stable-LoRA的优化策略，旨在解决LoRA（低秩适应）方法在微调大语言模型时存在的特征学习稳定性问题。论文首先从理论上分析了LoRA在特定条件下可实现自稳定，但指出其非零初始化会破坏这种稳定性，进而提出通过动态收缩矩阵A的权重来增强稳定性的方法。该方法在多个模型和任务上进行了实验验证，表现优于基线方法，且不增加内存开销，计算开销可忽略。代码已开源。作者团队未明确标注来自知名机构，故省略背景介绍。


### [On the Generalization of SFT: A Reinforcement Learning Perspective with Reward Rectification](https://arxiv.org/abs/2508.05629)

**作者**: Wu, Zhou, Ziheng, Peng, Ye 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2508.05629) · [PDF](https://arxiv.org/pdf/2508.05629)  \| [📖 全文分析](paper_2508.05629.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为动态微调（Dynamic Fine-Tuning, DFT）的改进方法，旨在解决大语言模型监督微调（SFT）相比强化学习（RL）泛化能力有限的问题。该方法通过理论分析揭示了标准SFT梯度隐含的问题奖励结构，并提出通过动态重缩放目标函数来稳定每个token的梯度更新。实验表明，该方法在数学推理、代码生成和多模态任务等多个困难基准测试和基础模型上均优于标准SFT，并在离线RL设置中取得了有竞争力的结果。论文代码已开源。


### [DiaBlo: Diagonal Blocks Are Sufficient For Finetuning](https://arxiv.org/abs/2506.03230)

**作者**: Gurses, Zhang, Deng, Dong, Li 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2506.03230) · [PDF](https://arxiv.org/pdf/2506.03230)  \| [📖 全文分析](paper_2506.03230.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为DiaBlo的新型参数高效微调方法，该方法通过仅更新选定模型权重矩阵的对角块来实现高效微调。论文通过理论分析和广泛的实验验证了方法的有效性，在常识推理、算术推理、代码生成和安全对齐等多个任务上展示了其竞争性能。作者团队来自学术界，但未明确标注知名机构，因此省略团队背景介绍。


### [Regularization Through Reasoning: Systematic Improvements in Language Model Classification via Explanation-Enhanced Fine-Tuning](https://arxiv.org/abs/2511.02044)

**作者**: Shah, Cogill, Yue, Chennupati, Khaziev  
**链接**: [arXiv](https://arxiv.org/abs/2511.02044) · [PDF](https://arxiv.org/pdf/2511.02044)  \| [📖 全文分析](paper_2511.02044.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种新颖的语言模型微调方法——解释增强微调（Explanation-Enhanced Fine-Tuning），通过在训练时为每个标签附加简短解释（即使是语义不连贯的随机标记序列），显著提升了模型在多个对话数据集上的分类性能。研究发现，这种改进主要源于额外的标记预算促进了更丰富的中间计算，起到了正则化作用，而非解释本身的语义内容。该方法在6个不同的对话数据集上进行了验证，实验设计严谨，结果具有启发性。


### [Beyond Prefixes: Graph-as-Memory Cross-Attention for Knowledge Graph Completion with Large Language Models](https://arxiv.org/abs/2510.08966)

**作者**: Liu, Lin, Li, Li, Wu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.08966) · [PDF](https://arxiv.org/pdf/2510.08966)  \| [📖 全文分析](paper_2510.08966.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Graph-as-Memory Tuning (GMT)的新范式，用于将知识图谱与大型语言模型深度融合以完成知识图谱补全任务。该方法超越了传统的基于前缀拼接的浅层交互方式，通过将局部图结构编码为显式的图记忆令牌，并利用跨注意力机制将其深度注入到LLM的多个Transformer层中，实现了在生成过程中对细粒度图证据的动态检索。实验表明，GMT显著优于现有基线方法。


### [Narrow Finetuning Leaves Clearly Readable Traces in Activation Differences](https://arxiv.org/abs/2510.13900)

**作者**: Minder, Dumas, Slocum, Casademunt, Holmes 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.13900) · [PDF](https://arxiv.org/pdf/2510.13900)  \| [📖 全文分析](paper_2510.13900.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由来自Anthropic、Redwood Research等知名AI安全研究机构的团队合作完成，提出了一种分析窄域微调在大型语言模型激活中留下可读痕迹的方法。研究发现，通过简单的模型差异分析技术，可以揭示微调领域的关键信息，并开发了基于LLM的解释性代理来理解微调领域。该方法在多种架构和规模的模型上进行了验证，对AI安全和可解释性研究具有重要启示。


### [BA-LoRA: Bias-Alleviating Low-Rank Adaptation to Mitigate Catastrophic Inheritance in Large Language Models](https://arxiv.org/abs/2408.04556)

**作者**: Chang, Chang, Wu  
**链接**: [arXiv](https://arxiv.org/abs/2408.04556) · [PDF](https://arxiv.org/pdf/2408.04556)  \| [📖 全文分析](paper_2408.04556.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为BA-LoRA的新方法，旨在解决大型语言模型参数高效微调中的关键问题——灾难性继承。该方法通过理论分解和针对性正则化，有效缓解了预训练阶段遗留的偏见、噪声和数据不平衡问题。实验在多个自然语言理解和生成任务上验证了其有效性，不仅性能优于现有LoRA变体，还在鲁棒性和偏见缓解方面表现出色。


### [Functionality-Oriented LLM Merging on the Fisher--Rao Manifold](https://arxiv.org/abs/2603.04972)

**作者**: Wang, Ye, Yin  
**链接**: [arXiv](https://arxiv.org/abs/2603.04972) · [PDF](https://arxiv.org/pdf/2603.04972)  \| [📖 全文分析](paper_2603.04972.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种基于Fisher-Rao流形的LLM权重空间合并新方法，将模型合并问题形式化为计算加权Karcher均值，有效解决了现有欧几里得方法在合并异构模型时出现的表示崩溃问题。论文通过推导实用的定点算法，实现了多专家模型的稳定合并，在多个基准测试和崩溃诊断中表现优于现有基线方法。


### [CoMoL: Efficient Mixture of LoRA Experts via Dynamic Core Space Merging](https://arxiv.org/abs/2603.00573)

**作者**: Cao, Fan, Wang, Lin, Zhao 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.00573) · [PDF](https://arxiv.org/pdf/2603.00573)  \| [📖 全文分析](paper_2603.00573.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为CoMoL（Core Space Mixture of LoRA）的新型MoE-LoRA框架，旨在解决现有参数高效微调方法中专家数量膨胀、参数效率低和适应粒度粗的问题。通过引入核心空间专家和核心空间路由两个关键组件，并结合软合并策略，该方法在保持模型适应性的同时显著提升了参数效率。实验表明，CoMoL在多个任务上均优于现有方法。

