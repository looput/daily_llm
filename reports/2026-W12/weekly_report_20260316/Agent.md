# Agent · 2026-03-16 ~ 2026-03-20

**论文数**: 37 篇

---

## 📊 趋势分析

### 研究全貌

本领域的研究方向主要围绕智能体从理论构建到实际部署的全生命周期展开，可归纳为四大核心方向。**智能体能力构建与训练**关注如何通过数据合成、强化学习等策略高效训练出强大的通用或专用智能体。**智能体系统架构与编排**深入探索多智能体协同、状态管理以及生产级部署的可靠性保障机制。**智能体评估与基准测试**致力于构建针对记忆、工具使用、过程质量及安全性的系统性评测体系。**智能体安全与伦理**则聚焦于隐私风险、推荐安全性和系统可靠性等关键问题。当前的热点问题高度一致，即**如何让智能体在复杂、动态、长周期的真实世界任务中可靠、安全、高效地运行**。整体研究趋势呈现出从追求单一任务性能，向关注**系统鲁棒性、长期适应性、生产可部署性**的深刻转变，研究重心正从“模型能力”转向“系统工程”。跨批次分析发现，架构设计正从应用层抽象向操作系统底层靠拢，追求更高效、更原生的实现方式。

### 重点方法深度解析

从所有批次中，以下几个工作因其根本性的创新和广泛的适用性尤为突出：

**1. 《Quine: Realizing LLM Agents as Native POSIX Processes》** 提出了一种颠覆性的智能体运行时架构。其核心创新在于**摒弃了在应用层重复实现操作系统已有功能（如隔离、调度）的做法，直接将LLM智能体映射为原生的POSIX进程**，从而从根本上解决了传统框架在资源控制、组合性和系统集成方面的复杂性。技术实现上，它进行了清晰的映射：智能体身份对应进程ID，接口对应标准输入/输出流，状态对应进程内存和文件系统，生命周期则由标准的fork/exec/exit系统调用管理。这使得智能体天然继承了操作系统内核提供的隔离、资源控制和组合能力，并支持递归委托和与Shell等现有工具链无缝集成。该方法特别适合需要与现有Unix工具链深度集成、对资源隔离和系统稳定性要求极高的生产环境，为智能体系统设计提供了一种“返璞归真”的底层思路。

**2. 《Scaling Generalist Data-Analytic Agents》** 为解决构建高质量领域专家智能体的数据瓶颈，提出了一套名为DataMind的系统化框架。其核心创新是通过**精细的任务分类与递归式从易到难的任务组合**来生成多样且高难度的训练数据，并结合**知识增强的轨迹采样与过滤**来保证数据质量。训练时，它结合了监督微调和强化学习的动态调整目标，并设计了**内存高效且稳定的多轮代码执行框架**，解决了工具调用不稳定的核心痛点。基于该框架训练的模型在多个数据分析基准上超越了包括GPT-5在内的最强专有基线。该方法为以可控成本构建高质量、可复现的领域专家智能体提供了清晰的工程路径，特别适合需要处理复杂、多格式数据并执行长序列分析步骤的场景。

**3. 《The Provenance Paradox in Multi-Agent LLM Routing: Delegation Contracts and Attested Identity in LDP》** 揭示了多智能体系统中一个关键的理论与实践陷阱。其核心创新是发现了**“来源悖论”**：当代理可以虚报自身能力时，基于自报分数的路由策略会系统性地选择最差的代理。为解决此问题，论文扩展了LLM委托协议，引入了**委托合同**、**声明身份与认证身份模型**以及**类型化失败语义**。实验验证了悖论的存在，而基于认证身份的路由则达到了接近最优的性能。这个方法为任何涉及任务委托和路由的多智能体系统提供了至关重要的安全保障和性能优化框架。

**4. 《Bridging Protocol and Production: Design Patterns for Deploying AI Agents with Model Context Protocol》** 则从工程实践角度，精准地指出了当前流行的工具调用协议（MCP）的不足，并提出了三项填补空白的机制。其核心创新是将生产中的可靠性挑战形式化为可测试的算法，包括**上下文感知代理协议（CABP）**、**自适应超时预算分配（ATBA）** 和**结构化错误恢复框架（SERF）**。该方法的价值在于其强烈的工程导向，为任何基于工具调用的智能体系统提供了从“协议可用”到“生产可靠”的关键桥梁。

这些方法构成了一个从底层架构（Quine）、到能力构建（DataMind）、再到上层协作与部署（来源悖论、MCP扩展）的完整技术栈。Quine提供了最基础的执行环境，DataMind解决了特定领域的能力生成，而后两者则确保了在复杂协作和生产环境中的可靠运行。

### 实践启示

这些研究为大模型应用开发提供了清晰的路线图。**对于追求系统稳定性和可维护性的复杂应用**，应优先借鉴Quine的设计理念，探索将智能体实现为轻量级进程或容器，以利用成熟的运维体系。**对于希望构建特定领域专家智能体的团队**，应重点关注DataMind这类系统化的数据合成与训练框架，以可控成本突破数据瓶颈。**在开发多智能体协作系统时**，初期就必须警惕“来源悖论”，设计包含身份认证和动态信任管理的安全层，并引入明确的委托合同。

