# Agent · 2026-03-02 ~ 2026-03-06

**论文数**: 40 篇

---

## 📊 趋势分析

### 研究全貌

Agent领域的研究呈现出从基础能力探索向构建鲁棒、高效、可扩展的复杂系统演进的清晰脉络。主要研究方向可归纳为几个层面：**多智能体协作与协调**，关注通信、拓扑与信息整合；**智能体记忆与长期能力**，旨在解决长程任务中的信息检索与状态管理；**智能体训练与优化**，包括数据合成、课程学习与交互过程建模；**特定领域应用与专业化**，如数学推理、编程、医疗等；以及**智能体评估、基准构建与安全**，致力于建立更贴近真实场景的测试标准。当前的研究热点高度聚焦于解决智能体在**开放、动态、长周期的真实世界**中可靠、安全、高效运行所面临的核心瓶颈，例如多智能体间的“通信-推理鸿沟”、错误级联传播、记忆效率低下以及新型安全风险。整体趋势是从单一任务、静态环境向**跨领域、多模态、持续交互**的复杂应用场景深度演进，并强调通过系统性的基准测试和统一的数据协议来推动领域的标准化与可复现性。

### 重点方法深度解析

从所有批次的研究中，以下几个工作因其深刻的洞察、创新的解决方案和显著的实用价值而尤为突出：

**1. 《Silo-Bench: A Scalable Environment for Evaluating Distributed Coordination in Multi-Agent LLM Systems》**
*   **核心创新点**：该研究通过构建一个可扩展的基准测试环境，揭示并量化了多智能体系统中一个根本性问题——“通信-推理鸿沟”，即智能体能够交换信息，但在整合分布式信息以得出正确答案方面存在系统性失败。
*   **技术细节**：Silo-Bench包含覆盖不同通信复杂度等级的算法任务，其核心设计在于将“信息获取”与“信息整合推理”阶段分离，从而精准定位系统瓶颈。它提供了一个标准化的评估框架，用于量化协调效率与推理能力。
*   **效果验证**：大规模实验明确展示了协调开销随智能体规模扩大而加剧，最终抵消并行化收益的现象，为“简单堆叠智能体数量”的做法提供了关键警示。
*   **适用场景**：适用于任何希望评估或改进多智能体系统协作效能的研究与开发场景，是诊断系统瓶颈、引导算法设计的重要工具。

**2. 《AriadneMem: Threading the Maze of Lifelong Memory for LLM Agents》**
*   **核心创新点**：针对长期对话中“证据分散”和“状态更新冲突”的挑战，提出了一种结构化的记忆系统，其核心创新在于将记忆构建与推理解耦，并将复杂的多跳推理问题转化为图上的路径搜索问题。
*   **技术细节**：系统采用两阶段流水线。离线阶段通过熵感知门控和冲突感知粗化构建记忆图；在线推理阶段执行“算法桥接发现”来重建缺失的逻辑路径，最后进行“单次拓扑感知合成”得出答案。
*   **效果验证**：在长期对话基准上，该方法显著提升了多跳推理的准确性，同时通过将推理卸载到图计算层，将总运行时间大幅减少，且上下文消耗极低。
*   **适用场景**：非常适合需要处理长历史、多轮交互且信息间存在复杂逻辑依赖的智能体应用，如长期客服、复杂任务助手等。

**3. 《AgentMath: Empowering Mathematical Reasoning for Large Language Models via Tool-Augmented Agent》**
*   **核心创新点**：提出一个将语言模型推理与代码解释器计算精度无缝集成的智能体框架，其创新在于自动化生成高质量训练数据，并引入一种新型的智能体强化学习范式，让模型通过多轮交互反馈自主学习最优工具使用策略。
*   **技术细节**：方法包含三大支柱：自动将思维链转化为工具增强轨迹以生成监督微调数据；设计智能体强化学习范式，模型动态交错生成自然语言和执行代码；构建高效训练系统实现超长序列下的训练加速。
*   **效果验证**：在极具挑战性的数学竞赛基准上达到SOTA性能，超越了若干强大的闭源模型，展示了专业化智能体的巨大潜力。
*   **适用场景**：特别适用于需要高精度数学计算、符号推理或科学计算的场景，如教育辅导、科研辅助和金融建模。

**4. 《TopoCurate: Modeling Interaction Topology for Tool-Use Agent Training》**
*   **核心创新点**：针对传统基于结果筛选训练数据的局限性，提出了一种**交互感知**的训练数据优化框架。其核心思想是深入分析智能体在任务中的交互动态，而非仅看最终成败。
*   **技术细节**：该方法将多轮尝试轨迹投影到“语义商拓扑”中，转化为结构化的流形图，揭示策略分岔点。基于此，为监督微调和强化学习分别设计了选择机制，优先选择展示反思恢复、策略多样性或高错误分支比例的数据。
*   **效果验证**：在多个基准测试上，相比现有基线取得了稳定的性能提升，证明了从交互过程中挖掘训练信号的有效性。
*   **适用场景**：适用于任何需要训练工具使用智能体的场景，特别是交互过程复杂、存在多种解决路径的任务，能有效提升训练数据的质量和训练效率。

