# From Narrow to Panoramic Vision: Attention-Guided Cold-Start Reshapes Multimodal Reasoning

**arXiv**: [2603.03825](https://arxiv.org/abs/2603.03825) · [PDF](https://arxiv.org/pdf/2603.03825)  
**领域**: Multimodal  
**作者**: Luo, Shi, Zhang, Yang, Jiang, Guan, Chen, Chu 等 13 人  
**综合评分**: 8.50  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文由阿里千问（Qwen）团队提出了一种针对多模态大语言模型冷启动阶段的新颖分析方法与优化框架。研究团队通过引入视觉注意力分数（VAS）这一量化指标，揭示了冷启动阶段存在的“注意力懒惰定位”现象，并基于此提出了AVAR（注意力引导的视觉锚定与反思）框架，在多个基准测试上显著提升了模型性能。该工作对理解多模态模型的训练动态具有重要理论价值，并提供了一套可操作的优化方案。

---

## 详细分析

> **社区热度**: ⭐ 7 (来自 papers.cool)

## 问题定义

这篇论文试图解决**多模态大型推理模型（Multimodal Large Reasoning Models, MLRMs）在冷启动初始化阶段（cold-start initialization stage）的视觉注意力分配失效问题**。

具体而言，论文针对以下核心挑战展开：

## 1. 反直觉的冷启动失效现象
当前MLRMs训练流程中存在一个关键瓶颈：**使用纯文本数据进行冷启动初始化反而比使用多模态数据带来更显著的后续强化学习（RL）性能提升**。这一现象表明，现有的多模态冷启动范式未能有效利用视觉信号，导致资源使用效率低下并限制了RL在多模态推理中的潜力。

## 2. 懒惰注意力局部化（Lazy Attention Localization）
论文通过引入**视觉注意力分数（Visual Attention Score, VAS）**揭示了这一现象的根本原因：
- 多模态冷启动**无法提升**模型对视觉token的注意力（VAS值接近基础模型）
- 纯文本冷启动反而能**显著增加**视觉注意力分配（比多模态冷启动高15-20%）
- 这种"懒惰注意力局部化"导致模型过度依赖系统提示（system tokens）而忽视视觉信息

## 3. 注意力分配失衡
论文发现推理性能与VAS呈强正相关（$r = 0.9616$）。当前模型存在以下注意力分配问题：
- **窄视模型（Narrow-View）**：VAS < 10，视觉注意力不足，推理性能差
- **宽视模型（Wide-View）**：VAS 10-15，注意力分配较均衡
- **全景模型（Panoramic-View）**：VAS > 15，具有强视觉基础，推理性能优异

## 提出的解决方案
为系统性解决上述问题，论文提出了**注意力引导的视觉锚定与反思框架（Attention-Guided Visual Anchoring and Reflection, AVAR）**，通过三个互补组件重新塑造注意力分配：

1. **视觉锚定反思数据合成**：将视觉锚点嵌入推理过程，生成具有内置视觉反思的合成数据
2. **注意力引导训练目标**：通过损失函数显式增强对视觉token的注意力，同时抑制对系统token的冗余关注
3. **视觉锚定奖励塑造**：在RL阶段引入视觉注意力奖励，确保模型在正确回答的同时保持强视觉基础

该框架旨在将模型从"窄视"转变为"全景视"，通过重新分配注意力（从系统token转向视觉token）来建立更强的跨模态推理能力。在Qwen2.5-VL-7B上的实验表明，AVAR在7个多模态推理基准上实现了**平均7.0%**的性能提升。

## 相关工作

根据论文第2节（Related Works），相关研究主要集中在以下两个领域：

## 1. 多模态大型推理模型（Multimodal Large Reasoning Models）

### 冷启动数据整理
近期研究致力于改进冷启动思考数据（cold-start thinking data）的整理方法，包括：
- **Huang et al., 2025**; **Meng et al., 2025**; **Deng et al., 2025**; **Wang et al., 2025a**; **Ding et al., 2025** 等探索了高质量多模态推理数据的构建策略。

### 基于强化学习的方法
- **Zhang et al., 2025a**; **Yang et al., 2025d**; **Yu et al., 2025a**; **Luo et al., 2025**; **Zhang et al., 2025c**; **Bai et al., 2025a** 等研究利用RL技术构建MLRMs，旨在增强跨模态推理能力。

### 单模态与多模态冷启动对比
- **Wei et al., 2025b**; **Xiaomi, 2025**; **Chen et al., 2025**; **Sun et al., 2025**; **Wang et al., 2025b** 等研究揭示了一个关键现象：高质量的单模态（纯文本）"思考数据"能显著提升MLRMs的推理能力，甚至优于多模态冷启动数据。然而，这些工作未能深入解释其背后机制，也未探讨如何优化多模态推理数据。

### 基础语言模型
- **OpenAI o1** (Jaech et al., 2024), **Qwen-Max** (Team, 2025), **DeepSeek-R1** (Shao et al., 2024; Guo et al., 2025) 等通过RL增强推理能力的LLMs为MLRMs提供了基础。

## 2. 视觉注意力分析（Visual Attention Analysis）

### 注意力分配机制
- **Yin et al., 2025** 发现模态融合主要发生在中间层，但模型对视觉信号关注不足，过度依赖语言先验（language priors）。
- **Tang et al., 2025** 揭示注意力在不同头（heads）间分布不均，某些头过度受语言先验主导。
- **Liu et al., 2025** 证明面向推理的MLLMs比非推理模型分配更少注意力给视觉token，导致在长推理链中幻觉（hallucination）问题加剧。

### 推理时间干预方法
为缓解注意力分配不当问题，研究者提出了无需训练的推理时干预方法：
- **Yin et al., 2025**; **Fazli et al., 2025**; **Tang et al., 2025** 等通过重新加权（reweighting）注意力分布，增强对视觉token的关注。

## 3. 评估基准（Benchmarks）
论文中使用的评估基准包括：
- **数学推理**：MathVista (Lu et al., 2023), MathVerse (Zhang et al., 2024a), MathVision (Wang et al., 2024), DynaMath (Zou et al., 2024)
- **多学科理解**：MMMU, MMMU-Pro (Yue et al., 2024; 2025a)
- **感知与幻觉**：MMStar (Chen et al., 2024a), HallusionBench (Guan et al., 2024)
- **几何与算法推理**：Geo3K (Lu et al., 2021), Super-CLEVER (Li et al., 2023), AI2D (Kembhavi et al., 2016), AlgoPuzzleVQA (Ghosal et al., 2025), SOLIDGEO (Wang et al., 2025c)

这些相关工作共同构成了本文研究的背景：现有MLRMs在冷启动阶段存在视觉注意力分配不足（Lazy Attention Localization）的问题，而本文提出的AVAR框架旨在通过训练阶段的注意力重塑（而非仅推理时干预）来系统性解决这一瓶颈。

## 解决方案

论文通过提出**注意力引导的视觉锚定与反思框架（Attention-Guided Visual Anchoring and Reflection, AVAR）** 来解决Lazy Attention Localization问题。该框架通过三个互补组件，在冷启动阶段显式重塑注意力分配，将冗余注意力从系统token转移到视觉token，从而建立强视觉基础。

## 1. 视觉锚定反思数据合成（Visual-Anchored Reflection Data Synthesis）

不同于传统的"先描述后推理"（caption-then-reason）流程，AVAR设计了一个三阶段数据合成管道，将视觉锚点直接嵌入推理过程：

- **高保真视觉描述生成**：使用Gemini 2.5-Pro生成详细的视觉描述，建立准确的视觉信息基础。
- **反思增强推理生成**：利用Qwen3-235B-A22B生成扩展推理链，要求其进行迭代自我反思和错误检查，确保推理链持续基于视觉上下文而非仅依赖文本。
- **视觉锚点整合**：使用Qwen3-32B在推理链中插入显式视觉锚点（如"回头看三角形"、"再次检查图像"），模拟直接图像感知，确保每个推理步骤都与图像明确关联。

这种数据合成方式产生具有内在视觉锚定的训练数据，模仿全景视模型的高视觉注意力模式。

## 2. 注意力引导训练目标（Attention-Guided Training Objectives）

为在训练中显式鼓励视觉锚定，AVAR引入基于注意力的损失函数，直接优化注意力分配模式：

总损失函数为：
$$
\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{LM}} + \alpha \cdot \mathcal{L}_{\text{enhance-img}} + \beta \cdot \mathcal{L}_{\text{suppress-sys}}
$$

