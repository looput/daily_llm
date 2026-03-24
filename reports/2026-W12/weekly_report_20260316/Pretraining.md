# Pretraining · 2026-03-16 ~ 2026-03-20

**论文数**: 19 篇

---

## 📊 趋势分析

### 研究全貌

本批次预训练领域的研究论文呈现出多样化的技术探索方向，主要聚焦于**架构创新**、**训练策略优化**以及**多语言/多模态扩展**。在架构层面，多篇工作致力于解决深层网络的信号退化、注意力机制缺陷和计算效率问题。训练策略方面，研究重点在于如何更高效地利用有限数据，提升模型在特定领域的性能。当前的热点问题是如何在资源受限的条件下（如数据稀缺、算力有限）开发出具有竞争力的模型，并深入理解模型内部的动态机制。整体研究趋势正从单纯扩大规模，转向对模型内部动力学、稀疏性、路径依赖等本质特性的深入剖析，并强调方法的实用性与可部署性。

### 重点方法深度解析

从这批论文中，以下几个工作因其创新性和实用性尤为突出：

**《Mixture-of-Depths Attention》** [URL](https://arxiv.org/abs/2603.15619)
*   **核心创新点**：针对LLM深度扩展时浅层特征被稀释的“信号退化”问题，提出了一种新颖的注意力机制。其核心思想是允许注意力头不仅关注当前层的序列信息，还能直接访问并利用来自前几层的深度特征，从而更有效地保留和传递信息。
*   **技术细节**：该方法的核心是让每个注意力头同时处理“序列KV对”（当前层）和“深度KV对”（来自前驱层）。作者还设计了一个硬件高效的算法，解决了非连续内存访问的难题，在64K序列长度下实现了FlashAttention-2 97.3%的效率。
*   **效果验证**：在1.5B参数模型上，MoDA在10个验证基准上平均降低困惑度0.2，在10个下游任务上平均提升性能2.11%，而额外计算开销（FLOPs）仅为3.7%。
*   **适用场景**：该方法是一种通用的架构改进，尤其适用于构建更深、更高效的模型，为解决深度诅咒问题提供了新的技术路径。

**《Attention Residuals》** [URL](https://arxiv.org/abs/2603.15031)
*   **核心创新点**：挑战了Transformer中标准的固定权重残差连接，提出用基于注意力的动态聚合来替代，以缓解深层网络中的隐藏状态稀释。
*   **技术细节**：AttnRes使用softmax注意力机制，让每一层可以学习输入依赖的权重，有选择性地聚合前面所有层的输出，而非简单累加。
*   **技术细节**：为解决全深度注意力带来的巨大开销，论文进一步提出了“块注意力残差”（Block AttnRes），将层划分为块，在块级别进行注意力聚合，大幅降低了内存和通信成本。
*   **效果验证**：该方法被集成到总参48B的Kimi Linear架构中，并在1.4T token上进行了预训练。结果表明，它有效缓解了PreNorm导致的稀释，使各层的输出幅度和梯度分布更均匀，并在所有评估的下游任务上提升了性能。
*   **适用场景**：作为标准残差连接的“即插即用”式替代，适用于希望改进现有模型架构、提升深度利用效率的场景。

**《The Finetuner‘s Fallacy: When to Pretrain with Your Finetuning Data》** [URL](https://arxiv.org/abs/2603.16177)
*   **核心创新点**：挑战了“先通用预训练，再领域微调”的常规做法，提出“专业化预训练”（SPT）策略，即在预训练阶段就重复使用领域数据。
*   **技术细节**：该方法操作简单，将原本留作微调的小规模领域数据集，作为总训练token的一部分，从预训练开始就反复使用。
*   **效果验证**：在ChemPile、MusicPile和ProofPile三个专业领域上，SPT在微调后不仅提升了领域性能，还更好地保留了通用能力。当目标领域在通用语料中代表性不足时，1B的SPT模型甚至能超越3B标准预训练模型。
*   **适用场景**：对于数据稀缺但要求高的垂直领域（如法律、医药、金融）模型开发具有直接的指导意义，能以更少的参数和总计算量获得更强的领域性能。

**对比分析**：MoDA和AttnRes都旨在解决深度扩展问题，但切入点不同。MoDA在**注意力计算层面**引入跨层信息，而AttnRes在**残差连接层面**引入动态路由。两者都关注信息在深度维度的流动，但MoDA更偏向于增强当前层的计算能力，而AttnRes则优化了层与层之间的聚合方式。SPT则从**数据利用策略**层面提供了新思路，强调了领域数据应尽早融入训练流程。这些方法从不同维度提升了预训练的效率和效果。

### 实践启示

这些研究为实际的大模型应用开发提供了宝贵的借鉴。对于**资源受限的团队或特定语言/文化领域**（如阿拉伯语），Fanar 2.0展示了一条可行的“主权AI”路径，强调数据质量、针对性持续训练和模型融合。对于**追求极致性能的通用模型开发**，应重点关注MoDA、AttnRes等底层架构创新，以构建更强大的基础模型。对于**垂直领域的产品化部署**，SPT策略极具价值，建议在项目规划初期就考虑将领域数据纳入预训练循环，而非仅作为最后的微调步骤。

**可落地的具体建议**包括：1）在预训练中尝试使用无衰减的恒定学习率（WSO），可能提升模型在下游任务上的适应能力。2）利用PathMoE等改进的MoE路由机制，以提升大规模稀疏模型的训练效率和路径一致性。**关键注意事项**：实施架构改进（如MoDA、AttnRes）时需仔细评估其与现有训练基础设施的兼容性及额外开销。采用SPT时，需参考论文中的过拟合缩放定律，谨慎确定领域数据的重复比例，避免损害模型的通用语言理解能力。

---

## 📄 论文列表（19 篇）

### [Fanar 2.0: Arabic Generative AI Stack](https://arxiv.org/abs/2603.16397)

**作者**: FANAR TEAM, Abbas, Ahmad, Ahmad, Al-Homaid 等 37 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16397) · [PDF](https://arxiv.org/pdf/2603.16397)  \| [📖 全文分析](paper_2603.16397.md)  
**评分**: 8.86  （novelty: 8.5 | method: 9.0 | evidence: 9.0 | clarity: 8.5）

> 本文由卡塔尔计算研究所（QCRI）团队提出，该团队隶属于哈马德·本·哈利法大学（HBKU），是阿拉伯世界领先的人工智能研究机构。论文介绍了Fanar 2.0，这是一个资源受限条件下开发的、以阿拉伯语为中心的第二代生成式AI平台。该工作展示了在数据稀缺（阿拉伯语网络数据仅占0.5%）和计算资源有限（256块H100 GPU）的约束下，通过强调数据质量、针对性持续预训练和模型融合等策略，依然能开发出具有竞争力的主权AI系统。其核心模型Fanar-27B在多项阿拉伯语及英语基准测试上取得了显著提升，并构建了涵盖内容审核、语音识别、多模态理解、工具调用、伊斯兰内容生成、古典诗歌创作和翻译的完整技术栈。


### [Mixture-of-Depths Attention](https://arxiv.org/abs/2603.15619)

**作者**: Zhu, Fang, Liao, Wang, Cheng 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15619) · [PDF](https://arxiv.org/pdf/2603.15619)  \| [📖 全文分析](paper_2603.15619.md)  
**评分**: 8.86  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为混合深度注意力（Mixture-of-Depths Attention, MoDA）的新机制，旨在解决大型语言模型（LLMs）深度扩展时出现的信号退化问题。该方法允许注意力头同时关注当前层的序列KV对和来自前几层的深度KV对，从而更有效地保留和利用浅层形成的特征。论文在1.5B参数模型上进行了实验，结果表明MoDA在多个验证基准和下游任务上均能稳定超越基线模型，且仅带来3.7%的额外FLOPs开销。作者还开发了硬件高效的算法，实现了接近FlashAttention-2的效率。代码已开源。


### [Attention Residuals](https://arxiv.org/abs/2603.15031)

**作者**: Kimi Team, Chen, Zhang, Su, Xu 等 37 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15031) · [PDF](https://arxiv.org/pdf/2603.15031)  \| [📖 全文分析](paper_2603.15031.md)  
**评分**: 8.80  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由Kimi Team（月之暗面公司）提出了一种名为Attention Residuals（AttnRes）的新方法，用于改进大型语言模型中的残差连接机制。该方法通过引入基于注意力机制的动态权重聚合，替代传统的固定权重累加，有效缓解了深层网络中的隐藏状态稀释问题。论文通过详实的实验验证了方法的有效性，包括在48B参数模型上的预训练和下游任务评估，展示了显著的性能提升。


### [PolyGLU: State-Conditional Activation Routing in Transformer Feed-Forward Networks](https://arxiv.org/abs/2603.13347)

**作者**: Medeiros  
**链接**: [arXiv](https://arxiv.org/abs/2603.13347) · [PDF](https://arxiv.org/pdf/2603.13347)  \| [📖 全文分析](paper_2603.13347.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为PolyGLU（Polychromatic Gated Linear Unit）的创新方法，用于替代Transformer前馈网络中的标准激活函数（如SwiGLU）。该方法受生物神经系统中多种神经递质的启发，允许每个神经元根据输入动态选择K=4种不同的激活函数，通过结合静态偏好和输入条件门控的可微分机制实现端到端训练。实验表明，该方法在仅增加0.23%参数开销的情况下，在训练中自发形成了深度依赖的激活函数选择模式，并在监督微调中表现出鲁棒性。尽管训练数据量远少于基线模型，但在标准基准测试中取得了有竞争力的性能。所有代码、权重和训练基础设施均已开源。


### [Path-Constrained Mixture-of-Experts](https://arxiv.org/abs/2603.18297)

**作者**: Gu, Likhomanenko, Thilak, Ramapuram, Jaitly  
**链接**: [arXiv](https://arxiv.org/abs/2603.18297) · [PDF](https://arxiv.org/pdf/2603.18297)  \| [📖 全文分析](paper_2603.18297.md)  
**评分**: 8.36  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种改进稀疏混合专家（MoE）架构路由机制的新方法Path-Constrained Mixture-of-Experts（PathMoE）。该方法通过跨连续层共享路由器参数，有效约束了专家路径空间，解决了传统独立路由导致的统计效率低下问题。在0.9B和16B参数模型上的实验表明，该方法在困惑度和下游任务上均优于传统独立路由，且无需辅助负载均衡损失。分析表明，遵循相同路径的token会按语言功能自然聚类，PathMoE能产生更集中的分组、更好的跨层一致性和更强的路由扰动鲁棒性。这些结果为通过专家路径视角理解MoE架构提供了新思路。


### [The Finetuner's Fallacy: When to Pretrain with Your Finetuning Data](https://arxiv.org/abs/2603.16177)

**作者**: Baek, Monti, Schwab, Abbas, Adiga 等 34 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16177) · [PDF](https://arxiv.org/pdf/2603.16177)  \| [📖 全文分析](paper_2603.16177.md)  
**评分**: 8.35  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由来自Meta GenAI、Stanford、MIT等知名机构的联合研究团队提出了一种名为'专业化预训练'（SPT）的新方法，用于解决模型在数据稀缺的垂直领域部署时的过拟合和遗忘问题。该方法通过在预训练阶段重复使用领域数据，显著提升了模型在特定领域的性能，同时保持了通用能力。论文在三个专业领域（ChemPile、MusicPile、ProofPile）上进行了充分验证，并推导了过拟合缩放定律来指导实践。


### [Pre-training LLM without Learning Rate Decay Enhances Supervised Fine-Tuning](https://arxiv.org/abs/2603.16127)

**作者**: Yano, Kiyono, Kobayashi, Takase, Suzuki  
**链接**: [arXiv](https://arxiv.org/abs/2603.16127) · [PDF](https://arxiv.org/pdf/2603.16127)  \| [📖 全文分析](paper_2603.16127.md)  
**评分**: 8.29  （novelty: 8.5 | method: 9.5 | evidence: 8.0 | clarity: 8.0）

> 本文探讨了大规模语言模型预训练中学习率调度策略对监督微调后下游任务性能的影响。研究发现，在预训练阶段使用无衰减的恒定学习率策略（Warmup-Stable-Only, WSO）相比传统衰减策略，虽然可能略微牺牲预训练损失，但能显著提升模型在监督微调后的下游任务表现。论文通过1B和8B参数模型的实验验证了这一结论，并利用损失景观分析揭示了其内在机理——衰减策略使模型陷入更尖锐的最小值，而WSO策略保持了更平坦的最小值，从而增强了模型的适应性。这项工作为LLM训练策略提供了新的视角和实用指导。


### [Attention Sinks Are Provably Necessary in Softmax Transformers: Evidence from Trigger-Conditional Tasks](https://arxiv.org/abs/2603.11487)

**作者**: Ran-Milo  
**链接**: [arXiv](https://arxiv.org/abs/2603.11487) · [PDF](https://arxiv.org/pdf/2603.11487)  \| [📖 全文分析](paper_2603.11487.md)  
**评分**: 8.28  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文通过理论证明和实验验证，揭示了softmax Transformer中注意力汇聚现象（attention sink）的功能必要性。研究证明在某些触发条件任务中，softmax归一化约束会迫使注意力汇聚到稳定的锚点，而非归一化的ReLU注意力则能避免此现象。该工作为理解Transformer内部机制提供了重要的理论洞见。


### [NerVE: Nonlinear Eigenspectrum Dynamics in LLM Feed-Forward Networks](https://arxiv.org/abs/2603.06922)

**作者**: Jha, Reagen  
**链接**: [arXiv](https://arxiv.org/abs/2603.06922) · [PDF](https://arxiv.org/pdf/2603.06922)  \| [📖 全文分析](paper_2603.06922.md)  
**评分**: 8.24  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为NerVE的统一特征谱框架，用于理解大型语言模型（LLMs）中前馈网络（FFNs）在高维潜在空间中组织和调节信息流的方式。该研究填补了FFN高维动力学理解不足的空白，通过轻量级、内存高效的特征谱动态跟踪，提供了四个互补的度量指标。研究验证了NerVE在不同模型规模、架构和优化器配置下的有效性，并发现其恢复的稳定谱特征与模型的泛化能力相关，且可推广到Transformer之外的MLP-Mixer架构，为超越试错的架构和优化器选择提供了可操作的见解。


### [Frayed RoPE and Long Inputs: A Geometric Perspective](https://arxiv.org/abs/2603.18017)

**作者**: Wertheimer, Zhang, Liu, Yin, Wang  
**链接**: [arXiv](https://arxiv.org/abs/2603.18017) · [PDF](https://arxiv.org/pdf/2603.18017)  \| [📖 全文分析](paper_2603.18017.md)  
**评分**: 8.19  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文针对RoPE位置编码在长文本输入时性能下降的问题，从几何视角提出了统一的理论解释，并提出了RoPE-ID改进方法。论文通过理论分析和实验验证，在LongBench和RULER基准上展示了方法的有效性。作者团队来自学术界，但未明确标注具体机构。


### [PRISM: Demystifying Retention and Interaction in Mid-Training](https://arxiv.org/abs/2603.17074)

**作者**: Runwal, Agrawal, Roy, Panda  
**链接**: [arXiv](https://arxiv.org/abs/2603.17074) · [PDF](https://arxiv.org/pdf/2603.17074)  \| [📖 全文分析](paper_2603.17074.md)  
**评分**: 8.17  （novelty: 8.0 | method: 9.0 | evidence: 9.0 | clarity: 8.0）

> 本文对大型语言模型的中期训练设计选择进行了全面的实证研究，提出了PRISM方法。研究通过跨七个基础模型、四种模型家族、两种架构类型和不同参数规模的对照实验，系统分析了中期训练对模型推理能力的影响机制。研究发现中期训练能显著提升数学、代码和科学推理能力，并揭示了中期训练与强化学习之间的协同作用机制。


### [Omnilingual SONAR: Cross-Lingual and Cross-Modal Sentence Embeddings Bridging Massively Multilingual Text and Speech](https://arxiv.org/abs/2603.16606)

**作者**: Omnilingual SONAR Team, Janeiro, Cabot, Tsiamas, Meng 等 19 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16606) · [PDF](https://arxiv.org/pdf/2603.16606)  \| [📖 全文分析](paper_2603.16606.md)  
**评分**: 8.14  （novelty: 9.5 | method: 9.0 | evidence: 9.0 | clarity: 8.5）

> 本文由Meta AI（原Facebook AI Research）团队提出，该团队在自然语言处理和语音处理领域具有深厚的研究积累和业界影响力。论文提出了一种名为OmniSONAR的全新全语言、跨语言、跨模态句子嵌入模型家族，能够在单一语义空间中嵌入文本、语音、代码和数学表达式，并在涵盖数千种语言（从高资源到极低资源）的规模上实现了最先进的下游性能。该方法创新性地结合了渐进式训练、LLM初始化的编码器-解码器架构、新颖的分割-softmax对比损失以及两阶段师生编码器蒸馏框架，显著提升了跨语言语义搜索和翻译等任务的性能。实验设计全面，证据充分，在多个大规模基准测试上取得了突破性成果。


### [M$^2$RNN: Non-Linear RNNs with Matrix-Valued States for Scalable Language Modeling](https://arxiv.org/abs/2603.14360)

**作者**: Mishra, Tan, Stoica, Gonzalez, Dao  
**链接**: [arXiv](https://arxiv.org/abs/2603.14360) · [PDF](https://arxiv.org/pdf/2603.14360)  \| [📖 全文分析](paper_2603.14360.md)  
**评分**: 8.13  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由加州大学伯克利分校（UC Berkeley）的研究团队提出了一种新型非线性循环神经网络架构M²RNN，该架构采用矩阵值隐藏状态和表达性非线性状态转移，旨在解决Transformer在计算表达能力上的局限性。论文在语言建模任务上展示了M²RNN在状态跟踪泛化、大规模建模效率和长上下文泛化方面的显著优势，特别是在混合架构中替换单个循环层即可带来可观的性能提升。


### [When Does Sparsity Mitigate the Curse of Depth in LLMs](https://arxiv.org/abs/2603.15389)

**作者**: Muhtar, Song, Pokutta, Zimmer, Pelleriti 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15389) · [PDF](https://arxiv.org/pdf/2603.15389)  \| [📖 全文分析](paper_2603.15389.md)  
**评分**: 8.10  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文探讨了稀疏性如何缓解大型语言模型（LLMs）中的深度诅咒问题。作者团队来自学术界（作者姓名未明确指向单一知名机构，但部分作者如Pokutta、Hofmann等常见于学术研究，推测为高校或研究机构团队）。论文通过系统性的实验和分析，揭示了训练和架构中自然产生的稀疏性（如权重衰减、长上下文注意力、分组查询注意力、混合专家）能够调节方差传播，从而改善深层网络的利用率。这一发现不仅具有理论洞察，还提炼出了实用的训练准则，并在下游任务上取得了显著的性能提升（4.6%准确率提升）。论文创新性突出，实验证据扎实，且代码已开源。


### [Genomic Next-Token Predictors are In-Context Learners](https://arxiv.org/abs/2511.12797)

**作者**: Breslow, Mishra, Revsine, Schatz, Liu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.12797) · [PDF](https://arxiv.org/pdf/2511.12797)  \| [📖 全文分析](paper_2511.12797.md)  
**评分**: 8.00  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.0）

> 本文探讨了在非语言序列领域（特别是基因组序列）中，通过大规模预测训练是否能够自然涌现上下文学习能力。研究团队开发了一个受控实验框架，将符号推理任务实例化为语言和基因组形式，直接比较基因组模型和语言模型的上下文学习表现。研究发现基因组模型与语言模型类似，随着上下文演示数量的增加，在模式归纳方面表现出对数线性增益。这是首次在基因组序列中观察到自然涌现的上下文学习能力的证据，支持了上下文学习是大规模预测建模在丰富数据上的结果的假设。这些发现将涌现的元学习能力扩展到了语言之外，指向了一种统一的、模态无关的上下文学习观点。


### [Ablate and Rescue: A Causal Analysis of Residual Stream Hyper-Connections](https://arxiv.org/abs/2603.14833)

**作者**: Peng, Rai, Tseng, Wang, Wu  
**链接**: [arXiv](https://arxiv.org/abs/2603.14833) · [PDF](https://arxiv.org/pdf/2603.14833)  \| [📖 全文分析](paper_2603.14833.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种针对多流Transformer架构的因果分析方法，首次开源了Manifold-Constrained Hyper-Connections (mHC)语言模型，并开发了系统性的流消融与恢复框架，用于探究并行残差流如何编码和利用信息。论文在机制分析方面具有创新性，实验设计严谨且代码开源，为理解复杂神经网络架构提供了新工具。


### [Directional Routing in Transformers](https://arxiv.org/abs/2603.14923)

**作者**: Taylor  
**链接**: [arXiv](https://arxiv.org/abs/2603.14923) · [PDF](https://arxiv.org/pdf/2603.14923)  \| [📖 全文分析](paper_2603.14923.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为'方向性路由'的轻量级机制，用于增强Transformer注意力头。该方法通过一个共享路由器控制每个注意力头的学习抑制方向，仅增加3.9%的参数成本。实验表明，该机制成为模型的主导计算路径，显著提升了事实回忆和归纳任务的准确性，并降低了困惑度。论文通过机制可解释性方法深入分析了路由电路的工作方式，发现模型在没有明确压力的情况下自组织为两种机制：早期层的领域自适应路由和后期层的固定句法剪枝。作者Taylor未注明所属知名机构。


### [A Family of LLMs Liberated from Static Vocabularies](https://arxiv.org/abs/2603.15953)

**作者**: Alpha, :, Abdessaied, Baranowski, Balles 等 38 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15953) · [PDF](https://arxiv.org/pdf/2603.15953)  \| [📖 全文分析](paper_2603.15953.md)  
**评分**: 7.74  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种基于分层自回归变换器（HAT）架构的LLM家族，旨在解决传统基于分词器的LLM在词汇表固定、领域适应性差等方面的局限性。该方法通过字节级处理、重用预训练模型等技术，在英语和德语任务上超越了原始Llama 3.1模型，并开源了模型和训练检查点。


### [Krause Synchronization Transformers](https://arxiv.org/abs/2602.11534)

**作者**: Liu, Yue, Welling, Song  
**链接**: [arXiv](https://arxiv.org/abs/2602.11534) · [PDF](https://arxiv.org/pdf/2602.11534)  \| [📖 全文分析](paper_2602.11534.md)  
**评分**: 7.74  （novelty: 9.0 | method: 9.5 | evidence: 8.0 | clarity: 7.0）

> 本文提出了一种名为Krause Attention的新型注意力机制，灵感来源于有界置信共识动力学。该方法旨在解决传统Transformer中全局归一化softmax权重导致的表示崩溃和注意力汇问题。通过在视觉（ViT在CIFAR/ImageNet）、自回归生成（MNIST/CIFAR-10）和大语言模型（Llama/Qwen）上的实验，证明了该方法在显著减少计算量的同时，能带来一致的性能提升。