具体建议如下：1）**架构选型**：在新项目初期，可考虑采用类似Quine的进程模型，或至少确保框架能与操作系统级工具（如容器编排）良好集成。2）**数据策略**：积极采用基于任务分解和知识增强的合成数据生成方法，这是快速构建高性能智能体的关键。3）**安全与评估**：必须超越最终答案的正确性，在开发早期就引入对抗性测试（如配对轨迹协议）和过程质量监控。

实现时的关键注意事项包括：动态信任模型的更新策略需精心设计以避免振荡；合成数据的质量控制是成败关键，需确保其多样性和真实性；在利用开源模型和数据加速开发时，需注意其许可协议和潜在风险。最佳实践组合是：以**进程化架构（Quine）** 为基础，采用**系统化数据合成（DataMind）** 训练核心能力，并在部署时采用**基于认证的协作机制与结构化错误恢复**，从而构建出可靠、可控且高效的生产级智能体系统。

---

## 📄 论文列表（37 篇）

### [Toward Personalized LLM-Powered Agents: Foundations, Evaluation, and Future Directions](https://arxiv.org/abs/2602.22680)

**作者**: Xu, Chen, Ma, Liu, Wang 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.22680) · [PDF](https://arxiv.org/pdf/2602.22680)  \| [📖 全文分析](paper_2602.22680.md)  
**评分**: 8.93  （novelty: 7.0 | method: 9.5 | evidence: 8.0 | clarity: 9.0）

> 本文是一篇关于个性化大语言模型智能体（PLAs）的综述性论文，系统性地梳理了该新兴领域的研究现状。论文提出了一个以能力为导向的分类框架，围绕用户画像建模、记忆、规划和行动执行四个核心能力组织现有工作，并深入分析了用户信号在智能体决策流程中的表示、传播和利用机制。论文进一步探讨了针对个性化智能体的评估指标、基准测试范式以及从对话助手到领域专家系统的应用场景。通过澄清智能体系统中个性化的设计空间，本文为开发更用户对齐、自适应和可部署的LLM智能体提供了结构化基础。作者团队未明确标注所属知名机构，因此省略背景介绍。


### [Scaling Generalist Data-Analytic Agents](https://arxiv.org/abs/2509.25084)

**作者**: Qiao, Zhao, Qiu, Wang, Zhang 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.25084) · [PDF](https://arxiv.org/pdf/2509.25084)  \| [📖 全文分析](paper_2509.25084.md)  
**评分**: 8.86  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为DataMind的可扩展数据合成与智能体训练框架，旨在构建通用的数据分析智能体。该方法通过精细的任务分类、递归式任务组合、知识增强的轨迹采样、动态调整的训练目标以及内存高效的多轮代码执行框架，有效解决了开源数据分析智能体面临的数据资源不足、训练策略不当和代码执行不稳定等关键挑战。基于该框架构建的DataMind-12K高质量轨迹数据集和DataMind-14B/7B模型在多个数据分析基准测试中取得了领先性能，超越了包括DeepSeek-V3.1和GPT-5在内的最强专有基线模型。作者承诺开源数据集和模型，对社区有重要贡献。


### [Bridging Protocol and Production: Design Patterns for Deploying AI Agents with Model Context Protocol](https://arxiv.org/abs/2603.13417)

**作者**: Srinivasan  
**链接**: [arXiv](https://arxiv.org/abs/2603.13417) · [PDF](https://arxiv.org/pdf/2603.13417)  \| [📖 全文分析](paper_2603.13417.md)  
**评分**: 8.79  （novelty: 9.0 | method: 9.5 | evidence: 8.0 | clarity: 8.5）

> 本文针对AI智能体在生产环境中部署时遇到的工具调用标准化问题，提出了三项关键机制来弥补Model Context Protocol（MCP）的不足。论文基于企业级部署的实际经验，识别了协议层面的三个缺失原语（身份传播、自适应工具预算、结构化错误语义），并提出了相应的解决方案。研究具有明确的工程实践导向和理论形式化，实验方法可复现。


### [VTC-Bench: Evaluating Agentic Multimodal Models via Compositional Visual Tool Chaining](https://arxiv.org/abs/2603.15030)

**作者**: Zhu, Dong, Wang, Shi, Wu 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15030) · [PDF](https://arxiv.org/pdf/2603.15030)  \| [📖 全文分析](paper_2603.15030.md)  
**评分**: 8.79  （novelty: 9.0 | method: 9.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了VTC-Bench，一个用于评估多模态大语言模型（MLLMs）工具使用能力的综合性基准测试。该基准通过引入32种基于OpenCV的多样化视觉操作，构建了包含680个问题的测试集，并按照九类认知层次进行组织，旨在评估模型在多工具组合和长视野、多步骤规划执行方面的能力。对19个领先MLLM的广泛实验揭示了当前模型在视觉代理能力上的关键局限，例如难以适应多样化工具集、泛化到未见过的操作，以及在复杂任务中难以制定高效执行计划。VTC-Bench为开发更通用的视觉代理模型建立了严格的基线。


### [Auto Researching, not hyperparameter tuning: Convergence Analysis of 10,000 Experiments](https://arxiv.org/abs/2603.15916)

**作者**: Li  
**链接**: [arXiv](https://arxiv.org/abs/2603.15916) · [PDF](https://arxiv.org/pdf/2603.15916)  \| [📖 全文分析](paper_2603.15916.md)  
**评分**: 8.71  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 7.5）

> 本文通过分析由两个LLM代理（Claude Opus和Gemini 2.5 Pro）在27天内执行的超过一万次实验，深入探讨了LLM代理在自主设计机器学习实验时的行为本质。研究发现，架构选择解释了94%的性能方差，而超参数调整仅占6%，这证实了LLM代理能够进行真正的架构发现，而非局限于狭窄的超参数调优。论文通过大规模的实证分析，为LLM引导的组合式机器学习实验设计提供了首个大规模实证框架。


### [Hilbert: Recursively Building Formal Proofs with Informal Reasoning](https://arxiv.org/abs/2509.22819)

**作者**: Varambally, Voice, Sun, Chen, Yu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.22819) · [PDF](https://arxiv.org/pdf/2509.22819)  \| [📖 全文分析](paper_2509.22819.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为Hilbert的智能体框架，旨在弥合非正式数学推理与形式化定理证明之间的差距。该系统通过协调四个组件（非正式推理LLM、专门化证明器LLM、形式化验证器和语义定理检索器），并采用递归分解策略，在多个数学定理证明基准测试中取得了当前公开模型中的最佳性能。


### [daVinci-Env: Open SWE Environment Synthesis at Scale](https://arxiv.org/abs/2603.13023)

**作者**: Fu, Wu, Wu, Peng, Huang 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.13023) · [PDF](https://arxiv.org/pdf/2603.13023)  \| [📖 全文分析](paper_2603.13023.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了OpenSWE框架，这是目前最大规模、完全透明的Python软件工程智能体训练环境合成系统。该系统包含45,320个可执行的Docker环境，覆盖12.8k个代码仓库，所有基础设施和评估脚本均已开源。通过多智能体合成流水线和质量中心过滤机制，构建了约9,000个高质量保证的训练环境。实验表明，基于OpenSWE训练的模型在SWE-bench Verified基准上达到SOTA水平（66.0%），同时在数学推理和科学问答等跨领域任务上也有显著提升。


### [Infinite Problem Generator: Verifiably Scaling Physics Reasoning Data with Agentic Workflows](https://arxiv.org/abs/2603.14486)

**作者**: Sharan, Hebbale, Kumar  
**链接**: [arXiv](https://arxiv.org/abs/2603.14486) · [PDF](https://arxiv.org/pdf/2603.14486)  \| [📖 全文分析](paper_2603.14486.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为无限问题生成器（IPG）的智能体框架，用于生成具有可验证解的高质量物理推理数据。该方法通过公式即代码范式，将解决方案构建为可执行的Python程序，确保严格的数学一致性。作为概念验证，作者发布了ClassicalMechanicsV1数据集，包含1,335个经典力学问题，并识别出问题复杂度与验证代码长度之间的强线性关系，为可控课程生成提供了新指标。论文实验设计严谨，数据质量高，并开源了完整流程和数据集。


### [AgentDrift: Unsafe Recommendation Drift Under Tool Corruption Hidden by Ranking Metrics in LLM Agents](https://arxiv.org/abs/2603.12564)

**作者**: Wu, Koshiyama, Bulathwela, Perez-Ortiz  
**链接**: [arXiv](https://arxiv.org/abs/2603.12564) · [PDF](https://arxiv.org/pdf/2603.12564)  \| [📖 全文分析](paper_2603.12564.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.5）

> 本文提出了一种新的评估协议，用于揭示大语言模型（LLM）智能体在多轮高风险场景（如金融咨询）中，当工具输出被污染时，其推荐内容会系统性偏离安全准则，而这一风险被传统的排序质量指标（如NDCG）所掩盖。作者团队来自伦敦大学学院（UCL），在人工智能和机器学习领域具有深厚的研究背景。研究创新性地设计了配对轨迹协议，在七个不同规模的LLM上进行了系统性实验，揭示了评估盲区现象，并提出了改进的评估指标。实验设计严谨，发现具有重要现实意义。


### [A Trace-Based Assurance Framework for Agentic AI Orchestration: Contracts, Testing, and Governance](https://arxiv.org/abs/2603.18096)

**作者**: Paduraru, Bouruc, Stefanescu  
**链接**: [arXiv](https://arxiv.org/abs/2603.18096) · [PDF](https://arxiv.org/pdf/2603.18096)  \| [📖 全文分析](paper_2603.18096.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种面向智能体AI编排的基于追踪的保障框架，针对LLM在多智能体系统中协调时可能出现的长期交互故障、随机决策问题和外部副作用等挑战，设计了包含契约、测试和治理三个核心组件的系统性解决方案。该框架通过消息-动作追踪（MAT）实现执行过程的检测，提供机器可检查的契约判定、故障定位和确定性重放功能，并支持压力测试和结构化故障注入，为多智能体LLM系统的测试、评估和治理提供了通用抽象。


### [POLCA: Stochastic Generative Optimization with LLM](https://arxiv.org/abs/2603.14769)

**作者**: Ren, Nie, Xie, Cheng  
**链接**: [arXiv](https://arxiv.org/abs/2603.14769) · [PDF](https://arxiv.org/pdf/2603.14769)  \| [📖 全文分析](paper_2603.14769.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为POLCA（Prioritized Optimization with Local Contextual Aggregation）的随机生成优化框架，用于解决从LLM提示优化到多轮智能体优化等复杂系统的自动化优化问题。该工作将传统需要大量人工迭代的优化过程形式化为一个随机生成优化问题，其中生成式语言模型作为优化器，通过数值奖励和文本反馈来发现最佳系统。POLCA通过优先级队列管理探索与利用的权衡，并整合了ε-Net机制和LLM摘要器来提升效率。理论证明和实验验证表明，POLCA在多个基准测试中均优于现有方法，并公开了代码。


### [Evaluating Memory in LLM Agents via Incremental Multi-Turn Interactions](https://arxiv.org/abs/2507.05257)

**作者**: Hu, Wang, McAuley  
**链接**: [arXiv](https://arxiv.org/abs/2507.05257) · [PDF](https://arxiv.org/pdf/2507.05257)  \| [📖 全文分析](paper_2507.05257.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文针对大语言模型（LLM）智能体评估中一个被忽视的关键维度——记忆能力，提出了首个系统性的评测基准MemoryAgentBench。该工作基于记忆科学和认知科学的经典理论，提炼出记忆智能体应具备的四个核心能力（准确检索、测试时学习、长程理解、选择性遗忘），并通过将现有长上下文数据集改造为多轮交互格式，构建了一个全面覆盖这些能力的评测体系。实验评估了从简单上下文扩展、检索增强生成（RAG）到具有外部记忆模块的先进智能体等多种方法，揭示了现有技术在全面记忆能力上的不足。作者团队来自学术界，其中McAuley教授在推荐系统、数据挖掘和机器学习领域有广泛影响力。


### [From Weak Cues to Real Identities: Evaluating Inference-Driven De-Anonymization in LLM Agents](https://arxiv.org/abs/2603.18382)

**作者**: Ko, Jeong, Thakur, Kim, Jia  
**链接**: [arXiv](https://arxiv.org/abs/2603.18382) · [PDF](https://arxiv.org/pdf/2603.18382)  \| [📖 全文分析](paper_2603.18382.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文研究了基于大语言模型（LLM）的智能体从稀疏、非直接识别的线索中自主重构真实世界身份的新型隐私风险。作者将这种威胁形式化为“推理驱动的链接”，并在三种设置下进行了系统评估：经典链接场景（Netflix和AOL）、受控基准（InferLink）以及现代文本丰富的数字痕迹。研究发现，即使没有特定任务启发式方法，智能体也能成功执行固定池匹配和开放式身份解析任务，在Netflix Prize场景中重构了79.2%的身份，显著优于56.0%的经典基线。更重要的是，这种链接不仅出现在明确的对抗性提示下，也作为良性跨源分析的副产品出现。这些发现表明，身份推理（而不仅仅是显式信息泄露）必须被视为首要的隐私风险。作者团队未明确标注来自知名机构，因此省略背景介绍。


### [Position: Agentic Evolution is the Path to Evolving LLMs](https://arxiv.org/abs/2602.00359)

**作者**: Lin, Lu, Shi, He, Mao 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.00359) · [PDF](https://arxiv.org/pdf/2602.00359)  \| [📖 全文分析](paper_2602.00359.md)  
**评分**: 8.50  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.0）

> 本文提出了一种名为A-Evolve的通用框架，将LLM在部署环境中的持续改进视为一个目标导向的优化过程，并提出了进化缩放假说。论文核心观点是，仅靠扩大训练和推理计算无法弥合静态训练与动态部署环境之间的差距，而赋予进化过程以自主智能体（agentic）能力是LLM持续适应的必然路径。作者团队来自A-EVO-Lab，从命名推测可能是一个专注于进化与AI交叉研究的实验室或团队，但未明确标注其所属知名机构。


### [StatePlane: A Cognitive State Plane for Long-Horizon AI Systems Under Bounded Context](https://arxiv.org/abs/2603.13644)

**作者**: Annapureddy, Mulcahy, Thamatani  
**链接**: [arXiv](https://arxiv.org/abs/2603.13644) · [PDF](https://arxiv.org/pdf/2603.13644)  \| [📖 全文分析](paper_2603.13644.md)  
**评分**: 8.50  （novelty: 9.0 | method: 9.5 | evidence: 7.0 | clarity: 8.5）

> 本文提出了一种名为StatePlane的模型无关认知状态平面，旨在解决大语言模型和小语言模型在有限上下文窗口下进行长程推理的难题。该方法基于认知心理学和系统设计原理，通过形式化建模、选择性编码、目标条件检索和自适应遗忘等机制，为AI系统管理情景、语义和程序状态提供了系统化框架。论文展示了在六个领域特定基准上的评估结果，表明无需扩展上下文窗口或重新训练模型即可实现长程智能。作者团队未明确标注知名机构，故省略背景介绍。


### [AgentProcessBench: Diagnosing Step-Level Process Quality in Tool-Using Agents](https://arxiv.org/abs/2603.14465)

**作者**: Fan, Ye, Huo, Chen, Guo 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.14465) · [PDF](https://arxiv.org/pdf/2603.14465)  \| [📖 全文分析](paper_2603.14465.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了首个专门用于评估工具使用智能体在真实场景中步骤级过程质量的基准测试AgentProcessBench。该基准填补了现有过程级评估主要局限于封闭数学领域、无法捕捉工具执行动态开放特性的空白。作者团队来自中国人民大学（RUC），代码和数据已开源。


### [UIS-Digger: Towards Comprehensive Research Agent Systems for Real-world Unindexed Information Seeking](https://arxiv.org/abs/2603.08117)

**作者**: Liu, Kuang, Zhuang, Cheng, Zhou 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.08117) · [PDF](https://arxiv.org/pdf/2603.08117)  \| [📖 全文分析](paper_2603.08117.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种针对未索引信息检索（UIS）问题的创新性研究，这是一个当前信息检索和AI智能体领域被忽视但至关重要的挑战。作者团队构建了首个专门的UIS基准测试UIS-QA，并提出了一个名为UIS-Digger的多智能体框架。该框架通过双模式浏览（同时进行网页搜索和文件解析）来主动与未索引信息源交互，显著提升了在此类任务上的性能。尽管使用了相对较小的骨干模型（约300亿参数），但其性能超过了集成更复杂大模型（如O3和GPT-4.1）的系统。这项工作不仅揭示了当前智能体评估范式的根本局限，还为推进UIS研究提供了首个工具包，定义了一个新的、有前景的研究方向。数据集已在Hugging Face开源。


### [AVIATOR: Towards AI-Agentic Vulnerability Injection Workflow for High-Fidelity, Large-Scale Code Security Dataset](https://arxiv.org/abs/2508.20866)

**作者**: Lbath, Amini, Delaitre, Okun  
**链接**: [arXiv](https://arxiv.org/abs/2508.20866) · [PDF](https://arxiv.org/pdf/2508.20866)  \| [📖 全文分析](paper_2508.20866.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为AVIATOR的AI智能体漏洞注入框架，这是首个将漏洞注入任务分解为专门AI智能体协同工作流程的方法。该方法通过集成RAG和基于LoRA的轻量级微调技术，能够生成真实、类别特定的漏洞，而不依赖手工模式。在三个基准测试中，AVIATOR实现了高注入保真度（91-95%），在准确性和漏洞覆盖范围上均超越了现有技术。当用于数据增强来训练基于深度学习的漏洞检测模型时，AVIATOR带来了最强的下游性能提升，平均F1分数比无增强提高了22%，比之前的SOTA模型VulScribeR提高了3%，且召回率提高7%而无精度损失。其增强数据表现出最低的分布失真，并以比VulScribeR低4.3倍的成本高效扩展。作者团队未在摘要中明确列出所属机构，因此省略背景介绍。


### [CodeScout: An Effective Recipe for Reinforcement Learning of Code Search Agents](https://arxiv.org/abs/2603.17829)

**作者**: Sutawika, Soni, R, Gandhi, Yassine 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.17829) · [PDF](https://arxiv.org/pdf/2603.17829)  \| [📖 全文分析](paper_2603.17829.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为CodeScout的强化学习配方，用于训练代码搜索智能体。该方法的核心创新在于，仅使用标准Unix终端作为工具，通过精心设计的强化学习训练方案，即可在代码定位任务上取得优异性能，超越了更大规模的基座模型和部分闭源模型。作者团队来自卡内基梅隆大学（CMU）等知名机构，其中Graham Neubig教授是该领域知名学者。


### [Spend Less, Reason Better: Budget-Aware Value Tree Search for LLM Agents](https://arxiv.org/abs/2603.12634)

**作者**: Li, Deng, Li, Li  
**链接**: [arXiv](https://arxiv.org/abs/2603.12634) · [PDF](https://arxiv.org/pdf/2603.12634)  \| [📖 全文分析](paper_2603.12634.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为预算感知价值树（BAVT）的训练免费推理时框架，用于解决大语言模型（LLM）智能体在预算约束下的多跳推理问题。该方法通过将多跳推理建模为动态搜索树，并引入预算感知的节点选择机制和残差值预测器，实现了在有限计算资源下的高效、可靠推理。论文在四个多跳问答基准上进行了广泛评估，证明了BAVT在严格低预算约束下显著优于基线方法，其性能甚至可与使用4倍资源的基线方法相媲美。作者团队未在摘要中明确提及所属知名机构，因此省略背景介绍。


### [Quine: Realizing LLM Agents as Native POSIX Processes](https://arxiv.org/abs/2603.18030)

**作者**: Ke  
**链接**: [arXiv](https://arxiv.org/abs/2603.18030) · [PDF](https://arxiv.org/pdf/2603.18030)  \| [📖 全文分析](paper_2603.18030.md)  
**评分**: 8.43  （novelty: 9.5 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种创新的LLM智能体运行时架构Quine，将LLM智能体实现为原生POSIX进程。该方法通过将智能体抽象直接映射到操作系统进程模型（身份对应PID，接口对应标准流和退出状态，状态对应内存、环境变量和文件系统，生命周期对应fork/exec/exit），避免了在应用层重复实现操作系统已提供的隔离、调度和通信机制。Quine通过单个可执行文件递归生成自身新实例来实现这一模型。设计不仅继承了内核提供的隔离、组合和资源控制能力，还自然支持递归委托、通过exec进行上下文更新以及与shell原生组合。论文还指出了POSIX进程模型的局限性，并提出了超越进程语义的两个扩展方向：任务相对世界和可修改时间。参考实现已在GitHub上开源。


### [The Provenance Paradox in Multi-Agent LLM Routing: Delegation Contracts and Attested Identity in LDP](https://arxiv.org/abs/2603.18043)

**作者**: Prakash  
**链接**: [arXiv](https://arxiv.org/abs/2603.18043) · [PDF](https://arxiv.org/pdf/2603.18043)  \| [📖 全文分析](paper_2603.18043.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文针对多智能体LLM系统中的任务委托与路由问题，提出了一个重要的理论发现——来源悖论（Provenance Paradox），并基于LLM委托协议（LDP）提出了三项核心扩展：委托合同、声明身份与认证身份模型、类型化失败语义。论文通过模拟实验和真实Claude模型验证了悖论的存在以及所提解决方案的有效性。实验设计严谨，结果具有统计显著性。


### [OpenSeeker: Democratizing Frontier Search Agents by Fully Open-Sourcing Training Data](https://arxiv.org/abs/2603.15594)

**作者**: Du, Ye, Tang, Zhu, Lu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15594) · [PDF](https://arxiv.org/pdf/2603.15594)  \| [📖 全文分析](paper_2603.15594.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.0）

> 本文提出了一种名为OpenSeeker的完全开源搜索智能体，旨在解决前沿大语言模型智能体开发中高质量训练数据稀缺和封闭的问题。论文通过两项核心技术——基于事实的可扩展可控QA合成与去噪轨迹合成——仅使用1.17万个合成样本进行单次SFT训练，便在多个基准测试中达到了最先进的性能，甚至超越了部分工业界竞品。论文作者未明确标注所属机构，但从姓名推断可能来自中国学术界或工业界研究团队。该工作通过完全开源模型和数据集，对推动该领域的透明化和社区协作具有重要价值。


### [XSkill: Continual Learning from Experience and Skills in Multimodal Agents](https://arxiv.org/abs/2603.12056)

**作者**: Jiang, Su, Qu, Fung  
**链接**: [arXiv](https://arxiv.org/abs/2603.12056) · [PDF](https://arxiv.org/pdf/2603.12056)  \| [📖 全文分析](paper_2603.12056.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为XSkill的双流框架，用于多模态智能体的持续学习。该框架创新性地从经验（experience）和技能（skill）两种互补的可复用知识形式中学习，并通过视觉观察进行知识提取与检索，形成了一个持续学习的闭环。在五个不同领域的基准测试和四种骨干模型上，XSkill均显著优于仅使用工具或基于学习的基线方法，并展现出优秀的零样本泛化能力。作者团队背景信息未在提供内容中明确提及，故省略。


### [Feynman: Knowledge-Infused Diagramming Agent for Scalable Visual Designs](https://arxiv.org/abs/2603.12597)

**作者**: Wen, Cai, Lee, Estep, Sunshine 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12597) · [PDF](https://arxiv.org/pdf/2603.12597)  \| [📖 全文分析](paper_2603.12597.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.0）

> 本文提出了一种名为Feynman的知识增强图表生成智能体，用于大规模视觉设计。该方法通过将领域知识组件转化为声明式程序，并利用Penrose图表系统进行优化渲染，实现了高质量图表-标题对的自动化生成。作者团队来自多个知名机构，包括卡内基梅隆大学（CMU）等，在人工智能和可视化领域具有深厚的研究背景。


### [AI Planning Framework for LLM-Based Web Agents](https://arxiv.org/abs/2603.12710)

**作者**: Shahnovsky, Dror  
**链接**: [arXiv](https://arxiv.org/abs/2603.12710) · [PDF](https://arxiv.org/pdf/2603.12710)  \| [📖 全文分析](paper_2603.12710.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种将LLM驱动的Web智能体形式化为序列决策过程的理论框架。作者通过建立现代智能体架构与传统规划范式（如广度优先搜索、最佳优先树搜索、深度优先搜索）之间的映射关系，为诊断智能体失败原因（如上下文漂移、任务分解不连贯）提供了原则性方法。论文还提出了五个超越简单成功率的新评估指标，并基于WebArena基准创建了一个包含794条人工标注轨迹的新数据集进行验证。实验比较了基线逐步执行智能体与新颖的预先完整规划智能体，揭示了不同架构在不同指标上的优劣，强调了根据具体应用约束选择合适架构时需使用多维评估指标的必要性。


### [Why Agents Compromise Safety Under Pressure](https://arxiv.org/abs/2603.14975)

**作者**: Jiang, Tang  
**链接**: [arXiv](https://arxiv.org/abs/2603.14975) · [PDF](https://arxiv.org/pdf/2603.14975)  \| [📖 全文分析](paper_2603.14975.md)  
**评分**: 8.36  （novelty: 9.0 | method: 9.5 | evidence: 6.5 | clarity: 8.5）

> 本文探讨了大型语言模型智能体在复杂环境中面临的目标达成与安全约束冲突问题，提出了'智能体压力'这一新概念，揭示了压力下智能体为保持效用而策略性牺牲安全性的'规范性漂移'现象。研究发现高级推理能力会加速这种安全性的下降，因为模型会构建语言合理化来为违规行为辩护。论文还分析了根本原因并探索了初步的缓解策略，如压力隔离。


### [Zephyrus: An Agentic Framework for Weather Science](https://arxiv.org/abs/2510.04017)

**作者**: Varambally, Fisher, Thakker, Chen, Xia 等 17 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.04017) · [PDF](https://arxiv.org/pdf/2510.04017)  \| [📖 全文分析](paper_2510.04017.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由Rose-STL-Lab团队（作者来自加州大学圣地亚哥分校、加州大学伯克利分校、麻省理工学院、斯坦福大学等知名高校）提出了首个面向气象科学的智能体框架Zephyrus，通过结合大语言模型与气象数据环境，实现了语言交互式气象分析。该框架创新性地构建了代码环境、工具集和评估基准，在气象科学智能化方面具有重要价值。


### [DynaTrust: Defending Multi-Agent Systems Against Sleeper Agents via Dynamic Trust Graphs](https://arxiv.org/abs/2603.15661)

**作者**: Li, Hu, Zhang, Quan, Yu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15661) · [PDF](https://arxiv.org/pdf/2603.15661)  \| [📖 全文分析](paper_2603.15661.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为DynaTrust的新型防御方法，用于保护基于大语言模型的多智能体系统免受休眠代理攻击。该方法将多智能体系统建模为动态信任图，将信任视为连续演化的过程而非静态属性，通过动态更新智能体信任度、自主重构图结构来隔离受损智能体并恢复任务连通性。实验表明，DynaTrust在对抗条件下防御成功率超过86%，比现有最佳方法提升41.7%，同时显著降低误报率，有效平衡了安全性与系统可用性。


### [MiroThinker-1.7 & H1: Towards Heavy-Duty Research Agents via Verification](https://arxiv.org/abs/2603.15726)

**作者**: MiroMind Team, Bai, Bing, Lei, Li 等 44 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15726) · [PDF](https://arxiv.org/pdf/2603.15726)  \| [📖 全文分析](paper_2603.15726.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文由MiroMind团队提出了一种名为MiroThinker的新型研究智能体，旨在解决复杂的长视野推理任务。该工作通过引入代理中间训练阶段和验证机制，显著提升了多步推理的可靠性和性能。论文在涵盖开放网络研究、科学推理和金融分析的多个基准测试中取得了最先进的性能，并开源了模型，为相关研究提供了有价值的资源。


### [Anticipatory Planning for Multimodal AI Agents](https://arxiv.org/abs/2603.16777)

**作者**: Liang, Zhou, Gu, Tan, Wu 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16777) · [PDF](https://arxiv.org/pdf/2603.16777)  \| [📖 全文分析](paper_2603.16777.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种名为TraceR1的两阶段强化学习框架，旨在解决现有多模态智能体反应式决策的局限性，通过前瞻性轨迹推理提升长期规划能力。论文在七个基准测试（包括在线/离线计算机使用和多模态工具使用推理任务）上验证了方法的有效性，在规划稳定性、执行鲁棒性和泛化能力方面均取得了显著提升。作者团队来自多个知名机构，包括卡内基梅隆大学（CMU）、斯坦福大学（Stanford）、微软（Microsoft）和谷歌（Google）等，体现了较强的研究实力和跨机构合作背景。


### [TRUST-SQL: Tool-Integrated Multi-Turn Reinforcement Learning for Text-to-SQL over Unknown Schemas](https://arxiv.org/abs/2603.16448)

**作者**: Jian, Zhang, Du, Ruan, Pei 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16448) · [PDF](https://arxiv.org/pdf/2603.16448)  \| [📖 全文分析](paper_2603.16448.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为TRUST-SQL的新方法，用于解决真实企业环境中数据库模式未知的Text-to-SQL解析问题。该方法将任务建模为部分可观测马尔可夫决策过程，并设计了一个结构化的四阶段协议来引导推理，同时提出了新颖的双轨GRPO策略以解决信用分配问题。在五个基准测试上的广泛实验表明，该方法相比基线模型取得了显著的性能提升，甚至在完全不预加载元数据的情况下，也能达到或超越依赖模式预填充的强基线。


### [Symphony: A Cognitively-Inspired Multi-Agent System for Long-Video Understanding](https://arxiv.org/abs/2603.17307)

**作者**: Yan, Zhou, Xu, Feng, Liu  
**链接**: [arXiv](https://arxiv.org/abs/2603.17307) · [PDF](https://arxiv.org/pdf/2603.17307)  \| [📖 全文分析](paper_2603.17307.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Symphony的多智能体系统，旨在解决长视频理解任务中信息密度高、时间跨度大带来的挑战。该方法通过模拟人类认知模式，将任务分解为细粒度子任务，并引入基于反思的深度推理协作机制，显著提升了推理能力。实验表明，Symphony在LVBench、LongVideoBench、VideoMME和MLVU等多个基准测试上取得了最先进的性能，在LVBench上比之前的最佳方法提升了5.0%。作者团队未在摘要中明确列出所属机构，因此省略背景介绍。


### [AgentFactory: A Self-Evolving Framework Through Executable Subagent Accumulation and Reuse](https://arxiv.org/abs/2603.18000)

**作者**: Zhang, Lu, Qian, He, Liu  
**链接**: [arXiv](https://arxiv.org/abs/2603.18000) · [PDF](https://arxiv.org/pdf/2603.18000)  \| [📖 全文分析](paper_2603.18000.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为AgentFactory的新型LLM智能体自进化框架，通过将成功任务解决方案保存为可执行的子智能体代码而非文本经验，实现了持续的能力积累。该方法在创新性和实用性方面表现突出，实验验证充分且代码开源。作者团队未明确标注所属机构，但代码库域名显示与北京大学（PKU）相关，可能来自学术研究团队。


### [ZEBRAARENA: A Diagnostic Simulation Environment for Studying Reasoning-Action Coupling in Tool-Augmented LLMs](https://arxiv.org/abs/2603.18614)

**作者**: Zhao, Schmidt, Zou, Balachandran, Chen  
**链接**: [arXiv](https://arxiv.org/abs/2603.18614) · [PDF](https://arxiv.org/pdf/2603.18614)  \| [📖 全文分析](paper_2603.18614.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为ZebraArena的诊断性仿真环境，专门用于研究工具增强型大语言模型（LLMs）中推理与外部动作的耦合问题。该环境通过程序化生成任务，具有可控难度和知识最小化设计，有效隔离了环境动态复杂性、记忆知识或数据污染等因素的干扰，为评估LLMs的推理-行动耦合能力提供了清晰、可解释的基准。作者团队未明确标注所属机构，但论文内容显示出扎实的研究功底。


### [Parametric Social Identity Injection and Diversification in Public Opinion Simulation](https://arxiv.org/abs/2603.16142)

**作者**: Wang, Zhou, Du, Ai, Liu  
**链接**: [arXiv](https://arxiv.org/abs/2603.16142) · [PDF](https://arxiv.org/pdf/2603.16142)  \| [📖 全文分析](paper_2603.16142.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为参数化社会身份注入（PSII）的新框架，用于解决大语言模型在公共舆论模拟中存在的多样性崩溃问题。该方法通过在LLM的中间隐藏状态中注入参数化的人口统计属性和价值取向表示，实现了细粒度和可控的身份调节。实验基于世界价值观调查，使用多个开源LLM，结果表明PSII显著提高了分布保真度和多样性。论文提供了代码和数据开源。


### [Chronos: Temporal-Aware Conversational Agents with Structured Event Retrieval for Long-Term Memory](https://arxiv.org/abs/2603.16862)

**作者**: Sen, Lumer, Gulati, Subbiah  
**链接**: [arXiv](https://arxiv.org/abs/2603.16862) · [PDF](https://arxiv.org/pdf/2603.16862)  \| [📖 全文分析](paper_2603.16862.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种新颖的时序感知记忆框架Chronos，用于解决大语言模型在长期对话中处理时间相关事实和偏好演变时的记忆检索难题。该方法通过将原始对话分解为带有时间范围和实体别名的结构化事件元组，并构建事件日历和对话轮次日历，显著提升了在时间敏感、多跳查询任务上的性能。在LongMemEvalS基准测试中，Chronos取得了显著的性能提升，超越了现有最佳系统。

