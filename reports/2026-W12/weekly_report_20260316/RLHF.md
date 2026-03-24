# RLHF · 2026-03-16 ~ 2026-03-20

**论文数**: 15 篇

---

## 📊 趋势分析

第1批汇总失败: 'NoneType' object is not subscriptable

---

## 📄 论文列表（15 篇）

### [Aligning Language Models from User Interactions](https://arxiv.org/abs/2603.12273)

**作者**: Buening, HÃ¼botter, PÃ¡sztor, Shenfeld, Ramponi 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12273) · [PDF](https://arxiv.org/pdf/2603.12273)  \| [📖 全文分析](paper_2603.12273.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种从用户多轮对话交互中学习语言模型对齐的新方法。作者团队来自苏黎世联邦理工学院（ETH Zurich）和Meta AI等知名机构，其中Andreas Krause是机器学习领域的知名学者。该方法利用语言模型自身的上下文学习能力，通过自蒸馏技术从用户后续消息中提取行为修正信号，实现了无需显式反馈的对齐、个性化和持续适应。实验证明该方法在真实用户对话数据上有效，且不损害模型其他能力。


### [MapReduce LoRA: Advancing the Pareto Front in Multi-Preference Optimization for Generative Models](https://arxiv.org/abs/2511.20629)

**作者**: Chen, Wang, Chen, Ye, Shi 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2511.20629) · [PDF](https://arxiv.org/pdf/2511.20629)  \| [📖 全文分析](paper_2511.20629.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为MapReduce LoRA的新方法，用于解决生成模型在多偏好优化中的对齐税问题。该方法通过并行训练偏好特定的LoRA专家并迭代合并，结合奖励感知的令牌嵌入技术，实现了在文本到图像、文本到视频和语言任务上的多模态偏好对齐。实验表明，该方法在多个基准测试中显著提升了性能，为多偏好优化问题提供了有效的解决方案。


### [A Comedy of Estimators: On KL Regularization in RL Training of LLMs](https://arxiv.org/abs/2512.21852)

**作者**: Shah, Obando-Ceron, Jain, Bartoldson, Kailkhura 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2512.21852) · [PDF](https://arxiv.org/pdf/2512.21852)  \| [📖 全文分析](paper_2512.21852.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文由来自Meta GenAI、Google、Mila（蒙特利尔学习算法研究所）等知名研究机构的团队合作完成，作者包括Bengio、Courville等深度学习领域的知名学者。该论文系统性地研究了强化学习训练大语言模型时KL正则化项估计器的配置问题，揭示了梯度偏差对训练稳定性和模型性能的影响，并通过实验验证了无偏梯度估计器配置的优越性。研究填补了该领域的重要空白，具有重要的理论和实践价值。


### [Alignment Makes Language Models Normative, Not Descriptive](https://arxiv.org/abs/2603.17218)

**作者**: Shapira, Tennenholtz, Reichart  
**链接**: [arXiv](https://arxiv.org/abs/2603.17218) · [PDF](https://arxiv.org/pdf/2603.17218)  \| [📖 全文分析](paper_2603.17218.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文通过系统性的实验设计，揭示了语言模型对齐过程中一个重要的边界条件：对齐使语言模型更倾向于规范性预测，而非描述性预测。研究比较了120个基础模型与对齐模型在多种战略游戏中的表现，发现基础模型在预测多轮战略互动中的人类行为时显著优于对齐模型，而对齐模型在单次决策和规范性较强的场景中表现更好。这一发现对理解语言模型对齐的本质及其在社会科学模拟中的应用具有重要意义。


### [Sharpness-Aware Minimization in Logit Space Efficiently Enhances Direct Preference Optimization](https://arxiv.org/abs/2603.18258)

**作者**: Luo, Deng, Do, Harandi, Phung 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18258) · [PDF](https://arxiv.org/pdf/2603.18258)  \| [📖 全文分析](paper_2603.18258.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为logits-SAM的高效方法，用于缓解直接偏好优化（DPO）训练中出现的“挤压效应”。该方法基于对logit空间坐标动态的理论分析，发现负梯度更新会导致残差沿高曲率方向快速扩张，而锐度感知最小化（SAM）可通过其曲率正则化效应抑制此行为。作者据此设计了仅扰动输出层、计算开销可忽略的logits-SAM变体。在Pythia-2.8B、Mistral-7B和Gemma-2B-IT等多个模型及数据集上的广泛实验表明，该方法能一致提升DPO及其变体的性能。论文已开源代码。


### [Co-rewarding: Stable Self-supervised RL for Eliciting Reasoning in Large Language Models](https://arxiv.org/abs/2508.00410)

**作者**: Zhang, Zhu, Ge, Zhao, Zhou 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2508.00410) · [PDF](https://arxiv.org/pdf/2508.00410)  \| [📖 全文分析](paper_2508.00410.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为Co-rewarding的新型自监督强化学习框架，旨在解决大型语言模型推理能力提升中，传统强化学习依赖人工标注（RLVR）难以扩展，以及现有自奖励方法易出现训练崩溃的问题。该方法通过从不同视角（数据侧和模型侧）引入互补的监督信号来增强训练稳定性，在多个数学推理基准测试上显著超越了其他自奖励基线方法，并在某些情况下达到甚至超过了使用真实标签的RLVR性能。论文代码已开源。


### [Swap-guided Preference Learning for Personalized Reinforcement Learning from Human Feedback](https://arxiv.org/abs/2603.12595)

**作者**: Kim, Kim  
**链接**: [arXiv](https://arxiv.org/abs/2603.12595) · [PDF](https://arxiv.org/pdf/2603.12595)  \| [📖 全文分析](paper_2603.12595.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文针对强化学习人类反馈（RLHF）中的个性化偏好学习问题，提出了交换引导偏好学习（SPL）方法。该方法创新性地解决了变分偏好学习（VPL）中存在的后验坍塌问题，通过构造虚拟交换标注者、引入偏好逆自回归流和自适应潜在条件等组件，有效丰富了用户特定的潜在变量表示并提升了偏好预测性能。论文实验设计严谨，代码和数据已开源。


### [Visual-ERM: Reward Modeling for Visual Equivalence](https://arxiv.org/abs/2603.13224)

**作者**: Liu, Ding, Fang, Dai, Yang 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.13224) · [PDF](https://arxiv.org/pdf/2603.13224)  \| [📖 全文分析](paper_2603.13224.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Visual-ERM（视觉等价奖励模型）的多模态生成式奖励模型，用于解决视觉到代码任务中强化学习奖励信号错位的问题。该方法直接在渲染的视觉空间中提供细粒度、可解释且与任务无关的反馈，显著提升了多个结构化视觉数据（如图表、表格、SVG）解析任务的性能。作者团队来自知名机构，其中Qwen3-VL模型由阿里巴巴开发，表明研究具有坚实的工业界背景和技术支持。


### [Inference-time Alignment in Continuous Space](https://arxiv.org/abs/2505.20081)

**作者**: Yuan, Xiao, Yunfan, Xu, Tao 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2505.20081) · [PDF](https://arxiv.org/pdf/2505.20081)  \| [📖 全文分析](paper_2505.20081.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Simple Energy Adaptation（SEA）的新算法，用于大语言模型在推理时与人类反馈对齐。该方法通过连续潜在空间中的梯度采样，直接优化基础策略生成的响应，避免了传统离散空间搜索方法的局限性。在AdvBench和MATH数据集上取得了显著提升，相对改进分别达到77.51%和16.36%。论文代码已开源。


### [Overthinking Reduction with Decoupled Rewards and Curriculum Data Scheduling](https://arxiv.org/abs/2509.25827)

**作者**: Jiang, Liao, Zhang, Wang, Wang  
**链接**: [arXiv](https://arxiv.org/abs/2509.25827) · [PDF](https://arxiv.org/pdf/2509.25827)  \| [📖 全文分析](paper_2509.25827.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为DECS的新框架，旨在解决大型推理模型中的“过度思考”问题。该框架通过解耦的令牌级奖励机制和课程批次调度策略，在七个基准测试中实现了推理令牌数量减少50%以上的显著效果，同时保持甚至提升了模型性能。作者团队未明确标注所属机构，但代码已开源，显示了良好的研究实践。


### [On-Policy RL Meets Off-Policy Experts: Harmonizing Supervised Fine-Tuning and Reinforcement Learning via Dynamic Weighting](https://arxiv.org/abs/2508.11408)

**作者**: Zhang, Xie, Sun, Chen, Wang 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2508.11408) · [PDF](https://arxiv.org/pdf/2508.11408)  \| [📖 全文分析](paper_2508.11408.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为CHORD的新框架，旨在统一监督微调（SFT）和强化学习（RL）这两种大语言模型（LLM）后训练范式。该框架通过动态加权机制，将SFT重构为在线RL过程中的一个辅助目标，并引入全局和细粒度的双重控制机制，以协调离线专家数据与在线探索，从而稳定学习过程并防止过拟合。实验表明，CHORD在多个实际任务上优于基线方法。作者团队来自ModelScope（魔搭社区），这是一个由阿里巴巴达摩院发起的开源模型社区，在AI模型开发和开源方面具有重要影响力。


### [Towards Understanding Valuable Preference Data for Large Language Model Alignment](https://arxiv.org/abs/2510.13212)

**作者**: Zhang, Wang, Ye, Zhu, Yao 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.13212) · [PDF](https://arxiv.org/pdf/2510.13212)  \| [📖 全文分析](paper_2510.13212.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种新的方法来评估和选择大语言模型对齐中的人类偏好数据质量。作者团队来自学术界（从作者姓氏判断，可能包括华人学者和国际合作者），提出了一种截断影响函数（TIF）来衡量单个数据点对模型验证性能的影响，并开发了更简单的评分函数和数据选择规则。该方法在多个对齐基准和不同LLM家族上进行了验证，证明了其有效性。


### [EvolveCoder: Evolving Test Cases via Adversarial Verification for Code Reinforcement Learning](https://arxiv.org/abs/2603.12698)

**作者**: Ruan, Jiang, Zeng, Nie, Chen  
**链接**: [arXiv](https://arxiv.org/abs/2603.12698) · [PDF](https://arxiv.org/pdf/2603.12698)  \| [📖 全文分析](paper_2603.12698.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为EvolveCoder的对抗性验证框架，用于增强代码生成强化学习中的验证信号。该方法通过基于候选解决方案执行行为迭代演化测试用例，显著提升了验证的难度和判别力。基于此框架构建了大规模数据集EvolveCoder-22k。实验表明，该方法能有效提升模型性能，在多个下游基准测试中使Qwen3-4B模型平均提升4.2分。


### [SteerRM: Debiasing Reward Models via Sparse Autoencoders](https://arxiv.org/abs/2603.12795)

**作者**: Sun, Yu, Gu, Zhang, Ye  
**链接**: [arXiv](https://arxiv.org/abs/2603.12795) · [PDF](https://arxiv.org/pdf/2603.12795)  \| [📖 全文分析](paper_2603.12795.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种无需重新训练即可消除奖励模型偏差的新方法SteerRM，该方法基于稀疏自编码器（SAE）进行干预，通过对比配对响应隔离风格效应，识别并抑制与偏差相关的SAE特征。在RM-Bench上的六个奖励模型中，SteerRM将Hard-split准确率平均提高了7.3个百分点，同时保持了整体性能。该方法在Gemma-based奖励模型和受控非格式偏差上的结果进一步表明其在不同RM架构和偏差类型上的泛化能力。研究发现格式相关特征集中在浅层并在模型间转移，揭示了共享的架构级偏差编码模式。这些结果表明，基于SAE的干预可以在不重新训练的情况下减轻奖励模型偏差，为对齐流程提供了实用且可解释的解决方案。


### [Long-form RewardBench: Evaluating Reward Models for Long-form Generation](https://arxiv.org/abs/2603.12963)

**作者**: Huang, He, Liu, Yang, Liu 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12963) · [PDF](https://arxiv.org/pdf/2603.12963)  \| [📖 全文分析](paper_2603.12963.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了首个专门针对长文本生成的奖励模型评估基准Long-form RewardBench，填补了该领域的重要空白。论文设计了包含五个关键子任务的综合测试集，通过精心设计的多阶段数据收集流程构建数据集，并对20多个主流奖励模型进行了广泛实验。研究发现当前模型在长文本奖励建模能力上仍有不足，并设计了新颖的'长文本大海捞针测试'来探究错误位置与模型性能的关系。

