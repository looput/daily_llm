# Pretraining · 2026-03-30 ~ 2026-04-03

**论文数**: 9 篇

---

## 📊 趋势分析

### 研究全貌

本批次预训练领域的研究论文呈现出多样化的探索方向，主要聚焦于**预训练科学化、效率优化与架构创新**。具体而言，部分研究致力于建立系统性的预训练方法论，通过大规模可控实验揭示数据、计算与模型能力之间的深层规律。另一些工作则关注于提升训练与推理效率，包括优化器改进、注意力机制创新以及模型转换技术。此外，还有研究探索了将因果语言模型转化为双向编码器、结合不同训练目标等新颖范式。当前的热点问题是如何在资源有限的情况下，更科学、高效地构建和利用大模型。整体研究趋势正从“黑盒式”的规模扩张，转向对预训练过程内在机制的**系统性、可解释性**研究，并强调方法的**轻量化、可组合性与可迁移性**。

### 重点方法深度解析

从这批论文中，以下几个工作因其创新性和实用价值尤为突出：

**1. 《daVinci-LLM: Towards the Science of Pretraining》 [URL](https://arxiv.org/abs/2603.27164)**
*   **核心创新点**：该研究旨在填补预训练“科学”研究的空白，提出了一个结合工业级资源与完全开放研究范式的框架。其核心是系统性地探索并量化了数据处理深度、领域饱和动态等关键因素对模型最终能力的影响，挑战了以往仅关注数据量的单一维度。
*   **技术细节**：研究提出了“Data Darwinism”数据处理框架，采用从L0（过滤）到L9（合成）的十级分类法，对数据进行精细化处理。在训练策略上，采用了两阶段自适应课程学习：第一阶段专注于构建基础能力，第二阶段则转向强化推理密集型能力。研究通过超过200个对照实验，严谨地验证了不同策略的效果。
*   **效果验证**：基于该框架，研究团队从头训练了一个3B参数的模型，使用了8T tokens的数据。实验结论表明，数据处理深度是独立于数据量的关键能力提升维度，不同领域的数据存在不同的饱和点，需要动态调整策略。
*   **适用场景**：该方法为任何希望系统化、科学化地进行大规模模型预训练的机构或团队提供了完整的方法论、数据管道和训练日志，具有极高的参考和复现价值。

**2. 《Problems with Chinchilla Approach 2: Systematic Biases in IsoFLOP Parabola Fits》 [URL](https://arxiv.org/abs/2603.22339)**
*   **核心创新点**：本文深入剖析了被广泛用于拟合神经缩放定律的Chinchilla Approach 2方法中存在的系统性偏差问题，并提出了一个更优的解决方案。
*   **技术细节**：论文指出Approach 2的抛物线近似会引入偏差，导致对计算最优分配（即模型参数量与数据量的最佳配比）的估计不准确。作者通过分析Llama 3的IsoFLOP数据，量化了这种偏差可能造成的巨大计算资源浪费。作为改进，论文论证并推广了Chinchilla Approach 3，并利用**变量投影（Variable Projection）** 技术对其进行优化，将其转化为一个条件良好、可解析微分的二维优化问题，从而高效、无偏地估计所有五个损失曲面参数。
*   **效果验证**：分析显示，在Llama 3的规模下，Approach 2的偏差可能导致约140万美元的不必要计算开销。改进后的方法能消除这些偏差，实现更精确的缩放定律拟合。
*   **适用场景**：对于任何计划进行大规模模型训练、需要依据缩放定律进行预算规划和资源配置的研究团队或公司，该方法提供了至关重要的理论修正和实用工具。

**3. 《Stochastic Attention: Connectome-Inspired Randomized Routing for Expressive Linear-Time Attention》 [URL](https://arxiv.org/abs/2604.00754)**
*   **核心创新点**：受果蝇大脑连接组中稀疏、随机的长距离连接启发，提出了一种即插即用的随机注意力（SA）模块，能在保持线性计算复杂度的同时，显著增强滑动窗口注意力（SWA）的全局表达能力。
*   **技术细节**：SA的核心操作非常简单：在应用滑动窗口注意力之前，对输入token序列进行一次随机排列，在注意力计算后再恢复原始顺序。这一操作将原本固定的局部感受野，在计算预算不变的情况下，转变为随机的全局感受野。通过堆叠多层，独立采样的随机排列能使感受野呈指数级增长，仅需O(log_w n)层即可覆盖整个序列，远优于SWA所需的O(n/w)层。
*   **效果验证**：在从头预训练语言模型的实验中，结合了门控机制的SA与SWA取得了最佳的平均零样本准确率。在已有的Qwen3模型上进行免训练推理时，SA持续优于标准的SWA，并在可比计算预算下达到或超过了混合块注意力（MoBA）的性能。
*   **适用场景**：该方法非常适合需要处理长文本、但对计算和内存有严格限制的场景，可以作为现有高效注意力机制（如线性注意力、稀疏注意力）的一个有效补充。

**4. 《GradPower: Powering Gradients for Faster Language Model Pre-Training》 [URL](https://arxiv.org/abs/2505.24275)**
*   **核心创新点**：提出了一种极其轻量级的梯度变换技术，仅需单行代码修改即可加速语言模型预训练，且无需调整优化器内部逻辑或超参数。
*   **技术细节**：给定梯度向量g，GradPower对其应用逐元素的符号幂变换：φ_p(g) = (sign(g_i) * |g_i|^p)，其中p>0是一个固定参数。然后将变换后的梯度输入给基础优化器（如Adam，此时称为AdamPower）。该方法的作用机制与梯度噪声的统计特性有关。
*   **效果验证**：实验表明，将GradPower应用于Adam后，在多种模型架构（LLaMA, Qwen2MoE）、参数规模（66M到2B）、数据集和学习率调度下，都能获得更低的最终损失。在训练现代混合专家（MoE）模型时，效果提升最为显著。
*   **适用场景**：这是一种“无痛”的加速技巧，几乎适用于所有基于梯度下降的预训练场景，尤其对于资源紧张的研究者或需要快速迭代的工程团队，具有极高的易用性和性价比。

### 实践启示

这些研究为实际的大模型应用开发提供了多方面的借鉴。首先，**效率优化是核心关切**。GradPower和Stochastic Attention分别从优化器和注意力机制层面提供了低成本、高回报的改进方案，建议在启动新的预训练或微调项目时优先考虑集成。其次，**科学化决策至关重要**。daVinci-LLM和关于Chinchilla Approach 2的批判性研究提醒我们，依赖经验或简化公式可能导致资源浪费，应尽可能采用更严谨的评估和规划方法。

针对不同场景，建议如下：对于**从头开始预训练大型模型**，应重点关注daVinci-LLM的数据处理框架和课程学习策略，并利用改进后的缩放定律分析方法进行资源配置。对于**在现有模型基础上进行高效推理或适配**（如构建嵌入模型），可借鉴BidirLM的模型转换与合并思路，以及LLM2Vec-Gen的自监督嵌入生成方法。对于**资源受限的研发或快速原型验证**，GradPower和Stochastic Attention是首选的轻量级增强手段。

在实现时需注意：GradPower中的幂参数p可能需要根据任务进行小幅调优；Stochastic Attention的随机性在训练初期可能引入不稳定性，需配合适当的学习率策略；而采用改进的缩放定律分析时，需确保有足够数量和质量的IsoFLOP运行数据作为输入。

---

## 📄 论文列表（9 篇）

### [daVinci-LLM:Towards the Science of Pretraining](https://arxiv.org/abs/2603.27164)

**作者**: Qin, Liu, Mi, Xie, Huang 等 15 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.27164) · [PDF](https://arxiv.org/pdf/2603.27164)  \| [📖 全文分析](paper_2603.27164.md)  
**评分**: 8.78  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 8.0）

> 本文由来自工业界与学术界合作团队（作者来自多个机构，包括工业界资源方与学术研究团队）提出了一种名为daVinci-LLM的大语言模型预训练科学研究框架。该研究填补了预训练科学领域的空白，通过结合工业级计算资源与完全开放的研究范式，系统探索了从数据处理到训练策略的完整预训练过程。研究创新性地提出了Data Darwinism数据处理框架和两阶段自适应课程学习策略，并通过200多个对照实验得出了关于数据处理深度、领域饱和动态等关键科学发现。实验设计严谨，数据规模庞大，且承诺完全开源，对推动预训练领域的科学发展具有重要意义。


### [Problems with Chinchilla Approach 2: Systematic Biases in IsoFLOP Parabola Fits](https://arxiv.org/abs/2603.22339)

**作者**: Czech, Xu, Elmatad, Wang, Held  
**链接**: [arXiv](https://arxiv.org/abs/2603.22339) · [PDF](https://arxiv.org/pdf/2603.22339)  \| [📖 全文分析](paper_2603.22339.md)  
**评分**: 8.71  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 7.5）

> 本文深入分析了Chinchilla Approach 2在拟合神经缩放定律时存在的系统性偏差问题，并提出了改进方案。论文指出，即使在没有噪声的合成数据上，Approach 2的抛物线近似也会导致计算最优分配估计出现偏差。作者通过分析Llama 3的IsoFLOP数据，量化了这种偏差带来的巨大计算资源浪费（约140万美元）。论文进一步探讨了误差的三个来源，并论证了Chinchilla Approach 3在消除这些偏差方面的有效性，同时驳斥了关于其数据效率低、数值不稳定等常见误解。作者提出利用变量投影（Variable Projection）技术来优化Approach 3的实现，使其成为一个条件良好、可解析微分且易于搜索的二维优化问题。论文提供了开源代码和详细分析结果，具有很高的实践参考价值。


### [BidirLM: From Text to Omnimodal Bidirectional Encoders by Adapting and Composing Causal LLMs](https://arxiv.org/abs/2604.02045)

**作者**: Boizard, Deschamps-Berger, Gisserot-Boukhlef, Hudelot, Colombo  
**链接**: [arXiv](https://arxiv.org/abs/2604.02045) · [PDF](https://arxiv.org/pdf/2604.02045)  \| [📖 全文分析](paper_2604.02045.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种将因果生成式语言模型转化为双向编码器的新方法BidirLM。该方法通过系统性的消融实验确定了成功适应的关键因素，引入了结合线性权重合并与轻量级多领域数据混合的双重策略来缓解灾难性遗忘，并通过与专业因果模型合并来增强编码器的多模态能力。论文在文本、视觉和音频表示基准测试中取得了优于现有方法的表现，并提供了开源实现。


### [The Latent Space: Foundation, Evolution, Mechanism, Ability, and Outlook](https://arxiv.org/abs/2604.02029)

**作者**: Yu, Chen, He, Fu, Yang 等 37 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.02029) · [PDF](https://arxiv.org/pdf/2604.02029)  \| [📖 全文分析](paper_2604.02029.md)  
**评分**: 8.49  （novelty: 6.5 | method: 9.5 | evidence: 7.0 | clarity: 8.5）

> 本文是一篇关于语言模型潜在空间的综合性综述论文，系统性地梳理了该领域的基础、演变、机制、能力和未来展望。论文作者团队来自多个研究机构，但未明确标注具体机构信息，因此省略团队背景介绍。该论文对潜在空间这一新兴计算范式进行了全面整理，为理解下一代智能系统提供了重要参考框架。


### [Stochastic Attention: Connectome-Inspired Randomized Routing for Expressive Linear-Time Attention](https://arxiv.org/abs/2604.00754)

**作者**: Jin, Sui  
**链接**: [arXiv](https://arxiv.org/abs/2604.00754) · [PDF](https://arxiv.org/pdf/2604.00754)  \| [📖 全文分析](paper_2604.00754.md)  
**评分**: 8.35  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.5）

> 本文提出了一种受果蝇全脑连接组启发的随机注意力机制（Stochastic Attention, SA），作为滑动窗口注意力（SWA）的即插即用增强模块。该方法通过在窗口注意力前对token序列进行随机排列，并在之后恢复原始顺序，在保持O(nw)每层计算复杂度的同时，将固定的局部窗口转换为随机的全局窗口。通过深度堆叠，独立采样的排列能实现指数级增长的感受野，在O(log_w n)层内实现全序列覆盖，相比SWA的O(n/w)层显著更高效。实验验证包括从头预训练语言模型（其中门控SA+SWA组合获得最佳平均零样本准确率）以及在Qwen3-8B和Qwen3-30B-A3B模型上进行免训练推理（SA持续优于SWA，并在可比计算预算下匹配或超过混合块注意力）。这些结果表明，受连接组启发的随机路由是提升高效注意力表达能力的实用原语，与现有线性和稀疏方法互补。


### [LLM2Vec-Gen: Generative Embeddings from Large Language Models](https://arxiv.org/abs/2603.10913)

**作者**: BehnamGhader, Adlakha, Schmidt, Chapados, Mosbach 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.10913) · [PDF](https://arxiv.org/pdf/2603.10913)  \| [📖 全文分析](paper_2603.10913.md)  
**评分**: 8.29  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为LLM2Vec-Gen的自监督方法，用于从大型语言模型生成文本嵌入。该方法通过在输入中附加可训练的特殊标记，并优化这些标记以将LLM自身的响应压缩成固定长度的嵌入，从而直接在LLM的输出空间中生成嵌入。该方法在Massive Text Embedding Benchmark上取得了最先进的自监督性能，并展示了嵌入能够继承LLM的安全对齐和推理能力，同时保持可解释性。


### [GradPower: Powering Gradients for Faster Language Model Pre-Training](https://arxiv.org/abs/2505.24275)

**作者**: Wang, Wang, Zhang, Wang, Pei 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2505.24275) · [PDF](https://arxiv.org/pdf/2505.24275)  \| [📖 全文分析](paper_2505.24275.md)  
**评分**: 8.16  （novelty: 8.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为GradPower的轻量级梯度变换技术，用于加速语言模型预训练。该方法通过对梯度向量进行逐元素的符号幂变换，然后输入基础优化器，仅需单行代码修改且无需调整优化器内部逻辑或超参数。实验表明，当应用于Adam（称为AdamPower）时，该方法在多种架构、参数规模、数据集和学习率调度下均能获得更低的最终损失，尤其在训练现代混合专家模型时效果显著。论文还提供了理论分析，揭示了GradPower的作用机制和梯度噪声的影响。


### [Dual-objective Language Models: Training Efficiency Without Overfitting](https://arxiv.org/abs/2512.14549)

**作者**: Samuel, Charpentier  
**链接**: [arXiv](https://arxiv.org/abs/2512.14549) · [PDF](https://arxiv.org/pdf/2512.14549)  \| [📖 全文分析](paper_2512.14549.md)  
**评分**: 8.12  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.0）

> 本文提出了一种结合自回归和掩码扩散训练目标的双目标语言模型训练方法，无需修改模型架构即可获得兼具训练效率和抗过拟合能力的语言模型。作者通过系统性的实验设计，在50个不同数据重复率下的语言模型上验证了方法的有效性，证明了在所有评估设置下结合两种目标都是最优选择。


### [Coupled Query-Key Dynamics for Attention](https://arxiv.org/abs/2604.01683)

**作者**: Gahtan, Bronstein  
**链接**: [arXiv](https://arxiv.org/abs/2604.01683) · [PDF](https://arxiv.org/pdf/2604.01683)  \| [📖 全文分析](paper_2604.01683.md)  
**评分**: 7.79  （novelty: 9.0 | method: 8.0 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为'耦合查询-键动态'（coupled QK dynamics）的新型注意力机制，通过在学习过程中让查询和键向量共享动态演化过程，显著提升了语言建模的困惑度和训练稳定性。在WikiText-103等数据集上取得了6.6-6.9%的困惑度降低，且仅增加0.11%的参数。论文通过结构消融实验、计算匹配比较和不同规模模型测试，系统分析了该方法的有效性和适用范围。作者Gahtan和Bronstein未明确标注其所属机构，因此省略团队背景介绍。

