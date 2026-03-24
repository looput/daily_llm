# Pretraining · 2026-03-16 ~ 2026-03-20

**论文数**: 16 篇

---

## 📊 趋势分析

### 研究全貌

本批次论文的研究方向主要集中在**预训练流程优化**、**模型架构创新**以及**训练动态的理论分析**三大方面。预训练流程优化研究探讨了如何更高效地利用领域数据（如SPT方法）和如何设计中期训练策略（如PRISM框架）。模型架构创新则聚焦于改进注意力机制（如Krause Attention、定向路由）和前馈网络（如PolyGLU）。理论分析则深入探究了“顿悟”（Grokking）等复杂训练现象的几何与动力学本质。当前的热点问题是如何在有限的算力与数据下，提升模型在特定领域的性能，同时保持其通用能力。整体研究趋势呈现出从“黑盒”经验性训练向“白盒”可解释、可预测、可调控的方向发展，研究者们正致力于通过更精细的机制设计和更深刻的理论理解来指导实践。

### 重点方法深度解析

从这批论文中，以下两个工作因其显著的实用价值和深刻的洞察力而尤为突出：

**1. 《The Finetuner's Fallacy: When to Pretrain with Your Finetuning Data》 [URL](https://arxiv.org/abs/2603.16177)
*   **核心创新点**：该研究挑战了“先预训练再微调”的传统范式，提出了“专业化预训练”（SPT）这一简单而有效的策略。它解决了在数据稀缺的垂直领域进行微调时，模型容易过拟合并遗忘通用知识的问题。其核心观点是：为了最大化领域数据的效用，应尽可能早地将其引入训练流程。
*   **技术细节**：SPT方法的核心是在标准的通用预训练过程中，将原本用于微调的小规模领域数据集作为一部分训练数据，并**重复使用**。具体而言，领域数据在整个预训练语料中占据一个固定的比例（例如，重复多次），而不是仅在最后阶段使用一次。论文还推导了**过拟合缩放定律**，为实践者提供了理论指导，帮助他们根据给定的预训练计算预算，选择最优的领域数据重复次数。
*   **效果验证**：在化学（ChemPile）、音乐（MusicPile）和数学证明（ProofPile）三个专业领域上，SPT在微调后不仅显著提升了领域性能，还更好地保留了模型的通用能力。实验表明，SPT可以将达到特定领域性能所需的预训练总token数减少高达1.75倍。当目标领域在通用预训练语料中代表性不足时，优势更加明显：一个采用SPT的1B参数模型，其性能可以超越一个标准预训练的3B参数模型。
*   **适用场景**：该方法特别适用于那些拥有少量高质量领域数据，但需要模型在该领域表现出色且不失通用性的场景，例如金融、法律、生物医药等垂直行业的模型定制。

