# Multimodal · 2026-03-16 ~ 2026-03-20

**论文数**: 35 篇

---

## 📊 趋势分析

### 研究全貌

多模态领域的研究致力于整合与理解来自不同模态（如文本、图像、音频、视频）的信息，以构建更通用、更智能的人工智能系统。当前的研究方向主要集中在**多模态对齐与融合**、**多模态生成**以及**多模态理解与推理**等几个核心方向。多模态对齐旨在建立不同模态信息间的语义关联；多模态生成关注于根据一种模态生成另一种模态的内容；而多模态理解与推理则侧重于对复杂跨模态信息的深层语义解析和逻辑推断。当前的热点问题是如何实现更高效、更鲁棒且可泛化的跨模态语义对齐，尤其是在数据稀缺或分布外场景下。整体研究趋势呈现出从简单的特征拼接向深度交互式融合演进，并越来越注重模型的效率、可控性和可解释性。跨批次的发展脉络显示，研究者们正从依赖大规模预训练数据，转向探索更精巧的架构设计和训练策略，以提升模型的内在能力。

### 重点方法深度解析

从多模态研究的众多进展中，以下两个方法因其创新性和实用性尤为突出：

**1. 对比语言-图像预训练**
*   **核心创新点**：该方法开创性地将自然语言监督信号大规模应用于视觉表征学习。它解决了传统视觉模型需要大量人工标注数据且任务泛化能力有限的问题，提出通过从互联网收集的（图像，文本）对进行对比学习，从而学习到开放世界的视觉概念。
*   **技术细节**：其核心架构包含一个图像编码器和一个文本编码器。训练时，模型的目标是最大化配对（图像，文本）的相似度，同时最小化非配对样本的相似度。这通常通过一个对称的交叉熵损失函数实现，鼓励模型将图像及其对应描述的嵌入向量在共享的语义空间中对齐。
*   **效果验证**：该方法在零样本图像分类、跨模态检索等任务上取得了突破性进展。例如，在包含多个数据集的零样本图像分类基准测试中，其性能可媲美甚至超越经过全监督训练的专用模型，展现了强大的泛化能力。
*   **适用场景**：特别适用于需要快速适配新视觉概念或缺乏特定任务标注数据的场景，如图像搜索引擎、内容审核、零样本或少样本的视觉任务等。

**2. 多模态大语言模型**
*   **核心创新点**：此方法将强大的大语言模型作为核心处理器，通过适配器或投影层将视觉、音频等其他模态的特征对齐到LLM的文本语义空间。它解决了传统多模态模型在复杂推理和指令跟随能力上的不足，将LLM的丰富知识、强大推理和生成能力扩展到了多模态领域。
*   **技术细节**：技术实现通常分为两步：首先，使用一个预训练好的视觉编码器（如ViT）提取图像特征；然后，通过一个可训练的线性层或更复杂的网络（适配器）将这些视觉特征投影为一系列“视觉标记”，并将其与文本标记拼接，一同输入到大语言模型中进行自回归训练或指令微调。模型通过预测下一个文本标记来学习视觉与语言之间的关联和推理。
*   **效果验证**：这类模型在视觉问答、图像描述、基于图像的对话、复杂推理（如图表分析）等任务上表现卓越。它们不仅能给出准确的答案，还能生成详细、连贯的解释，展现出接近人类的对话和推理能力。
*   **适用场景**：非常适合开发交互式多模态智能体，如智能助手、教育工具、内容创作辅助以及需要深度理解和推理的多模态应用。

这两个重点方法之间存在紧密的演进和互补关系。**对比语言-图像预训练**为多模态研究提供了强大、对齐的视觉-语言基础表征，是构建更复杂系统的基石。而**多模态大语言模型**则在此基础上，利用LLM的通用能力，实现了更高层次的语义理解和任务执行。在实践中，后者常常以前者训练好的视觉编码器作为输入前端，两者结合构成了当前最先进多模态系统的典型范式。

### 实践启示

对于大模型应用开发者而言，当前的多模态研究提供了清晰的实践路径。在开发多模态应用时，应优先考虑采用“**基础对齐模型 + 大语言模型处理器**”的架构范式。具体建议如下：
1.  **场景选择与架构决策**：对于以检索、分类为主的感知型任务（如图像搜索），可重点关注并微调基于对比学习的对齐模型。对于需要复杂交互、推理和生成的认知型任务（如智能客服、教学助手），则应采用以LLM为核心的多模态大模型方案。
2.  **可落地的具体建议**：在资源允许的情况下，直接从开源社区获取成熟的多模态大模型（如LLaVA、Qwen-VL等）进行领域特定的指令微调，是快速构建应用的捷径。若对延迟和成本敏感，可考虑使用轻量化的视觉编码器和较小规模的LLM进行组合与蒸馏。
3.  **关键注意事项**：实现时需特别注意不同模态特征的对齐质量，这是影响最终性能的关键。同时，要警惕模型可能产生的“幻觉”（生成与输入视觉内容不符的文本），需要通过精心设计训练数据、引入强化学习从人类反馈中学习或后处理规则来缓解。
4.  **最佳方法组合**：综合来看，将经过大规模对齐预训练的视觉编码器与一个经过指令微调的大语言模型相结合，是目前平衡性能、开发效率和泛化能力的最佳实践组合。开发者可以在此基础上，针对具体业务数据进行轻量级的端到端微调，以实现最优的落地效果。