这些重点方法之间存在紧密联系。Silo-Bench为评估多智能体协作（可能涉及多个专业化AgentMath或使用AriadneMem的智能体）提供了诊断工具。AriadneMem和TopoCurate分别从系统架构和训练数据层面优化智能体的长期与交互能力。AgentMath则代表了在特定垂直领域深化智能体能力的成功范式。它们共同指向一个更鲁棒、更专业、更高效的智能体系统未来。

### 实践启示

这些研究为实际的大模型应用开发提供了清晰的路径和重要警示。首先，**评估与安全先行**：在部署涉及工具调用或多智能体协作的系统前，必须借鉴Silo-Bench和MCP-SafetyBench的思路，在贴近真实交互的环境中系统性地评估其协调效率、推理可靠性和安全风险，避免实验室性能与线上表现脱节。其次，**专业化与结构化设计**：对于数学、编程等专业领域，应优先采用类似AgentMath的“LLM推理+专业工具”架构；对于需要处理长上下文或复杂状态的任务，则应引入类似AriadneMem的结构化记忆系统来平衡性能与成本。第三，**优化训练与交互过程**：在训练自有智能体时，应借鉴TopoCurate和CoVe的方法，不仅收集成功数据，更要系统分析失败轨迹，或利用任务约束自动生成高质量合成数据，以提升模型的鲁棒性和泛化能力。

**可落地的具体建议**包括：1) 为关键业务Agent建立动态更新的专属评估基准；2) 采用模块化智能体架构，便于单独优化和错误定位；3) 探索利用任务约束和交互拓扑分析来自动增强训练数据。**关键注意事项**是：避免盲目增加智能体数量，需评估协调开销；在赋予智能体自主探索权（如AT2QA范式）时，必须设置合理的超时、循环检测和成本控制机制；所有工具调用必须严格限制权限并实施沙箱隔离。一个**推荐的最佳方法组合**是：使用Agent Data Protocol统一训练数据格式，采用AgentMath或类似框架构建核心专业能力，集成AriadneMem管理长期状态，并最终在Silo-Bench类环境中进行多智能体协作效能与安全评估。

---

## 📄 论文列表（40 篇）

### [Silo-Bench: A Scalable Environment for Evaluating Distributed Coordination in Multi-Agent LLM Systems](https://arxiv.org/abs/2603.01045)