**图像增强损失**（鼓励持续关注视觉token）：
$$
\mathcal{L}_{\text{enhance-img}} = -\frac{1}{|\mathcal{L}|} \sum_{l \in \mathcal{L}} \frac{1}{H} \sum_{h=1}^{H} \log \left( \frac{1}{|\mathcal{Q}| \cdot |\mathcal{K}_{\text{img}}|} \sum_{q \in \mathcal{Q}} \sum_{k \in \mathcal{K}_{\text{img}}} A_{q,k}^{l,h} \right)
$$

**系统抑制损失**（减少对系统token的冗余关注）：
$$
\mathcal{L}_{\text{suppress-sys}} = \frac{1}{|\mathcal{L}|} \sum_{l \in \mathcal{L}} \frac{1}{H} \sum_{h=1}^{H} \log \left( \frac{1}{|\mathcal{Q}| \cdot |\mathcal{K}_{\text{sys}}|} \sum_{q \in \mathcal{Q}} \sum_{k \in \mathcal{K}_{\text{sys}}} A_{q,k}^{l,h} + \epsilon \right)
$$

其中，$\mathcal{L}$为目标层集合，$H$为注意力头数，$\mathcal{Q}$、$\mathcal{K}_{\text{img}}$、$\mathcal{K}_{\text{sys}}$分别表示查询、图像和系统token集合，$A_{q,k}^{l,h}$表示在层$l$和头$h$中从查询$q$到键$k$的注意力权重。

