# Pretraining · 2026年04月03日

**论文数**: 6 篇

---

## 📊 趋势分析

### 研究全貌

本批次论文的研究方向主要集中在**模型架构与训练优化**、**模型融合与适应**以及**训练稳定性**等几个方面。在模型架构与训练优化方面，研究旨在通过改进注意力机制或梯度更新策略来提升训练效率与模型性能。模型融合与适应则关注如何将不同来源或模态的模型能力有效整合，以构建更强大的统一模型。训练稳定性研究致力于解决预训练后期常见的数值不稳定问题。当前的热点问题是如何高效、稳定地利用和扩展现有大规模预训练模型的能力，而非总是从头训练。整体研究趋势呈现出从单一模态或同构模型向多模态、异构模型协同进化的特点，同时更加注重训练过程的鲁棒性和计算效率。

### 重点方法深度解析

从这批论文中，以下几个工作因其创新性和实用性尤为突出：

**《BidirLM: From Text to Omnimodal Bidirectional Encoders by Adapting and Composing Causal LLMs》** [URL](https://arxiv.org/abs/2604.02045)
该论文的核心创新点在于提出了一套系统性的方法，将因果生成式语言模型（如Gemma3、Qwen3）高效地转化为强大的双向编码器，并赋予其多模态理解能力。它解决了现有方法在适应过程中存在的灾难性遗忘、训练目标不统一以及难以整合专业模型等问题。
**技术细节**上，该方法首先通过消融实验确定了在适应训练前加入一个“先验掩码阶段”至关重要。为了在缺乏原始预训练数据的情况下进行规模化适应，作者引入了双重策略：一是通过线性权重合并来保留原始模型的知识；二是使用一个轻量级的多领域数据混合进行微调，以缓解灾难性遗忘。最后，通过将转化后的编码器与专门处理视觉、音频等模态的因果模型进行合并，实现了多模态能力的无缝迁移。
**效果验证**表明，该方法生成的BidirLM编码器家族在文本、视觉和音频的表示学习基准测试中均超越了现有方法。**适用场景**广泛，尤其适合需要利用现有强大LLM构建高效、多模态理解下游任务（如检索、分类）的场景。

**《GradPower: Powering Gradients for Faster Language Model Pre-Training》** [URL](https://arxiv.org/abs/2505.24275)
该工作的核心创新点是提出了一种极其轻量级的梯度变换技术，旨在加速语言模型预训练并提升最终性能。它通过改变梯度的分布形态来优化更新过程，而非修改优化器内部逻辑。
**技术细节**非常简洁：对于梯度向量g，GradPower首先对其每个元素进行符号-幂次变换：sign(g_i) * |g_i|^p（p>0为固定参数），然后将变换后的梯度输入给基础优化器（如Adam）。这种方法仅需单行代码修改，且无需调整优化器的超参数。
**效果验证**显示，当应用于Adam（称为AdamPower）时，该方法在从6600万到20亿参数的不同架构（包括混合专家模型）、不同数据集和学习率调度下，都能持续获得更低的最终损失。在训练采用warmup-stable-decay调度的MoE模型时，收益最为显著。**适用场景**是所有基于梯度下降的大规模模型预训练，因其部署成本极低，具有很高的普适性。

**《Can Heterogeneous Language Models Be Fused?》** [URL](https://arxiv.org/abs/2604.01674)
这篇论文直面当前模型融合技术的一个关键局限：只能有效处理同构模型。其核心创新是提出了HeteroFusion方法，首次系统性地解决了在不同家族（如Llama, Qwen, Mistral）的异构语言模型之间进行知识融合的难题。
**技术细节**包含两个核心组件：1）**基于拓扑的对齐**：不再尝试对齐原始的、不兼容的张量坐标，而是通过匹配功能模块（如注意力层、FFN层）的结构来实现跨架构的知识迁移。2）**冲突感知去噪**：在融合过程中抑制来自不同源模型的不兼容或噪声信号。这确保了融合过程的稳定性和有效性。
**效果验证**表明，HeteroFusion在异构迁移、多源融合、噪声源鲁棒性等多个具有挑战性的场景下，均优于现有的模型合并、融合和集成方法。**适用场景**是希望整合来自不同开源社区、不同架构的多个专家模型优势，以创建一个更全面、更强大的统一模型。

### 实践启示

这些研究为大模型应用开发提供了宝贵的借鉴。**对于希望快速构建高性能编码器的团队**，BidirLM提供了一条清晰的路径，可以直接利用成熟的生成式LLM，通过相对高效的适应过程获得强大的双向表示能力，尤其适合多模态理解任务。**对于正在进行大规模预训练或微调的研究机构与企业**，GradPower是一种“无痛”且高效的优化手段，几乎零成本地提升训练稳定性和模型质量，应优先尝试集成到现有训练流程中。**对于需要集成多个来源模型能力的场景**，HeteroFusion为解决异构模型融合这一现实难题提供了可行的技术方案。

在落地时需注意：BidirLM的适应过程需要精心设计数据混合策略以避免遗忘；GradPower中的幂次参数p可能需要在小规模实验中进行初步调优；而HeteroFusion的成功依赖于对源模型架构的深入分析和功能模块的准确匹配。整体而言，该批次论文强调了对现有模型资产进行高效复用和组合创新的巨大潜力。

---

## 📄 论文列表（6 篇）

### [BidirLM: From Text to Omnimodal Bidirectional Encoders by Adapting and Composing Causal LLMs](https://arxiv.org/abs/2604.02045)

**作者**: Boizard, Deschamps-Berger, Gisserot-Boukhlef, Hudelot, Colombo  
**链接**: [arXiv](https://arxiv.org/abs/2604.02045) · [PDF](https://arxiv.org/pdf/2604.02045)  \| [📖 全文分析](paper_2604.02045.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种将因果生成式语言模型转换为双向编码器的新方法BidirLM。该方法通过系统性的消融实验确定了成功适应的关键因素，引入了结合线性权重合并与轻量级多领域数据混合的双重策略来缓解灾难性遗忘，并通过与专业因果模型合并来增强编码器的多模态能力。论文在文本、视觉和音频表示基准测试中取得了优于现有方法的表现，并提供了开源实现。


### [Output Embedding Centering for Stable LLM Pretraining](https://arxiv.org/abs/2601.02031)

**作者**: Stollenwerk, Lokrantz, Hertzberg  
**链接**: [arXiv](https://arxiv.org/abs/2601.02031) · [PDF](https://arxiv.org/pdf/2601.02031)  \| [📖 全文分析](paper_2601.02031.md)  
**评分**: 8.45  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种名为输出嵌入中心化（Output Embedding Centering, OEC）的新方法，用于解决大语言模型预训练后期常见的输出logit发散不稳定问题。论文从输出嵌入的几何特性角度分析了问题的根源（各向异性嵌入），并提出了两种具体实现方式（μ-centering和μ-loss）。实验表明，该方法在训练稳定性方面优于广泛使用的z-loss，且对超参数调优的敏感性更低。作者团队信息未在摘要中明确提及，故省略背景介绍。


### [Can Heterogeneous Language Models Be Fused?](https://arxiv.org/abs/2604.01674)

**作者**: Chen, Zhou, Chen, Wu, Li 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.01674) · [PDF](https://arxiv.org/pdf/2604.01674)  \| [📖 全文分析](paper_2604.01674.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.0）

> 本文提出了一种名为HeteroFusion的异构语言模型融合方法，解决了当前模型融合技术主要局限于同构模型的局限性。该方法通过拓扑对齐和冲突感知去噪两个核心组件，实现了在不同架构（如Llama、Qwen、Mistral）的语言模型之间进行有效的知识融合。论文在异构迁移、多源融合、噪声源鲁棒性和跨家族泛化等多个场景下进行了验证，均取得了优于现有融合、合并和集成基线方法的效果。


### [The Latent Space: Foundation, Evolution, Mechanism, Ability, and Outlook](https://arxiv.org/abs/2604.02029)

**作者**: Yu, Chen, He, Fu, Yang 等 37 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.02029) · [PDF](https://arxiv.org/pdf/2604.02029)  \| [📖 全文分析](paper_2604.02029.md)  
**评分**: 8.38  （novelty: 7.0 | method: 9.5 | evidence: 6.5 | clarity: 8.5）

> 本文是一篇关于语言模型潜在空间的综合性综述论文，系统性地梳理了该领域的基础、演变、机制、能力和未来展望。论文作者团队来自多个研究机构，但未明确标注具体机构信息，因此省略团队背景介绍。该论文对潜在空间这一新兴计算范式进行了全面而深入的整理，为理解下一代智能系统的计算基础提供了重要参考。


### [GradPower: Powering Gradients for Faster Language Model Pre-Training](https://arxiv.org/abs/2505.24275)

**作者**: Wang, Wang, Zhang, Wang, Pei 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2505.24275) · [PDF](https://arxiv.org/pdf/2505.24275)  \| [📖 全文分析](paper_2505.24275.md)  
**评分**: 8.33  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为GradPower的轻量级梯度变换技术，用于加速语言模型预训练。该方法通过对梯度向量进行逐元素的符号-幂次变换（sign-power transformation），然后输入基础优化器，仅需单行代码修改且无需调整优化器内部逻辑或超参数。实验表明，当应用于Adam（称为AdamPower）时，该方法在多种架构（LLaMA, Qwen2MoE）、参数规模（66M至2B）、数据集（C4, OpenWebText）和学习率调度策略下，均能持续获得更低的最终损失。在训练采用warmup-stable-decay调度的现代混合专家模型时，收益最为显著。该方法还能无缝集成其他先进优化器（如Muon）以进一步提升性能。论文还提供了理论分析，揭示了GradPower的作用机制并强调了梯度噪声的影响。


### [Coupled Query-Key Dynamics for Attention](https://arxiv.org/abs/2604.01683)

**作者**: Gahtan, Bronstein  
**链接**: [arXiv](https://arxiv.org/abs/2604.01683) · [PDF](https://arxiv.org/pdf/2604.01683)  \| [📖 全文分析](paper_2604.01683.md)  
**评分**: 7.79  （novelty: 9.0 | method: 8.0 | evidence: 9.0 | clarity: 8.5）

> 本文提出了一种名为'耦合查询-键动态'（coupled QK dynamics）的新型注意力机制，通过在计算注意力分数前让查询和键向量通过共享的学习动态共同演化，显著提升了语言建模的困惑度和训练稳定性。在WikiText-103等数据集上的实验表明，该方法能以极少的额外参数量（仅0.11%）实现显著的性能提升（-6.6%--6.9%困惑度），并揭示了其作为一种样本效率机制的本质。论文还深入分析了该方法适用的条件（在领域连贯文本上有效，在异构文本上可能无效），为实践提供了指导。

