# Multimodal · 2026-03-30 ~ 2026-04-03

**论文数**: 26 篇

---

## 📊 趋势分析

### 研究全貌

本领域的研究方向高度聚焦于**模型能力的深度评估与可靠性提升**，主要可归纳为三个层面：**空间、时间与物理世界理解**、**模型效率与鲁棒性**以及**跨模态对齐与生成**。具体而言，部分研究致力于揭示模型在空间心理建模、时间流向理解等基础认知上的根本缺陷，并提出了相应的解决方案。另一部分研究则关注如何高效、精确地解决特定应用任务，如GUI定位、视觉异常分割和机器人技能学习。当前的热点问题是如何让大型视觉语言模型进行更**可靠、可解释且具备真实世界理解能力**的推理，避免评估幻觉和“海市蜃楼”式的虚假能力。整体研究趋势呈现出从追求通用基准性能向**解决特定瓶颈、构建高质量评估与训练数据、以及开发高效且不依赖闭源模型的训练范式**转变，旨在为构建可信、可落地的多模态智能系统提供坚实支撑。

### 重点方法深度解析

从所有批次中，以下几个工作因其创新性和实用性尤为突出：

**1. 《Molmo2: Open Weights and Data for Vision-Language Models with Video Understanding and Grounding》**
*   **核心创新点**：旨在打破闭源模型在视频理解和像素级定位任务上的垄断，提供了一套**完全开源、不依赖闭源模型蒸馏的完整解决方案**，包括高质量数据集、创新的训练方法和模型架构。
*   **技术细节**：其核心贡献在于发布了一系列高质量视频/多图像数据集，并提出了高效的训练方法，如**消息树编码方案**来组织多模态输入，以及**双向注意力机制**和**新颖的令牌权重策略**。这些数据和方法均未使用闭源模型生成，确保了独立性。
*   **效果验证**：其模型在多项视频理解和定位任务上超越了若干开源模型，甚至在部分任务上超越了顶尖闭源模型。
*   **适用场景**：适用于所有需要**高级视频理解、计数、描述以及精确像素级定位**的应用，如视频内容分析、交互式机器人操作等。

**2. 《MindCube: Spatial Mental Modeling from Limited Views》**
*   **核心创新点**：系统性地揭示了视觉语言模型在**空间心理建模**（即从有限视角想象完整场景）方面的严重缺陷，并提出了有效的“**先建图后推理**”协同方法。
*   **技术细节**：该方法的核心是**联合训练模型生成一个内部认知地图**（如空间布局的文本或结构化描述），然后基于这个地图进行推理，并进一步结合**强化学习**来优化地图的构建与利用。
*   **效果验证**：该方法将模型在专门构建的MindCube基准上的准确率显著提升。
*   **适用场景**：对于需要**空间推理、规划或从局部信息推断全局**的任务至关重要，如增强现实导航、机器人环境理解等。

**3. 《When to Think and When to Look: Uncertainty-Guided Lookback》**
*   **核心创新点**：对流行的“测试时思考”策略进行了批判性分析，提出了一种**无需训练的解码策略**，引导模型在推理过程中适时“回看”图像，以克服因过度思考而产生的幻觉。
*   **技术细节**：该方法结合**不确定性信号**（判断当前推理是否可靠）和**自适应回看提示**，在推理过程中动态决定何时需要重新关注视觉信息。
*   **效果验证**：在需要复杂推理的基准上，该方法在固定模型和计算预算下取得了新的最优性能。
*   **适用场景**：适用于任何使用大型视觉语言模型进行**复杂、多步视觉推理**的场景，能有效提升答案的视觉基础性和准确性。

**4. 《GUI-AIMA: Aligning Intrinsic Multimodal Attention with a Context Anchor for GUI Grounding》**
*   **核心创新点**：针对GUI定位任务，提出了一种**基于注意力对齐的无坐标监督微调框架**，旨在高效激发模型固有的定位潜能。
*   **技术细节**：该方法的关键在于**自适应地计算分块定位信号**，利用模型的查询-视觉注意力矩阵生成对齐目标，采用“坐标无关”的设计。
*   **效果验证**：仅使用相对少量的样本进行训练，其模型在多个GUI定位基准上达到了同类模型中的最优性能，验证了卓越的数据效率。
*   **适用场景**：特别适用于需要将自然语言指令映射到屏幕可操作区域的**计算机使用代理**、**自动化测试**等应用。

