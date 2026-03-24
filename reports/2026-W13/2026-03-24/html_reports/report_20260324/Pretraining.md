# Pretraining · 2026年03月24日

**论文数**: 8 篇

---

## 📊 趋势分析

### 研究全貌

本批次预训练领域的研究论文呈现出多样化的探索方向，主要聚焦于**模型架构与训练范式创新**、**效率与性能的权衡**以及**学习机制的理论分析**。具体来看，部分研究致力于通过引入外部记忆或优化训练流程来提升模型效率与性能，另一部分则对自回归与扩散等不同生成范式进行了深入的对比分析，还有研究从进化生物学或数学理论的角度为模型的学习行为提供新的解释。当前的热点问题清晰指向如何在有限的算力与参数规模下，有效提升模型在特定领域的性能，同时保持其通用能力。整体研究趋势正从单纯追求模型规模的扩大，转向更精细化的架构设计、更高效的训练策略以及对模型内在机制更深刻的理论理解。

### 重点方法深度解析

在这些论文中，以下几项工作因其创新性和实用价值尤为突出：

**《Pretraining with hierarchical memories: separating long-tail and common knowledge》** [URL](https://arxiv.org/abs/2510.02375)
*   **核心创新点**：该研究旨在解决将海量世界知识全部压缩进模型参数所带来的效率瓶颈。它提出了一种内存增强架构，将知识分为“通用知识”和“长尾知识”两类，分别由一个小型语言模型（LM）和一个大型层次化参数化记忆库来存储和处理。
*   **技术细节**：其核心是“层次化前馈记忆”模块。在预训练和推理时，模型根据当前上下文动态地从庞大的记忆库中检索并加载一个极小的、相关的记忆块（例如，从46亿参数库中提取1800万参数），并将其注入到小型LM中。这种设计使得小型LM专注于通用推理能力，而长尾的、不常用的知识则被卸载到外部记忆中，实现了参数的有效分离与按需使用。
*   **效果验证**：通过万亿token级别的实验验证，一个仅1.6亿参数的小模型，在接入上述记忆机制后，其性能可与参数规模超过其两倍的常规模型相媲美。该方法在多种Transformer架构上均表现稳健。
*   **适用场景**：特别适合资源受限的边缘设备部署，或任何需要在保持高性能的同时严格控制模型参数总量的场景。

**《The Finetuner‘s Fallacy: When to Pretrain with Your Finetuning Data》** [URL](https://arxiv.org/abs/2603.16177)
*   **核心创新点**：本文挑战了“先通用预训练，后领域微调”的传统范式，提出了“专业化预训练”策略，旨在解决微调导致的领域过拟合和通用知识遗忘问题。
*   **技术细节**：该方法的核心是在通用预训练阶段，就将少量目标领域数据作为训练语料的一部分重复使用，而不是留到最后的微调阶段。作者还推导了“过拟合缩放定律”，为实践者提供了理论指导，以根据给定的计算预算确定最优的领域数据重复比例。
*   **效果验证**：在ChemPile、MusicPile和ProofPile三个专业领域上的实验表明，SPT不仅能提升模型在目标领域的性能，还能在后续微调后更好地保留通用能力。当目标领域与通用语料差异较大时，一个10亿参数的SPT模型甚至能超越一个30亿参数的标准预训练模型。
*   **适用场景**：适用于数据稀缺但需求明确的垂直领域（如法律、医药、代码），能够帮助企业在更小的模型规模和更低的总体计算成本下，获得更优的专业化性能。

**《MemDLM: Memory-Enhanced DLM Training》** [URL](https://arxiv.org/abs/2603.22241)
*   **核心创新点**：针对扩散语言模型存在的“训练-推理不匹配”问题（训练是单步预测，推理是多步去噪），提出通过双层优化引入参数化记忆来弥合这一差距。
*   **技术细节**：该方法采用双层优化框架。内层循环为每个训练样本更新一组“快速权重”，形成一个临时的参数化记忆，以模拟多步去噪的轨迹经验；外层循环则基于这个记忆来更新基础模型。这种设计将轨迹记忆的压力从词元表示转移到了参数上。
*   **效果验证**：MemDLM带来了更快的收敛速度和更低的训练损失。更重要的是，在推理时重新启用内层循环（作为一种适应步骤），可以进一步提升模型在长上下文理解和“大海捞针”式检索任务上的表现。
*   **适用场景**：适用于希望利用扩散模型并行解码等优势，但又担心其生成质量或长程依赖建模能力的场景。

**对比分析**：上述三项工作都涉及“记忆”或知识管理，但侧重点不同。第一篇（层次化记忆）是**架构层面的革新**，通过硬件友好的外部存储来扩展模型的知识容量。第二篇（SPT）是**训练策略的优化**，通过改变数据使用方式来提升学习效率。第三篇（MemDLM）则专注于**解决特定范式（DLM）的内部训练问题**。三者从不同维度为解决模型效率与能力扩展问题提供了有价值的思路。

### 实践启示

这批研究为实际的大模型应用开发提供了清晰的优化路径。对于**资源敏感的边缘部署或成本控制严格**的场景，应优先关注《层次化记忆》这类内存增强架构，它能以极小的参数量代价换取接近大模型的性能，是降低推理成本的利器。对于**垂直领域快速落地**的需求，《专业化预训练》策略极具借鉴意义，它提示我们应尽早将领域数据融入训练流程，而非仅仅依赖最后的微调，这能有效平衡专业化与通用性。对于**探索非自回归生成模型**的团队，《MemDLM》提供了解决其固有训练难题的具体方案。

**具体建议**：1）在规划领域专用模型时，可尝试将少量高质量领域数据从预训练初期就混合进训练集，并参考其缩放定律进行调优。2）在模型压缩或小型化项目中，可探索引入可检索的外部参数化记忆模块。**关键注意事项**：引入外部记忆或双层优化会增加系统的复杂性，需仔细评估检索开销和训练稳定性。同时，SPT策略中领域数据的混合比例需要根据具体数据和计算预算进行精细调整。

---

## 📄 论文列表（8 篇）

### [Pretraining with hierarchical memories: separating long-tail and common knowledge](https://arxiv.org/abs/2510.02375)

**作者**: Pouransari, Grangier, Thomas, Kirchhof, Tuzel  
**链接**: [arXiv](https://arxiv.org/abs/2510.02375) · [PDF](https://arxiv.org/pdf/2510.02375)  \| [📖 全文分析](paper_2510.02375.md)  
**评分**: 8.80  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种创新的内存增强架构和预训练策略，通过引入层次化参数化记忆库，使小型语言模型能够访问大规模世界知识，在保持推理能力的同时显著减少参数需求。作者团队来自知名机构：Hadi Pouransari（Google Research）、David Grangier（Google Research）、Diego Thomas（Google Research）、Michael Kirchhof（Google Research）、Oncel Tuzel（Google Research），体现了Google在语言模型优化方面的前沿研究实力。


### [Your Absorbing Discrete Diffusion Secretly Models the Conditional Distributions of Clean Data](https://arxiv.org/abs/2406.03736)

**作者**: Ou, Nie, Xue, Zhu, Sun 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2406.03736) · [PDF](https://arxiv.org/pdf/2406.03736)  \| [📖 全文分析](paper_2406.03736.md)  
**评分**: 8.79  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为RADD（重参数化吸收离散扩散）的新方法，揭示了吸收扩散中的具体分数可以表示为干净数据的条件概率乘以一个解析形式的时间相关标量。基于这一发现，作者构建了无需时间条件的扩散模型，能够通过缓存时间无关网络的输出来减少函数评估次数，从而实现采样加速。论文进一步统一了吸收离散扩散模型和任意阶自回归模型，并在GPT-2规模的5个零样本语言建模基准测试中取得了扩散模型中的SOTA性能。代码已开源。


### [The Finetuner's Fallacy: When to Pretrain with Your Finetuning Data](https://arxiv.org/abs/2603.16177)

**作者**: Baek, Monti, Schwab, Abbas, Adiga 等 34 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16177) · [PDF](https://arxiv.org/pdf/2603.16177)  \| [📖 全文分析](paper_2603.16177.md)  
**评分**: 8.75  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 8.5）

> 本文由来自Meta GenAI、Stanford、MIT、CMU等知名机构的研究团队提出了一种名为'专业化预训练'（Specialized Pretraining, SPT）的新策略，旨在解决现实场景中模型在数据稀缺的垂直领域部署时面临的过拟合与通用知识遗忘问题。该方法通过在预训练阶段重复使用少量领域数据，显著提升了模型在目标领域的性能，同时保持了通用能力，并推导了过拟合缩放定律来指导实践。论文在三个专业领域（ChemPile, MusicPile, ProofPile）上进行了充分验证，展示了其有效性和效率优势。


### [Autoregressive vs. Masked Diffusion Language Models: A Controlled Comparison](https://arxiv.org/abs/2603.22075)

**作者**: Vicentino  
**链接**: [arXiv](https://arxiv.org/abs/2603.22075) · [PDF](https://arxiv.org/pdf/2603.22075)  \| [📖 全文分析](paper_2603.22075.md)  
**评分**: 8.64  （novelty: 7.5 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文对自回归语言模型与掩码扩散语言模型进行了受控实证比较，通过严格控制训练数据、计算预算和硬件环境，将生成范式作为唯一变量进行对比分析。研究发现两种范式在训练吞吐量上相近，但收敛特性和生成质量存在显著差异：自回归模型收敛更快但容易过拟合，而掩码扩散模型收敛较慢但能生成更多样化的文本，揭示了两种范式在多样性-流畅性之间的权衡关系。


### [MemDLM: Memory-Enhanced DLM Training](https://arxiv.org/abs/2603.22241)

**作者**: Pei, Zhen, Lin, Pan, Wang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.22241) · [PDF](https://arxiv.org/pdf/2603.22241)  \| [📖 全文分析](paper_2603.22241.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为MemDLM（Memory-Enhanced DLM）的新方法，用于解决扩散语言模型（DLM）中存在的训练-推理不匹配问题。该方法通过双层优化，在训练中嵌入模拟去噪过程，并引入参数化记忆机制，有效提升了模型性能，在长上下文理解和检索任务上表现出色。论文实验设计严谨，代码已开源。


### [Token Sample Complexity of Attention](https://arxiv.org/abs/2512.10656)

**作者**: Bohbot, Letrouit, PeyrÃ©, Vialard  
**链接**: [arXiv](https://arxiv.org/abs/2512.10656) · [PDF](https://arxiv.org/pdf/2512.10656)  \| [📖 全文分析](paper_2512.10656.md)  
**评分**: 8.24  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文由来自法国知名研究机构的学者团队（Bohbot, Letrouit, Peyré, Vialard）提出，他们通常与巴黎高等师范学院、巴黎文理研究大学等机构相关，在数学与机器学习交叉领域有深厚的研究背景。论文针对大语言模型中注意力机制在超长序列下的行为进行了理论分析，首次提出了'Token样本复杂度'的概念，从理论上刻画了注意力映射在有限token数下收敛到无限token极限的收敛速率。论文创新性强，理论分析严谨，并通过合成数据和真实BERT模型实验验证了理论预测。


### [An evolutionary perspective on modes of learning in Transformers](https://arxiv.org/abs/2505.09855)

**作者**: Ku, Griffiths, Chan  
**链接**: [arXiv](https://arxiv.org/abs/2505.09855) · [PDF](https://arxiv.org/pdf/2505.09855)  \| [📖 全文分析](paper_2505.09855.md)  
**评分**: 8.22  （novelty: 9.0 | method: 9.5 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种从进化生物学视角理解Transformer学习模式的新颖框架，将Transformer的权重学习（IWL）与上下文学习（ICL）分别类比为遗传进化和表型可塑性，并通过可控任务环境（正弦回归和Omniglot分类）系统研究了环境稳定性与线索可靠性对学习策略选择的影响。研究发现，稳定环境偏好IWL，而可靠线索在波动环境中偏好ICL，并揭示了策略转换受策略渐近最优性和优化成本共同支配的机制。作者团队来自普林斯顿大学（Thomas L. Griffiths教授团队）和斯坦福大学（Stephanie C. Y. Chan），在认知科学、机器学习交叉领域具有深厚的研究背景。


### [Scaling Laws are Redundancy Laws](https://arxiv.org/abs/2509.20721)

**作者**: Bi, Calhoun  
**链接**: [arXiv](https://arxiv.org/abs/2509.20721) · [PDF](https://arxiv.org/pdf/2509.20721)  \| [📖 全文分析](paper_2509.20721.md)  
**评分**: 8.19  （novelty: 9.5 | method: 9.0 | evidence: 8.0 | clarity: 8.0）

> 本文从理论角度深入探讨了深度学习中的缩放定律（Scaling Laws）的数学本质，提出将其解释为冗余定律（Redundancy Laws）的创新观点。论文通过核回归分析，建立了数据协方差谱的多项式尾部与超额风险幂律之间的数学关系，揭示了缩放指数并非普适，而是取决于数据冗余度。研究证明了该定律在多种场景下的普适性，包括有界可逆变换、多模态混合、有限宽度近似以及Transformer架构的线性和特征学习机制。这项工作首次为缩放定律提供了严格的数学解释，统一了经验观察与理论基础。

