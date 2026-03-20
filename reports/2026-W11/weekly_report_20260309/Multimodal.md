# Multimodal · 2026-03-09 ~ 2026-03-13

**论文数**: 20 篇

---

## 📊 趋势分析

### 研究全貌

本批次多模态领域的研究呈现出多元化且深入的特点，主要研究方向可归纳为以下几个方面：**数据质量与模型训练**、**模态对齐与理解**、**推理能力评估与提升**以及**具身智能与机器人应用**。数据质量研究强调通过高质量数据集和系统性流水线提升模型性能；模态对齐工作则深入诊断文本与图像模态间的性能差距；推理能力研究不仅构建了新的诊断性基准，还提出了多种提升推理可靠性的方法；具身智能方向则聚焦于视觉-语言-动作模型在复杂环境中的鲁棒性。当前的热点问题是如何构建更可靠、可解释且能处理复杂时空信息的多模态模型。整体研究趋势正从追求通用能力向解决具体瓶颈（如模态差距、过程幻觉、视觉杂乱）和提升模型在特定任务（如持续学习、细粒度推理）上的实用性转变。

### 重点方法深度解析

从这批论文中，有几个工作因其创新性和实用价值而尤为突出：

**《Bee: A High-Quality Corpus and Full-Stack Suite to Unlock Advanced Fully Open MLLMs》** [URL](https://arxiv.org/abs/2510.13795)
*   **核心创新点**：针对完全开源多模态大语言模型（MLLMs）因数据质量不足而落后的问题，提出了一个系统性解决方案，核心是构建高质量数据集和开源完整的数据治理工具链。
*   **技术细节**：1）构建了约1500万QA对的Honey-Data-15M数据集，通过多重清洗技术和创新的**双级（短链与长链）思维链（CoT）增强策略**来提升数据复杂性。2）开源了数据治理流水线HoneyPipe及其底层框架DataStudio，提供了透明、可复现的数据处理方法论，超越了静态数据集发布。3）基于该数据集训练了8B参数的Bee-8B模型。
*   **效果验证**：Bee-8B在多个基准测试中为完全开源MLLMs建立了新的SOTA，性能可与甚至超越部分半开源模型（如InternVL3.5-8B）。
*   **适用场景**：适用于任何希望从头构建或微调高质量开源MLLMs的研究团队和开发者，其数据治理方法论和工具链具有极高的借鉴价值。

**《Reading, Not Thinking: Understanding and Bridging the Modality Gap When Text Becomes Pixels in Multimodal LLMs》** [URL](https://arxiv.org/abs/2603.09095)
*   **核心创新点**：系统性地诊断了MLLMs在处理图像化文本时出现的“模态差距”问题，并提出了一种基于自蒸馏的训练方法来弥合这一差距。
*   **技术细节**：通过大规模实验（7个模型、7个基准、5种输入模式）发现，模态差距与任务、数据及渲染方式（如字体）强相关。错误分析揭示图像模式主要放大了“阅读错误”（计算、格式错误），而非知识或推理错误。为此，作者提出了一种**自蒸馏方法**：使用模型在纯文本输入下生成的推理轨迹作为监督信号，与对应的图像输入配对进行训练，从而教会模型在视觉输入下进行正确的推理。
*   **效果验证**：该方法在GSM8K数学推理任务上，将图像模式的准确率从30.71%大幅提升至92.72%，并能泛化到未见过的基准上。
*   **适用场景**：适用于所有需要MLLMs处理文档图像、图表、屏幕截图等包含大量文本信息的视觉场景，能显著提升OCR后处理或直接视觉理解的可靠性。

**《Let's Think in Two Steps: Mitigating Agreement Bias in MLLMs with Self-Grounded Verification》** [URL](https://arxiv.org/abs/2507.11662)
*   **核心创新点**：发现了MLLMs在作为验证器（评估智能体行为轨迹）时存在的“同意偏见”（倾向于过度认可任何行为），并提出了一种轻量级的自锚定验证（SGV）方法来缓解此问题。
*   **技术细节**：SGV方法分为两步：首先，引导MLLM**独立于待评估轨迹**，生成关于期望行为的广泛先验知识（“应该做什么”）。然后，**基于这些自生成的先验**，对具体的候选轨迹进行推理和评估。这种方法通过调制条件与非条件生成，更好地利用了MLLM的推理能力。
*   **效果验证**：在网页导航、计算机使用和机器人操作等领域的实验中，SGV将失败检测率提升了25个百分点，准确率提升14个百分点，并在自改进和在线监督任务中显著提升了智能体的任务完成率。
*   **适用场景**：适用于使用MLLMs进行奖励建模、轨迹评估、行为对齐或在线监督的任何应用，如机器人策略学习、AI智能体评估与引导。

**《Overcoming Visual Clutter in Vision Language Action Models via Concept-Gated Visual Distillation》** [URL](https://arxiv.org/abs/2603.10340)
*   **核心创新点**：针对视觉-语言-动作（VLA）模型在杂乱环境中因背景干扰导致“精确推理鸿沟”的问题，提出了一种训练无关、模型无关的推理时视觉蒸馏框架。
*   **技术细节**：CGVD框架包含几个关键步骤：1）将指令解析为“安全集”和“干扰集”；2）通过**交叉验证和空间消歧的两层目标精炼过程**来隔离真正的操作目标；3）利用**基于傅里叶的修复技术**对原始观察图像进行处理，生成一个“干净”的图像，该图像主动抑制语义干扰物，同时保留关键的空间几何和视觉本体感觉信息。
*   **效果验证**：在高度杂乱的机器人操作任务中，该方法将成功率从基线的43.0%大幅提升至77.5%，有效防止了性能崩溃。
*   **适用场景**：直接适用于真实世界机器人抓取、操作等任务，特别是在家庭、仓库等存在大量视觉干扰物的非结构化环境中，能提升VLA策略的鲁棒性。

### 实践启示

这些研究为多模态大模型的应用开发提供了清晰的路径和重要警示。首先，**数据质量是基石**，开源社区应借鉴Bee工作的系统化数据治理思想，而不仅仅是使用现成数据集。其次，在涉及**文本图像理解**（如文档处理）的应用中，必须关注并测试“模态差距”，可采用自蒸馏等方法来针对性提升性能。再者，当计划使用MLLMs进行**自动化评估或奖励生成**时（如智能体训练），需警惕“同意偏见”，SGV的两步法提供了一个简单有效的缓解思路。最后，对于**机器人或具身AI应用**，在部署到复杂环境前，应重点考虑推理时的视觉抗干扰能力，CGVD这类无需重新训练的后处理框架具有很高的落地价值。实现时需注意，许多提升方法（如SGV、CGVD）会增加单次推理的计算或步骤，需在性能提升与延迟/成本之间进行权衡。

---

## 📄 论文列表（20 篇）

### [Bee: A High-Quality Corpus and Full-Stack Suite to Unlock Advanced Fully Open MLLMs](https://arxiv.org/abs/2510.13795)

**作者**: Zhang, Ni, Chen, Zhang, Rao 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.13795) · [PDF](https://arxiv.org/pdf/2510.13795)  \| [📖 全文分析](paper_2510.13795.md)  
**评分**: 8.71  （novelty: 9.0 | method: 9.0 | evidence: 9.0 | clarity: 8.0）

> 本文针对当前完全开源多模态大语言模型（MLLMs）因数据质量不足而落后于闭源模型的问题，提出了一个系统性解决方案。作者团队（从作者姓名看，未明确标注来自知名机构，故省略背景介绍）的核心贡献包括：1）构建了高质量、大规模且包含复杂推理链（CoT）的SFT数据集Honey-Data-15M；2）开源了完整的数据处理流水线HoneyPipe及其底层框架DataStudio，提供了透明、可复现的数据治理方法论；3）基于该数据集训练了Bee-8B模型，在多个基准测试中达到了完全开源MLLMs的新SOTA，部分性能甚至超越了半开源模型。这项工作强调了数据质量对模型性能的关键作用，并为社区提供了一套从数据到模型再到评估的完整工具链，具有重要的开源生态价值。


### [Reading, Not Thinking: Understanding and Bridging the Modality Gap When Text Becomes Pixels in Multimodal LLMs](https://arxiv.org/abs/2603.09095)

**作者**: Sun, Yuan, Liu, Zhao, Zhang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.09095) · [PDF](https://arxiv.org/pdf/2603.09095)  \| [📖 全文分析](paper_2603.09095.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由约翰斯·霍普金斯大学、马里兰大学等研究团队（作者包括知名学者Mark Dredze）对多模态大语言模型中的模态差距问题进行了系统性研究。论文通过大规模实验揭示了文本图像化处理中的性能下降现象，并提出了一种基于自蒸馏的训练方法，显著提升了模型在视觉文本理解任务上的性能。研究创新性强，实验设计严谨，为多模态模型优化提供了重要见解。


### [Daily-Omni: Towards Audio-Visual Reasoning with Temporal Alignment across Modalities](https://arxiv.org/abs/2505.17862)

**作者**: Zhou, Wang, Wu, Jiang  
**链接**: [arXiv](https://arxiv.org/abs/2505.17862) · [PDF](https://arxiv.org/pdf/2505.17862)  \| [📖 全文分析](paper_2505.17862.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了Daily-Omni，一个专注于跨模态时序推理的音频-视觉问答基准。该工作系统地构建了一个包含真实世界视频和复杂问题的数据集，并开发了半自动标注流程以确保数据质量。通过大规模的诊断性评估（24个基础模型，37种模态设置），揭示了当前多模态大语言模型在跨模态时序对齐任务上的显著不足，为该领域指出了一个重要的开放挑战。


### [Simple Recipe Works: Vision-Language-Action Models are Natural Continual Learners with Reinforcement Learning](https://arxiv.org/abs/2603.11653)

**作者**: Hu, Shim, Tang, Sung, Liu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.11653) · [PDF](https://arxiv.org/pdf/2603.11653)  \| [📖 全文分析](paper_2603.11653.md)  
**评分**: 8.64  （novelty: 8.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由德克萨斯大学奥斯汀分校（UT Austin）机器人研究团队提出，该团队在机器人学习领域具有较强影响力。论文对视觉-语言-动作模型的持续强化学习进行了系统性研究，发现简单的顺序微调配合低秩适配在多个基准测试中表现出色，挑战了传统持续学习中灾难性遗忘的固有认知。研究设计严谨，代码开源，为大规模预训练模型的持续学习提供了新见解。


### [Vision Language Models Cannot Reason About Physical Transformation](https://arxiv.org/abs/2603.07109)

**作者**: Luo, Li, Wang, Zhao, Wang 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.07109) · [PDF](https://arxiv.org/pdf/2603.07109)  \| [📖 全文分析](paper_2603.07109.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文针对当前视觉语言模型在物理变换理解方面的能力进行了系统性评估，提出了ConservationBench基准测试，揭示了现有模型在保持物理量守恒方面的系统性缺陷。研究发现，即使模型在守恒任务上有所改进，也会在控制任务上表现下降，表明模型并未真正理解物理变换的本质。作者团队来自学术界，但未明确标注具体机构。


### [Modality Collapse as Mismatched Decoding: Information-Theoretic Limits of Multimodal LLMs](https://arxiv.org/abs/2602.23136)

**作者**: Billa  
**链接**: [arXiv](https://arxiv.org/abs/2602.23136) · [PDF](https://arxiv.org/pdf/2602.23136)  \| [📖 全文分析](paper_2602.23136.md)  
**评分**: 8.50  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 7.0）

> 本文从信息论角度对多模态大语言模型（MLLMs）的模态坍缩现象进行了理论分析，提出了将MLLM推理框架化为不匹配解码器问题的创新视角。论文通过理论推导和实验验证，揭示了训练目标（评分规则）而非模型架构是多模态信息提取的关键瓶颈。作者Billa未标注所属机构，故不介绍团队背景。


### [Think While Watching: Online Streaming Segment-Level Memory for Multi-Turn Video Reasoning in Multimodal Large Language Models](https://arxiv.org/abs/2603.11896)

**作者**: Wang, Jin, Hao, Chen, Liu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.11896) · [PDF](https://arxiv.org/pdf/2603.11896)  \| [📖 全文分析](paper_2603.11896.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为'Think While Watching'的在线流式视频推理框架，针对多模态大语言模型在连续视频流多轮交互中的局限性进行了创新性改进。该方法通过构建分段级记忆机制、三阶段多轮思维链数据集以及阶段匹配训练策略，实现了观看与思考的并行处理，显著提升了流式视频推理的性能。论文在StreamingBench和OVO-Bench等基准测试上取得了显著效果提升，并开源了代码。


### [Learning Transferable Sensor Models via Language-Informed Pretraining](https://arxiv.org/abs/2603.11950)

**作者**: Chen, Pillai, Wu, Griffin, Marsch 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.11950) · [PDF](https://arxiv.org/pdf/2603.11950)  \| [📖 全文分析](paper_2603.11950.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为SLIP（Sensor Language-Informed Pretraining）的新型传感器-语言预训练框架，旨在解决现有方法在跨传感器配置泛化方面的局限性。该方法通过结合对比对齐与传感器条件描述生成，实现了对多样化传感器设置的通用表示学习。在11个数据集上的实验表明，SLIP在零样本迁移、信号描述和问答任务上均取得了优越性能，平均线性探测准确率达到77.14%，相比基线相对提升5.93%。


### [ORIC: Benchmarking Object Recognition under Contextual Incongruity in Large Vision-Language Models](https://arxiv.org/abs/2509.15695)

**作者**: Li, Ling, Zhou, Gong, BÄ±yÄ±k 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.15695) · [PDF](https://arxiv.org/pdf/2509.15695)  \| [📖 全文分析](paper_2509.15695.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种评估大型视觉语言模型在上下文不一致场景下物体识别能力的框架ORIC。作者团队来自学术界，论文针对LVLMs在非典型场景中容易漏检或幻觉物体的问题，从不确定性的角度切入，构建了系统的评估基准和训练数据。研究问题具有现实意义，实验设计全面，代码和数据已开源。


### [Can Vision-Language Models Solve the Shell Game?](https://arxiv.org/abs/2603.08436)

**作者**: Liu, Lee  
**链接**: [arXiv](https://arxiv.org/abs/2603.08436) · [PDF](https://arxiv.org/pdf/2603.08436)  \| [📖 全文分析](paper_2603.08436.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文针对视觉语言模型在视觉实体跟踪方面的核心缺陷，提出了创新的解决方案。论文首先揭示了现有视频基准测试中的视觉捷径问题，并创建了VET-Bench诊断测试平台来暴露VLMs在跟踪视觉相同物体时的根本局限性。通过理论分析证明了基于固定深度Transformer的VLMs在跟踪不可区分物体时的表达能力限制，并提出了Spatiotemporal Grounded Chain-of-Thought（SGCoT）方法，通过生成物体轨迹作为显式中间状态，结合Molmo2的物体跟踪能力和合成文本数据的微调，实现了超过90%的准确率。实验设计严谨，代码和数据已开源。


### [ALARM: Audio-Language Alignment for Reasoning Models](https://arxiv.org/abs/2603.09556)

**作者**: Grinberg, Shahmohammadi  
**链接**: [arXiv](https://arxiv.org/abs/2603.09556) · [PDF](https://arxiv.org/pdf/2603.09556)  \| [📖 全文分析](paper_2603.09556.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种针对推理型大语言模型的音频-语言对齐方法ALARM，解决了现有冻结LLM训练适配器方法在推理模型上暴露文本代理输入导致不自然响应的问题。通过自重构技术将自生成响应转换为音频理解变体，并融合压缩多个音频编码器增强表示能力。在构建的大规模多任务语料库上训练，取得了优异的音频推理性能，同时保持了文本能力。作者Grinberg和Shahmohammadi未在摘要中明确所属机构，故省略团队背景介绍。


### [Let's Think in Two Steps: Mitigating Agreement Bias in MLLMs with Self-Grounded Verification](https://arxiv.org/abs/2507.11662)

**作者**: Andrade, Cha, Ho, Srihari, Yadav 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2507.11662) · [PDF](https://arxiv.org/pdf/2507.11662)  \| [📖 全文分析](paper_2507.11662.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.0）

> 本文提出了一种名为SGV（Self-Grounded Verification）的轻量级方法，用于缓解多模态大语言模型（MLLMs）在作为验证器（verifier）时存在的‘同意偏见’（agreement bias）问题。该方法通过引导MLLM先生成关于期望行为的广泛先验知识，再基于此先验知识评估具体轨迹，从而提升了验证器与人类判断的对齐程度。实验在网页导航、计算机使用和机器人操作等多个领域展开，涉及13个以上模型和数千条轨迹，结果显示该方法显著提升了失败检测率和任务完成率。作者团队（Andrade, Cha, Ho, Srihari, Yadav, Kira）未明确标注其所属机构，因此省略团队背景介绍。


### [PaLMR: Towards Faithful Visual Reasoning via Multimodal Process Alignment](https://arxiv.org/abs/2603.06652)

**作者**: Li, Hui, Yan, Cheng, Zhao 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.06652) · [PDF](https://arxiv.org/pdf/2603.06652)  \| [📖 全文分析](paper_2603.06652.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为PaLMR的新框架，旨在解决多模态大语言模型（MLLMs）在视觉推理中存在的‘过程幻觉’问题。该框架通过构建感知对齐的数据层和过程对齐的优化层，不仅关注最终答案的正确性，更强调推理过程与视觉证据的对齐。实验表明，该方法在HallusionBench上取得了最先进的结果，并在MMMU、MathVista和MathVerse上保持了强劲性能，为提升MLLMs的可靠性和可解释性提供了一条有原则且实用的路径。


### [MMGraphRAG: Bridging Vision and Language with Interpretable Multimodal Knowledge Graphs](https://arxiv.org/abs/2507.20804)

**作者**: Wan, Yu  
**链接**: [arXiv](https://arxiv.org/abs/2507.20804) · [PDF](https://arxiv.org/pdf/2507.20804)  \| [📖 全文分析](paper_2507.20804.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种创新的多模态知识图谱增强生成方法MMGraphRAG，通过引入SpecLink跨模态实体链接技术和路径检索机制，有效解决了传统GraphRAG方法在视觉结构知识保留和跨模态推理方面的不足。该方法在CMEL、DocBench和MMLongBench等多个基准测试中取得了最先进的性能，展现了强大的领域适应性和多模态信息处理能力。作者团队发布了专门设计的CMEL数据集，为细粒度多实体对齐研究提供了重要资源。


### [From Spatial to Actions: Grounding Vision-Language-Action Model in Spatial Foundation Priors](https://arxiv.org/abs/2510.17439)

**作者**: Zhang, Li, Dai, Zhu, Zhou 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.17439) · [PDF](https://arxiv.org/pdf/2510.17439)  \| [📖 全文分析](paper_2510.17439.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为FALCON的新型视觉-语言-动作模型范式，通过将丰富的3D空间令牌注入动作头部，解决了现有VLA模型因基于2D编码器而存在的空间推理差距问题。该方法利用空间基础模型从RGB图像中提取几何先验，并设计了可选的具身空间模型来融合深度或姿态信息，同时通过空间增强动作头部保持语言推理能力。在三个仿真基准和十一个真实世界任务上的综合评估表明，FALCON实现了最先进的性能，并展现出良好的鲁棒性。


### [BiCLIP: Domain Canonicalization via Structured Geometric Transformation](https://arxiv.org/abs/2603.08942)

**作者**: Mantini, Shah  
**链接**: [arXiv](https://arxiv.org/abs/2603.08942) · [PDF](https://arxiv.org/pdf/2603.08942)  \| [📖 全文分析](paper_2603.08942.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为BiCLIP的框架，通过结构化几何变换实现领域规范化，以增强跨模态对齐。该方法基于理论洞察，假设不同领域的图像特征可以通过少量锚点恢复的规范化几何变换相关联。在11个标准基准测试中，BiCLIP均取得了最先进的结果，验证了其有效性。


### [TikArt: Stabilizing Aperture-Guided Fine-Grained Visual Reasoning with Reinforcement Learning](https://arxiv.org/abs/2602.14482)

**作者**: Ding, Yang, Ge, Gao, Lu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.14482) · [PDF](https://arxiv.org/pdf/2602.14482)  \| [📖 全文分析](paper_2602.14482.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为TikArt的孔径引导多模态推理方法，通过强化学习训练模型在细粒度视觉推理任务中执行序列化的证据采集。该方法在多个高分辨率推理和分割任务上取得了优于基线的性能。作者团队来自阿里巴巴（Qwen系列模型的开发者），在大型语言模型和多模态AI领域具有深厚的技术积累和影响力。


### [Overcoming Visual Clutter in Vision Language Action Models via Concept-Gated Visual Distillation](https://arxiv.org/abs/2603.10340)

**作者**: Song, Kodagoda, Carmichael, Thiyagarajan  
**链接**: [arXiv](https://arxiv.org/abs/2603.10340) · [PDF](https://arxiv.org/pdf/2603.10340)  \| [📖 全文分析](paper_2603.10340.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为概念门控视觉蒸馏（CGVD）的训练无关、模型无关的推理框架，用于解决视觉-语言-动作（VLA）模型在杂乱环境中存在的“精确推理鸿沟”问题。该方法通过指令解析、目标精炼和基于傅里叶的修复技术，在推理时主动抑制语义干扰，显著提升了VLA策略在高度杂乱操作任务中的鲁棒性和成功率。实验结果表明，在密集语义干扰环境中，该方法将成功率从基线的43.0%大幅提升至77.5%。


### [SciMDR: Benchmarking and Advancing Scientific Multimodal Document Reasoning](https://arxiv.org/abs/2603.12249)

**作者**: Chen, Zhao, Wang, Han, Patwardhan 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12249) · [PDF](https://arxiv.org/pdf/2603.12249)  \| [📖 全文分析](paper_2603.12249.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种创新的合成与再锚定框架（synthesize-and-reground framework），用于构建科学多模态文档推理数据集，解决了规模、忠实度和真实性之间的权衡问题。基于该框架构建的SciMDR数据集包含30万QA对和显式推理链，其评估基准SciMDR-Eval由专家标注。实验表明，在SciMDR上微调的模型在多个科学QA基准上取得了显著提升，特别是在需要复杂文档级推理的任务中。


### [Frequency-Modulated Visual Restoration for Matryoshka Large Multimodal Models](https://arxiv.org/abs/2603.11220)

**作者**: Pan, Dou, Li  
**链接**: [arXiv](https://arxiv.org/abs/2603.11220) · [PDF](https://arxiv.org/pdf/2603.11220)  \| [📖 全文分析](paper_2603.11220.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为FMVR（Frequency-Modulated Visual Restoration）的即插即用视觉恢复策略，旨在解决大型多模态模型（LMMs）因视觉令牌数量过多而难以适应不同计算预算的问题。该方法通过将少量视觉令牌的表示解耦为低频和高频分量，并利用轻量级可学习参数进行调制，有效保留了关键视觉语义并恢复了被稀释的语义。通过在10个图像基准和4个视频基准上的实验验证，FMVR-LLaVA将LLaVA-1.5-7B的FLOPs降低了89%，同时保持了接近100%的原始准确率。作者承诺将开源代码。