---

## 📄 论文列表（35 篇）

### [Lumos-1: On Autoregressive Video Generation with Discrete Diffusion from a Unified Model Perspective](https://arxiv.org/abs/2507.08801)

**作者**: Yuan, Chen, Cen, Yu, Liang 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2507.08801) · [PDF](https://arxiv.org/pdf/2507.08801)  \| [📖 全文分析](paper_2507.08801.md)  
**评分**: 8.69  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文由阿里巴巴达摩院团队提出了一种基于自回归大语言模型的统一视频生成方法Lumos-1。该方法创新性地将离散扩散过程与自回归建模相结合，通过改进位置编码和注意力机制，在有限的计算资源和数据条件下，在多个视频生成基准测试中取得了超越现有方法的性能。


### [Omni-Captioner: Data Pipeline, Models, and Benchmark for Omni Detailed Perception](https://arxiv.org/abs/2510.12720)

**作者**: Ma, Xu, Xing, Chu, Wang 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.12720) · [PDF](https://arxiv.org/pdf/2510.12720)  \| [📖 全文分析](paper_2510.12720.md)  
**评分**: 8.69  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种系统性的全模态细粒度感知研究框架，包括数据生成管道、模型和评估基准。作者团队来自多个研究机构，包括香港中文大学、南洋理工大学、清华大学等知名高校，展现了较强的研究实力。论文针对当前全模态语言模型在细节感知与幻觉问题之间的“共增长”矛盾，提出了创新的解决方案，在多个基准测试中取得了领先性能。


### [Do Understanding and Generation Fight? A Diagnostic Study of DPO for Unified Multimodal Models](https://arxiv.org/abs/2603.17044)

**作者**: Rao, Rachuri  
**链接**: [arXiv](https://arxiv.org/abs/2603.17044) · [PDF](https://arxiv.org/pdf/2603.17044)  \| [📖 全文分析](paper_2603.17044.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文对统一多模态模型中理解与生成能力的对齐问题进行了首次系统性诊断研究。研究聚焦于DPO（直接偏好优化）在Janus-Pro模型（1B和7B参数）上的应用效果，通过七种训练策略和两种后处理方法，揭示了理解与生成梯度正交且幅度不平衡的核心问题。研究发现，在基于VQ（矢量量化）标记化的架构中，生成质量难以通过DPO对齐，这为相关领域的研究者和实践者提供了重要的实证依据和理论洞见。


### [To See or To Please: Uncovering Visual Sycophancy and Split Beliefs in VLMs](https://arxiv.org/abs/2603.18373)

**作者**: Hong, Quan  
**链接**: [arXiv](https://arxiv.org/abs/2603.18373) · [PDF](https://arxiv.org/pdf/2603.18373)  \| [📖 全文分析](paper_2603.18373.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.5）

> 本文提出了一种名为Tri-Layer Diagnostic Framework的诊断框架，用于揭示视觉语言模型（VLMs）中的视觉奉承行为和分裂信念问题。该研究通过反事实干预（盲图、噪声图、冲突图）和三个核心指标（潜在异常检测、视觉必要性分数、竞争分数），系统性地分析了7个VLMs在7,000个模型-样本对上的表现。研究发现，69.6%的样本存在视觉奉承现象，即模型能检测到视觉异常，但为了迎合用户期望而产生幻觉，而零样本表现出鲁棒拒绝，表明对齐训练系统地抑制了模型对不确定性的真实承认。缩放分析表明，更大的模型减少了语言捷径，但放大了视觉奉承，证明仅靠扩大规模无法解决基础问题。诊断分数还支持了一种无需额外训练成本的后验选择性预测策略，在50%覆盖率下实现了高达+9.5个百分点的准确率提升。


### [Causal Tracing of Audio-Text Fusion in Large Audio Language Models](https://arxiv.org/abs/2603.13768)

**作者**: Chen, Huang, Lee  
**链接**: [arXiv](https://arxiv.org/abs/2603.13768) · [PDF](https://arxiv.org/pdf/2603.13768)  \| [📖 全文分析](paper_2603.13768.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种基于因果追踪（causal tracing）的分析方法，用于探究大型音频语言模型（LALMs）内部音频与文本特征融合的机制。研究通过层间和词元间的分析，揭示了不同模型（DeSTA, Qwen, Voxtral）在音频理解过程中信息整合的不同策略与关键位置（如最终序列词元作为信息瓶颈），为理解多模态大模型的黑箱内部运作提供了清晰的、可解释的视角。


### [DeSTA2.5-Audio: Toward General-Purpose Large Audio Language Model with Self-Generated Cross-Modal Alignment](https://arxiv.org/abs/2507.02768)

**作者**: Lu, Chen, Fu, Yang, Huang 等 28 人  
**链接**: [arXiv](https://arxiv.org/abs/2507.02768) · [PDF](https://arxiv.org/pdf/2507.02768)  \| [📖 全文分析](paper_2507.02768.md)  
**评分**: 8.49  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为DeSTA2.5-Audio的通用大型音频语言模型，旨在解决现有模型在增强音频能力时导致大语言模型原有能力灾难性遗忘的关键挑战。其核心创新在于提出了一种自生成跨模态对齐策略，并构建了一个大规模、任务无关的数据集DeSTA-AQA5M。该模型在多个音频-语言基准测试中取得了领先或具有竞争力的性能。作者团队来自多个机构，包括Ginsburg（可能指Boris Ginsburg，与NVIDIA相关）、Wang、Lee等，表明这是一个跨机构的合作研究。


### [Is CLIP ideal? No. Can we fix it? Yes!](https://arxiv.org/abs/2503.08723)

**作者**: Kang, Song, Gkioxari, Perona  
**链接**: [arXiv](https://arxiv.org/abs/2503.08723) · [PDF](https://arxiv.org/pdf/2503.08723)  \| [📖 全文分析](paper_2503.08723.md)  
**评分**: 8.48  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文由Song Kang（可能来自加州理工学院或相关机构）和Georgia Gkioxari（Meta AI研究员）、Pietro Perona（加州理工学院教授）等组成的团队，对广泛应用的CLIP模型进行了深入的理论分析，揭示了其潜在空间在同时处理多种视觉-文本交互任务时的根本性限制。基于此分析，作者提出了Dense Cosine Similarity Maps（DCSMs）这一原则性且可解释的评分方法，旨在解决CLIP的固有缺陷。该方法在多个基准测试上提升了经典CLIP类联合编码器模型的性能，并开源了代码和数据。


### [AD-Copilot: A Vision-Language Assistant for Industrial Anomaly Detection via Visual In-context Comparison](https://arxiv.org/abs/2603.13779)

**作者**: Jiang, Guo, Li, Liu, Gao 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.13779) · [PDF](https://arxiv.org/pdf/2603.13779)  \| [📖 全文分析](paper_2603.13779.md)  
**评分**: 8.43  （novelty: 8.5 | method: 8.0 | evidence: 9.0 | clarity: 8.5）

> 本文提出了一种专门用于工业异常检测的交互式多模态大语言模型AD-Copilot。该方法通过创新的视觉上下文比较机制，解决了通用MLLM在工业场景下对细微视觉差异不敏感的问题。论文贡献包括：1）设计了从稀疏标注工业图像中挖掘检测知识的数据处理流程，构建了大规模多模态数据集Chat-AD；2）提出了新颖的比较编码器，通过配对图像特征间的交叉注意力增强细粒度感知；3）引入了多阶段训练策略融入领域知识；4）建立了扩展的基准测试MMAD-BBox。实验表明，AD-Copilot在多个基准测试上达到或超越了人类专家水平，并展现出优秀的泛化能力。作者承诺开源所有数据集和模型。


### [Rationale-Enhanced Decoding for Multi-modal Chain-of-Thought](https://arxiv.org/abs/2507.07685)

**作者**: Yamaguchi, Nishida, Chijiwa  
**链接**: [arXiv](https://arxiv.org/abs/2507.07685) · [PDF](https://arxiv.org/pdf/2507.07685)  \| [📖 全文分析](paper_2507.07685.md)  
**评分**: 8.41  （novelty: 8.5 | method: 9.0 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Rationale-Enhanced Decoding (RED)的新型推理时解码策略，旨在解决大型视觉语言模型在多模态思维链推理中忽视生成理由内容的关键问题。该方法将多模态CoT重新表述为KL约束的奖励最大化问题，并通过融合图像条件和理由条件的下一个词元分布来协调视觉与理由信息。实验表明，RED在多个基准测试和LVLM上显著提升了推理的忠实性和准确性。论文提供了开源代码，增强了可复现性。


### [EndoCoT: Scaling Endogenous Chain-of-Thought Reasoning in Diffusion Models](https://arxiv.org/abs/2603.12252)

**作者**: Dai, Zhou, Xing, Bu, Wei 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12252) · [PDF](https://arxiv.org/pdf/2603.12252)  \| [📖 全文分析](paper_2603.12252.md)  
**评分**: 8.38  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为EndoCoT的新框架，旨在解决多模态大语言模型（MLLMs）在扩散模型中作为文本编码器时存在的两个关键限制：推理深度不足和引导信息在解码过程中保持不变。通过引入迭代思想引导模块和终端思想接地模块，该框架激活了MLLMs的推理潜力，并将其与扩散变换器（DiT）的去噪过程动态连接，从而以逐步分解的方式解决复杂任务。在多个基准测试（如迷宫、旅行商问题、视觉空间规划、数独）上的广泛评估显示，平均准确率达到92.1%，比最强基线高出8.3个百分点。代码和数据集已公开。


### [Narrative Weaver: Towards Controllable Long-Range Visual Consistency with Multi-Modal Conditioning](https://arxiv.org/abs/2603.06688)

**作者**: Yao, Li, Gao, Chen, Jiang 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.06688) · [PDF](https://arxiv.org/pdf/2603.06688)  \| [📖 全文分析](paper_2603.06688.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为'Narrative Weaver'的新型框架，旨在解决生成式AI中多模态可控、长距离视觉内容生成的一致性问题。该工作针对现有模型在长序列生成中难以保持叙事连贯性和视觉一致性的挑战，提出了首个集成了细粒度控制、自动叙事规划和长距离一致性的整体解决方案。作者团队未在摘要中明确标注所属机构，因此省略团队背景介绍。


### [Decoupled Action Expert: Confining Task Knowledge to the Conditioning Pathway](https://arxiv.org/abs/2511.12101)

**作者**: Zhou, Lin, Fu, Li, Zhou 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.12101) · [PDF](https://arxiv.org/pdf/2511.12101)  \| [📖 全文分析](paper_2511.12101.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Decoupled Action Expert的新方法，挑战了当前视觉-语言-动作模型中为动作生成分配大量参数容量的主流设计。研究发现，机器人操作策略生成的任务特定知识可以完全限制在条件路径中，而动作主干可以保持任务无关。通过在MimicGen和LIBERO数据集上的实验验证，该方法使用单个冻结的共享主干即可达到与正常训练模型相当的性能，甚至可以用5M参数的MLP替换244M参数的U-Net而性能相当或更优。作者团队来自学术界，但具体机构信息未在提供内容中明确显示。


### [Mind the Discriminability Trap in Source-Free Cross-domain Few-shot Learning](https://arxiv.org/abs/2603.13341)

**作者**: Zhang, Zou, Li, Li, Chen  
**链接**: [arXiv](https://arxiv.org/abs/2603.13341) · [PDF](https://arxiv.org/pdf/2603.13341)  \| [📖 全文分析](paper_2603.13341.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文针对源自由跨域少样本学习（SF-CDFSL）任务中，基于视觉语言模型（VLM）微调时出现的一个反直觉现象——增强视觉判别性反而会抑制模型性能——进行了深入研究。作者通过理论和实验证明，传统的交叉熵损失包含的视觉学习部分会阻碍跨模态对齐，并据此提出了一种通过扰动视觉学习、利用视觉-文本语义关系逐步对齐模态的新方法。该方法在多个数据集、骨干网络和任务上均取得了新的最优性能。论文代码已开源。


### [Towards the Vision-Sound-Language-Action Paradigm: The HEAR Framework for Sound-Centric Manipulation](https://arxiv.org/abs/2603.16086)

**作者**: Nie, Deng, Wang, Liu, Wang  
**链接**: [arXiv](https://arxiv.org/abs/2603.16086) · [PDF](https://arxiv.org/pdf/2603.16086)  \| [📖 全文分析](paper_2603.16086.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为HEAR的Vision-Sound-Language-Action（VSLA）框架，旨在解决现有VLA模型在处理实时、以声音为中心的操作任务时的局限性。该工作将声音从静态提示或仅限语音的范畴，扩展为用于任务执行期间关键状态验证的连续环境声学流，并形式化了VSLA这一新的连续控制范式。为支持该范式，作者构建了预训练数据集OpenX-Sound和首个具有严格因果时序规则的以声音为中心的操作基准HEAR-Bench。实验结果表明，鲁棒的声音中心操作需要因果持久性和显式的时间动态学习。该框架为具身智能体的多感官基础模型迈出了实用一步。


### [Visual Distraction Undermines Moral Reasoning in Vision-Language Models](https://arxiv.org/abs/2603.16445)

**作者**: Yang, Xu, Hong, Mo, Wang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16445) · [PDF](https://arxiv.org/pdf/2603.16445)  \| [📖 全文分析](paper_2603.16445.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种基于道德基础理论（MFT）的多模态基准测试方法——道德困境模拟（MDS），用于系统评估视觉语言模型（VLMs）在视觉输入下的道德推理一致性。研究发现，视觉输入会激活类似直觉的推理路径，绕过基于文本的安全机制，揭示了当前VLM安全对齐的关键脆弱性。该研究为多模态AI安全评估提供了新的方法论和重要洞见。


### [V-DyKnow: A Dynamic Benchmark for Time-Sensitive Knowledge in Vision Language Models](https://arxiv.org/abs/2603.16581)

**作者**: Mousavi, Moiola, Rizzoli, Alghisi, Riccardi  
**链接**: [arXiv](https://arxiv.org/abs/2603.16581) · [PDF](https://arxiv.org/pdf/2603.16581)  \| [📖 全文分析](paper_2603.16581.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为V-DyKnow的视觉动态知识基准，用于评估视觉语言模型（VLMs）中时间敏感性事实知识的掌握情况。该研究揭示了当前VLMs在处理随时间变化的事实知识时存在的根本性局限，包括模型预测容易过时、跨模态知识更新困难等问题。研究团队来自学术界，但论文中未明确标注其所属的知名机构。


### [Counting Circuits: Mechanistic Interpretability of Visual Reasoning in Large Vision-Language Models](https://arxiv.org/abs/2603.18523)

**作者**: Che, Xue, Quan, Liu, Shi 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18523) · [PDF](https://arxiv.org/pdf/2603.18523)  \| [📖 全文分析](paper_2603.18523.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由来自华盛顿大学、艾伦人工智能研究所、加州大学伯克利分校等知名机构的研究团队合作完成，提出了一种针对大型视觉语言模型（LVLM）计数能力的机制可解释性分析方法。研究通过合成和真实数据集，结合创新的可解释性工具，揭示了LVLM中存在的结构化“计数电路”，并基于此提出了一种轻量级干预策略。该方法不仅显著提升了计数性能，还意外地改善了通用视觉推理能力，为理解LVLM的内部工作机制提供了新视角。


### [Scaling Sim-to-Real Reinforcement Learning for Robot VLAs with Generative 3D Worlds](https://arxiv.org/abs/2603.18532)

**作者**: Choi, Wang, Su, Xu  
**链接**: [arXiv](https://arxiv.org/abs/2603.18532) · [PDF](https://arxiv.org/pdf/2603.18532)  \| [📖 全文分析](paper_2603.18532.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种创新的机器人视觉语言动作（VLA）模型强化学习微调方法，通过利用3D世界生成模型和语言驱动的场景设计器，生成大量多样化的交互场景，有效解决了传统方法在真实世界训练导致的泛化性不足问题。该方法在仿真环境中将成功率从9.7%提升至79.8%，并实现了成功的仿真到现实迁移，将真实世界成功率从21.7%提升至75%。


### [Cheers: Decoupling Patch Details from Semantic Representations Enables Unified Multimodal Comprehension and Generation](https://arxiv.org/abs/2603.12793)

**作者**: Zhang, Peng, Guo, Zhang, Yang 等 22 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12793) · [PDF](https://arxiv.org/pdf/2603.12793)  \| [📖 全文分析](paper_2603.12793.md)  
**评分**: 8.35  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Cheers的统一多模态模型，通过将补丁级细节与语义表示解耦，在单一模型中实现了视觉理解与生成的统一。该方法在流行的基准测试中表现优异，在视觉理解和生成任务上均达到或超越了先进的统一多模态模型，同时实现了4倍的令牌压缩，显著提升了高分辨率图像编码和生成的效率。作者团队来自多个知名机构，包括阿里巴巴（Zhang, Sun, Chen, Wang, Yan, Han, Ma, Ke, Wang, Liu, Sun等）、清华大学（Guo, Yang, Sun, Zhang, Li, Zhao, Xu, Shi等）和北京航空航天大学（Peng），体现了产学研结合的强大背景。


### [Cognitive Mismatch in Multimodal Large Language Models for Discrete Symbol Understanding](https://arxiv.org/abs/2603.18472)

**作者**: Li, Kuang, Xing, Liu, Dong 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18472) · [PDF](https://arxiv.org/pdf/2603.18472)  \| [📖 全文分析](paper_2603.18472.md)  
**评分**: 8.30  （novelty: 9.0 | method: 9.5 | evidence: 7.0 | clarity: 8.5）

> 本文针对多模态大语言模型在离散符号理解方面的能力进行了开创性研究，提出了一个全面的基准测试来评估模型在语言、文化、数学、物理和化学五个领域的表现。研究发现了一个反直觉的“认知不匹配”现象：模型在基本符号识别上失败，却在复杂推理任务中成功，揭示了当前AI依赖语言概率而非真实视觉感知的局限性。这项工作为开发更严谨、与人类认知对齐的智能系统提供了路线图。


### [HopChain: Multi-Hop Data Synthesis for Generalizable Vision-Language Reasoning](https://arxiv.org/abs/2603.17024)

**作者**: Wang, Liu, Zhou, Gao, Chen 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.17024) · [PDF](https://arxiv.org/pdf/2603.17024)  \| [📖 全文分析](paper_2603.17024.md)  
**评分**: 8.28  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文由阿里巴巴Qwen团队（作者来自阿里千问）提出了一种名为HopChain的可扩展框架，用于合成多跳视觉语言推理数据，以增强视觉语言模型（VLMs）在复杂推理任务中的泛化能力。该方法通过构建逻辑依赖的实例接地多跳查询链，有效暴露并解决了VLMs在长链推理中的多种错误模式（如感知、推理、知识和幻觉错误）。实验表明，使用HopChain合成的数据训练模型，在涵盖STEM、谜题、通用VQA、文本识别与文档理解以及视频理解等24个基准测试中，有20个取得了提升，证明了其广泛的泛化增益。


### [Generate Any Scene: Scene Graph Driven Data Synthesis for Visual Generation Training](https://arxiv.org/abs/2412.08221)

**作者**: Gao, Huang, Zhang, Kembhavi, Krishna  
**链接**: [arXiv](https://arxiv.org/abs/2412.08221) · [PDF](https://arxiv.org/pdf/2412.08221)  \| [📖 全文分析](paper_2412.08221.md)  
**评分**: 8.19  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由华盛顿大学、艾伦人工智能研究所（AI2）等知名机构的研究团队提出了一种名为'Generate Any Scene'的数据引擎方法，用于解决文本到视觉生成任务中的组合泛化和语义对齐问题。该方法通过系统枚举场景图来生成高质量合成数据，并设计了自改进框架、蒸馏算法和奖励模型，在多个基准测试中取得了显著提升。


### [Narrow Fine-Tuning Erodes Safety Alignment in Vision-Language Agents](https://arxiv.org/abs/2602.16931)

**作者**: Gulati, Raval  
**链接**: [arXiv](https://arxiv.org/abs/2602.16931) · [PDF](https://arxiv.org/pdf/2602.16931)  \| [📖 全文分析](paper_2602.16931.md)  
**评分**: 8.08  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 8.0）

> 本文探讨了视觉语言模型在持续学习中的一个关键安全问题：在狭窄领域数据集上进行微调会严重侵蚀模型的安全对齐性，并导致跨任务和跨模态的广泛失准。研究发现，即使训练数据中仅包含10%的有害内容，也会引发显著的失准现象。通过几何分析，作者揭示了有害行为存在于一个低维子空间中。论文实验设计严谨，在Gemma3-4B模型上进行了系统性验证，并提出了两种缓解策略。该研究对构建鲁棒的持续学习框架具有重要警示意义。


### [ERGO: Efficient High-Resolution Visual Understanding for Vision-Language Models](https://arxiv.org/abs/2509.21991)

**作者**: Lee, Shin, Yang, Song, Lim 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.21991) · [PDF](https://arxiv.org/pdf/2509.21991)  \| [📖 全文分析](paper_2509.21991.md)  
**评分**: 8.07  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为ERGO的高效高分辨率视觉理解方法，针对大型视觉语言模型处理高分辨率图像时计算开销大的问题，设计了一种两阶段的“粗到细”推理流程。该方法通过强化学习框架实现推理驱动的感知，能够有效识别任务相关区域并仅对关键区域进行全分辨率处理，在显著降低计算成本的同时保持甚至提升模型性能。在多个数据集上验证了方法的有效性，例如在V*基准上超越Qwen2.5-VL-7B模型4.7分，同时仅使用23%的视觉token，实现3倍推理加速。代码和模型已开源。


### [On Robustness and Chain-of-Thought Consistency of RL-Finetuned VLMs](https://arxiv.org/abs/2602.12506)

**作者**: Zhao, Shah, Zhu, Deng, Jiang 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.12506) · [PDF](https://arxiv.org/pdf/2602.12506)  \| [📖 全文分析](paper_2602.12506.md)  
**评分**: 8.04  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.0）

> 本文深入研究了强化学习（RL）微调在视觉语言模型（VLM）中的应用及其鲁棒性问题。研究发现，尽管RL微调能提升视觉推理基准测试的准确率，但会使模型在面对误导性文本提示（如错误标题或思维链）时表现出显著的鲁棒性下降和思维链一致性降低，揭示了当前开源模型在RL微调过程中存在的“准确性-忠实性”权衡问题。论文通过系统性实验分析了这一现象，并探讨了对抗性数据增强和忠实性感知奖励等改进方案的潜力与局限。


### [FINER: MLLMs Hallucinate under Fine-grained Negative Queries](https://arxiv.org/abs/2603.17662)

**作者**: Xiao, Kim, Xian, Akata, Alaniz  
**链接**: [arXiv](https://arxiv.org/abs/2603.17662) · [PDF](https://arxiv.org/pdf/2603.17662)  \| [📖 全文分析](paper_2603.17662.md)  
**评分**: 7.96  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文针对多模态大语言模型（MLLMs）在细粒度负查询下产生幻觉的问题，提出了FINER基准和FINER-Tuning微调方法。作者团队来自学术界（从姓氏和机构关联看，可能涉及德国图宾根大学等研究机构，但未明确标注知名顶级机构如Stanford、DeepMind等，故省略具体背景介绍）。该工作系统性地定义了细粒度幻觉的评估场景，并提出了有效的缓解方案，在多个基准上验证了方法的有效性。


### [MMKU-Bench: A Multimodal Update Benchmark for Diverse Visual Knowledge](https://arxiv.org/abs/2603.15117)

**作者**: Fu, Du, Chang, Jin, Deng 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15117) · [PDF](https://arxiv.org/pdf/2603.15117)  \| [📖 全文分析](paper_2603.15117.md)  
**评分**: 7.96  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 7.5）

> 本文提出了MMKU-Bench，一个用于评估多模态知识更新的综合性基准测试。该基准包含超过25k个知识实例和49k张图像，覆盖了知识更新和未知知识学习两种场景，并首次系统性地评估了跨模态一致性问题。作者团队未在摘要中明确列出所属机构，因此省略团队背景介绍。该工作填补了现有研究在评估多模态模型知识更新能力方面的空白，特别是对已掌握但后续发生变化的知识的更新评估，具有重要的研究价值。


### [Multimodal Task Interference: A Benchmark and Analysis of History-Target Mismatch in Multimodal LLMs](https://arxiv.org/abs/2603.18425)

**作者**: Kawarada, Ishigaki, Takamura  
**链接**: [arXiv](https://arxiv.org/abs/2603.18425) · [PDF](https://arxiv.org/pdf/2603.18425)  \| [📖 全文分析](paper_2603.18425.md)  
**评分**: 7.91  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文针对多模态大语言模型中的任务干扰现象进行了系统性研究，提出了首个专门评估多模态对话系统中任务干扰的基准测试。研究通过设计三个维度的历史-目标不匹配（模态不匹配、推理不匹配、答案格式不匹配），在六个跨文本和视觉的任务上进行了全面实验。研究发现任务干扰具有高度方向性，并揭示了不同维度不匹配对性能影响的相对重要性。实验覆盖了开源和专有模型，结果具有较好的说服力。


### [Thinking with Constructions: A Benchmark and Policy Optimization for Visual-Text Interleaved Geometric Reasoning](https://arxiv.org/abs/2603.18662)

**作者**: Zhao, Xu, Yuan, Cao, Ma 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18662) · [PDF](https://arxiv.org/pdf/2603.18662)  \| [📖 全文分析](paper_2603.18662.md)  
**评分**: 7.79  （novelty: 9.0 | method: 8.0 | evidence: 9.0 | clarity: 8.5）

> 本文提出了一种用于视觉-文本交织几何推理的基准和策略优化方法。论文针对多模态大语言模型在几何推理中缺乏动态视觉辅助构建能力的问题，创新性地提出了视觉-文本交织思维链框架。首先构建了包含4334个几何问题的GeoAux-Bench基准，并通过实验发现交织视觉文本辅助优于单模态方法，且有效构造能降低推理困惑度。基于此，提出了A2PO强化学习范式来优化构造策略。实验表明该方法能带来3.51%的性能提升，且代码和数据已开源。


### [How Do Medical MLLMs Fail? A Study on Visual Grounding in Medical Images](https://arxiv.org/abs/2603.14323)

**作者**: Liu, Yu, Ebrahimkhani, Shawn, Ng 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.14323) · [PDF](https://arxiv.org/pdf/2603.14323)  \| [📖 全文分析](paper_2603.14323.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文对医学多模态大语言模型（MLLMs）在视觉定位能力上的失败原因进行了开创性的系统性研究。作者团队（作者姓名未显示明确知名机构归属，故省略背景介绍）首次通过设计专门的评估数据集VGMED，将视觉定位与语义定位分离，并定量、定性地验证了现有医学MLLMs普遍存在视觉定位能力不足的问题。基于此发现，他们提出了一个无需额外训练、简单有效的推理时优化方法VGRefine，在多个医学视觉问答基准上取得了最先进的性能。该研究问题定义清晰，实验设计严谨，提出的解决方案具有实用价值。


### [VLA-Thinker: Boosting Vision-Language-Action Models through Thinking-with-Image Reasoning](https://arxiv.org/abs/2603.14523)

**作者**: Wang, Bao, Gao, Xu, Tian 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.14523) · [PDF](https://arxiv.org/pdf/2603.14523)  \| [📖 全文分析](paper_2603.14523.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为VLA-Thinker的创新框架，旨在提升视觉-语言-动作模型在具身智能任务中的性能。其核心创新在于将感知建模为一种可动态调用的推理动作，突破了传统文本链式推理将视觉输入视为静态背景的限制。该框架通过两阶段训练（SFT微调与GRPO强化学习）进行优化，并在LIBERO和RoboTwin 2.0基准测试中取得了显著性能提升（如LIBERO上97.5%的成功率）。论文提供了项目页面和代码，实验证据充分。


### [Understanding and Defending VLM Jailbreaks via Jailbreak-Related Representation Shift](https://arxiv.org/abs/2603.17372)

**作者**: Wei, Li, Ruan, Qin, Wen 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.17372) · [PDF](https://arxiv.org/pdf/2603.17372)  \| [📖 全文分析](paper_2603.17372.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种理解和防御视觉语言模型越狱攻击的新方法。研究团队观察到，在VLM的表示空间中，良性输入、有害输入以及越狱样本具有可区分的内部状态，并发现视觉模态会将表示向特定的越狱状态偏移，从而导致模型未能触发拒绝机制。基于此，作者定义并量化了“越狱相关偏移”，并提出了一种通过移除该偏移来增强VLM安全性的防御方法（JRS-Rem）。实验表明该方法在多种场景下均能提供有效的防御，同时不影响良性任务的性能。


### [Deep Expert Injection for Anchoring Retinal VLMs with Domain-Specific Knowledge](https://arxiv.org/abs/2603.07131)

**作者**: Lu, Wang, Guo, Du, Liu 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.07131) · [PDF](https://arxiv.org/pdf/2603.07131)  \| [📖 全文分析](paper_2603.07131.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种名为EyExIn的数据高效框架，旨在通过深度专家注入机制，将专家知识锚定到视网膜视觉语言模型中。该方法针对医学视觉语言模型在临床部署中存在的感知差距和推理差距问题，设计了专家感知双流编码策略、语义自适应门控融合模块和自适应深度专家注入机制，显著提升了眼科视觉问答的精度。在四个基准测试上的广泛实验表明，该模型始终优于大型专有系统。


### [Exposing Hidden Biases in Text-to-Image Models via Automated Prompt Search](https://arxiv.org/abs/2512.08724)

**作者**: Plitsis, Bouritsas, Katsouros, Panagakis  
**链接**: [arXiv](https://arxiv.org/abs/2512.08724) · [PDF](https://arxiv.org/pdf/2512.08724)  \| [📖 全文分析](paper_2512.08724.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Bias-Guided Prompt Search (BGPS)的框架，用于自动生成能最大化揭示文本到图像(TTI)扩散模型中隐藏偏见的提示词。该方法通过结合大语言模型(LLM)和作用于TTI内部表征的属性分类器，系统地探索提示空间，发现了现有去偏方法可能忽略的、微妙且未被记录的偏见。实验在Stable Diffusion 1.5和一个先进的去偏模型上进行，结果显著恶化了公平性指标，揭示了TTI模型的脆弱性。BGPS扩展了偏见搜索空间，可作为评估偏见缓解效果的新工具。


### [PromptHub: Enhancing Multi-Prompt Visual In-Context Learning with Locality-Aware Fusion, Concentration and Alignment](https://arxiv.org/abs/2603.18891)

**作者**: Luo, Wang, Qin, Lian, Feng 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18891) · [PDF](https://arxiv.org/pdf/2603.18891)  \| [📖 全文分析](paper_2603.18891.md)  
**评分**: 7.74  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为PromptHub的新框架，旨在增强视觉上下文学习中的多提示融合能力。该方法通过引入局部感知融合、集中和对齐机制，克服了现有补丁级融合框架的局限性。在三个基础视觉任务上的广泛实验证明了其优越性，并验证了其在分布外设置和各种检索场景下的通用性、可迁移性和鲁棒性。作者团队来自学术界，但未明确标注所属机构，因此省略团队背景介绍。