## 3. 视觉锚定奖励塑造（Visual-Anchored Reward Shaping）

在强化学习阶段，AVAR引入视觉注意力奖励，显式鼓励模型在扩展推理链中维持视觉锚定：

$$
r_{\text{visual}} = 
\begin{cases} 
0 & \text{if rollout outcome is incorrect} \\
\frac{1}{|T|} \sum_{t \in T} \left( \frac{1}{|\mathcal{L}|} \sum_{l \in \mathcal{L}} \frac{\sum_{k \in \mathcal{K}_{\text{img}}} A_{t,k}^{l}}{\sum_{k \in \mathcal{K}_{\text{sys}}} A_{t,k}^{l} + \epsilon} \right) & \text{if rollout outcome is correct}
\end{cases}
$$

总奖励结合了正确性奖励、视觉注意力奖励和格式奖励：
$$
r_{\text{total}} = r_{\text{accuracy}} + \lambda_v \cdot r_{\text{visual}} + \lambda_f \cdot r_{\text{format}}
$$

使用**组相对策略优化（GRPO）**进行策略优化：
$$
A_i = \frac{r_{\text{total},i} - \text{mean}(\{r_{\text{total},1}, \ldots, r_{\text{total},G}\})}{\text{std}(\{r_{\text{total},1}, \ldots, r_{\text{total},G}\})}
$$

$$
J_{\text{GRPO}}(\theta) = \mathbb{E}_{(q,y) \sim \mathcal{D}, \{o_i\}_{i=1}^G \sim \pi_{\theta_{\text{old}}}(\cdot|q)} \left[ \frac{1}{G} \sum_{i=1}^{G} \frac{1}{|o_i|} \sum_{t=1}^{|o_i|} \left( \min(r_{i,t}(\theta)A_i, \text{clip}(r_{i,t}(\theta), 1-\epsilon, 1+\epsilon)A_i) - \beta D_{\text{KL},t}(\pi_\theta \| \pi_{\text{ref}}) \right) \right]
$$

## 4. 训练无干预的因果验证（先导实验）

在提出AVAR之前，论文先通过**训练无关的注意力调制实验**验证了注意力重新分配的因果作用：

