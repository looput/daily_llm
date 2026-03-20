# RLHF · 2026-03-09 ~ 2026-03-13

**论文数**: 9 篇

---

## 📊 趋势分析

### 研究全貌
本批次RLHF领域的研究论文主要聚焦于几个核心方向：**对齐方法的扩展与优化**、**奖励模型的鲁棒性与安全性分析**，以及**在复杂任务（如多轮对话、检索增强推理）中的信用分配与训练效率提升**。当前的热点问题是如何在更贴近实际应用场景（如非可验证偏好、复合系统、长程交互）中，构建稳定、可靠且可扩展的对齐框架。整体研究趋势呈现出从单一模型、静态反馈的对齐范式，向处理动态交互、系统级优化和对抗性安全等更复杂、更实际问题的深刻转变。

### 重点方法深度解析
从这批论文中，以下几个工作因其对核心挑战的深刻洞察和提出的实用解决方案而尤为突出：

**《Aligning to Illusions: Choice Blindness in Human and AI Feedback》** [URL](https://arxiv.org/abs/2603.08412)
该研究挑战了RLHF的基石假设，即人类偏好是稳定且可被准确标注的。其核心创新在于将心理学中的“选择盲视”概念引入AI对齐领域，通过一系列精巧实验揭示了人类标注者和LLM评判者都存在显著的偏好构造问题。技术细节上，作者设计了三个递进实验：1）在人类研究中，他们暗中调换偏好对，发现高达91%的参与者未察觉；2）测试LLM作为评判者时，发现其“检测”能力依赖于浅层文本匹配，移除上下文中的先前推理会导致盲视率从接近零飙升至50%以上；3）通过“剂量-反应”实验，量化了奖励信号对标签污染的脆弱性。效果验证表明，当50%的标签被污染时，基于奖励的Best-of-N选择效果与随机采样无异，而代理模型却报告单调递增的分数。这项工作适用于任何依赖人类或AI反馈进行对齐的场景，它警示我们，标准评估指标可能无法检测到反馈信号中的系统性偏差。

**《Aligning Compound AI Systems via System-level DPO》** [URL](https://arxiv.org/abs/2502.17721)
随着由多个组件（如LLM、扩散模型、工具）构成的复合AI系统日益普及，如何对其进行端到端的对齐成为一个关键挑战。该论文的核心创新是提出了**SysDPO**框架，将直接偏好优化（DPO）扩展至系统级。其技术关键在于将复合系统建模为**有向无环图（DAG）**，以显式刻画组件间的交互和数据流。针对系统级偏好无法直接分解为组件级偏好的问题，作者提出了两种变体：**SysDPO-Direct**（直接使用系统级偏好数据）和**SysDPO-Sampling**（通过采样构造偏好对）。该方法在语言模型与扩散模型联合对齐、LLM协作系统对齐两个应用场景中验证了有效性。它特别适用于需要协调多个AI组件以完成复杂任务的应用，如AI Agent工作流、多模态生成系统等。

**《Revisiting the (Sub)Optimality of Best-of-N for Inference-Time Alignment》** [URL](https://arxiv.org/abs/2603.05739)
Best-of-N（BoN）采样是实践中广泛使用的推理时对齐方法，但先前理论分析认为其统计次优且易受奖励黑客攻击。本文的核心创新在于**在更贴近实践的假设下（以“胜率”而非“期望真实奖励”为目标）重新审视BoN**。作者证明，在参考模型和奖励模型质量满足基本条件时，经过适当调优的BoN在计算和统计上对于最大化胜率是**最优的**，这解释了其实际成功的原因。同时，针对奖励黑客问题，作者提出了一种简单实用的BoN变体，可证明地消除黑客攻击并保持最优性能。这项研究为BoN这一经典方法提供了坚实的理论辩护和改进方向，适用于所有使用奖励模型进行推理时筛选的场景，强调了目标函数选择对方法评估的重要性。

**《MAPO: Mixed Advantage Policy Optimization for Long-Horizon Multi-Turn Dialogue》** [URL](https://arxiv.org/abs/2603.06194)
在长视野、开放式的多轮主观对话（如情感支持）中，如何进行有效的强化学习训练是一大难题。MAPO算法的核心创新是提出了一个**无评论家（critic-free）且高效的RL框架**，通过**混合优势估计器**解决了信用分配和训练稳定性的问题。技术细节上，它利用评判模型提供**密集的过程反馈**，通过蒙特卡洛回报传播长期影响。其关键组件“混合优势估计器”**结合了回合级归一化和批次级归一化**，实现了细粒度且可扩展的信用分配。在EMPA、EmoBench等多个主观对话基准上，MAPO显著提升了训练稳定性和最终性能（如在EMPA上对话分数提升高达+43.2），并展现出良好的跨任务泛化能力。该方法非常适合需要模型进行多轮、策略性交互的对话应用场景。

### 实践启示
这些研究为大模型应用开发提供了重要借鉴。首先，**必须审慎对待反馈信号的质量**（如“选择盲视”研究所揭示的），在构建高质量偏好数据集和设计鲁棒的评估指标上投入更多精力。其次，对于**复合AI系统（如AI Agent）**，SysDPO提供了系统级对齐的思路，是未来复杂应用落地的关键技术。再者，在**推理阶段**，BoN及其改进变体仍是实用且理论可靠的选择。对于**长程交互任务（如客服、陪伴）**，MAPO等结合密集过程反馈的RL算法值得关注。

具体建议如下：1）在部署基于奖励模型的筛选机制前，应对奖励模型进行对抗性压力测试（参考“Reward Under Attack”），避免奖励黑客；2）开发复合系统时，可借鉴DAG建模思想，并探索SysDPO进行端到端微调；3）在多轮对话场景中，尝试引入过程监督和混合优势估计来提升策略的连贯性和有效性。关键注意事项包括：避免过度依赖单一的、可能被污染的反馈信号；在系统级对齐中，需仔细设计组件间的接口以支持梯度或偏好信号的传播；使用过程奖励时，需确保评判模型本身的质量和公正性。

---

## 📄 论文列表（9 篇）

### [Aligning to Illusions: Choice Blindness in Human and AI Feedback](https://arxiv.org/abs/2603.08412)

**作者**: Wu  
**链接**: [arXiv](https://arxiv.org/abs/2603.08412) · [PDF](https://arxiv.org/pdf/2603.08412)  \| [📖 全文分析](paper_2603.08412.md)  
**评分**: 8.57  （novelty: 9.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文挑战了强化学习从人类反馈（RLHF）的基本假设，通过三个精心设计的实验揭示了偏好标注过程中的“选择盲视”问题。研究创新性地将心理学中的选择盲视概念扩展到AI反馈领域，发现人类标注者和LLM评判者都存在显著的偏好构造问题，且标准评估指标无法检测这种信号污染。实验设计严谨，从人类行为到AI模型进行了系统性验证。


### [Aligning Compound AI Systems via System-level DPO](https://arxiv.org/abs/2502.17721)

**作者**: Wang, Zhang, Ding, Tsai, Wu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2502.17721) · [PDF](https://arxiv.org/pdf/2502.17721)  \| [📖 全文分析](paper_2502.17721.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文由来自斯坦福大学（Stanford）和伊利诺伊大学厄巴纳-香槟分校（UIUC）等知名高校的研究团队（作者包括Sanae Koyejo等知名学者）提出了一种名为SysDPO的新框架，用于对齐复合人工智能系统。该工作将复合AI系统建模为有向无环图（DAG），并扩展了直接偏好优化（DPO）方法，以解决系统级对齐的挑战。方法创新性强，实验设计合理，在两个应用场景中验证了有效性。


### [Revisiting the (Sub)Optimality of Best-of-N for Inference-Time Alignment](https://arxiv.org/abs/2603.05739)

**作者**: Sriraman, Block  
**链接**: [arXiv](https://arxiv.org/abs/2603.05739) · [PDF](https://arxiv.org/pdf/2603.05739)  \| [📖 全文分析](paper_2603.05739.md)  
**评分**: 8.43  （novelty: 8.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文重新审视了语言模型推理时对齐中广泛使用的Best-of-N采样方法。作者在更贴近实际应用的假设下，从胜率（win-rate）这一与奖励模型训练和评估更一致的指标出发，证明了适当调优的BoN在计算和统计上都是最优的，从而部分解释了其广泛成功的实践原因。针对BoN仍易受奖励黑客攻击的问题，作者提出了一种简单实用的变体，可证明地消除奖励黑客攻击，同时保持最优的统计性能。此外，作者还证明了先前基于期望真实奖励的分析方法在考虑胜率时是次优的，强调了分析推理时对齐方法时选择合适目标的重要性。


### [How Far Can Unsupervised RLVR Scale LLM Training?](https://arxiv.org/abs/2603.08660)

**作者**: He, Zuo, Liu, Zhao, Fu 等 21 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.08660) · [PDF](https://arxiv.org/pdf/2603.08660)  \| [📖 全文分析](paper_2603.08660.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文对无监督强化学习与可验证奖励（URLVR）在大语言模型训练中的扩展潜力进行了系统性研究。论文首先对URLVR方法进行了分类（内在奖励与外部奖励），建立了统一的理论框架，揭示了内在奖励方法的收敛机制及其局限性，并通过大量实验验证了理论发现。研究还提出了模型崩溃步数作为RL可训练性的实用指标，并探索了基于计算不对称性的外部奖励方法的潜力。论文在理论分析、实验设计和实践指导方面均有显著贡献。


### [MAPO: Mixed Advantage Policy Optimization for Long-Horizon Multi-Turn Dialogue](https://arxiv.org/abs/2603.06194)

**作者**: Zhang, Sun, Su, Yang, Pan 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.06194) · [PDF](https://arxiv.org/pdf/2603.06194)  \| [📖 全文分析](paper_2603.06194.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为MAPO（混合优势策略优化）的新型强化学习算法，用于解决长视野多轮主观对话任务中的信用分配难题。该方法通过利用评判模型提供的密集过程反馈，结合蒙特卡洛回报传播长期效应，并引入混合优势估计器（结合回合级和批次级归一化）来稳定优化。在多个主观对话基准测试（如EMPA、EmoBench、EQ-Bench）和不同模型规模（7B至32B）上，MAPO均显著提升了训练稳定性和最终性能，并展现出良好的泛化能力。


### [RbtAct: Rebuttal as Supervision for Actionable Review Feedback Generation](https://arxiv.org/abs/2603.09723)

**作者**: Wu, Ma, Zhao, Hu, Jiang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.09723) · [PDF](https://arxiv.org/pdf/2603.09723)  \| [📖 全文分析](paper_2603.09723.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为RbtAct的新方法，利用同行评审中的反驳（rebuttal）作为隐式监督信号，来优化可操作的审稿反馈生成。该方法创新性地将反驳文本作为监督信号，构建了新的任务（视角条件的分段级审稿反馈生成）和大规模数据集RMR-75K，并通过监督微调和偏好优化训练模型。实验表明，该方法在可操作性和具体性上优于基线模型。作者团队来自学术界，但未明确标注具体机构（如斯坦福、MIT等），因此省略团队背景介绍。


### [Reward Under Attack: Analyzing the Robustness and Hackability of Process Reward Models](https://arxiv.org/abs/2603.06621)

**作者**: Tiwari, Tomar, Bamba, Maheswaran, Yang 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.06621) · [PDF](https://arxiv.org/pdf/2603.06621)  \| [📖 全文分析](paper_2603.06621.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由加州大学伯克利分校（UC Berkeley）和SqueezeAI实验室的研究团队提出，针对过程奖励模型（PRMs）在对抗性攻击下的脆弱性进行了系统性分析。研究揭示了当前PRMs作为流畅度检测器而非推理验证器的本质缺陷，并开发了诊断框架和基准测试工具。这项工作对强化学习安全性和大语言模型对齐具有重要意义。


### [Bradley-Terry Policy Optimization for Generative Preference Modeling](https://arxiv.org/abs/2510.15242)

**作者**: Feng, He, Ma, Li, Xiong 等 15 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.15242) · [PDF](https://arxiv.org/pdf/2510.15242)  \| [📖 全文分析](paper_2510.15242.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种针对生成式偏好建模的新方法——Bradley-Terry策略优化（BTPO），解决了在仅有人类成对偏好监督的非可验证任务中，将思维链推理融入偏好建模的挑战。该方法通过推导出偏好似然梯度的蒙特卡洛估计器，实现了稳定有效的训练，并在多个基准测试和模型规模上超越了先前的启发式方法。


### [Truncated Step-Level Sampling with Process Rewards for Retrieval-Augmented Reasoning](https://arxiv.org/abs/2602.23440)

**作者**: Samarinas, Chang, Zamani  
**链接**: [arXiv](https://arxiv.org/abs/2602.23440) · [PDF](https://arxiv.org/pdf/2602.23440)  \| [📖 全文分析](paper_2602.23440.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为SLATE的新框架，用于解决检索增强推理中强化学习的信用分配问题。该框架结合了截断步级采样和密集分解的LLM-as-judge奖励机制，在七个QA基准测试中均优于现有基线方法，特别是在多跳任务和小模型上表现突出。

