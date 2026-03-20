# RLHF · 2026-03-16 ~ 2026-03-20

**论文数**: 11 篇

---

## 📊 趋势分析

### 研究全貌

本批次RLHF领域的论文研究呈现出多元化且深入的特点，主要研究方向集中在**训练算法优化**、**奖励模型改进**、**数据高效利用**以及**新兴模型架构（如扩散模型）的对齐**等方面。训练算法优化聚焦于提升RLHF/DPO等方法的稳定性与效率；奖励模型改进旨在解决偏差、噪声和可扩展性问题；数据高效利用探索如何从低成本观测数据或交互中学习；扩散模型对齐则致力于将成熟的RL技术迁移到新的生成范式。当前的热点问题是如何在保证模型性能与对齐质量的同时，显著降低训练成本、提升数据利用效率并增强训练过程的稳定性。整体研究趋势正从依赖昂贵、受控的人工标注反馈，转向利用更丰富、更自然的交互数据，并更加注重对算法内在机制（如梯度偏差、数据质量）的理论分析与工程实践的结合。

### 重点方法深度解析

从这批论文中，以下几个工作因其创新性和实用价值尤为突出：

**1. 《A Comedy of Estimators: On KL Regularization in RL Training of LLMs》 [URL](https://arxiv.org/abs/2512.21852)**
*   **核心创新点**：系统性地研究了RL训练大语言模型时，KL正则化项估计器的配置如何影响梯度偏差，进而影响训练稳定性和最终模型性能。该研究填补了实践中的一个重要空白，揭示了目标函数与实现之间可能存在的差异。
*   **技术细节**：论文分析了多种KL估计器配置（如是否使用重要性采样、是否对参考策略进行停止梯度操作）的梯度性质。关键发现是，某些广泛采用的配置会产生有偏梯度，导致训练不稳定；而采用能产生无偏梯度的配置（例如，对参考策略使用停止梯度）则能带来更稳定、性能更好的训练。
*   **效果验证**：通过在Qwen2.5-7B、Llama-3.1-8B-Instruct等模型上进行RL微调实验，验证了使用无偏梯度配置的估计器能在领域内和领域外任务上取得更好的性能，并有助于稳定异步设置下的离线策略训练。
*   **适用场景**：该方法适用于所有使用策略梯度方法（如PPO）进行RLHF或RL微调的场景，为算法库实现和超参数配置提供了直接的、可操作的指导，是提升RL训练鲁棒性的基础性工作。

**2. 《CausalRM: Causal-Theoretic Reward Modeling for RLHF from Observational User Feedbacks》 [URL](https://arxiv.org/abs/2603.18736)**
*   **核心创新点**：提出了一种从观测性用户反馈（如点击、点赞）中学习无偏奖励模型的因果框架，旨在以低成本、可扩展的方式替代昂贵的人工标注偏好数据。
*   **技术细节**：框架针对观测反馈的两大挑战设计了解决方案：(1) **噪声问题**：通过显式建模标注错误生成过程，设计了一个噪声感知的代理损失函数，该函数在无噪声条件下与原始损失等价。(2) **选择偏差问题**：引入倾向得分（用户对给定反馈提供反馈的概率）对训练样本进行重加权，从而消除因用户偏好导致的分布偏移。
*   **效果验证**：在多种LLM骨干网络和基准数据集上的实验表明，CausalRM能有效从有噪、有偏的观测反馈中学习准确的奖励信号，并在下游RLHF任务（如安全对齐）上带来显著性能提升（例如，在WildGuardMix上提升49.2%）。
*   **适用场景**：该方法非常适合拥有大量真实用户交互数据的产品或应用，能够低成本地构建和迭代奖励模型，是实现持续学习和个性化对齐的有力工具。

**3. 《Co-rewarding: Stable Self-supervised RL for Eliciting Reasoning in LLMs》 [URL](https://arxiv.org/abs/2508.00410)**
*   **核心创新点**：提出了一种新颖的自监督强化学习框架，通过引入多视角监督信号来解决自奖励方法中常见的训练崩溃和奖励黑客问题，从而稳定地提升模型的推理能力。
*   **技术细节**：框架提供了两种实现方式：**数据侧**（Co-rewarding-I）通过对比语义相似问题的答案一致性来生成奖励；**模型侧**（Co-rewarding-II）通过维护一个缓慢更新的教师模型进行自蒸馏来提供伪标签。这两种方式都引入了“差异”，增加了模型陷入平凡解的难度。两者还可以正交结合以进一步提升性能。
*   **效果验证**：在多个数学推理基准测试上，Co-rewarding表现出稳定的训练过程，并显著优于其他自奖励基线（平均提升3.31%），甚至在部分情况下达到了或超越了使用真实标签的强化学习方法。
*   **适用场景**：该方法特别适用于缺乏高质量人工标注奖励的复杂推理任务（如数学、代码），为解锁大模型内在推理能力提供了一条不依赖外部奖励模型的可行路径。

**4. 《SteerRM: Debiasing Reward Models via Sparse Autoencoders》 [URL](https://arxiv.org/abs/2603.12795)**
*   **核心创新点**：提出了一种无需重新训练即可消除奖励模型偏差的干预方法，利用稀疏自编码器（SAE）的可解释性来识别和抑制与风格等表面特征相关的偏差特征。
*   **技术细节**：方法首先使用对比配对响应（相同语义、不同风格）来定位风格效应；然后应用“强度-稳定性”准则识别SAE中与偏差相关的特征；最后在推理时直接抑制这些特征。研究发现，格式相关的偏差特征往往集中在模型的浅层。
*   **效果验证**：在RM-Bench的六个奖励模型上，SteerRM将Hard-split准确率平均提升了7.3个百分点，同时保持了整体性能。该方法对不同的RM架构和偏差类型都表现出良好的泛化能力。
*   **适用场景**：这是一种轻量级、即插即用的奖励模型后处理技术，适用于任何已部署的奖励模型，能够快速、低成本地缓解其已知的偏差问题，提升对齐流程的可靠性。

### 实践启示

这些研究为大模型应用开发提供了多方面的借鉴。**对于追求训练稳定性和算法可靠性的团队**，应首要关注《A Comedy of Estimators》中关于KL估计器配置的结论，并检查现有RL训练代码是否符合最佳实践。**对于拥有海量用户交互数据的公司**，CausalRM提供了一条将“数据富矿”转化为高质量对齐信号的清晰技术路径，是实现数据驱动迭代的关键。**在开发专注于数学、编程等复杂推理能力的应用时**，Co-rewarding这类自监督RL框架是降低对标注数据依赖、激发模型潜力的有力工具。**对于已部署奖励模型并观察到特定偏差（如过度偏好某种格式）的场景**，SteerRM提供了一种快速、无需重训的修复方案。

可落地的具体建议包括：1) 在实施RLHF时，优先采用能产生无偏梯度的KL估计器配置；2) 探索将观测性用户反馈（如对话日志中的有效/无效标记）通过CausalRM框架构建初始奖励模型；3) 对于推理任务，可以尝试将Co-rewarding作为DPO/RLHF的补充或替代进行实验。关键注意事项在于，任何新方法在应用于生产环境前，都需在特定领域和模型上进行充分的消融实验与效果评估，确保其收益大于引入的复杂性。

---

## 📄 论文列表（11 篇）

### [A Comedy of Estimators: On KL Regularization in RL Training of LLMs](https://arxiv.org/abs/2512.21852)

**作者**: Shah, Obando-Ceron, Jain, Bartoldson, Kailkhura 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2512.21852) · [PDF](https://arxiv.org/pdf/2512.21852)  \| [📖 全文分析](paper_2512.21852.md)  
**评分**: 8.79  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 8.0）

> 本文由来自Meta GenAI、Google、Mila研究所（蒙特利尔大学）等知名机构的作者团队（包括Yoshua Bengio、Aaron Courville等知名学者）合作完成。论文系统性地研究了强化学习训练大语言模型时KL正则化项估计器的配置问题，揭示了梯度偏差对训练稳定性和模型性能的影响，并通过在多个主流LLM上的实验验证了理论分析。研究填补了该领域的重要空白，对RLHF实践具有重要指导意义。


### [Nemotron-Cascade 2: Post-Training LLMs with Cascade RL and Multi-Domain On-Policy Distillation](https://arxiv.org/abs/2603.19220)

**作者**: Yang, Liu, Chen, Dai, Wang 等 17 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.19220) · [PDF](https://arxiv.org/pdf/2603.19220)  \| [📖 全文分析](paper_2603.19220.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由NVIDIA研究团队（作者来自NVIDIA，包括Yang Liu、Wei-Cheng Chen、Jianfeng Gao等知名研究人员）提出了一种名为Nemotron-Cascade 2的30B MoE模型，通过创新的Cascade RL和多领域策略蒸馏技术，在数学、编程推理和智能体能力方面取得了突破性进展。该模型在参数效率方面表现卓越，以远少于前沿模型的参数量达到了国际顶级竞赛的金牌水平。论文技术路线清晰，实验证据充分，并开源了模型检查点和训练数据。


### [Reinforcement Learning for Diffusion LLMs with Entropy-Guided Step Selection and Stepwise Advantages](https://arxiv.org/abs/2603.12554)

**作者**: Kunde, Doudi, Farahbakhsh, Kalathil, Narayanan 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12554) · [PDF](https://arxiv.org/pdf/2603.12554)  \| [📖 全文分析](paper_2603.12554.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种针对扩散语言模型（DLMs）的强化学习后训练新方法。该方法将扩散序列生成建模为有限时域马尔可夫决策过程，并推导出精确、无偏的策略梯度，通过熵引导的步骤选择和单步去噪奖励估计，实现了高效训练。在代码生成和逻辑推理基准测试中取得了最先进的结果，并在数学推理上表现出强大的竞争力。代码已开源。


### [Sharpness-Aware Minimization in Logit Space Efficiently Enhances Direct Preference Optimization](https://arxiv.org/abs/2603.18258)

**作者**: Luo, Deng, Do, Harandi, Phung 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18258) · [PDF](https://arxiv.org/pdf/2603.18258)  \| [📖 全文分析](paper_2603.18258.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为logits-SAM的新方法，用于缓解直接偏好优化（DPO）训练中出现的“挤压效应”。该方法基于对logit空间坐标动态的理论分析，发现负梯度更新会导致残差沿高曲率方向快速扩张，而锐度感知最小化（SAM）可以通过其曲率正则化效应抑制此行为。作者据此设计了仅扰动输出层、计算开销可忽略的logits-SAM变体。实验在Pythia-2.8B、Mistral-7B和Gemma-2B-IT等多个模型及多个数据集和基准上进行，结果表明该方法能持续提升DPO效果，并能无缝集成到其他DPO变体中。代码已开源。


### [CausalRM: Causal-Theoretic Reward Modeling for RLHF from Observational User Feedbacks](https://arxiv.org/abs/2603.18736)

**作者**: Wang, Pan, Chen, Zheng, Chu 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18736) · [PDF](https://arxiv.org/pdf/2603.18736)  \| [📖 全文分析](paper_2603.18736.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种基于因果理论的奖励建模框架CausalRM，用于从观测性用户反馈（如点击、复制、点赞）中学习无偏的奖励模型，以替代传统RLHF中昂贵且受控的人工标注反馈。该方法通过噪声感知的代理损失项和倾向得分重加权，有效解决了观测反馈中的噪声和用户偏好偏差问题。在多个LLM骨干网络和基准数据集上的实验验证了其有效性，在WildGuardMix和HarmBench等下游RLHF任务上取得了显著性能提升（分别提升49.2%和32.7%）。代码已开源。


### [Aligning Language Models from User Interactions](https://arxiv.org/abs/2603.12273)

**作者**: Buening, HÃ¼botter, PÃ¡sztor, Shenfeld, Ramponi 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12273) · [PDF](https://arxiv.org/pdf/2603.12273)  \| [📖 全文分析](paper_2603.12273.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种从用户多轮交互中学习语言模型对齐的新方法。作者团队来自苏黎世联邦理工学院（ETH Zurich）和Meta AI等知名机构，其中Andreas Krause是机器学习领域的知名学者。该方法利用语言模型自身的上下文学习能力，通过自蒸馏技术从用户后续消息中提取反馈信号，实现了无需显式反馈的对齐、个性化和持续适应。


### [Alignment Makes Language Models Normative, Not Descriptive](https://arxiv.org/abs/2603.17218)

**作者**: Shapira, Tennenholtz, Reichart  
**链接**: [arXiv](https://arxiv.org/abs/2603.17218) · [PDF](https://arxiv.org/pdf/2603.17218)  \| [📖 全文分析](paper_2603.17218.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由以色列理工学院（Technion）和以色列开放大学的研究团队（Shapira, Tennenholtz, Reichart）完成，探讨了语言模型对齐训练对预测人类行为能力的影响。研究发现，对齐训练使模型更倾向于预测规范性（normative）的人类行为，而非描述性（descriptive）的实际行为，在多轮战略博弈中，基础模型预测人类决策的准确性显著优于对齐模型（约10:1）。这一发现揭示了模型优化目标（人类偏好对齐）与作为人类行为代理之间的根本权衡。研究创新性强，实验设计严谨，数据规模大，结论对理解对齐模型的本质具有重要意义。


### [dTRPO: Trajectory Reduction in Policy Optimization of Diffusion Large Language Models](https://arxiv.org/abs/2603.18806)

**作者**: Zhang, Wu, Zhao, Chang, Zhuge 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.18806) · [PDF](https://arxiv.org/pdf/2603.18806)  \| [📖 全文分析](paper_2603.18806.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种针对扩散大语言模型（dLLMs）的策略优化新方法dTRPO，通过轨迹概率计算成本降低技术，实现了可扩展的离线策略训练。论文在理论证明和实验验证方面都表现出色，在多个基准测试中显著提升了模型性能。作者团队来自多个知名研究机构，包括Meta GenAI、Stanford等，显示了较强的研究背景。


### [Co-rewarding: Stable Self-supervised RL for Eliciting Reasoning in Large Language Models](https://arxiv.org/abs/2508.00410)

**作者**: Zhang, Zhu, Ge, Zhao, Zhou 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2508.00410) · [PDF](https://arxiv.org/pdf/2508.00410)  \| [📖 全文分析](paper_2508.00410.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Co-rewarding的新型自监督强化学习框架，旨在解决大语言模型推理能力提升中自奖励方法常见的训练崩溃问题。该方法通过引入多视角监督信号（数据侧对比和模型侧自蒸馏）来增加训练难度，防止奖励黑客攻击，在多个数学推理基准测试上取得了显著优于现有自奖励基线的性能，甚至在某些情况下超越了使用真实标签的强化学习方法。作者团队来自清华大学（Tsinghua University，作者姓氏如Zhang, Zhou, Yao, Han等常见于清华相关论文），显示了较强的学术背景。


### [Towards Understanding Valuable Preference Data for Large Language Model Alignment](https://arxiv.org/abs/2510.13212)

**作者**: Zhang, Wang, Ye, Zhu, Yao 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.13212) · [PDF](https://arxiv.org/pdf/2510.13212)  \| [📖 全文分析](paper_2510.13212.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种新的评估和选择大语言模型对齐中偏好数据质量的方法。作者团队来自学术界，其中Sugiyama教授是机器学习领域的知名学者。研究通过新提出的截断影响函数（TIF）揭示了偏好数据质量是模型依赖的特性，并开发了更简单的评分函数和数据选择规则，在多个对齐基准和LLM家族上验证了用更少数据实现更好性能的有效性。


### [SteerRM: Debiasing Reward Models via Sparse Autoencoders](https://arxiv.org/abs/2603.12795)

**作者**: Sun, Yu, Gu, Zhang, Ye  
**链接**: [arXiv](https://arxiv.org/abs/2603.12795) · [PDF](https://arxiv.org/pdf/2603.12795)  \| [📖 全文分析](paper_2603.12795.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种无需重新训练即可消除奖励模型偏差的新方法SteerRM，该方法基于稀疏自编码器（SAE）进行干预，通过对比配对响应隔离风格效应，识别并抑制与偏差相关的SAE特征。在RM-Bench的六个奖励模型上，SteerRM将Hard-split准确率平均提高了7.3个百分点，同时保持了整体性能。该方法在Gemma-based奖励模型和非格式偏差上的结果进一步表明其在不同RM架构和偏差类型上的泛化能力。研究发现格式相关特征集中在浅层且可在模型间迁移，揭示了共享的架构级偏差编码模式。这些结果表明，基于SAE的干预可以在不重新训练的情况下减轻奖励模型偏差，为对齐流程提供了实用且可解释的解决方案。