$$
\hat{Z}_{l,h} = Z_{l,h} + \alpha_{\text{img}} \cdot M_{l,h}^{\text{enh}} \odot Z_{l,h} - \alpha_{\text{sys}} \cdot M_{l,h}^{\text{sup}} \odot Z_{l,h}
$$

通过在推理时直接放大视觉token注意力（$\alpha_{\text{img}} = 0.15$）并减少系统token注意力（$\alpha_{\text{sys}} \in \{0.00, 0.40\}$），模型性能提升1-2%，证实了**系统token冗余区**的存在以及视觉注意力对推理能力的决定性作用。

## 协同效应

AVAR的三个组件形成递进式解决方案：
- **数据合成**提供具有内在视觉锚定的训练材料（VAS从7.5提升至10.1）
- **注意力引导目标**在训练阶段显式重塑注意力分布（VAS提升至13.8）
- **奖励塑造**在RL阶段防止模型 revert 到纯文本推理模式（VAS最终达到18.9）

通过这种从"窄视"（Narrow-View）到"全景视"（Panoramic-View）的转变，AVAR系统性地解决了Lazy Attention Localization，使模型在7个多模态推理基准上实现平均7.0%的性能提升。

## 实验验证

论文进行了全面的实验验证，涵盖**训练无关干预**、**主实验对比**、**消融研究**、**注意力演化分析**以及**跨模型泛化验证**等多个层面：

## 1. 训练无关注意力干预实验（第4节）

为验证注意力分配的因果作用，论文首先设计了**无需重新训练**的推理时干预实验：

- **实验设计**：在Transformer各层直接调制注意力权重，通过元素级操作增强图像token注意力（$\alpha_{\text{img}}=0.15$）并抑制系统token注意力（$\alpha_{\text{sys}} \in \{0.00, 0.05, 0.40, 0.60\}$）
- **测试模型**：Qwen2.5-VL-7B、Revisual-R1-CS、OVR-CS（覆盖不同基线水平）
- **测试基准**：MathVista、MathVision、MathVerse-VO
- **关键发现**：在$\alpha_{\text{sys}} \in \{0.00, 0.40\}$时，性能一致提升**1-2%**，揭示了"系统token冗余区"的存在，证明视觉注意力是推理能力的关键决定因素。

## 2. 主实验对比（第6.2节）

在7个多模态推理基准上评估AVAR-Thinker：

| 评估维度 | 基准测试 | 主要结果 |
|---------|---------|---------|
| **数学推理** | MathVista、MathVision、MathVerse-VO | 相比Qwen2.5-VL-7B基线，分别提升+6.5%、+12.2%、+9.3% |
| **多学科理解** | MMMU-VAL、MMMU-Pro | 分别提升+5.7%、+4.6% |
| **感知与鲁棒性** | MMStar、HallusionBench | 分别提升+2.0%、+8.8% |
| **平均提升** | - | **+7.0%** |

对比模型包括：
- **闭源**：GPT-4o、Claude-3.7-Sonnet
- **开源通用模型**：InternVL2.5-8B、LLaVA-OneVision-7B等
- **多模态推理模型**：R1-OneVision、ThinkLite-VL、MM-Eureka-7B、Vision-R1等

AVAR-Thinker在7B模型中达到新的SOTA，特别是在需要多步几何推理的MathVision（+12.2%）和评估视觉幻觉鲁棒性的HallusionBench（+8.8%）上表现突出。

## 3. 消融研究（第6.3节）

逐步验证AVAR各组件的贡献：

| 配置 | VARD | AGTO | VARS | 平均性能 |
|------|------|------|------|---------|
| 基线 | - | - | - | 49.1% |
| +VARD | ✓ | - | - | 51.0% (+1.9%) |
| +AGTO | ✓ | ✓ | - | 52.6% (+1.6%) |
| AVAR-Thinker | ✓ | ✓ | ✓ | **56.1% (+3.5%)** |

**数据合成对比实验**：将VARD与其他冷启动数据方法（R1-OneVision、OpenVLThinker、Vision-SR1）在相同基线模型上比较，VARD显著优于其他方法（分别+6.4%、+2.9%、+6.2%），证明视觉锚定设计的有效性。

