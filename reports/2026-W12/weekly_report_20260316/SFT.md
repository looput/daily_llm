# SFT · 2026-03-16 ~ 2026-03-20

**论文数**: 7 篇

---

## 📊 趋势分析

### 研究全貌

本批次SFT领域的研究论文主要聚焦于**参数高效微调（PEFT）的算法创新**、**低资源场景下的指令微调**以及**后训练范式的理论探索与比较**。参数高效微调是当前最活跃的研究热点，多篇论文致力于突破现有方法（如LoRA）的瓶颈，通过引入非线性机制、上下文感知路由或新的优化目标来提升模型在复杂任务上的性能。同时，如何为低资源语言构建高质量的指令跟随能力也是一个重要的应用方向。整体研究趋势呈现出从简单的线性适配向更精细、动态且理论驱动的微调方法演进，并更加关注方法的可解释性、泛化能力与实际部署效率。

### 重点方法深度解析

在这些论文中，以下几项工作因其创新性和实用性尤为突出：

**《CeRA: Breaking the Linear Ceiling of Low-Rank Adaptation via Manifold Expansion》** [URL](https://arxiv.org/abs/2602.22911)
*   **核心创新点**：该工作直指LoRA方法在复杂推理任务中存在的“线性天花板”问题，即单纯增加适配器秩（rank）带来的性能收益会迅速递减。为此，作者提出了CeRA，一种通过“流形扩展”来增强模型容量的权重级并行适配器。
*   **技术细节**：CeRA的核心是在标准的低秩适配矩阵中，引入了**SiLU门控（Sigmoid Linear Unit）和结构化Dropout**。SiLU门控引入了非线性激活，而结构化Dropout则作用于适配器的权重矩阵，二者协同作用，有效地扩展了参数更新的表示空间（即“流形”），打破了纯线性更新的限制。
*   **效果验证**：在SlimOrca和MATH等复杂推理基准上，CeRA展现了卓越的参数效率。例如，在MATH数据集上，**仅使用秩为64的CeRA（Pass@1 16.36%）就超越了秩为512的传统LoRA（Pass@1 15.72%）**，以1/8的参数预算实现了更优的推理准确率。奇异值分解（SVD）分析证实，CeRA能够激活传统线性方法中“沉睡”的奇异值尾部，有效防止了秩塌缩。
*   **适用场景**：该方法特别适用于对数学推理、代码生成等复杂逻辑任务进行微调的场景，能够在严格控制新增参数数量的前提下，显著提升模型性能。

**《NeuroLoRA: Context-Aware Neuromodulation for Parameter-Efficient Multi-Task Adaptation》** [URL](https://arxiv.org/abs/2603.12378)
*   **核心创新点**：针对多任务适配中参数干扰和静态路由的局限性，NeuroLoRA受生物神经调节机制启发，提出了一个**上下文感知的、动态的专家混合（MoE）LoRA框架**。
*   **技术细节**：该框架保留了FlyLoRA中冻结的随机投影以维持效率，但关键创新在于引入了一个轻量级的、**可学习的“神经调节门”**。这个门网络根据当前输入上下文，动态地重新缩放投影空间，然后再进行专家选择。此外，作者还提出了**对比正交性损失**，以显式地增强不同专家子空间之间的分离度，从而更好地解耦任务并提升持续学习能力。
*   **效果验证**：在MMLU、GSM8K和ScienceQA等基准的广泛实验中，NeuroLoRA在单任务适配、多任务模型合并以及序列持续学习场景下，均一致性地超越了FlyLoRA及其他基线模型，同时保持了可比的参数效率。
*   **适用场景**：NeuroLoRA非常适合需要让一个基础模型同时或顺序地适应多个不同任务的场景，例如构建通用助手或需要不断学习新知识的持续学习系统。

**《Matching Features, Not Tokens: Energy-Based Fine-Tuning of Language Models》** [URL](https://arxiv.org/abs/2603.12248)
*   **核心创新点**：这项工作挑战了传统的基于交叉熵（CE）的监督微调范式，认为其优化的是教师强制下的下一个令牌预测，而非模型自回归展开时的序列级行为。为此，作者提出了一种**基于能量模型的微调方法**，通过匹配序列级别的特征分布来提供密集的语义反馈。
*   **技术细节**：该方法的核心是**能量基微调**。它使用跨步块并行采样从嵌套前缀并发生成多个模型展开序列，在这些序列上批量提取特征（如CLS嵌入或平均池化），然后利用得到的嵌入特征执行基于策略梯度的在线更新。从理论上看，EBFT连接了KL正则化的特征匹配和能量基建模。
*   **效果验证**：在问答编程、非结构化编程和翻译任务上，EBFT在最终下游准确率上匹配或超越了RLVR，并优于标准的SFT。同时，EBFT还能取得比SFT和RLVR更低的验证交叉熵，表明其生成的分布更接近真实数据分布。
*   **适用场景**：EBFT适用于那些最终评估指标是序列级质量（如代码功能正确性、翻译流畅度与忠实度），而非逐词准确率的任务，为超越传统SFT提供了新的优化思路。

### 实践启示

对于大模型应用开发，本批次研究提供了清晰的路径选择。**如果目标是针对单一复杂任务（如数学推理）进行高效微调，CeRA是极具吸引力的选择**，它能以极小的参数量代价突破性能瓶颈。**若需构建一个能处理多领域请求或需持续学习的通用模型，则应优先考虑NeuroLoRA这类动态路由架构**，它能有效管理任务冲突并提升整体适应性。对于追求更优序列级生成质量且计算资源相对充足的场景，可以探索EBFT作为对传统SFT的补充或替代。

在落地时需注意：CeRA和NeuroLoRA都属于PEFT方法，易于集成到现有训练管线，但需仔细调整门控或Dropout相关的超参数。EBFT涉及模型展开采样，训练计算成本高于SFT，需权衡收益与开销。总体而言，当前SFT研究已超越简单的“微调”概念，进入精细化、智能化适配的新阶段，开发者应依据具体任务需求，选择最具针对性的高效方法。

---

## 📄 论文列表（7 篇）

### [Instructing Large Language Models for Low-Resource Languages: A Systematic Study for Basque](https://arxiv.org/abs/2506.07597)

**作者**: Sainz, Perez, Etxaniz, de Landa, Aldabe 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2506.07597) · [PDF](https://arxiv.org/pdf/2506.07597)  \| [📖 全文分析](paper_2506.07597.md)  
**评分**: 8.86  （novelty: 8.0 | method: 9.0 | evidence: 9.5 | clarity: 8.0）

> 本文由巴斯克大学、巴斯克语言学院、巴斯克地区大学等机构的研究团队合作完成，针对低资源语言（巴斯克语）的指令微调问题进行了系统性研究。作者在仅拥有目标语言语料库、开源多语言基础模型和指令模型、以及从指令模型生成的合成指令的现实场景下，探索了替代传统指令适应流程的方法。通过全面的实验设计和1680名参与者的人类偏好评估，证明了目标语言语料库的重要性、合成指令的有效性，以及使用指令微调模型作为骨干的优越性。研究最终发布了代码、模型、指令数据集和人类偏好数据，支持完全复现。


### [A Unified Generalization Framework for Model Merging: Trade-offs, Non-Linearity, and Scaling Laws](https://arxiv.org/abs/2601.21690)

**作者**: Li, Tang, Zhang, Wang, Yin 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2601.21690) · [PDF](https://arxiv.org/pdf/2601.21690)  \| [📖 全文分析](paper_2601.21690.md)  
**评分**: 8.57  （novelty: 9.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种用于模型合并的统一泛化理论框架，首次将L2稳定性理论引入异构环境，系统分析了合并模型的优化与泛化误差，并推导出可量化的缩放定律。论文在ResNet和ViT架构上进行了大规模实验验证，涉及20个视觉分类任务和数千个微调模型，理论预测与实证结果高度一致。作者团队来自学术界，但未明确标注具体机构。


### [Supervised Fine-Tuning versus Reinforcement Learning: A Study of Post-Training Methods for Large Language Models](https://arxiv.org/abs/2603.13985)

**作者**: Jiang, Zhang, Yao, Cai, Wang 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.13985) · [PDF](https://arxiv.org/pdf/2603.13985)  \| [📖 全文分析](paper_2603.13985.md)  
**评分**: 8.57  （novelty: 6.5 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文是一篇关于大语言模型后训练方法的综述性研究，系统性地比较了监督微调（SFT）与强化学习（RL）两种主流技术。论文通过整合理论见解、实践方法和实证证据，在一个统一的框架内建立了对SFT和RL的连贯理解，并概述了未来可扩展、高效和可泛化的LLM后训练研究方向。作者团队未明确标注所属知名机构，故省略背景介绍。


### [Machines acquire scientific taste from institutional traces](https://arxiv.org/abs/2603.16659)

**作者**: Gong, Li, Zhou  
**链接**: [arXiv](https://arxiv.org/abs/2603.16659) · [PDF](https://arxiv.org/pdf/2603.16659)  \| [📖 全文分析](paper_2603.16659.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种创新的方法，通过微调语言模型在期刊发表决策数据上，来提取和自动化科学“品味”——即判断哪些未经验证的科学想法值得追求的评估能力。研究发现，经过微调的模型在评估研究提案质量方面超越了前沿大语言模型和人类专家小组，准确率最高可达59%（经济学领域达70%），并展现出校准的置信度和良好的泛化能力。论文的核心创新在于将难以形式化的“科学品味”视为可从机构历史记录（发表决策）中提取的模式，为解决科学产出爆炸式增长下的质量筛选问题提供了一个可扩展的机制。作者团队未明确标注来自上述知名机构，故省略背景介绍。


### [CeRA: Breaking the Linear Ceiling of Low-Rank Adaptation via Manifold Expansion](https://arxiv.org/abs/2602.22911)

**作者**: Chen  
**链接**: [arXiv](https://arxiv.org/abs/2602.22911) · [PDF](https://arxiv.org/pdf/2602.22911)  \| [📖 全文分析](paper_2602.22911.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为CeRA（Capacity-enhanced Rank Adaptation）的新型参数高效微调方法，旨在解决LoRA在复杂推理任务中面临的“线性天花板”问题。通过引入SiLU门控和结构化Dropout实现流形扩展，CeRA在SlimOrca和MATH等复杂推理数据集上展现出显著的参数效率和性能优势，仅用1/8的参数预算即可超越传统LoRA方法。论文实验设计严谨，机制分析深入，具有较高的创新价值。


### [NeuroLoRA: Context-Aware Neuromodulation for Parameter-Efficient Multi-Task Adaptation](https://arxiv.org/abs/2603.12378)

**作者**: Yang, Zhang, Li, Xu, Shen 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12378) · [PDF](https://arxiv.org/pdf/2603.12378)  \| [📖 全文分析](paper_2603.12378.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为NeuroLoRA的新型参数高效微调框架，该框架受生物神经调节机制启发，通过上下文感知的门控机制动态调节随机投影空间，在多任务适应、模型合并和持续学习场景中均表现出优越性能。论文在MMLU、GSM8K和ScienceQA等多个基准数据集上进行了充分实验验证，方法创新性强且具有较好的通用性。


### [Matching Features, Not Tokens: Energy-Based Fine-Tuning of Language Models](https://arxiv.org/abs/2603.12248)

**作者**: Jelassi, Kwun, Zhao, Li, Fusi 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12248) · [PDF](https://arxiv.org/pdf/2603.12248)  \| [📖 全文分析](paper_2603.12248.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由来自斯坦福大学、普林斯顿大学、微软研究院等知名机构的学者团队（作者包括来自Stanford的Jelassi、Kakade，来自Microsoft Research的Fusi、Du等）提出了一种基于能量模型的微调方法（EBFT），通过特征匹配而非传统的交叉熵训练来优化语言模型的序列级行为。该方法在问答编程、非结构化编程和翻译任务上均表现出色，超越了监督微调（SFT）并取得了更低的验证交叉熵。