**2. 《PRISM: Demystifying Retention and Interaction in Mid-Training》 [URL](https://arxiv.org/abs/2603.17074)
*   **核心创新点**：PRISM是一项关于大型语言模型“中期训练”的综合性实证研究。它系统地探索了在预训练后期、强化学习（RL）之前，插入一个专门针对推理能力（数学、代码、科学）进行优化的训练阶段。该研究揭示了中期训练对于后续RL成功的关键作用，并阐明了其与RL在机制上的根本差异。
*   **技术细节**：研究的关键发现是，**数据构成在中期训练阶段至关重要，而非在RL阶段**。例如，在中期训练中加入科学数据，能在后续RL中带来显著的GPQA-Diamond分数提升（+17至+28点），而调整RL的数据混合比例则收效甚微。机制上，中期训练会**密集地重构超过90%的模型权重**，从根本上改变模型的表示结构。而RL则是对模型进行**稀疏的、前置的微调**，仅影响约5%的参数，并完全保留中期训练形成的表示几何结构。
*   **效果验证**：通过在Granite、LLaMA、Mistral等多个模型家族、不同架构（密集Transformer、注意力-Mamba混合）和规模（3B至24B）上的控制实验，证明了中期训练能带来数学（+15至+40点）、代码（+5至+12点）和科学（+6至+13点）基准测试的稳定提升。完整的“PRISM中期训练 → RL”流程，能将多个推理基准的宏观平均分数提升3-4倍。
*   **适用场景**：该方法为希望显著提升模型在数学、编程和科学推理等复杂任务上性能的团队提供了清晰的实践蓝图。它强调了在RL之前，通过高质量、有针对性的数据对模型进行“塑形”的重要性。

**对比分析**：SPT与PRISM都关注于优化预训练流程以提升特定能力。SPT更侧重于**数据利用策略**，通过早期重复曝光来“拉伸”领域数据的价值，适用于数据稀缺的垂直领域。而PRISM则更侧重于**训练阶段的策略性设计**，通过一个集中的、目标明确的“中期训练”阶段来为后续优化（如RL）奠定基础。前者是“数据驱动”的流程改进，后者是“阶段化”的训练策略创新，两者从不同角度提升了预训练的效率与效果。

### 实践启示

这批研究为实际的大模型应用开发提供了极具价值的借鉴。**对于垂直领域应用（如企业知识库、专业工具）**，应优先关注**专业化预训练（SPT）** 方法。其核心启示是：不要将宝贵的领域数据仅仅留到最后一步微调，而应将其融入更早期的训练中，这能以更低的总体计算成本获得更优的领域性能与通用性平衡。**对于追求模型高阶推理能力（如代码生成、数学解题）**，则应借鉴**PRISM框架**，考虑在标准预训练和RL之间，增加一个针对性的中期训练阶段，这比单纯调整RL策略有效得多。

**具体建议**：在启动一个领域模型项目时，可以规划一个“轻量级SPT”流程：收集领域数据后，从一个通用基础模型开始，用“通用数据 + 重复的领域数据”混合进行继续预训练，然后再进行指令微调或RLHF。**关键注意事项**：实施SPT时，需通过其过拟合缩放定律或实验，谨慎确定领域数据的重复比例，避免过度偏向领域而损害模型基础。同时，架构创新如PolyGLU（动态激活路由）和Krause Attention（局部化稀疏注意力）虽然新颖，但尚未经过大规模验证，建议在充分评估其稳定性和兼容性后再考虑引入。

---

## 📄 论文列表（16 篇）

### [The Finetuner's Fallacy: When to Pretrain with Your Finetuning Data](https://arxiv.org/abs/2603.16177)

**作者**: Baek, Monti, Schwab, Abbas, Adiga 等 34 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16177) · [PDF](https://arxiv.org/pdf/2603.16177)  \| [📖 全文分析](paper_2603.16177.md)  
**评分**: 8.86  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 8.0）

> 本文由来自Meta GenAI、Stanford、MIT、CMU等知名机构的研究团队提出了一种名为'专业化预训练'（SPT）的新方法，用于解决模型在数据稀缺的垂直领域进行微调时面临的过拟合和遗忘通用知识的问题。该方法通过在预训练阶段重复使用领域数据，显著提升了模型在特定领域的性能，同时保持了通用能力，并推导了过拟合缩放定律来指导实践。论文在三个专业领域（化学、音乐、数学证明）上进行了充分验证，实验设计严谨，具有重要的实践指导意义。


### [PRISM: Demystifying Retention and Interaction in Mid-Training](https://arxiv.org/abs/2603.17074)

**作者**: Runwal, Agrawal, Roy, Panda  
**链接**: [arXiv](https://arxiv.org/abs/2603.17074) · [PDF](https://arxiv.org/pdf/2603.17074)  \| [📖 全文分析](paper_2603.17074.md)  
**评分**: 8.71  （novelty: 8.0 | method: 9.0 | evidence: 9.5 | clarity: 7.5）

> 本文对大型语言模型的中期训练设计选择进行了全面的实证研究，提出了PRISM框架。研究通过控制实验，在多个基础模型、架构类型和参数规模上验证了中期训练对数学、代码和科学推理能力的显著提升效果，并揭示了中期训练与强化学习之间的机制差异。论文实验设计严谨，证据充分，为语言模型训练流程提供了重要的实践指导。


### [Directional Routing in Transformers](https://arxiv.org/abs/2603.14923)

**作者**: Taylor  
**链接**: [arXiv](https://arxiv.org/abs/2603.14923) · [PDF](https://arxiv.org/pdf/2603.14923)  \| [📖 全文分析](paper_2603.14923.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为'定向路由'的轻量级机制，用于增强Transformer注意力头之间的协调。该方法通过一个共享路由器控制每个注意力头的学习抑制方向，仅增加3.9%的参数成本。实验表明，该机制成为模型的主导计算路径，对事实回忆和归纳任务至关重要，并能显著降低困惑度。论文通过机制可解释性方法深入分析了其工作原理，发现模型在没有显式压力的情况下自组织为两种机制：早期层的领域自适应路由和后期层的固定句法剪枝。作者信息有限，仅显示为'Taylor'，未明确提及知名机构。


### [Why Grokking Takes So Long: A First-Principles Theory of Representational Phase Transitions](https://arxiv.org/abs/2603.13331)

**作者**: Khanh, Hoa, Trung, Duc  
**链接**: [arXiv](https://arxiv.org/abs/2603.13331) · [PDF](https://arxiv.org/pdf/2603.13331)  \| [📖 全文分析](paper_2603.13331.md)  
**评分**: 8.50  （novelty: 9.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文针对深度学习训练中观察到的'顿悟'（grokking）现象——即模型在完美记忆训练数据后很久才突然泛化的现象，提出了首个基于第一性原理的理论解释。论文建立了'顿悟延迟'的标度律，并通过293个训练实验验证了理论预测。作者团队未在知名机构列表中明确标注，因此省略背景介绍。


### [Genomic Next-Token Predictors are In-Context Learners](https://arxiv.org/abs/2511.12797)

**作者**: Breslow, Mishra, Revsine, Schatz, Liu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.12797) · [PDF](https://arxiv.org/pdf/2511.12797)  \| [📖 全文分析](paper_2511.12797.md)  
**评分**: 8.50  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.0）

> 本文探讨了上下文学习能力是否能在非语言序列领域通过大规模预测训练自然涌现这一基础问题。研究团队将基因组序列作为替代的符号领域，研究了Evo2基因组模型，该模型主要训练于下一个核苷酸预测任务，规模与中型语言模型相当。研究开发了一个包含符号推理任务的受控实验框架，这些任务以语言和基因组形式实例化，从而能够直接比较基因组模型和语言模型的上下文学习能力。结果表明，基因组模型与语言模型类似，随着上下文演示数量的增加，在模式归纳方面表现出对数线性增益。据我们所知，这是首次在基因组序列中观察到自然涌现的上下文学习的证据，支持了上下文学习是大规模预测建模在丰富数据上的结果的假设。这些发现将涌现的元学习能力扩展到了语言之外，指向了一种统一的、与模态无关的上下文学习观点。


### [PolyGLU: State-Conditional Activation Routing in Transformer Feed-Forward Networks](https://arxiv.org/abs/2603.13347)

**作者**: Medeiros  
**链接**: [arXiv](https://arxiv.org/abs/2603.13347) · [PDF](https://arxiv.org/pdf/2603.13347)  \| [📖 全文分析](paper_2603.13347.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种新颖的Transformer前馈网络激活路由机制PolyGLU，灵感来源于生物神经系统中多种神经递质的分工。该方法通过可学习的静态偏好和输入条件门控，使每个神经元能在K=4个激活函数间动态路由，训练过程端到端且收敛后展现出确定性的路由选择模式。实验表明，该方法仅增加0.23%的参数开销，在远少于基准模型的数据量下，仍能取得其62-89%的性能。所有代码、权重和训练设施均已开源。


### [Krause Synchronization Transformers](https://arxiv.org/abs/2602.11534)

**作者**: Liu, Yue, Welling, Song  
**链接**: [arXiv](https://arxiv.org/abs/2602.11534) · [PDF](https://arxiv.org/pdf/2602.11534)  \| [📖 全文分析](paper_2602.11534.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为Krause Attention的新型注意力机制，该机制受有界置信度共识动力学启发，旨在解决传统Transformer中全局归一化softmax权重导致的表示坍缩和注意力汇问题。通过将基于相似性的全局聚合替换为基于距离的、局部化的、选择性稀疏的交互，该方法促进了结构化的局部同步而非全局混合，并将计算复杂度从序列长度的二次方降低到线性。实验在视觉（ViT on CIFAR/ImageNet）、自回归生成（MNIST/CIFAR-10）和大语言模型（Llama/Qwen）等多个领域和任务上验证了其有效性和计算效率。作者团队背景未在提供信息中明确提及知名机构。


### [NerVE: Nonlinear Eigenspectrum Dynamics in LLM Feed-Forward Networks](https://arxiv.org/abs/2603.06922)

**作者**: Jha, Reagen  
**链接**: [arXiv](https://arxiv.org/abs/2603.06922) · [PDF](https://arxiv.org/pdf/2603.06922)  \| [📖 全文分析](paper_2603.06922.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为NerVE的统一特征谱框架，用于理解大型语言模型（LLMs）中前馈网络（FFNs）在高维潜在空间中组织和调控信息流的方式。该研究填补了FFN高维动态特性理解不足的空白，通过轻量级、内存高效的特征谱动态跟踪，揭示了非线性、优化器几何形状等对信息流调控的关键机制。方法在多种模型规模、架构和优化器配置下得到验证，并显示出与模型泛化能力相关的稳定特征谱特征，其见解可推广至Transformer以外的架构（如MLP-Mixer），为超越试错的架构和优化器选择提供了可操作的见解。


### [Frayed RoPE and Long Inputs: A Geometric Perspective](https://arxiv.org/abs/2603.18017)

**作者**: Wertheimer, Zhang, Liu, Yin, Wang  
**链接**: [arXiv](https://arxiv.org/abs/2603.18017) · [PDF](https://arxiv.org/pdf/2603.18017)  \| [📖 全文分析](paper_2603.18017.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种新的几何视角来理解RoPE（旋转位置编码）在长输入序列下的失效机制，并基于此提出了RoPE-ID改进方法。论文通过理论和实证分析揭示了注意力机制中关键/查询潜在点云的聚类行为以及“汇令牌”的形成机制，为理解Transformer位置编码提供了新的理论框架。作者团队来自学术界，但未明确标注具体机构。


### [Enhancing LLM Training via Spectral Clipping](https://arxiv.org/abs/2603.14315)

**作者**: Jiang, Semenov, Stich  
**链接**: [arXiv](https://arxiv.org/abs/2603.14315) · [PDF](https://arxiv.org/pdf/2603.14315)  \| [📖 全文分析](paper_2603.14315.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为SPECTRA的通用优化框架，通过谱裁剪技术解决大语言模型训练中的谱范数过大和梯度噪声谱稀疏尖峰问题。论文在LLM预训练实验中展示了该方法能一致提升多种优化器的验证损失，并达到最先进的结果。作者团队来自学术界，但未明确标注具体机构，因此省略团队背景介绍。


### [Low-Dimensional and Transversely Curved Optimization Dynamics in Grokking](https://arxiv.org/abs/2602.16746)

**作者**: Xu  
**链接**: [arXiv](https://arxiv.org/abs/2602.16746) · [PDF](https://arxiv.org/pdf/2602.16746)  \| [📖 全文分析](paper_2602.16746.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文对深度学习中的'Grokking'现象（即模型从记忆到泛化的延迟转变）进行了深入的几何分析。研究通过分析在模运算任务上训练的Transformer的优化动态，发现训练主要在一个低维执行子空间内进行，并观察到横向曲率的增长先于泛化现象的出现。实验设计严谨，因果干预实验增强了结论的可信度。作者Xu未明确标注所属机构，因此省略团队背景介绍。


### [Ablate and Rescue: A Causal Analysis of Residual Stream Hyper-Connections](https://arxiv.org/abs/2603.14833)

**作者**: Peng, Rai, Tseng, Wang, Wu  
**链接**: [arXiv](https://arxiv.org/abs/2603.14833) · [PDF](https://arxiv.org/pdf/2603.14833)  \| [📖 全文分析](paper_2603.14833.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文对多流Transformer架构中的残差流超连接机制进行了首次深入的因果分析，提出了系统性的流消融与恢复框架，能够直接比较推理过程中不同残差流的功能。作者团队未明确标注所属机构，但提供了开源模型（https://huggingface.co/wgpeng/mhc-780m），表明研究具有较好的可复现性。论文在方法创新和实验设计方面表现突出，为理解复杂神经网络架构提供了新的分析工具。


### [Learning to Recall with Transformers Beyond Orthogonal Embeddings](https://arxiv.org/abs/2603.15923)

**作者**: Vural, Bietti, Soltanolkotabi, Wu  
**链接**: [arXiv](https://arxiv.org/abs/2603.15923) · [PDF](https://arxiv.org/pdf/2603.15923)  \| [📖 全文分析](paper_2603.15923.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由加州大学洛杉矶分校（UCLA）和斯坦福大学（Stanford）的研究团队合作完成，作者来自知名高校。论文针对Transformer模型在非正交嵌入下的记忆能力进行了理论分析，填补了现有理论在理想化假设（正交嵌入）与实际情况（随机嵌入）之间的空白。通过分析单层Transformer在简单token检索任务上的训练动态，推导出模型存储容量的显式公式，揭示了样本量、嵌入维度和序列长度之间的乘法依赖关系，并通过数值实验验证了理论结果。


### [Byte-token Enhanced Language Models for Temporal Point Processes Analysis](https://arxiv.org/abs/2502.07139)

**作者**: Kong, Zhang, Liu, Tong, Liu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2502.07139) · [PDF](https://arxiv.org/pdf/2502.07139)  \| [📖 全文分析](paper_2502.07139.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Language-TPP的统一框架，旨在弥合传统时序点过程（TPP）模型与大型语言模型（LLM）之间的鸿沟，以增强对网络事件序列（如用户评论、社交媒体帖子）的建模。其核心创新在于设计了一种新颖的时间编码机制，将连续时间间隔转换为专门的字节令牌，从而能够在不修改TPP特定结构的情况下，直接与标准语言模型架构集成。该方法在多个真实世界网络数据集（电子商务评论、社交媒体、在线问答平台）的TPP基准测试（事件时间预测和类型预测）中取得了最先进的性能，并展示了在提升生成事件描述质量（如ROUGE-L分数）和对齐情感分布方面的新能力。代码已开源。


### [Path-Constrained Mixture-of-Experts](https://arxiv.org/abs/2603.18297)

**作者**: Gu, Likhomanenko, Thilak, Ramapuram, Jaitly  
**链接**: [arXiv](https://arxiv.org/abs/2603.18297) · [PDF](https://arxiv.org/pdf/2603.18297)  \| [📖 全文分析](paper_2603.18297.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由Google Research团队（作者包括Tara Sainath、Likhomanenko等）提出了一种新的稀疏混合专家（MoE）架构优化方法Path-Constrained Mixture-of-Experts（PathMoE）。该方法通过跨层共享路由器参数来约束专家路径空间，解决了传统独立路由导致的统计效率低下问题。在0.9B和16B参数模型上的实验表明，该方法在困惑度和下游任务上均优于传统方法，同时无需辅助负载平衡损失。分析显示，该方法能产生更集中的令牌分组和更好的跨层一致性。


### [When Does Sparsity Mitigate the Curse of Depth in LLMs](https://arxiv.org/abs/2603.15389)

**作者**: Muhtar, Song, Pokutta, Zimmer, Pelleriti 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15389) · [PDF](https://arxiv.org/pdf/2603.15389)  \| [📖 全文分析](paper_2603.15389.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文探讨了稀疏性如何缓解大型语言模型中的深度诅咒问题。研究发现，无论是训练过程中自然产生的隐式稀疏性（如权重衰减导致的权重稀疏、长上下文输入导致的注意力稀疏），还是架构设计引入的显式稀疏性（如分组查询注意力中的键/值共享稀疏性、混合专家模型中的专家激活稀疏性），都能通过调节方差传播来改善深层网络的利用率。论文通过系统的深度扩展实验和针对性的层有效性干预，验证了这一关系，并最终提炼出实用的经验法则，在下游任务上实现了4.6%的准确率提升。作者团队来自学术界，但未明确标注其所属机构为上述知名机构，因此省略团队背景介绍。