这些重点方法之间存在互补关系。Molmo2和MindCube分别从数据和架构层面提升模型对动态视频和静态空间的理解能力。而“不确定性引导回看”和GUI-AIMA则聚焦于推理过程的优化，前者通过动态策略提升复杂推理的可靠性，后者通过轻量微调激发特定任务的精确能力。它们共同指向一个核心目标：构建更可靠、更可控的多模态模型。

### 实践启示

这些研究为多模态大模型的应用开发提供了清晰的路径。**首先，评估先行**：在将模型应用于新领域前，应借鉴相关研究的思路，设计针对性的“压力测试”来暴露模型在特定上下文或物理理解上的短板，警惕评估基准的局限性。**其次，优先选择高效、开源的解决方案**：对于通用视频理解和定位任务，应优先关注基于高质量开源数据构建的模型（如Molmo2）。对于需要精确交互（如GUI操作）或零样本识别（如工业异常）的任务，可借鉴GUI-AIMA和AG-VAS等方法，通过引入引导信号（注意力对齐、语义锚点）来激发模型潜能，避免对海量标注数据的依赖。

**具体建议**：在开发复杂视觉推理应用时，强烈建议采用“不确定性引导回看”这类无需训练的解码策略，能以极低成本提升可靠性。对于机器人等具身智能应用，利用生成式技术构建仿真环境进行预训练和强化学习微调，是平衡泛化能力与任务性能的可行路径。**关键注意事项**包括：1) 选择高效微调方案时，需确保预训练基座模型具备良好的固有注意力质量；2) 构建领域特定应用时，需要注入领域知识来构建结构化的指令或评测数据；3) 在关键应用（如医疗）中，必须设计私有、无文本泄露的评估集，并主动测试模型在缺乏视觉输入时的表现。最佳实践组合是：以开源、高质量的模型和数据为基础，针对具体任务采用高效的微调或推理优化策略，并始终以严谨的评估来校准对模型真实能力的认知。

---

## 📄 论文列表（26 篇）

### [Molmo2: Open Weights and Data for Vision-Language Models with Video Understanding and Grounding](https://arxiv.org/abs/2601.10611)