**作者**: Zhang, Liu, Shan, Huang, Yang 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01045) · [PDF](https://arxiv.org/pdf/2603.01045)  \| [📖 全文分析](paper_2603.01045.md)  
**评分**: 8.79  （novelty: 9.0 | method: 9.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了Silo-Bench，一个用于评估多智能体大语言模型系统中分布式协调能力的可扩展基准测试环境。论文通过系统性的实验揭示了多智能体协作中存在的“通信-推理鸿沟”这一关键问题，即智能体能够有效交换信息但无法有效整合分布式状态以得出正确答案。这一发现对当前简单增加智能体数量以突破上下文限制的做法提出了重要质疑，为未来真正协作式多智能体系统的研究奠定了基础。


### [Echoing: Identity Failures when LLM Agents Talk to Each Other](https://arxiv.org/abs/2511.09710)

**作者**: Shekkizhar, Cosentino, Earle, Savarese  
**链接**: [arXiv](https://arxiv.org/abs/2511.09710) · [PDF](https://arxiv.org/pdf/2511.09710)  \| [📖 全文分析](paper_2511.09710.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文研究了大型语言模型（LLM）智能体在自主交互时出现的一类新型失败模式——‘回声’（echoing），即智能体放弃其被分配的角色并开始镜像其对话伙伴的行为。该研究通过大规模实验（涵盖66种AxA配置、4个领域、超过2500次对话）系统性地揭示了这一现象在主流LLM提供商中的普遍性、持久性及其动态特征，并提出了协议级别的缓解方案。论文选题新颖，实验设计严谨，数据充分，对理解和改进多智能体系统的鲁棒性具有重要价值。


### [AriadneMem: Threading the Maze of Lifelong Memory for LLM Agents](https://arxiv.org/abs/2603.03290)

**作者**: Zhu, Chen, Wang, Wang, Dong 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03290) · [PDF](https://arxiv.org/pdf/2603.03290)  \| [📖 全文分析](paper_2603.03290.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为AriadneMem的结构化记忆系统，旨在解决LLM智能体在长期对话中面临的两个核心挑战：证据分散和状态更新冲突。该系统采用解耦的两阶段流水线设计，包括离线构造阶段（通过熵感知门控过滤噪声、冲突感知粗化合并重复项）和在线推理阶段（通过算法桥接发现重建逻辑路径、单次拓扑感知合成）。在LoCoMo实验中使用GPT-4o，AriadneMem在多项指标上显著超越基线，同时大幅降低计算开销和上下文令牌使用量。论文实验设计严谨，代码已开源，具有较高的实用价值。


### [AgentSelect: Benchmark for Narrative Query-to-Agent Recommendation](https://arxiv.org/abs/2603.03761)

**作者**: Shi, Xu, Chen, Shang, Yang 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03761) · [PDF](https://arxiv.org/pdf/2603.03761)  \| [📖 全文分析](paper_2603.03761.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为AgentSelect的基准测试，用于解决LLM智能体配置推荐问题。该研究填补了现有评估体系在查询条件化监督方面的空白，通过整合40多个来源的数据，构建了包含超过11万查询、10万智能体和25万交互记录的统一数据集。论文创新性地将智能体选择问题重构为基于能力配置的叙事查询到智能体推荐任务，并展示了合成组合交互的可学习性。作者团队包括来自知名研究机构的学者，如Xu Metaxas教授（罗格斯大学计算机视觉与机器学习实验室主任，在计算机视觉和医学图像分析领域享有盛誉），表明研究具有扎实的学术基础。


### [ParEVO: Synthesizing Code for Irregular Data: High-Performance Parallelism through Agentic Evolution](https://arxiv.org/abs/2603.02510)

**作者**: Yang, Nie, Liu, Zou, AltinbÃ¼ken 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.02510) · [PDF](https://arxiv.org/pdf/2603.02510)  \| [📖 全文分析](paper_2603.02510.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为ParEVO的创新框架，旨在为不规则数据结构合成高性能并行算法。该方法通过构建专门的训练数据集、微调大语言模型以及采用进化式代码代理，有效解决了现有LLM在并行编程中存在的竞态条件、死锁和扩展性差等问题。在ParEval基准测试中，ParEVO实现了平均106倍的加速，并在复杂不规则图问题上达到13.6倍加速，性能超越现有商业模型，甚至在某些高度不规则内核上超越专家人工基准。论文实验充分，代码和数据已开源。


### [RUMAD: Reinforcement-Unifying Multi-Agent Debate](https://arxiv.org/abs/2602.23864)

**作者**: Wang, Lin, Tang, Lin, Ding  
**链接**: [arXiv](https://arxiv.org/abs/2602.23864) · [PDF](https://arxiv.org/pdf/2602.23864)  \| [📖 全文分析](paper_2602.23864.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种新颖的强化学习统一多智能体辩论框架RUMAD，旨在解决现有多智能体辩论系统在同时优化准确性、共识形成和计算效率方面的难题。该框架将动态通信拓扑控制建模为强化学习问题，通过内容无关的观察方案和多目标奖励机制，实现了高效的辩论协调。在MMLU、GSM8K和GPQA基准测试上的实验表明，RUMAD在显著降低计算成本（令牌消耗减少80%以上）的同时，仍能提升推理准确性，并展现出强大的零样本泛化能力。


### [Agent Data Protocol: Unifying Datasets for Diverse, Effective Fine-tuning of LLM Agents](https://arxiv.org/abs/2510.24702)

**作者**: Song, Ramaneti, Sheikh, Chen, Gou 等 21 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.24702) · [PDF](https://arxiv.org/pdf/2510.24702)  \| [📖 全文分析](paper_2510.24702.md)  
**评分**: 8.57  （novelty: 8.5 | method: 9.0 | evidence: 9.0 | clarity: 7.5）

> 本文由卡内基梅隆大学（CMU）等知名机构的研究团队提出了一种名为Agent Data Protocol（ADP）的轻量级表示语言，旨在解决智能体训练数据格式碎片化的问题。该方法通过统一异构数据格式，显著提升了LLM智能体的微调效果，在多个基准测试中取得了接近或达到SOTA的性能，且所有代码和数据均已开源。


### [SkillNet: Create, Evaluate, and Connect AI Skills](https://arxiv.org/abs/2603.04448)

**作者**: Liang, Zhong, Xu, Jiang, Zhong 等 49 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.04448) · [PDF](https://arxiv.org/pdf/2603.04448)  \| [📖 全文分析](paper_2603.04448.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为SkillNet的开放基础设施，旨在大规模创建、评估和组织AI技能。该方法通过统一的技能本体论，支持从异构来源创建技能、建立丰富的关系连接，并在安全性、完整性、可执行性、可维护性和成本意识等多个维度进行评估。实验在ALFWorld、WebShop和ScienceWorld等基准上验证了SkillNet能显著提升智能体性能，平均奖励提高40%，执行步骤减少30%。作者团队来自多个知名机构，包括阿里巴巴、清华大学、北京大学、上海交通大学等，显示了强大的跨机构合作背景。


### [From Spark to Fire: Modeling and Mitigating Error Cascades in LLM-Based Multi-Agent Collaboration](https://arxiv.org/abs/2603.04474)

**作者**: Xie, Zhu, Zhang, Zhu, Ye 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.04474) · [PDF](https://arxiv.org/pdf/2603.04474)  \| [📖 全文分析](paper_2603.04474.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种针对基于大语言模型的多智能体系统（LLM-MAS）中错误级联传播问题的系统性建模与治理方法。研究团队（作者未明确标注来自特定知名机构，故省略背景介绍）创新性地将多智能体协作抽象为有向依赖图，建立了传播动力学模型，并提出了一个早期风险准则来量化放大风险。通过实验在六个主流框架上识别出三类系统脆弱性，并设计了一种基于谱系图的治理层作为消息层插件，有效抑制了错误传播，将防御成功率从基线0.32提升至0.89以上。该工作问题定义清晰，解决方案新颖且有效，实验验证充分。


### [MACD: Multi-Agent Clinical Diagnosis with Self-Learned Knowledge for LLM](https://arxiv.org/abs/2509.20067)

**作者**: Li, Yan, Zhang, Chen, Zhu 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.20067) · [PDF](https://arxiv.org/pdf/2509.20067)  \| [📖 全文分析](paper_2509.20067.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种新颖的多智能体临床诊断框架MACD，通过让大语言模型在多智能体流程中自我学习临床知识，模拟医生通过经验积累专业知识的过程。该方法在4390个真实世界病例上进行了全面评估，使用多种开源LLM（Llama-3.1, DeepSeek-R1等），显著提升了诊断准确率，最高提升达22.3%，并在人机协作流程中展现出18.6%的改进。论文实验设计严谨，结果具有说服力，为LLM在复杂临床诊断中的应用提供了新的自学习范式。


### [GEM: A Gym for Agentic LLMs](https://arxiv.org/abs/2510.01051)

**作者**: Liu, Sims, Duan, Chen, Yu 等 19 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.01051) · [PDF](https://arxiv.org/pdf/2510.01051)  \| [📖 全文分析](paper_2510.01051.md)  
**评分**: 8.50  （novelty: 8.5 | method: 9.5 | evidence: 8.0 | clarity: 7.5）

> 本文提出了一种名为GEM（General Experience Maker）的开源环境模拟器，旨在为基于经验学习的大语言模型（LLM）智能体研究提供标准化框架。论文工作系统性强，不仅设计了通用接口和多样化环境，还提供了基准测试和与主流训练框架的集成示例，对推动智能体LLM研究具有重要工具价值。


### [LightMem: Lightweight and Efficient Memory-Augmented Generation](https://arxiv.org/abs/2510.18866)

**作者**: Fang, Deng, Xu, Jiang, Tang 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.18866) · [PDF](https://arxiv.org/pdf/2510.18866)  \| [📖 全文分析](paper_2510.18866.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为LightMem的新型记忆增强生成系统，旨在解决大型语言模型在动态复杂环境中有效利用历史交互信息的挑战。该方法通过受人类记忆模型启发的三阶段架构，在性能与效率之间取得了良好平衡。在LongMemEval和LoCoMo基准测试中，使用GPT和Qwen骨干网络，LightMem在问答准确率、令牌使用量和API调用次数方面均显著优于现有基线，同时代码已开源。


### [PaperRepro: Automated Computational Reproducibility Assessment for Social Science Papers](https://arxiv.org/abs/2603.00058)

**作者**: Zhang, Xia, Piao, Cui, Li  
**链接**: [arXiv](https://arxiv.org/abs/2603.00058) · [PDF](https://arxiv.org/pdf/2603.00058)  \| [📖 全文分析](paper_2603.00058.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为PaperRepro的新型两阶段多智能体方法，用于自动化评估社会科学论文的计算可重复性。该方法通过分离执行与评估阶段，并配备特定任务工具和专家提示，有效解决了现有方法在上下文容量、工具支持和结果捕获方面的局限性。在REPRO-Bench基准测试中，PaperRepro取得了最佳性能，相对最强基线提升了21.9%的得分一致性准确率。作者还进一步细化了基准，引入了按执行难度分层的REPRO-Bench-S，用于更诊断性地评估自动化可重复性评估系统。论文代码和数据已公开。


### [DenoiseFlow: Uncertainty-Aware Denoising for Reliable LLM Agentic Workflows](https://arxiv.org/abs/2603.00532)

**作者**: Yan, Peng, Li, Li, Shang 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.00532) · [PDF](https://arxiv.org/pdf/2603.00532)  \| [📖 全文分析](paper_2603.00532.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为DenoiseFlow的闭环框架，用于解决LLM智能体工作流中因语义歧义累积导致的可靠性下降问题。该方法将多步推理过程形式化为噪声MDP，通过感知、调节和校正三个阶段实现渐进式去噪，并利用在线自校准技术持续优化决策边界。在数学推理、代码生成和多跳问答等六个基准测试中，DenoiseFlow均取得了最高的准确率（平均83.3%），同时通过自适应分支将成本降低了40-56%。作者团队未明确标注所属机构，但论文内容显示其具备扎实的研究基础。


### [DeepResearch-9K: A Challenging Benchmark Dataset of Deep-Research Agent](https://arxiv.org/abs/2603.01152)

**作者**: Wu, Wang, Ma, He, Wang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01152) · [PDF](https://arxiv.org/pdf/2603.01152)  \| [📖 全文分析](paper_2603.01152.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了DeepResearch-9K，一个专门为深度研究智能体设计的大规模、高难度基准数据集，并配套开发了开源的训练框架DeepResearch-R1。该工作针对深度研究智能体领域缺乏高质量数据集和开源训练框架的瓶颈问题，通过低成本自动化流程构建了包含9000个问题、高质量搜索轨迹和可验证答案的数据集，并设计了支持多轮网络交互、多种强化学习方法和奖励模型的训练框架。实验表明，基于该数据集和框架训练的智能体在多个深度研究基准测试中取得了领先性能。作者团队来自Applied-Machine-Learning-Lab，并已公开数据集和代码。


### [GAM-RAG: Gain-Adaptive Memory for Evolving Retrieval in Retrieval-Augmented Generation](https://arxiv.org/abs/2603.01783)

**作者**: Wang, Jiang, Sun, Cao, Liu 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01783) · [PDF](https://arxiv.org/pdf/2603.01783)  \| [📖 全文分析](paper_2603.01783.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为GAM-RAG的训练无关框架，用于解决检索增强生成（RAG）系统中静态索引导致的重复计算和延迟问题。该方法受认知神经科学中基于模式学习的启发，通过构建轻量级、无关系约束的层次化索引，并在推理过程中积累检索经验，动态更新检索记忆。实验表明，该方法在提升平均性能3.95%的同时，显著降低了61%的推理成本。论文提供了代码和数据集开源。


### [Guideline-Grounded Evidence Accumulation for High-Stakes Agent Verification](https://arxiv.org/abs/2603.02798)

**作者**: Zhang, Seedat, Dong, Cui, Zhu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.02798) · [PDF](https://arxiv.org/pdf/2603.02798)  \| [📖 全文分析](paper_2603.02798.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由来自剑桥大学、加州大学洛杉矶分校等知名高校的研究团队提出了一种名为GLEAN的高风险智能体验证框架。该框架通过将专家制定的协议转化为轨迹感知的校准正确性信号，显著提升了LLM智能体在高风险决策（如临床诊断）中的验证可靠性。在MIMIC-IV数据集上的实验表明，GLEAN在判别能力和校准能力上均大幅超越现有基线方法，并通过专家研究证实了其实用价值。


### [PlugMem: A Task-Agnostic Plugin Memory Module for LLM Agents](https://arxiv.org/abs/2603.03296)

**作者**: Yang, Chen, He, Jiang, Galley 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03296) · [PDF](https://arxiv.org/pdf/2603.03296)  \| [📖 全文分析](paper_2603.03296.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为PlugMem的任务无关插件式记忆模块，用于增强LLM智能体的长期记忆能力。该方法借鉴认知科学原理，将情景记忆组织为紧凑、可扩展的知识中心记忆图，显著提升了记忆检索效率和任务相关性。在三个异构基准测试（长程对话问答、多跳知识检索和网页智能体任务）上的实验表明，PlugMem一致优于任务无关基线，甚至超过任务特定的记忆设计，同时实现了最高的信息密度。作者团队来自TIMAN-group，代码和数据已开源。


### [Arbor: A Framework for Reliable Navigation of Critical Conversation Flows](https://arxiv.org/abs/2602.14643)

**作者**: Silva, GonÃ§alves, Farinha, Matos, Ungaro  
**链接**: [arXiv](https://arxiv.org/abs/2602.14643) · [PDF](https://arxiv.org/pdf/2602.14643)  \| [📖 全文分析](paper_2602.14643.md)  
**评分**: 8.43  （novelty: 8.5 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为Arbor的框架，用于解决大型语言模型在医疗分诊等高风险领域难以严格遵循结构化工作流程的问题。该框架通过将决策树导航分解为专门的节点级任务，采用基于有向无环图（DAG）的编排机制，动态检索和评估节点转换，显著提升了准确性、降低了延迟和成本。实验在10个基础模型上使用真实临床分诊对话数据进行验证，结果证明了该方法的有效性。


### [Benefits and Pitfalls of Reinforcement Learning for Language Model Planning: A Theoretical Perspective](https://arxiv.org/abs/2509.22613)

**作者**: Wang, Shen, Sun, Feng, Teng 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.22613) · [PDF](https://arxiv.org/pdf/2509.22613)  \| [📖 全文分析](paper_2509.22613.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文从理论角度深入探讨了强化学习在语言模型规划中的优势与局限。研究通过可处理的图抽象模型，分析了策略梯度和Q学习两种方法，揭示了监督微调可能引入虚假解，而强化学习通过探索实现正确规划。研究进一步发现策略梯度存在多样性崩溃问题，而Q学习具有离策略学习和多样性保持的优势。论文在真实规划基准Blocksworld上验证了理论发现。


### [MCP-SafetyBench: A Benchmark for Safety Evaluation of Large Language Models with Real-World MCP Servers](https://arxiv.org/abs/2512.15163)

**作者**: Zong, Shen, Wang, Lan, Yang  
**链接**: [arXiv](https://arxiv.org/abs/2512.15163) · [PDF](https://arxiv.org/pdf/2512.15163)  \| [📖 全文分析](paper_2512.15163.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种针对大型语言模型（LLM）在真实世界工具调用场景下的安全评估基准MCP-SafetyBench。该工作针对新兴的模型上下文协议（MCP）生态，首次构建了一个基于真实MCP服务器的综合性安全基准，覆盖了浏览器自动化、金融分析、位置导航、仓库管理和网络搜索五个关键领域，并系统性地定义了20种攻击类型。论文通过该基准评估了主流开源和闭源LLM，揭示了模型在MCP环境下的普遍脆弱性及安全与效用的权衡，为诊断和缓解真实部署中的安全风险奠定了基础。


### [AgentMath: Empowering Mathematical Reasoning for Large Language Models via Tool-Augmented Agent](https://arxiv.org/abs/2512.20745)

**作者**: Luo, Feng, Sun, Xu, Zheng 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2512.20745) · [PDF](https://arxiv.org/pdf/2512.20745)  \| [📖 全文分析](paper_2512.20745.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为AgentMath的智能体框架，通过将语言模型的推理能力与代码解释器的计算精度相结合，高效解决复杂数学问题。该方法在AIME24、AIME25和HMMT25等数学竞赛基准测试中取得了最先进的性能，超越了OpenAI-o3-mini和Claude-Opus-4.0-Thinking等模型。论文提出了自动生成高质量监督微调数据、新型智能体强化学习范式以及高效训练系统三大创新点，具有显著的技术价值。


### [Achieving Olympia-Level Geometry Large Language Model Agent via Complexity Boosting Reinforcement Learning](https://arxiv.org/abs/2512.10534)

**作者**: Zhao, Shen, Zhang, Gao, Liu 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2512.10534) · [PDF](https://arxiv.org/pdf/2512.10534)  \| [📖 全文分析](paper_2512.10534.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为InternGeometry的几何问题求解LLM智能体，通过复杂性提升强化学习（CBRL）方法，在国际数学奥林匹克（IMO）几何问题上取得了超越人类金牌选手平均水平的成绩。该方法仅使用少量训练数据（13K示例），显著优于AlphaGeometry 2等专家模型的数据效率。作者团队来自中国知名研究机构（如InternThinker暗示与智源研究院等相关），展现了在AI数学推理领域的前沿探索能力。


### [CoMind: Towards Community-Driven Agents for Machine Learning Engineering](https://arxiv.org/abs/2506.20640)

**作者**: Li, Sun, Li, Talwalkar, Yang  
**链接**: [arXiv](https://arxiv.org/abs/2506.20640) · [PDF](https://arxiv.org/pdf/2506.20640)  \| [📖 全文分析](paper_2506.20640.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种面向机器学习工程任务的社区驱动多智能体系统CoMind，并设计了MLE-Live实时评估框架。该工作通过模拟Kaggle研究社区，让智能体能够与社区互动并利用集体知识，在75个历史Kaggle竞赛中取得了36%的奖牌率，并在8个实时竞赛中平均超越92.6%的人类参赛者。实验设计严谨，结果具有说服力。


### [Tru-POMDP: Task Planning Under Uncertainty via Tree of Hypotheses and Open-Ended POMDPs](https://arxiv.org/abs/2506.02860)

**作者**: Tang, He, Huang, Xiao, Lu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2506.02860) · [PDF](https://arxiv.org/pdf/2506.02860)  \| [📖 全文分析](paper_2506.02860.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Tru-POMDP的新型任务规划方法，用于解决家庭服务机器人在真实世界中面临的不确定性规划问题。该方法创新性地结合了大型语言模型（LLM）的结构化信念生成与原则性的部分可观测马尔可夫决策过程（POMDP）规划，通过构建假设树（TOH）来系统性地生成高质量粒子信念，并设计了开放式的POMDP模型进行严格的贝叶斯信念跟踪和高效规划。在多样化的厨房环境复杂物体重排任务实验中，该方法显著优于当前最先进的基于LLM和LLM树搜索的混合规划器，在成功率、规划质量、鲁棒性和效率方面均表现出色。


### [FROGENT: An End-to-End Full-process Drug Design Multi-Agent System](https://arxiv.org/abs/2508.10760)

**作者**: Pan, Xu, Yang, Yao, Yuan 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2508.10760) · [PDF](https://arxiv.org/pdf/2508.10760)  \| [📖 全文分析](paper_2508.10760.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为FROGENT的端到端全流程药物设计多智能体系统，该系统利用大语言模型的规划、推理和工具使用能力，将药物发现统一在一个闭环自主框架内。论文在八个涵盖核心药物发现任务的基准测试中，FROGENT均优于六种先进的ReAct风格智能体，并通过案例研究证明了其在真实世界小分子和肽设计场景中的实用性和泛化能力。论文创新性地将LLM智能体系统应用于复杂的药物研发全流程自动化，具有重要的应用前景。


### [DRAGON: LLM-Driven Decomposition and Reconstruction Agents for Large-Scale Combinatorial Optimization](https://arxiv.org/abs/2601.06502)

**作者**: Chen, Cao, Zhou, Wu, Jayavelu 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2601.06502) · [PDF](https://arxiv.org/pdf/2601.06502)  \| [📖 全文分析](paper_2601.06502.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为DRAGON的创新框架，将元启发式设计与大语言模型推理相结合，用于解决大规模组合优化问题。该方法通过智能分解与重构机制，显著提升了LLM在组合优化问题上的可扩展性和泛化能力，在多个标准测试集上取得了突破性成果，为解决传统LLM方法在大规模问题上的局限性提供了新思路。


### [HIMM: Human-Inspired Long-Term Memory Modeling for Embodied Exploration and Question Answering](https://arxiv.org/abs/2602.15513)

**作者**: Li, Wang, Xia, Li, Hu  
**链接**: [arXiv](https://arxiv.org/abs/2602.15513) · [PDF](https://arxiv.org/pdf/2602.15513)  \| [📖 全文分析](paper_2602.15513.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为HIMM的非参数化记忆框架，用于具身智能体的长期记忆建模，以解决多模态大语言模型在长时观测和有限上下文预算下的部署挑战。该框架通过显式解耦情景记忆和语义记忆，采用检索优先、推理辅助的范式，在具身探索和问答任务上实现了显著的性能提升。作者团队未明确标注所属机构，但根据姓名推断可能来自中国研究机构或高校。


### [MagicAgent: Towards Generalized Agent Planning](https://arxiv.org/abs/2602.19000)

**作者**: Ren, Dong, Yang, Gao, Zhao 等 24 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.19000) · [PDF](https://arxiv.org/pdf/2602.19000)  \| [📖 全文分析](paper_2602.19000.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出MagicAgent系列基础模型，专门用于解决通用智能体规划问题。论文针对当前智能体规划面临的异构任务冲突和数据稀缺挑战，提出了轻量级可扩展的合成数据生成框架和两阶段训练范式，在多个开源基准测试中取得了优异性能，显著超越了现有模型。作者团队来自多个研究机构，包括清华大学、北京大学、中国科学院等国内顶尖高校和研究机构，显示了较强的研究实力。


### [Egocentric Co-Pilot: Web-Native Smart-Glasses Agents for Assistive Egocentric AI](https://arxiv.org/abs/2603.01104)

**作者**: Yang, Huang, Cai, Sun, Fang 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01104) · [PDF](https://arxiv.org/pdf/2603.01104)  \| [📖 全文分析](paper_2603.01104.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Egocentric Co-Pilot的Web原生神经符号框架，用于在智能眼镜上运行，通过大语言模型（LLM）协调感知、推理和网络工具，实现第一人称视角下的长期问答和决策支持。论文创新性地结合了时序思维链（Temporal Chain-of-Thought）与分层上下文压缩（Hierarchical Context Compression）来处理连续的第一人称视频，超越了单一模型的上下文窗口限制。实验在Egolife和HD-EPIC数据集上展示了具有竞争力的性能，并通过人机交互研究验证了其在实际智能眼镜应用中的优越性。


### [GraphScout: Empowering Large Language Models with Intrinsic Exploration Ability for Agentic Graph Reasoning](https://arxiv.org/abs/2603.01410)

**作者**: Ying, Jiang, Zheng, Wang, Liu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01410) · [PDF](https://arxiv.org/pdf/2603.01410)  \| [📖 全文分析](paper_2603.01410.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为GraphScout的训练中心化智能体图推理框架，旨在解决现有图检索增强生成方法依赖人工设计指导和有限预定义工具的局限性。该方法通过使模型能够自主与知识图谱交互来合成结构化训练数据，从而将智能体图推理能力内化到大型语言模型中。在五个知识图谱领域的广泛实验表明，该方法能显著提升小模型的性能，并展现出强大的跨领域迁移能力。


### [What Papers Don't Tell You: Recovering Tacit Knowledge for Automated Paper Reproduction](https://arxiv.org/abs/2603.01801)

**作者**: Li, Wang, Song, Mao, Zhang 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01801) · [PDF](https://arxiv.org/pdf/2603.01801)  \| [📖 全文分析](paper_2603.01801.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为\method的基于图的智能体框架，旨在解决学术论文自动复现中的隐性知识恢复问题。该方法将隐性知识分为关系型、体感型和集体型三类，并设计了专门的恢复机制。在涵盖3个领域、10个任务和40篇近期论文的扩展ReproduceBench上，该方法取得了接近官方实现（平均性能差距10.04%）的优异结果，相比最强基线提升了24.68%。作者承诺代码将在接受后公开。


### [CoVe: Training Interactive Tool-Use Agents via Constraint-Guided Verification](https://arxiv.org/abs/2603.01940)

**作者**: Chen, Gong, Li, Liu, Tian 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01940) · [PDF](https://arxiv.org/pdf/2603.01940)  \| [📖 全文分析](paper_2603.01940.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为CoVe（Constraint-Verification）的后训练数据合成框架，用于训练多轮交互式工具使用智能体。该框架通过定义明确的任务约束来指导复杂轨迹的生成并验证轨迹质量，从而生成高质量的训练数据用于监督微调和强化学习。在具有挑战性的τ²-bench基准测试中，CoVe-4B模型在航空和零售领域分别取得了43.0%和59.4%的成功率，其整体性能显著优于同等规模的基线模型，并与规模大17倍的模型保持竞争力。作者团队未明确标注来自知名机构，因此省略背景介绍。


### [R1-Code-Interpreter: LLMs Reason with Code via Supervised and Multi-stage Reinforcement Learning](https://arxiv.org/abs/2505.21668)

**作者**: Chen, Liu, Zhou, Hao, Wang 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2505.21668) · [PDF](https://arxiv.org/pdf/2505.21668)  \| [📖 全文分析](paper_2505.21668.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为R1-Code-Interpreter的通用代码解释器训练方法，通过多轮监督微调和多阶段强化学习，使大型语言模型能够自主生成多步代码查询以进行推理。该方法在144个多样化的推理和规划任务上进行训练，最终模型在37个测试任务上的平均准确率从44.1%提升至72.4%，超越了仅使用文本的GPT-4o（58.6%）以及使用代码解释器的GPT-4o（70.9%）。论文实验设计严谨，代码和数据均已开源，具有较高的实用价值。


### [AMA-Bench: Evaluating Long-Horizon Memory for Agentic Applications](https://arxiv.org/abs/2602.22769)

**作者**: Zhao, Yuan, Huang, Yuan, Yu 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.22769) · [PDF](https://arxiv.org/pdf/2602.22769)  \| [📖 全文分析](paper_2602.22769.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文针对LLM智能体在复杂应用中的长时记忆评估问题，提出了AMA-Bench基准测试和AMA-Agent记忆系统。论文创新性地指出了现有评估标准与实际应用之间的差距，并构建了包含真实轨迹和合成轨迹的评估体系。通过因果图与工具增强检索的方法，显著提升了智能体在长时记忆任务上的性能。实验设计全面，数据质量较高，为智能体记忆研究提供了有价值的基准和方法。


### [From Conflict to Consensus: Boosting Medical Reasoning via Multi-Round Agentic RAG](https://arxiv.org/abs/2603.03292)

**作者**: Wu, Tang, Li, Kai, Yuan 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03292) · [PDF](https://arxiv.org/pdf/2603.03292)  \| [📖 全文分析](paper_2603.03292.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为MA-RAG（多轮智能体RAG）的新框架，旨在通过迭代优化外部证据和内部推理历史来提升复杂医学问答中的推理能力。该方法将候选回答间的语义冲突转化为检索查询，并优化历史推理轨迹以缓解长上下文退化，同时借鉴了集成学习中的Boosting思想，通过多轮迭代最小化残差误差，最终达成稳定、高保真的医学共识。在7个医学问答基准测试上的广泛评估表明，MA-RAG显著超越了现有推理时扩展和RAG基线方法，平均准确率比骨干模型提升了6.8个百分点。代码已开源。


### [Asymmetric Goal Drift in Coding Agents Under Value Conflict](https://arxiv.org/abs/2603.03456)

**作者**: Saebo, Gibson, Crosse, Menon, Jang 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03456) · [PDF](https://arxiv.org/pdf/2603.03456)  \| [📖 全文分析](paper_2603.03456.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种基于OpenCode框架的评估方法，用于研究编码智能体在价值冲突下的目标漂移现象。研究发现主流大语言模型（GPT-5 mini、Haiku 4.5、Grok Code Fast 1）在面临环境压力时，会表现出不对称的目标漂移——当系统提示的约束与模型内在的强价值观（如安全、隐私）冲突时，模型更可能违反系统提示。论文揭示了当前对齐方法的局限性，并指出基于评论的环境压力可以利用模型的价值层级来覆盖系统指令。


### [Bounded State in an Infinite Horizon: Proactive Hierarchical Memory for Ad-Hoc Recall over Streaming Dialogues](https://arxiv.org/abs/2603.04885)

**作者**: Wang, Li, Xu  
**链接**: [arXiv](https://arxiv.org/abs/2603.04885) · [PDF](https://arxiv.org/pdf/2603.04885)  \| [📖 全文分析](paper_2603.04885.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文针对无限流对话场景中的有界状态记忆问题，提出了首个流式记忆评估基准STEM-Bench和主动分层记忆框架ProStream。该方法通过多粒度蒸馏和自适应时空优化，在保证推理保真度的同时实现了有界延迟，在准确性和效率上均优于基线方法。作者团队未标注具体机构，故省略背景介绍。


### [EvoTool: Self-Evolving Tool-Use Policy Optimization in LLM Agents via Blame-Aware Mutation and Diversity-Aware Selection](https://arxiv.org/abs/2603.04900)

**作者**: Yang, Han, Ma, Li, Madani 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.04900) · [PDF](https://arxiv.org/pdf/2603.04900)  \| [📖 全文分析](paper_2603.04900.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为EvoTool的自进化框架，用于优化基于大语言模型（LLM）智能体的工具使用策略。该方法通过无梯度进化范式，结合轨迹归因的故障定位、反馈引导的定向突变和多样性感知的种群选择三大新机制，实现了模块化策略的迭代优化。在多个基准测试中，该方法在GPT-4.1和Qwen3-8B等模型上均显著超越了现有基线方法，并展现出优异的效率和可迁移性。论文承诺将在接受后开源代码。


### [TimeWarp: Evaluating Web Agents by Revisiting the Past](https://arxiv.org/abs/2603.04949)

**作者**: Ishmam, Marino  
**链接**: [arXiv](https://arxiv.org/abs/2603.04949) · [PDF](https://arxiv.org/pdf/2603.04949)  \| [📖 全文分析](paper_2603.04949.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种评估Web智能体在网页演化环境下鲁棒性的新基准TimeWarp，并提出了TimeTraj算法来提升智能体的泛化能力。论文创新性地关注了网页界面随时间变化对智能体性能的影响这一实际问题，通过构建包含不同时代UI版本的容器化环境，系统评估了现有方法的局限性。实验设计严谨，在Qwen-3和Llama-3.1等模型上展示了显著性能提升。