## 4. 注意力演化分析（第6.4节）

跟踪VAS（视觉注意力分数）在训练各阶段的变化：

| 模型阶段 | VAS | 平均性能 | 状态分类 |
|---------|-----|---------|---------|
| Qwen2.5-VL-7B（基线） | 7.5 | 49.3% | 窄视模型 |
| +VARD数据 | 10.1 | 51.0% | 宽视模型 |
| AVAR-CS（+注意力引导训练） | 13.8 | 52.6% | 宽视模型 |
| **AVAR-Thinker（完整版）** | **18.9** | **56.1%** | **全景视模型** |

该实验证实每个组件都逐步提升VAS，最终从"窄视"（Narrow-View）转变为"全景视"（Panoramic-View）。

## 5. VAS与性能相关性分析（第3节）

对10个代表性7B模型（包括Qwen2.5-VL-7B、R1-OneVision、ThinkLite-VL、Revisual-R1-CS/RL、OVR-CS/RL、MiMo-VL-CS/RL等）进行系统分析：

- **量化指标**：Pearson相关系数 $r = 0.9616$（$p = 9.0 \times 10^{-6}$）
- **关键发现**：VAS与推理性能呈强正相关，将模型分为三类：
  - **窄视模型**（VAS < 10）：性能较差
  - **宽视模型**（VAS 10-15）：中等性能
  - **全景视模型**（VAS > 15）：最优性能

## 6. 细粒度注意力分布可视化（附录D）

对多个模型进行层-wise注意力分配分析（图4-6）：

- **对比模型**：Qwen2.5-VL-7B vs R1-OneVision（多模态冷启动）vs OVR-CS（文本冷启动）
- **可视化内容**：视觉特征、用户指令、系统提示三类token的注意力分布
- **结论**：多模态冷启动（R1-OneVision）未能改变基模型的注意力行为，而文本冷启动（OVR-CS）成功减少了系统token的冗余注意力并增强了对图像信息的关注。

## 7. 跨架构泛化实验（附录B）

验证AVAR组件在**Llama-3.2-11B-Vision-Instruct**上的通用性：

| 配置 | MathVista | MathVision | HallusionBench | 平均 |
|------|-----------|------------|----------------|------|
| 基线 | 48.6 | 19.7 | 40.3 | 37.2% |
| +VARD | 56.6 | 25.5 | 45.5 | 43.2% (+6.0%) |
| +AGTO | 57.4 | 25.2 | 46.4 | 44.0% (+0.8%) |
| 完整AVAR | 61.7 | 26.9 | 50.1 | 46.7% (+2.7%) |

证明AVAR方法在不同基础模型架构上均能产生一致的增量改进。

## 8. 案例研究（附录F）

在MathVerse-VO上展示具体推理案例（图7），直观展示AVAR-Thinker如何通过"回头看图像"（"check the image again"）等视觉锚定语句，在推理过程中保持对几何图形的持续关注和自我修正能力。

## 未来工作

基于论文的发现与局限，以下方向值得进一步探索：

## 1. **视觉注意力分数（VAS）的理论解释与预测模型**
- **机制深化**：当前VAS是经验性度量，可探索其理论上限——是否存在VAS与推理能力的因果关系阈值（如VAS>20是否必然带来性能饱和）？
- **动态VAS建模**：现有VAS为静态平均值，可研究**层-wise自适应VAS权重**，不同层（早期视觉编码vs. 晚期推理层）的视觉注意力贡献度差异。
- **跨模态扩展**：将VAS扩展至视频（时序注意力分数TAS）、音频（声学注意力分数AAS）等多模态场景，建立统一的**多模态注意力熵理论**。

## 2. **Lazy Attention Localization的深层机理**
- **语言先验的固化机制**：探究为何多模态冷启动无法打破语言先验——是否由于视觉-语言表征空间的对齐缺陷？可通过**表示工程（Representation Engineering）**方法可视化冷启动前后视觉token的表征流形变化。
- **系统Token冗余的本质**：论文发现系统token存在"冗余区"，需进一步区分：
  - 哪些系统token（如指令模板、安全提示）是真正必要的？
  - 是否存在**最小必要系统注意力阈值**？可通过信息瓶颈（Information Bottleneck）理论量化。

