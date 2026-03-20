# Pretraining · 2026-03-02 ~ 2026-03-06

**论文数**: 15 篇

---

## 📊 趋势分析

### 研究全貌
本批次预训练领域的研究论文呈现出多元化且深入的特点，主要研究方向可归纳为几个方面：**训练优化与稳定性**（如优化器设计、训练策略）、**数据工程**（如数据筛选、重写与混合）、**模型架构与缩放律**（如MoE稀疏性、缩放定律分析、新型架构）以及**能力扩展与理解**（如时间序列理解、推理能力涌现、模型内部机制）。当前的热点问题聚焦于如何突破现有训练范式与缩放定律的瓶颈，以更高效、更稳定地训练出具备强大泛化与推理能力的大模型。整体研究趋势显示出从“大力出奇迹”的粗放式规模扩张，向精细化、理论指导下的高效训练与能力定向塑造转变，同时更加关注模型的可解释性与特定领域（如企业应用）的实用性。

### 重点方法深度解析
从这批论文中，以下几个工作因其深刻的洞察力、显著的实用价值或新颖的视角而尤为突出：

**1. 《What Scales in Cross-Entropy Scaling Law?》 [URL](https://arxiv.org/abs/2510.04067)**
*   **核心创新点**：针对交叉熵缩放定律在极大模型规模下失效的关键问题，提出了一个开创性的理论解释。论文将交叉熵分解为三个组成部分：误差熵、自对齐和置信度，并发现只有**误差熵**遵循稳健的幂律缩放规律。
*   **技术细节**：通过理论推导和实证分析，作者证明了误差熵主导了小模型的损失，但随着模型规模增大，其占比下降，而自对齐和置信度项基本不变。这解释了为何传统缩放定律在小规模时准确，在大规模时失效。该方法为分析训练动态提供了新的、更精确的数学工具。
*   **效果验证**：在跨越五个数量级的32个模型上进行广泛实验，验证了误差熵缩放律的普适性和准确性，为预测超大模型性能提供了更可靠的依据。
*   **适用场景**：该方法对于指导超大语言模型的训练预算规划、架构设计决策以及理解模型优化本质具有根本性的意义，是模型缩放研究的基石性工作。

**2. 《Progressive Residual Warmup for Language Model Pretraining》 [URL](https://arxiv.org/abs/2603.05369)**
*   **核心创新点**：提出了一种简单而有效的训练策略——渐进残差预热（ProRes），旨在提升Transformer模型预训练的稳定性和收敛速度。其核心思想是让浅层网络先充分学习，深层网络随后逐步参与。
*   **技术细节**：为每一层的残差连接引入一个从0逐渐增加到1的标量系数（预热系数）。关键设计是让更深层的预热步数更长，即浅层系数先达到1，深层系数后达到1。这模拟了一个“由浅入深”的学习过程，避免了深层网络在早期训练不稳定时引入过大噪声。
*   **效果验证**：在不同规模的模型、归一化和初始化方案下的预训练实验表明，ProRes不仅能稳定训练、防止损失尖峰，还能带来更快的收敛速度、更强的泛化能力和更好的下游任务性能。
*   **适用场景**：这是一种通用、轻量级的训练技巧，几乎可以无成本地集成到任何基于Transformer架构的预训练流程中，尤其对于训练深层或超大模型时缓解不稳定性问题具有直接的应用价值。

**3. 《MobileLLM-R1: Exploring the Limits of Sub-Billion Language Model Reasoners with Open Training Recipes》 [URL](https://arxiv.org/abs/2509.24945)**
*   **核心创新点**：挑战了“推理能力需要海量训练数据”的固有认知，证明了通过精心筛选和重采样高质量数据，可以在远少于主流模型的数据量（约2T tokens）上，训练出具备强大推理能力的亚十亿参数模型。
*   **技术细节**：作者设计了数据质量评估指标，从开源数据集中精心筛选出对推理有益的部分，并通过重采样构建了高质量训练集。其训练流程包括预训练和成熟的后训练过程。
*   **效果验证**：训练出的MobileLLM-R1系列模型（如950M参数）在多项推理基准（如AIME）上大幅超越了使用完全开源数据训练的同类模型，甚至能与使用数十倍私有数据训练的模型（如Qwen3-0.6B）相媲美。
*   **适用场景**：为资源受限（计算、数据）环境下的高效模型训练提供了清晰的路径和实证支持，对开发边缘设备上的轻量级推理模型、降低模型训练成本具有重大启示。

**4. 《Prior-based Noisy Text Data Filtering: Fast and Strong Alternative For Perplexity》 [URL](https://arxiv.org/abs/2509.18577)**
*   **核心创新点**：提出了一种基于词频先验的快速数据过滤方法，作为传统基于模型困惑度（PPL）过滤的高效替代方案，解决了PPL方法计算成本高、对噪声数据不可靠的问题。
*   **技术细节**：该方法利用语料库级别的词频统计计算每个词元的先验概率。过滤时，仅需计算文档中所有词元先验的均值和标准差，无需任何模型前向传播。均值反映文档的总体“常见度”，标准差反映用词多样性。
*   **效果验证**：在20个下游任务基准测试中取得了最高的平均性能，同时将过滤时间成本降低了超过1000倍。该方法还被证明适用于代码、数学等多语言和符号语言场景。
*   **适用场景**：特别适合在大规模预训练数据清洗的初期阶段快速筛除低质量文档，能极大加速数据准备流程，降低总体训练成本，是一种极具工程实用价值的技术。

### 实践启示
这些研究为大模型的应用开发与训练实践提供了多方面的借鉴。**对于追求训练效率与稳定性的团队**，应优先关注**渐进残差预热（ProRes）** 和**基于先验的数据过滤**这类“即插即用”型技术，它们能以极低的代价带来显著的训练速度提升和稳定性改善。**对于资源受限或专注于轻量化场景的开发者**，**MobileLLM-R1**的工作路线图极具参考价值，其核心在于“数据质量优于数据数量”，通过精心设计的数据配方而非盲目堆叠数据量来激发小模型潜力。**对于进行前沿模型研发或超大规模训练的机构**，**交叉熵分解理论**和**MoE最优稀疏性研究**提供了重要的理论指导，有助于更科学地进行模型规模规划与架构设计。

在落地时需注意：ProRes需要合理设置各层的预热计划表；先验过滤方法的效果依赖于基础词频统计的质量，可能需要对目标领域语料进行适配；小模型高质量训练路线的成功，高度依赖于对任务有益数据的精准定义与筛选能力，这本身是一个需要深入研究的课题。总体而言，本批次研究强调了从“规模驱动”到“效率与质量驱动”的范式转变，精细化、理论化的方法正成为提升大模型实用价值的关键。

---

## 📄 论文列表（15 篇）

### [What Scales in Cross-Entropy Scaling Law?](https://arxiv.org/abs/2510.04067)

**作者**: Yan, Wei, Ai, Liu, Zhan  
**链接**: [arXiv](https://arxiv.org/abs/2510.04067) · [PDF](https://arxiv.org/pdf/2510.04067)  \| [📖 全文分析](paper_2510.04067.md)  
**评分**: 8.86  （novelty: 9.5 | method: 9.0 | evidence: 9.0 | clarity: 8.5）

> 本文针对大语言模型开发中的关键问题——交叉熵缩放定律在极大模型规模下失效的现象，提出了创新的理论解释和解决方案。论文通过理论推导和实证分析，将交叉熵分解为误差熵、自对齐和置信度三个组成部分，并发现只有误差熵遵循稳健的幂律缩放规律。这一发现不仅解释了现有缩放定律的局限性，还建立了更准确的误差熵缩放定律，对大语言模型的训练、理解和未来发展具有重要指导意义。


### [Progressive Residual Warmup for Language Model Pretraining](https://arxiv.org/abs/2603.05369)

**作者**: Chen, Xu, Yin, Chen, Wang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.05369) · [PDF](https://arxiv.org/pdf/2603.05369)  \| [📖 全文分析](paper_2603.05369.md)  
**评分**: 8.71  （novelty: 8.5 | method: 9.0 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为渐进残差预热（Progressive Residual Warmup, ProRes）的新方法，用于提升Transformer架构在大语言模型预训练中的稳定性和收敛速度。该方法通过为每一层的残差连接引入一个从0逐渐增加到1的标量系数，并让更深层的预热步数更长，实现了“浅层先学习，深层后参与”的优化哲学。实验表明，ProRes能有效稳定训练、加速收敛并提升模型在下游任务上的泛化性能。论文代码已开源。


### [MobileLLM-R1: Exploring the Limits of Sub-Billion Language Model Reasoners with Open Training Recipes](https://arxiv.org/abs/2509.24945)

**作者**: Zhao, Chang, Liu, Chang, Wen 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.24945) · [PDF](https://arxiv.org/pdf/2509.24945)  \| [📖 全文分析](paper_2509.24945.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由Meta（Facebook）人工智能研究团队提出，该团队在语言模型领域具有深厚的技术积累和影响力。论文挑战了当前大语言模型领域关于推理能力需要海量训练数据的普遍假设，通过精心设计和筛选高质量训练数据，证明了在仅使用约2T高质量token的情况下，也能训练出具备强大推理能力的亚十亿参数模型。这项工作为资源受限环境下的高效模型训练提供了新的思路和实证支持。


### [Optimal Sparsity of Mixture-of-Experts Language Models for Reasoning Tasks](https://arxiv.org/abs/2508.18672)

**作者**: Nakamura, Ishikawa, Kawamura, Okamoto, Nohara 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2508.18672) · [PDF](https://arxiv.org/pdf/2508.18672)  \| [📖 全文分析](paper_2508.18672.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文研究了混合专家（MoE）语言模型在推理任务中的最优稀疏性问题，提出了基于活跃FLOPs和总标记数/参数比（TPP）的联合优化框架。研究揭示了MoE稀疏性对记忆能力和推理能力的不同影响规律，修正了传统的计算最优缩放理论。论文实验设计严谨，代码和数据开源，对MoE模型的实际部署具有重要指导意义。


### [The Token Tax: Systematic Bias in Multilingual Tokenization](https://arxiv.org/abs/2509.05486)

**作者**: Lundin, Zhang, Karim, Louzan, Wei 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.05486) · [PDF](https://arxiv.org/pdf/2509.05486)  \| [📖 全文分析](paper_2509.05486.md)  
**评分**: 8.57  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文系统性地研究了多语言分词中的效率不平等问题，提出了'Token Tax'（令牌税）的概念，揭示了分词效率对形态复杂、低资源语言的结构性不利影响。论文通过评估10个大语言模型在AfriMMLU数据集（包含16种非洲语言的9000个多项选择题）上的表现，发现'生育率'（每个单词对应的令牌数）能可靠预测模型准确率，且高生育率始终与低准确率相关。研究还发现推理模型（如DeepSeek、o1）在高资源和低资源语言上的表现差距小于前代模型。最后，论文将令牌膨胀转化为经济成本分析，指出令牌数翻倍会导致训练成本和时间增加四倍。这些发现为形态感知分词、公平定价和多语言基准测试提供了有力依据。


### [Prior-based Noisy Text Data Filtering: Fast and Strong Alternative For Perplexity](https://arxiv.org/abs/2509.18577)

**作者**: Seo, Kim, Kim, Yeo  
**链接**: [arXiv](https://arxiv.org/abs/2509.18577) · [PDF](https://arxiv.org/pdf/2509.18577)  \| [📖 全文分析](paper_2509.18577.md)  
**评分**: 8.57  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种基于先验的噪声文本数据过滤方法，作为困惑度（PPL）过滤的快速且强大的替代方案。该方法利用语料库级别的词频统计来估计词元先验，通过计算文档中词元先验的均值和标准差进行过滤，无需模型推理，速度比基于PPL的方法快1000倍以上。在20个下游基准测试中取得了最高的平均性能，并展示了其在代码、数学等符号语言以及多语言语料上的适用性。作者团队未明确来自知名机构，故省略背景介绍。


### [Rewriting Pre-Training Data Boosts LLM Performance in Math and Code](https://arxiv.org/abs/2505.02881)

**作者**: Fujii, Tajima, Mizuki, Kawamura, Shimada 等 17 人  
**链接**: [arXiv](https://arxiv.org/abs/2505.02881) · [PDF](https://arxiv.org/pdf/2505.02881)  \| [📖 全文分析](paper_2505.02881.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.5 | clarity: 7.5）

> 本文提出了一种通过系统化重写预训练数据来提升大语言模型在数学和代码任务性能的方法。作者团队来自日本产业技术综合研究所（AIST）、东京大学等机构，具有扎实的研究背景。论文创新性地提出了“转换并保留”的数据增强策略，而非传统的排除式过滤，在两个公开数据集上验证了方法的有效性，并开源了数据集、代码和检查点，确保了研究的可复现性。


### [Thoth: Mid-Training Bridges LLMs to Time Series Understanding](https://arxiv.org/abs/2603.01042)

**作者**: Lin, Wang, Wu, Luo, Pei 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01042) · [PDF](https://arxiv.org/pdf/2603.01042)  \| [📖 全文分析](paper_2603.01042.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由清华大学（THUML）团队提出了一种名为Thoth的中期训练方法，旨在为大型语言模型（LLMs）赋予通用时间序列理解能力。该方法通过构建高质量的时间序列-文本对齐语料库（Book-of-Thoth），在LLMs的预训练和下游任务微调之间引入了一个关键的中间阶段（mid-training），实现了任务和领域无关的时序-语言对齐。实验表明，该方法在多个时间序列问答基准上显著超越了基础模型和先进LLMs，并在数据稀缺场景下表现出色。


### [Controlled LLM Training on Spectral Sphere](https://arxiv.org/abs/2601.08393)

**作者**: Xie, Luo, Tang, Hu, Liu 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2601.08393) · [PDF](https://arxiv.org/pdf/2601.08393)  \| [📖 全文分析](paper_2601.08393.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种新的优化器——谱球优化器（SSO），用于解决大规模模型训练中的稳定性问题。该方法通过施加严格的模块级谱约束，实现了与μP理论完全对齐的优化过程，并在多种架构的预训练实验中表现出优于AdamW和Muon的性能。论文实验设计全面，在Dense 1.7B、MoE 8B-A1B和200层DeepNet等模型上进行了验证，并观察到实际稳定性收益，如改进的MoE路由器负载均衡、抑制异常值和严格有界的激活。


### [YuriiFormer: A Suite of Nesterov-Accelerated Transformers](https://arxiv.org/abs/2601.23236)

**作者**: Zimin, Polyanskiy, Rigollet  
**链接**: [arXiv](https://arxiv.org/abs/2601.23236) · [PDF](https://arxiv.org/pdf/2601.23236)  \| [📖 全文分析](paper_2601.23236.md)  
**评分**: 8.43  （novelty: 9.5 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文由麻省理工学院（MIT）和普林斯顿大学（Princeton University）的研究团队提出。作者Zimin、Polyanskiy和Rigollet分别来自MIT和Princeton，在优化理论和机器学习领域具有深厚的研究背景。他们提出了一种将Transformer层解释为优化算法迭代的变分框架，并基于此设计了Nesterov加速的Transformer架构（YuriiFormer）。该方法在TinyStories和OpenWebText数据集上超越了nanoGPT基线，证明了优化理论见解能够转化为实际性能提升。论文创新性强，理论框架扎实，实验验证有效，为Transformer架构设计提供了新的理论视角和实用工具。


### [Train Once, Answer All: Many Pretraining Experiments for the Cost of One](https://arxiv.org/abs/2509.23383)

**作者**: Bordt, Pawelczyk  
**链接**: [arXiv](https://arxiv.org/abs/2509.23383) · [PDF](https://arxiv.org/pdf/2509.23383)  \| [📖 全文分析](paper_2509.23383.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种创新的预训练实验方法，能够在单次训练运行中同时进行多个实验，显著降低了大规模语言模型研究的计算成本。作者通过训练2.7B参数模型在210B token上的实验，成功复现了数据污染、中毒和记忆化等经典研究结果，并探索了知识获取、数学推理和水印等新问题。论文还提出了持续预训练依赖测试（CPDT）技术来检测实验间的交互影响。该方法为在有限计算预算下进行严谨的大模型科学研究提供了新思路。


### [Soft-Masked Diffusion Language Models](https://arxiv.org/abs/2510.17206)

**作者**: Hersche, Moor-Smith, Hofmann, Rahimi  
**链接**: [arXiv](https://arxiv.org/abs/2510.17206) · [PDF](https://arxiv.org/pdf/2510.17206)  \| [📖 全文分析](paper_2510.17206.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文由IBM研究团队提出了一种名为软掩码扩散（Soft-Masked Diffusion）的新方法，用于改进基于掩码的扩散语言模型。该方法通过动态混合掩码标记与预测标记的嵌入，为模型提供更丰富的先验信息，从而提升生成质量。实验表明，该方法在困惑度、MAUVE分数以及多个代码生成基准上均优于传统二值掩码基线，且代码已开源。


### [Retrievit: In-context Retrieval Capabilities of Transformers, State Space Models, and Hybrid Architectures](https://arxiv.org/abs/2603.02874)

**作者**: Pantazopoulos, Nikandrou, Konstas, Suglia  
**链接**: [arXiv](https://arxiv.org/abs/2603.02874) · [PDF](https://arxiv.org/pdf/2603.02874)  \| [📖 全文分析](paper_2603.02874.md)  
**评分**: 8.36  （novelty: 8.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文系统研究了Transformer、状态空间模型（SSM）及其混合架构在上下文检索任务中的能力差异。研究通过两个合成检索任务（n-gram检索和位置检索），在数据效率、长度泛化、领域外鲁棒性和表示学习等方面进行了全面对比分析。研究发现混合模型在信息密集的上下文检索任务中能超越SSM并匹配或超越Transformer，而Transformer在位置检索任务中仍保持优势。通过表示分析，揭示了SSM类模型能形成局部感知的嵌入结构，这一特性解释了不同架构在不同任务上的表现差异。研究结果为基于任务需求的架构选择提供了原则性指导，并揭示了不同模型学习位置关联的根本差异。


### [Yuan3.0 Ultra: A Trillion-Parameter Enterprise-Oriented MoE LLM](https://arxiv.org/abs/2601.14327)

**作者**: ai, :, Wu, Luo, Chen 等 28 人  
**链接**: [arXiv](https://arxiv.org/abs/2601.14327) · [PDF](https://arxiv.org/pdf/2601.14327)  \| [📖 全文分析](paper_2601.14327.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文介绍了Yuan3.0 Ultra，一个面向企业场景的万亿参数开源MoE大语言模型，并提出了用于MoE LLM预训练阶段的层自适应专家剪枝（LAEP）算法。该算法通过选择性剪枝未充分利用的专家并根据令牌分布统计跨计算设备重组专家，有效提升了训练效率。实验表明，LAEP在保持模型多领域性能的同时，显著提升了预训练效率并减少了总参数量。Yuan3.0 Ultra在企业场景基准测试中取得了领先的准确率。模型和代码已开源。


### [The Spike, the Sparse and the Sink: Anatomy of Massive Activations and Attention Sinks](https://arxiv.org/abs/2603.05498)

**作者**: Sun, Canziani, LeCun, Zhu  
**链接**: [arXiv](https://arxiv.org/abs/2603.05498) · [PDF](https://arxiv.org/pdf/2603.05498)  \| [📖 全文分析](paper_2603.05498.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由纽约大学（NYU）和Meta AI研究院的知名学者团队（包括Yann LeCun等）合作完成，深入研究了Transformer语言模型中两个普遍存在的现象：大规模激活和注意力汇。论文通过系统实验揭示了这两种现象的功能角色和因果关系，指出它们主要是现代Transformer架构设计的产物，并阐明了预归一化配置的关键作用。研究创新性地将大规模激活解释为模型的隐式参数，将注意力汇视为局部注意力调节机制，为理解Transformer内部工作机制提供了新视角。