**作者**: Clark, Zhang, Ma, Park, Salehi 等 21 人  
**链接**: [arXiv](https://arxiv.org/abs/2601.10611) · [PDF](https://arxiv.org/pdf/2601.10611)  \| [📖 全文分析](paper_2601.10611.md)  
**评分**: 9.03  （novelty: 9.5 | method: 9.0 | evidence: 9.5 | clarity: 8.0）

> 本文由华盛顿大学、斯坦福大学、Meta AI、加州大学伯克利分校、艾伦人工智能研究所等知名机构的联合团队提出了一种新的开源视频-语言模型Molmo2，该模型在视频理解和像素级定位任务上取得了突破性进展。论文的核心贡献在于发布了一系列高质量、无需依赖闭源模型生成的新型视频和多图像数据集，并提出了创新的训练方法和模型架构。实验结果表明，Molmo2在多项基准测试中超越了现有的开源模型，并在某些任务上甚至优于Gemini 3 Pro等闭源模型，为开源社区提供了强大的基础模型和训练资源。


### [Scaling Spatial Intelligence with Multimodal Foundation Models](https://arxiv.org/abs/2511.13719)

**作者**: Cai, Wang, Gu, Pu, Xu 等 29 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.13719) · [PDF](https://arxiv.org/pdf/2511.13719)  \| [📖 全文分析](paper_2511.13719.md)  
**评分**: 8.91  （novelty: 8.5 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由商汤科技（SenseTime）研究团队提出，基于其SenseNova系列多模态基础模型（如Qwen3-VL、InternVL3、Bagel），通过构建大规模、系统化的空间智能数据集SenseNova-SI-8M（800万样本），显著提升了多模态基础模型在空间智能方面的能力。该方法在多个空间智能基准测试中取得了领先性能，同时保持了强大的通用多模态理解能力。论文还深入分析了数据缩放效应、涌现的泛化能力、过拟合风险等关键问题，并开源了所有新训练的多模态基础模型。


### [MindCube: Spatial Mental Modeling from Limited Views](https://arxiv.org/abs/2506.21458)

**作者**: Wang, Yin, Zhang, Zhang, Wang 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2506.21458) · [PDF](https://arxiv.org/pdf/2506.21458)  \| [📖 全文分析](paper_2506.21458.md)  
**评分**: 8.76  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由斯坦福大学（Stanford）李飞飞团队与多位研究人员合作，提出了一种名为MindCube的基准测试和系统性方法，用于评估和提升视觉语言模型（VLMs）的空间心理建模能力。该研究通过构建包含21,154个问题的基准数据集，揭示了现有VLMs在从有限视角构建空间心理模型方面的严重不足，并提出了一种“先建图后推理”（map-then-reason）的协同方法，结合强化学习，将模型准确率从37.8%显著提升至61.3%。研究创新性强，实验设计系统且证据充分，为提升AI的空间理解与推理能力提供了重要思路。


### [KARL: Knowledge-Aware Reasoning and Reinforcement Learning for Knowledge-Intensive Visual Grounding](https://arxiv.org/abs/2503.12797)

**作者**: Ma, Ding, Luo, Chen, Guo 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2503.12797) · [PDF](https://arxiv.org/pdf/2503.12797)  \| [📖 全文分析](paper_2503.12797.md)  
**评分**: 8.73  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由清华大学自然语言处理与社会人文计算实验室（THUNLP）团队提出了一种针对知识密集型视觉定位（KVG）任务的新训练范式KARL。该方法通过构建知识引导的推理数据，并引入知识感知的强化学习框架，有效弥合了多模态大语言模型在内部知识与定位预测之间的知识-定位鸿沟。论文创新性地提出了KVG-Bench基准测试，在10个领域上进行了系统评估，实验结果表明该方法在跨领域泛化方面表现优异。


### [A Comprehensive Information-Decomposition Analysis of Large Vision-Language Models](https://arxiv.org/abs/2603.29676)

**作者**: Xiu, Luo, Nakayama  
**链接**: [arXiv](https://arxiv.org/abs/2603.29676) · [PDF](https://arxiv.org/pdf/2603.29676)  \| [📖 全文分析](paper_2603.29676.md)  
**评分**: 8.71  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 7.5）

> 本文提出了一种基于部分信息分解（PID）的新颖框架，用于定量分析大型视觉语言模型（LVLMs）的内部决策过程。该方法超越了传统的仅基于准确率的评估，为理解LVLMs的信息处理机制提供了定量视角。论文实验设计全面，覆盖了26个模型、4个数据集，并从广度、深度和时间三个维度进行分析，代码和数据已开源。


### [When to Think and When to Look: Uncertainty-Guided Lookback](https://arxiv.org/abs/2511.15613)

**作者**: Bi, Bellos, Guo, Li, Huang 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.15613) · [PDF](https://arxiv.org/pdf/2511.15613)  \| [📖 全文分析](paper_2511.15613.md)  
**评分**: 8.40  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 8.5）

> 本文由来自知名机构的研究团队（作者来自多个知名机构，包括InternVL和Qwen3-VL相关团队，这些团队在视觉语言模型领域具有重要影响力）提出了一种新的训练无关的解码策略——不确定性引导回看（uncertainty-guided lookback），用于改进大型视觉语言模型（LVLMs）的测试时思考（test-time thinking）过程。该方法通过分析思考链中的视觉接地（visual grounding）问题，结合不确定性信号和自适应回看提示，在多个基准测试上取得了显著性能提升，并建立了新的最先进水平。


### [ResAdapt: Adaptive Resolution for Efficient Multimodal Reasoning](https://arxiv.org/abs/2603.28610)

**作者**: Liao, Jiang, Hao, Tan, He 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.28610) · [PDF](https://arxiv.org/pdf/2603.28610)  \| [📖 全文分析](paper_2603.28610.md)  
**评分**: 8.39  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为ResAdapt的自适应分辨率框架，用于解决多模态大语言模型在处理高分辨率视觉输入时面临的视觉令牌增长问题。该方法通过轻量级分配器在编码前动态调整每帧图像的视觉预算，在保持模型骨干不变的同时显著提升了处理效率。在视频问答、时序定位和图像推理等任务上，ResAdapt在相同视觉预算下支持多达16倍帧数，并带来超过15%的性能提升。论文实验设计严谨，代码已开源。


### [MIRAGE: The Illusion of Visual Understanding](https://arxiv.org/abs/2603.21687)

**作者**: Asadi, O'Sullivan, Cao, Nedaee, Rajabalifardi 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.21687) · [PDF](https://arxiv.org/pdf/2603.21687)  \| [📖 全文分析](paper_2603.21687.md)  
**评分**: 8.36  （novelty: 9.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文揭示了多模态AI系统中一个关键但被忽视的问题：视觉语言模型在缺乏真实图像输入时，仍能生成详细的图像描述和推理过程，作者称之为'海市蜃楼推理'。研究发现前沿模型在通用和医学多模态基准测试中，即使没有图像输入也能获得高分，这挑战了现有评估方法的有效性。作者提出了B-Clean解决方案，用于实现更公平、基于视觉的评估。论文作者来自学术界，但未明确标注知名机构，因此省略团队背景介绍。


### [Bias Is a Subspace, Not a Coordinate: A Geometric Rethinking of Post-hoc Debiasing in Vision-Language Models](https://arxiv.org/abs/2511.18123)

**作者**: Zhao, Li, Shen, Qiu, Xu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.18123) · [PDF](https://arxiv.org/pdf/2511.18123)  \| [📖 全文分析](paper_2511.18123.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种几何原理驱动的后处理去偏框架SPD（子空间投影去偏），用于解决视觉语言模型中的社会人口统计偏见问题。论文创新性地将偏见识别为线性子空间而非单个坐标，通过识别并移除线性可解码的偏见子空间，同时重新插入中性均值分量以保持语义保真度。在零样本分类、文本到图像检索和图像生成等多个任务上的实验表明，该方法在四个公平性指标上平均提升18.5%，同时任务性能损失最小。作者团队未明确标注所属机构，但根据姓名推断可能来自中国高校或研究机构。


### [Spectral-Aware Text-to-Time Series Generation with Billion-Scale Multimodal Meteorological Data](https://arxiv.org/abs/2603.27135)

**作者**: Zhang  
**链接**: [arXiv](https://arxiv.org/abs/2603.27135) · [PDF](https://arxiv.org/pdf/2603.27135)  \| [📖 全文分析](paper_2603.27135.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种用于文本到时间序列生成的统一框架，特别针对气象学领域。该研究的主要贡献包括：1）构建了大规模、物理基础的多模态气象数据集MeteoCap-3B，通过多智能体协作标注（MACC）流程生成信息密集且物理一致的专家级标注；2）提出了MTransformer模型，这是一种基于扩散的模型，通过光谱提示生成器将文本描述映射到多波段光谱先验，并利用频率感知注意力指导生成，从而实现对复杂大气动力学的精确语义控制。实验表明，该方法在真实世界基准测试中实现了最先进的生成质量、准确的跨模态对齐、强大的语义可控性，并在数据稀疏和零样本设置的下游预测任务中取得了显著提升。此外，在通用时间序列基准测试上的结果表明，该框架能够推广到气象学以外的领域。


### [Science-T2I: Addressing Scientific Illusions in Image Synthesis](https://arxiv.org/abs/2504.13129)

**作者**: Li, Chai, Fu, Xu, Xie  
**链接**: [arXiv](https://arxiv.org/abs/2504.13129) · [PDF](https://arxiv.org/pdf/2504.13129)  \| [📖 全文分析](paper_2504.13129.md)  
**评分**: 8.29  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文针对当前图像生成模型在科学合理性方面的不足，提出了ScienceT2I数据集、SciScore评估指标以及一个两阶段对齐框架。工作系统性地构建了覆盖16个科学领域的对抗性评测基准，并验证了通过针对性数据和模型对齐可以显著提升生成图像的科学合理性。作者团队未明确标注所属机构，但工作质量较高，具有重要学术价值。


### [UniGame: Turning a Unified Multimodal Model Into Its Own Adversary](https://arxiv.org/abs/2511.19413)

**作者**: Su, Lu, Chen, Li, Wang  
**链接**: [arXiv](https://arxiv.org/abs/2511.19413) · [PDF](https://arxiv.org/pdf/2511.19413)  \| [📖 全文分析](paper_2511.19413.md)  
**评分**: 8.29  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为UniGame的自对抗后训练框架，旨在解决统一多模态模型中理解与生成任务之间的内在不一致性问题。该方法通过在共享令牌接口应用轻量级扰动器，使生成分支主动挑战脆弱的理解能力，将模型自身转化为其对抗者。实验表明，该方法在一致性、理解能力、生成质量以及分布外和对抗鲁棒性方面均取得了显著提升。框架与架构无关，仅引入少于1%的额外参数，并与现有后训练方法互补。代码已开源。


### [EVA: Efficient Reinforcement Learning for End-to-End Video Agent](https://arxiv.org/abs/2603.22918)

**作者**: Zhang, Wang, Wang, Tang, Zheng 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.22918) · [PDF](https://arxiv.org/pdf/2603.22918)  \| [📖 全文分析](paper_2603.22918.md)  
**评分**: 8.19  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种基于强化学习的高效端到端视频智能体框架EVA，通过创新的'总结-规划-行动-反思'推理机制，实现了查询驱动的自适应视频理解。该方法在六个视频理解基准测试中取得了显著提升，相比通用多模态大语言模型基线提高了6-12%，相比现有自适应智能体方法进一步提升了1-3%。作者团队来自中国知名高校和研究机构（如清华大学、北京大学、上海人工智能实验室等），在计算机视觉和人工智能领域具有深厚的研究背景。


### [Dynin-Omni: Omnimodal Unified Large Diffusion Language Model](https://arxiv.org/abs/2604.00007)

**作者**: Kim, Kim, Hong, Lee, Hyeon 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.00007) · [PDF](https://arxiv.org/pdf/2604.00007)  \| [📖 全文分析](paper_2604.00007.md)  
**评分**: 8.16  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Dynin-Omni的新型全模态基础模型，首次将掩码扩散范式应用于统一文本、图像、语音和视频的理解与生成任务。该模型采用共享离散令牌空间和双向上下文迭代精炼的架构，避免了传统自回归或组合式统一模型的局限性。通过在19个多模态基准测试上的评估，Dynin-Omni在语言推理、图像生成与编辑、视频理解、语音识别与合成等多个领域均展现出优异性能，证明了掩码扩散作为统一建模范式的潜力。作者团队来自韩国科学技术院（KAIST）和Naver公司，结合了顶尖学术机构与工业界的研究力量。


### [ChartNet: A Million-Scale, High-Quality Multimodal Dataset for Robust Chart Understanding](https://arxiv.org/abs/2603.27064)

**作者**: Kondic, Li, Joshi, Sanchez, Wiesel 等 27 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.27064) · [PDF](https://arxiv.org/pdf/2603.27064)  \| [📖 全文分析](paper_2603.27064.md)  
**评分**: 8.11  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由IBM Research、麻省理工学院（MIT）、哥伦比亚大学等知名研究机构组成的团队提出了一种名为ChartNet的大规模、高质量多模态数据集，专门用于提升图表理解和推理能力。该研究通过创新的代码引导合成流程生成了150万个多样化的图表样本，覆盖24种图表类型和6种绘图库，为多模态模型提供了精细的跨模态对齐监督数据。实验表明，在ChartNet上微调能持续提升模型在多个基准测试上的性能，证明了其作为大规模监督数据的实用性。


### [VectorGym: A Multitask Benchmark for SVG Code Generation, Sketching, and Editing](https://arxiv.org/abs/2603.29852)

**作者**: Rodriguez, Zhang, Puri, Zhang, Pramanik 等 16 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.29852) · [PDF](https://arxiv.org/pdf/2603.29852)  \| [📖 全文分析](paper_2603.29852.md)  
**评分**: 8.11  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由ServiceNow研究院团队（作者来自知名企业AI研究部门）提出了VectorGym，这是一个全面的可缩放矢量图形（SVG）基准测试套件，涵盖了从文本和草图生成、复杂编辑到视觉理解的多项任务。该工作通过引入包含人类专家标注的真实数据集、多任务强化学习方法以及基于渲染的评估指标，显著提升了SVG相关任务的评估标准。实验表明，其训练的中等规模模型在多项任务上超越了更大规模的模型，并匹配了GPT-4o的性能。


### [Reflect to Inform: Boosting Multimodal Reasoning via Information-Gain-Driven Verification](https://arxiv.org/abs/2603.26348)

**作者**: Lv, Liu, Tang, Yuan, Zhou 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.26348) · [PDF](https://arxiv.org/pdf/2603.26348)  \| [📖 全文分析](paper_2603.26348.md)  
**评分**: 8.07  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为视觉再检查（Visual Re-Examination, VRE）的自演进训练框架，旨在解决多模态大语言模型（MLLMs）在生成长文本时逐渐偏离图像证据、依赖文本先验从而导致幻觉的问题。该方法通过驱动模型进行基于信息增益的视觉自省，无需额外视觉输入即可提升推理的准确性和感知可靠性。实验表明，该方法在多个多模态基准测试上有效减少了幻觉，尤其在长链推理场景中表现突出。作者团队来自中国科学技术大学（USTC），在计算机视觉和人工智能领域具有扎实的研究基础。


### [DIAL: Decoupling Intent and Action via Latent World Modeling for End-to-End VLA](https://arxiv.org/abs/2603.29844)

**作者**: Chen, Ge, Zhou, Ding, Ge 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.29844) · [PDF](https://arxiv.org/pdf/2603.29844)  \| [📖 全文分析](paper_2603.29844.md)  
**评分**: 8.07  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为DIAL的新型视觉-语言-动作（VLA）模型框架，旨在解决现有端到端VLA模型未能充分利用预训练视觉语言模型（VLM）高级决策潜力、并导致训练不稳定的问题。该框架通过引入一个可微分的潜在意图瓶颈，将高级决策（System-2）与低级运动执行（System-1）解耦，并采用两阶段训练范式确保优化稳定性。在RoboCasa GR1 Tabletop基准测试中，DIAL以远少于先前方法的演示数据量取得了新的最优性能，并在人形机器人上展示了强大的零样本泛化能力。


### [How to Train Your Long-Context Visual Document Model](https://arxiv.org/abs/2602.15257)

**作者**: Veselka  
**链接**: [arXiv](https://arxiv.org/abs/2602.15257) · [PDF](https://arxiv.org/pdf/2602.15257)  \| [📖 全文分析](paper_2602.15257.md)  
**评分**: 8.05  （novelty: 8.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文针对长上下文视觉文档理解这一重要挑战，首次开展了大规模、系统性的训练方法研究，将视觉语言模型的上下文长度扩展至344K。研究填补了当前开源模型训练方法不透明的空白，通过系统实验揭示了多个关键发现，并在MMLongBenchDoc基准上取得了最先进的性能。作者团队Veselka未在知名机构列表中，故省略背景介绍。


### [SOLE-R1: Video-Language Reasoning as the Sole Reward for On-Robot Reinforcement Learning](https://arxiv.org/abs/2603.28730)

**作者**: Schroeder, Weng, Schmeckpeper, Rosen, Hart 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.28730) · [PDF](https://arxiv.org/pdf/2603.28730)  \| [📖 全文分析](paper_2603.28730.md)  
**评分**: 8.00  （novelty: 9.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为SOLE-R1的视频语言推理模型，专门设计作为机器人强化学习的唯一奖励信号。该方法通过时空链式思维推理生成密集的任务进度估计，实现了在无地面真实奖励、成功指示、演示或任务特定调优情况下的零样本在线强化学习。在四个不同的仿真环境和真实机器人场景中，SOLE-R1成功完成了24个未见任务，显著优于包括GPT-5和Gemini-3-Pro在内的强视觉语言奖励模型，并展现出更强的抗奖励攻击鲁棒性。


### [ORIC: Benchmarking Object Recognition under Contextual Incongruity in Large Vision-Language Models](https://arxiv.org/abs/2509.15695)

**作者**: Li, Ling, Zhou, Gong, BÄ±yÄ±k 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.15695) · [PDF](https://arxiv.org/pdf/2509.15695)  \| [📖 全文分析](paper_2509.15695.md)  
**评分**: 8.00  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种评估大型视觉语言模型在上下文不一致场景下物体识别能力的框架ORIC，通过构建不一致的物体-上下文对来系统研究LVLMs的识别失败模式。作者团队来自学术界，但未明确标注具体机构。该研究创新性地从不确定性角度分析LVLMs的识别问题，并提供了可复现的基准数据集和训练数据，对提升LVLMs的鲁棒性具有重要价值。


### [GUI-AIMA: Aligning Intrinsic Multimodal Attention with a Context Anchor for GUI Grounding](https://arxiv.org/abs/2511.00810)

**作者**: Zhou, Lai, Tan, Kil, Zhu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.00810) · [PDF](https://arxiv.org/pdf/2511.00810)  \| [📖 全文分析](paper_2511.00810.md)  
**评分**: 7.88  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为GUI-AIMA的基于注意力机制的无坐标监督微调框架，用于图形用户界面（GUI）的视觉定位任务。该方法通过将多模态大语言模型（MLLM）固有的注意力与分块定位信号对齐，并引入可插拔的放大阶段，实现了高效的数据利用和卓越的性能。在多个基准数据集（ScreenSpot-Pro, ScreenSpot-v2, OSWorld-G, MMBench-GUI-LUI-Vision）上，其3B参数模型取得了当前最优结果。论文实验设计严谨，代码已开源，验证了方法的有效性。


### [Which Way Does Time Flow? A Psychophysics-Grounded Evaluation for Vision-Language Models](https://arxiv.org/abs/2510.26241)

**作者**: Matta, Pereira, Han, Cheng, Kitazawa  
**链接**: [arXiv](https://arxiv.org/abs/2510.26241) · [PDF](https://arxiv.org/pdf/2510.26241)  \| [📖 全文分析](paper_2510.26241.md)  
**评分**: 7.85  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种新颖的评估范式，通过构建一个基于心理物理学的基准测试AoT-PsyPhyBENCH，系统性地揭示了当前主流视觉语言模型在理解视频时间流向（箭头）这一基础能力上的严重不足。研究指出，模型在人类几乎能瞬间判断的物理不可逆过程（如自由落体、扩散/爆炸）和因果性手动动作上表现远逊于人类，这凸显了当前多模态系统在时间连续性和因果理解方面存在根本性缺陷。作者团队（Matta, Pereira, Han, Cheng, Kitazawa）来自学术界，其研究聚焦于多模态人工智能的基础能力评估与理解。


### [AG-VAS: Anchor-Guided Zero-Shot Visual Anomaly Segmentation with Large Multimodal Models](https://arxiv.org/abs/2603.01305)

**作者**: Qu, Tao, Bao, Wang, Qu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01305) · [PDF](https://arxiv.org/pdf/2603.01305)  \| [📖 全文分析](paper_2603.01305.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为AG-VAS（Anchor-Guided Visual Anomaly Segmentation）的新框架，用于解决基于大型多模态模型（LMMs）的零样本视觉异常分割（ZSAS）任务中的核心挑战。该方法通过引入可学习的语义锚点令牌（[SEG]、[NOR]、[ANO]）建立统一的锚点引导分割范式，并设计了语义-像素对齐模块（SPAM）和锚点引导掩码解码器（AGMD）来增强跨模态对齐与精确定位。此外，作者构建了大规模指令数据集Anomaly-Instruct20K。在六个工业和医学基准测试上的广泛实验表明，AG-VAS在零样本设置下取得了最先进的性能。


### [Scaling Sim-to-Real Reinforcement Learning for Robot VLAs with Generative 3D Worlds](https://arxiv.org/abs/2603.18532)

**作者**: Choi, Wang, Su, Xu  
**链接**: [arXiv](https://arxiv.org/abs/2603.18532) · [PDF](https://arxiv.org/pdf/2603.18532)  \| [📖 全文分析](paper_2603.18532.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种创新的机器人视觉语言动作（VLA）模型强化学习微调方法，通过利用3D世界生成模型和语言驱动的场景设计器，生成数百个多样化的交互场景，实现了可扩展的并行策略学习。该方法在模拟环境中将成功率从9.7%提升至79.8%，并实现了1.25倍的任务完成速度提升；通过领域随机化技术，成功实现了从模拟到现实的迁移，将真实世界成功率从21.7%提升至75%，速度提升1.13倍。作者团队来自学术界，但具体机构信息未在摘要中明确说明。


### [Hallucination-aware intermediate representation edit in large vision-language models](https://arxiv.org/abs/2603.29405)

**作者**: Suo, Zhang, Zhang, Ma, Wang 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.29405) · [PDF](https://arxiv.org/pdf/2603.29405)  \| [📖 全文分析](paper_2603.29405.md)  
**评分**: 7.74  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种针对大型视觉语言模型幻觉问题的新框架HIRE，通过动态检测幻觉表示并对其进行编辑来消除幻觉。该方法在现有基准测试中取得了最先进的性能，同时仅需最小的额外计算成本。论文实验充分，代码已开源，具有较好的实用价值。