## 3. **更细粒度的注意力干预策略**
- **Head-Level差异化干预**：Tang et al. (2025)发现注意力头存在功能分化（视觉专注头vs. 语言先验头）。未来可探索：
  - **注意力头剪枝**：直接移除过度依赖语言先验的注意力头，而非仅重新加权。
  - **动态头路由**：根据输入复杂度动态选择激活视觉专注头或推理头。
- **稀疏注意力机制**：针对长推理链中的视觉锚点，设计**视觉token稀疏化算法**，减少计算开销同时维持VAS。

## 4. **AVAR框架的优化与泛化**
- **自动化视觉锚点插入**：当前依赖Qwen3-32B进行视觉锚点整合，可训练**轻量级锚点预测器**，自动在推理链中最优位置插入"回头看"指令。
- **课程学习（Curriculum Learning）策略**：从窄视到全景视的过渡是否可通过渐进式课程实现？例如早期训练高VAS样本，后期降低视觉锚点密度。
- **跨架构泛化**：论文仅在Qwen2.5-VL和Llama-3.2-Vision上验证，需在**混合专家模型（MoE）**（如MiMo-VL）和**原生多模态模型**（如Gemini、GPT-4o）上验证AVAR有效性。

## 5. **幻觉与鲁棒性增强**
- **注意力漂移检测**：长推理链中的**视觉注意力衰减**（VAS随推理步骤增加而下降）是幻觉的主要来源。可设计**注意力正则化器**，强制要求在生成结论前必须满足最小VAS阈值。
- **对抗性攻击下的注意力鲁棒性**：测试AVAR在对抗性视觉扰动（如添加噪声、遮挡）下的注意力稳定性，是否仍能保持高VAS。

## 6. **计算效率与实用性**
- **训练开销优化**：AVAR的三阶段数据合成（Gemini-2.5-Pro + Qwen3-235B + Qwen3-32B）成本较高，可探索**蒸馏策略**：
  - 使用AVAR训练的教师模型蒸馏出**单阶段数据生成器**。
- **实时注意力监控**：部署阶段实时计算VAS作为**置信度指标**，当VAS低于阈值时触发"再次观察"机制。

## 7. **认知科学视角的验证**
- **人类视觉注意力对齐**：对比人类解决相同多模态推理问题时的眼动数据（eyetracking），验证AVAR的注意力分配是否与人类**认知视线（cognitive gaze）**一致，从而解释其有效性。
- **系统2思维的可视化**：AVAR中的"反思"机制与Kahneman的系统2思维（慢思考）相关，可量化分析视觉锚点插入频率与推理深度的关系。

## 8. **与先进RL算法的结合**
- **过程奖励模型（PRM）融合**：当前AVAR使用结果奖励（正确性）+ 注意力奖励，可训练专门的**视觉过程奖励模型（Visual PRM）**，对每个推理步骤的视觉 grounding 进行细粒度评分。
- **DAPO/DPO等离线RL适配**：将AVAR的注意力引导目标适配至直接偏好优化（DPO）框架，探索无需在线采样的注意力塑造方法。

## 总结

这篇论文围绕多模态大型推理模型（MLRMs）的冷启动初始化阶段展开研究，核心贡献可概括如下：

## 1. 问题定义与核心发现

**研究背景**：当前MLRMs的冷启动阶段存在一个反直觉现象——使用纯文本数据进行冷启动初始化比使用多模态数据能带来更显著的后续强化学习（RL）性能提升，但其机制尚不明确。

**核心发现——Lazy Attention Localization**：
论文引入**视觉注意力分数（Visual Attention Score, VAS）**量化模型对视觉token的关注程度：
$$
\text{VAS}_i(l,h) = \frac{\sum_{j \in V} A_{i,j}(l,h)}{\sum_{j \in S} A_{i,j}(l,h)}, \quad \text{VAS} = \frac{1}{L \cdot H \cdot |U|} \sum_{l=1}^L \sum_{h=1}^H \sum_{i \in U} \text{VAS}_i(l,h)
$$

