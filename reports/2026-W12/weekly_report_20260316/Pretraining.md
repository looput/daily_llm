# Pretraining · 2026-03-16 ~ 2026-03-20

**论文数**: 11 篇

---

## 📊 趋势分析

### 研究全貌
本批次论文聚焦于预训练（Pretraining）领域的核心挑战与前沿探索，研究方向主要围绕**数据策略优化**、**模型架构创新**与**训练过程改进**三大方面。在数据策略上，研究关注如何更高效地利用领域数据以提升专业化能力，并解决大规模数据预处理中质量与规模的权衡问题。在模型架构上，探索了注意力机制、残差连接、位置编码等基础组件的改进，旨在提升效率与表达能力。训练过程方面，则深入研究了学习率调度、中期训练等策略对下游任务适应性的影响。当前的热点问题是如何在有限的算力与数据下，通过更精巧的设计（而非单纯堆叠规模）来提升模型的通用与专用性能。整体研究趋势呈现出从“粗放式”规模扩张向“精细化”设计与过程控制转变，强调方法的可解释性、可迁移性与计算效率。

### 重点方法深度解析
从这批论文中，以下几个工作因其深刻的洞见与显著的实用价值而尤为突出：

**1. 《The Finetuner‘s Fallacy: When to Pretrain with Your Finetuning Data》 [URL](https://arxiv.org/abs/2603.16177)**
*   **核心创新点**：该研究挑战了“先通用预训练，后领域微调”的传统范式，揭示了“微调者谬误”——即微调看似是领域适应的捷径，实则可能导致过拟合与通用知识遗忘。为此，它提出了“专业化预训练”策略，主张将少量领域数据提前、重复地引入预训练阶段。
*   **技术细节**：SPT方法的核心是在预训练语料中，将原本用于微调的少量领域数据作为一部分（例如占总token数的某个比例）进行重复采样。研究者还推导了过拟合缩放定律，为给定计算预算下选择最优的领域数据重复比例提供了理论指导。
*   **效果验证**：在ChemPile、MusicPile和ProofPile三个专业领域上，SPT在微调后不仅提升了领域性能，还更好地保留了通用能力。对于在预训练语料中代表性不足的领域，其优势更为明显：一个10亿参数的SPT模型甚至能超越一个30亿参数的标准预训练模型。该方法最高可将达到特定领域性能所需的预训练token数减少1.75倍。
*   **适用场景**：该方法特别适用于数据稀缺但需求明确的垂直领域（如法律、生物、金融），是构建高质量领域专用模型的实用策略。

**2. 《FLUX: Data Worth Training On》 [URL](https://arxiv.org/abs/2603.13972)**
*   **核心创新点**：针对大规模预训练中数据质量与规模不可兼得的痛点，FLUX提出了一种新型数据预处理流水线，旨在同时实现高数据保留率和严格的质量控制，打破长期存在的权衡。
*   **技术细节**：FLUX的具体技术细节未在摘要中完全展开，但其核心思想是通过创新的过滤与清洗策略，在保证数据高质量的同时，最大化从原始网络语料中提取可用token的数量。
*   **效果验证**：使用FLUX处理的数据训练的模型在多个基准测试中表现优异。例如，一个在600亿FLUX token上训练的30亿参数模型，在MMLU上达到了32.14%的准确率，超越了此前的最佳方法。FLUX从单次网络快照中提取了500亿可用token，比DCLM方法多出25%。在达到相同性能的情况下，FLUX可节省高达34.4%的训练计算量。
*   **适用场景**：适用于任何需要从头开始构建大规模、高质量预训练数据集的场景，对于希望优化训练效率与模型性能的研究机构和企业具有直接价值。

**3. 《Pre-training LLM without Learning Rate Decay Enhances Superformed Fine-Tuning》 [URL](https://arxiv.org/abs/2603.16127)**
*   **核心创新点**：该工作挑战了预训练中广泛使用的学习率衰减策略，发现尽管衰减有助于降低预训练损失，但可能导致模型陷入尖锐的最小值，损害其在下游任务微调后的适应性。因此，它提出在预热后保持恒定学习率。
*   **技术细节**：该方法称为Warmup-Stable-Only，即在预热阶段结束后，学习率不再衰减，而是保持恒定直至训练结束。损失景观分析证实，WSO策略能使模型收敛到更平坦的最小值区域。
*   **效果验证**：在10亿和80亿参数模型的实验中，WSO在监督微调后的下游任务性能上持续优于使用衰减策略的模型，即使后者在预训练结束时的损失更低。这一结论在不同训练阶段（中期、过度训练）均成立。
*   **适用场景**：适用于所有计划进行下游任务微调的大模型预训练过程。这为模型训练和发布策略提供了重要指导：旨在提升下游适应性的预训练，应谨慎使用学习率衰减。

**4. 《Attention Residuals》 [URL](https://arxiv.org/abs/2603.15031)**
*   **核心创新点**：针对标准残差连接中固定权重累加导致深层网络隐藏状态增长和层贡献稀释的问题，提出用基于注意力的动态加权聚合来替代固定累加。
*   **技术细节**：AttnRes让每一层通过softmax注意力机制，学习并聚合前面所有层的输出表示，权重取决于当前输入。为降低大规模训练时的开销，进一步提出了Block AttnRes，将层分组为块，在块级别进行注意力聚合，并结合缓存通信和两阶段计算策略，使其成为可高效实现的即插即用模块。
*   **效果验证**：缩放定律实验表明其收益在不同模型规模下一致。集成到480亿总参数（30亿激活）的Kimi Linear架构并在1.4万亿token上预训练后，AttnRes缓解了梯度分布不均的问题，并在所有评估的下游任务上提升了性能。
*   **适用场景**：适用于构建更深或更复杂的大模型架构，尤其关注改善模型内部的表示流动和梯度传播，是提升模型表达能力和训练稳定性的有效工具。

### 实践启示
这批研究为实际的大模型应用开发提供了清晰的优化路径。**对于垂直领域应用**，应优先借鉴《The Finetuner‘s Fallacy》的洞见，将领域数据前置到预训练阶段，而非仅依赖最终微调，这能以更小的模型规模和计算成本获得更优的领域性能与通用性平衡。**在构建自有预训练数据管线时**，《FLUX》的方法指明了同时追求高质量与高保留率的方向，是降低训练成本的关键。**在模型训练策略上**，《Pre-training LLM without Learning Rate Decay》的发现提醒我们，评估预训练模型不应只看其损失曲线，更应关注其在下游任务上的适应性潜力，采用WSO等策略可能带来更好的微调起点。**在模型架构选型或改进时**，《Attention Residuals》等对基础组件的创新工作值得关注，它们能以较小的改动代价带来性能提升。

具体落地时需注意：SPT策略需要精确控制领域数据的混合比例，可参考其提出的缩放定律进行调优；采用恒定学习率策略时，需确保有足够长的稳定训练阶段；而架构层面的改进（如AttnRes）在引入时需仔细评估其带来的额外计算与内存开销是否在可接受范围内。整体而言，当前预训练研究正从“大力出奇迹”转向“四两拨千斤”，精细化、可解释的设计与过程控制是实现高效能模型的关键。

---

## 📄 论文列表（11 篇）

### [The Finetuner's Fallacy: When to Pretrain with Your Finetuning Data](https://arxiv.org/abs/2603.16177)

**作者**: Baek, Monti, Schwab, Abbas, Adiga 等 34 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16177) · [PDF](https://arxiv.org/pdf/2603.16177)  \| [📖 全文分析](paper_2603.16177.md)  
**评分**: 8.86  （novelty: 9.0 | method: 9.0 | evidence: 9.0 | clarity: 8.5）

> 本文由来自卡内基梅隆大学（CMU）、斯坦福大学（Stanford）、Google、Meta等知名高校和机构的联合研究团队提出。作者深入研究了模型在数据稀缺的垂直领域进行专业化部署时面临的核心挑战，揭示了传统微调方法存在的“微调者谬误”，并提出了一种名为“专业化预训练”的简单而有效的策略。该方法通过在预训练阶段重复引入少量领域数据，显著提升了模型在目标领域的性能，同时更好地保留了通用能力。论文在多个专业领域进行了充分的实验验证，并推导了过拟合缩放定律来指导实践，具有重要的理论价值和实践指导意义。


### [Omnilingual SONAR: Cross-Lingual and Cross-Modal Sentence Embeddings Bridging Massively Multilingual Text and Speech](https://arxiv.org/abs/2603.16606)

**作者**: Omnilingual SONAR Team, Janeiro, Cabot, Tsiamas, Meng 等 19 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16606) · [PDF](https://arxiv.org/pdf/2603.16606)  \| [📖 全文分析](paper_2603.16606.md)  
**评分**: 8.71  （novelty: 9.5 | method: 9.0 | evidence: 9.0 | clarity: 8.0）

> 本文由Meta AI（原Facebook AI Research）团队提出，该团队在自然语言处理和多模态学习领域具有国际领先地位。论文提出了一种名为OmniSONAR的全新跨语言、跨模态句子嵌入模型家族，能够将文本、语音、代码和数学表达式嵌入到统一的语义空间中，并在数千种语言上实现了最先进的性能。该方法创新性地结合了渐进式训练、LLM初始化、新型对比损失和师生蒸馏框架，实验设计全面，证据充分。


### [Genomic Next-Token Predictors are In-Context Learners](https://arxiv.org/abs/2511.12797)

**作者**: Breslow, Mishra, Revsine, Schatz, Liu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.12797) · [PDF](https://arxiv.org/pdf/2511.12797)  \| [📖 全文分析](paper_2511.12797.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.5）

> 本文探讨了基因组序列模型是否能够像语言模型一样，通过大规模预测训练自然涌现出上下文学习能力。研究团队开发了受控实验框架，在语言和基因组形式中实例化符号推理任务，直接比较基因组模型和语言模型的上下文学习表现。结果表明，基因组模型确实表现出与语言模型相似的上下文学习能力，支持上下文学习是大规模预测建模在丰富数据上的自然结果的假设。这些发现将涌现的元学习能力扩展到了语言之外，指向了一种统一的、与模态无关的上下文学习观点。


### [FLUX: Data Worth Training On](https://arxiv.org/abs/2603.13972)

**作者**: Gowtham, Rupesh, Kumar, Saravanan, Chaithanya  
**链接**: [arXiv](https://arxiv.org/abs/2603.13972) · [PDF](https://arxiv.org/pdf/2603.13972)  \| [📖 全文分析](paper_2603.13972.md)  
**评分**: 8.57  （novelty: 8.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为FLUX的新型数据预处理流水线，旨在解决大规模语言模型训练中数据规模与质量之间的权衡问题。该方法通过同时实现高数据保留率和严格质量控制，在多个基准测试中超越了现有方法，显著降低了训练计算成本。作者团队未在摘要中明确提及所属机构，因此省略团队背景介绍。


### [Rethinking Attention: Polynomial Alternatives to Softmax in Transformers](https://arxiv.org/abs/2410.18613)

**作者**: Saratchandran, Zheng, Ji, Zhang, Lucey  
**链接**: [arXiv](https://arxiv.org/abs/2410.18613) · [PDF](https://arxiv.org/pdf/2410.18613)  \| [📖 全文分析](paper_2410.18613.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种对Transformer注意力机制的新视角，挑战了softmax必须产生概率分布的传统认知。作者认为softmax的有效性源于其对注意力矩阵Frobenius范数的隐式正则化作用，并探索了多项式激活函数作为替代方案。理论分析和大量实验表明，某些多项式函数能够达到与softmax相当的性能，即使它们不满足正性、归一化和稀疏性等典型性质。这一研究为注意力机制的设计提供了新的理论洞见和实用替代方案。


### [Pre-training LLM without Learning Rate Decay Enhances Supervised Fine-Tuning](https://arxiv.org/abs/2603.16127)

**作者**: Yano, Kiyono, Kobayashi, Takase, Suzuki  
**链接**: [arXiv](https://arxiv.org/abs/2603.16127) · [PDF](https://arxiv.org/pdf/2603.16127)  \| [📖 全文分析](paper_2603.16127.md)  
**评分**: 8.57  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文研究了大规模语言模型预训练中学习率调度策略对监督微调后下游性能的影响，提出了一种名为Warmup-Stable-Only（WSO）的恒定学习率调度方法。通过1B和8B参数模型的实验表明，WSO在监督微调后的性能上持续优于传统的衰减式调度器，即使后者在预训练阶段表现更好。损失景观分析进一步揭示了衰减式调度器导致模型陷入更尖锐的最小值，而WSO保持更平坦的最小值，从而支持更好的适应性。这些发现表明，为改善预训练指标而应用学习率衰减可能会损害下游任务的适应性。


### [MANAR: Memory-augmented Attention with Navigational Abstract Conceptual Representation](https://arxiv.org/abs/2603.18676)

**作者**: Jahshan, Ishay, Yavits  
**链接**: [arXiv](https://arxiv.org/abs/2603.18676) · [PDF](https://arxiv.org/pdf/2603.18676)  \| [📖 全文分析](paper_2603.18676.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为MANAR的新型注意力机制，该机制基于全局工作空间理论（GWT），通过引入可训练的抽象概念记忆和抽象概念表示（ACR），对标准多头注意力（MHA）进行了泛化。MANAR实现了线性时间复杂度的注意力计算，同时保持了与预训练Transformer的兼容性，支持权重迁移。在语言（GLUE）、视觉（ImageNet）和语音（LibriSpeech）等多个领域的基准测试中，其性能均达到或超越了现有基线，展示了其作为高效且表达能力强的二次注意力替代方案的潜力。


### [Attention Residuals](https://arxiv.org/abs/2603.15031)

**作者**: Kimi Team, Chen, Zhang, Su, Xu 等 37 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15031) · [PDF](https://arxiv.org/pdf/2603.15031)  \| [📖 全文分析](paper_2603.15031.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由Kimi Team（月之暗面公司）提出了一种名为Attention Residuals（AttnRes）的新方法，用于改进大型语言模型中的残差连接机制。该方法用基于注意力机制的动态加权聚合替代传统的固定权重累加，有效缓解了深度模型中隐藏状态增长和层贡献稀释的问题。论文通过详实的实验验证了方法的有效性，包括在48B参数的Kimi Linear架构上的大规模预训练，并在多个下游任务上取得了性能提升。


### [A Family of LLMs Liberated from Static Vocabularies](https://arxiv.org/abs/2603.15953)

**作者**: Alpha, :, Abdessaied, Baranowski, Balles 等 38 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15953) · [PDF](https://arxiv.org/pdf/2603.15953)  \| [📖 全文分析](paper_2603.15953.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种基于分层自回归变换器（HAT）架构的大语言模型家族，旨在解决传统基于分词器的LLMs在词汇表固定、领域适应性差等方面的局限性。该方法通过字节级处理、重用预训练模型等技术，在英语和德语任务上超越了原始Llama 3.1模型的表现。作者团队来自多个研究机构，包括德国人工智能研究中心（DFKI）、柏林工业大学等欧洲知名研究机构，具有扎实的研究背景。


### [PRISM: Demystifying Retention and Interaction in Mid-Training](https://arxiv.org/abs/2603.17074)

**作者**: Runwal, Agrawal, Roy, Panda  
**链接**: [arXiv](https://arxiv.org/abs/2603.17074) · [PDF](https://arxiv.org/pdf/2603.17074)  \| [📖 全文分析](paper_2603.17074.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文对大型语言模型的中期训练设计选择进行了全面的实证研究，提出了PRISM方法。研究通过跨七个基础模型、四种模型家族、两种架构类型和不同参数规模的受控实验，系统分析了中期训练对模型推理能力提升的作用机制。论文实验设计严谨，数据充分，为语言模型训练流程优化提供了重要指导。


### [Frayed RoPE and Long Inputs: A Geometric Perspective](https://arxiv.org/abs/2603.18017)

**作者**: Wertheimer, Zhang, Liu, Yin, Wang  
**链接**: [arXiv](https://arxiv.org/abs/2603.18017) · [PDF](https://arxiv.org/pdf/2603.18017)  \| [📖 全文分析](paper_2603.18017.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种针对RoPE位置编码在长输入场景下性能崩溃问题的几何视角解释和改进方法。论文通过理论和实证分析揭示了RoPE导致注意力机制失效的几何机制，并提出了RoPE-ID这一简单有效的改进方案，在LongBench和RULER基准测试上验证了其有效性。作者团队来自学术界，但论文中未明确标注具体机构，因此省略团队背景介绍。