研究发现：
- 推理性能与VAS呈强正相关（$r = 0.9616$）
- **多模态冷启动无法提升VAS**（分布接近基线模型），而**文本冷启动显著增加VAS**（提升15-20%）
- 据此将模型分为三类：窄视模型（VAS<10）、宽视模型（VAS 10-15）、全景视模型（VAS>15）

## 2. 因果验证

通过**无需训练的注意力干预实验**验证注意力分配的因果作用：
$$
\hat{Z}_{l,h} = Z_{l,h} + \alpha_{\text{img}} \cdot M_{l,h}^{\text{enh}} \odot Z_{l,h} - \alpha_{\text{sys}} \cdot M_{l,h}^{\text{sup}} \odot Z_{l,h}
$$

在$\alpha_{\text{img}}=0.15$且$\alpha_{\text{sys}} \in \{0.00, 0.40\}$时，模型性能提升**1-2%**，证实系统token存在"冗余区"，视觉注意力是推理能力的关键决定因素。

## 3. 解决方案——AVAR框架

提出**注意力引导的视觉锚定与反思（Attention-Guided Visual Anchoring and Reflection, AVAR）**框架，包含三个协同组件：

### （1）视觉锚定反思数据合成（VARD）
三阶段数据生成管道：
- 使用Gemini 2.5-Pro生成高保真视觉描述
- 使用Qwen3-235B-A22B生成反思增强的推理链
- 使用Qwen3-32B插入显式视觉锚点（如"回头看三角形"），确保推理链持续视觉 grounding

### （2）注意力引导训练目标（AGTO）
在标准语言建模损失基础上增加注意力约束：
$$
\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{LM}} + \alpha \cdot \mathcal{L}_{\text{enhance-img}} + \beta \cdot \mathcal{L}_{\text{suppress-sys}}
$$

其中图像增强损失鼓励关注视觉token，系统抑制损失减少对系统提示的冗余关注。

### （3）视觉锚定奖励塑造（VARS）
在RL阶段引入视觉注意力奖励：
$$
r_{\text{visual}} = \frac{1}{|T|} \sum_{t \in T} \left( \frac{1}{|\mathcal{L}|} \sum_{l \in \mathcal{L}} \frac{\sum_{k \in \mathcal{K}_{\text{img}}} A_{t,k}^{l}}{\sum_{k \in \mathcal{K}_{\text{sys}}} A_{t,k}^{l} + \epsilon} \right)
$$

总奖励$r_{\text{total}} = r_{\text{accuracy}} + \lambda_v \cdot r_{\text{visual}} + \lambda_f \cdot r_{\text{format}}$，使用GRPO进行优化。

## 4. 实验结果

在Qwen2.5-VL-7B上应用AVAR，在7个多模态推理基准上实现**平均7.0%**的性能提升：

| 基准 | 提升幅度 | 特点 |
|------|---------|------|
| MathVision | +12.2% | 多步几何推理 |
| HallusionBench | +8.8% | 视觉幻觉鲁棒性 |
| MathVerse-VO | +9.3% | 精确视觉理解 |
| MMMU-VAL | +5.7% | 多学科理解 |

**消融研究**显示各组件贡献递增：VARD (+1.9%) → AGTO (+1.6%) → VARS (+3.5%)，VAS从基线7.5逐步提升至18.9，实现从"窄视"到"全景视"的转变。

## 5. 主要贡献

1. **诊断指标**：提出VAS量化视觉注意力，揭示其与推理性能的强相关性（$r=0.9616$）
2. **机制发现**：识别Lazy Attention Localization现象，解释为何多模态冷启动失效而文本冷启动有效
3. **因果验证**：通过训练无关干预实验确立视觉注意力与推理能力的因果关系
4. **完整方案**：AVAR框架通过数据合成、注意力引导目标和奖励塑造三阶段，系统性地将注意力从系统token重新分配至视觉token，在7B模型中达到新的SOTA

论文代码、数据和模型已开源：https://github.com/lrlbbzl/Qwen-AVAR



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
