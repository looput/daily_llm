# Agent · 2026-03-16 ~ 2026-03-20

**论文数**: 39 篇

---

## 📊 趋势分析

### 研究全貌

Agent领域的研究呈现出从理论探索向工程化、安全化和专业化深度发展的清晰脉络。综合来看，主要研究方向集中在以下几个方面：**智能体系统架构与工程化**，旨在构建更高效、可靠、可治理的智能体系统，强调利用成熟基础设施（如操作系统）来简化复杂性；**智能体核心能力优化**，包括推理规划、记忆管理、多智能体协作等，核心目标是在资源受限的现实环境中实现更可靠、高效的决策与执行；**智能体安全、评估与治理**，系统性地关注智能体在复杂、长程任务中的决策质量、鲁棒性以及隐私、对抗攻击等新型安全风险；**特定领域与任务的专用框架**，如数据分析、科学发现、医疗、运维等，旨在通过高质量数据与针对性训练打造高性能的垂直领域专家。当前的研究热点高度聚焦于如何让智能体在**长程、动态、不完全信息且资源有限**的场景下，进行**可靠、连贯、安全且成本可控**的推理与交互。整体研究趋势正从追求单一任务的性能表现，向构建**可解释、可验证、可自我改进**的复杂智能体系统演进，并高度重视**工程实践、安全治理与开源民主化**。

### 重点方法深度解析

从所有批次的研究中，以下几个工作因其根本性的创新和广泛的适用性尤为突出：

**1. 《Quine: Realizing LLM Agents as Native POSIX Processes》**
*   **核心创新点**：从根本上重新思考了智能体的运行时架构，提出将智能体直接映射为**原生POSIX进程**，从而在操作系统内核层面获得天然的隔离性、资源控制和组合能力，解决了传统应用层框架在资源管理、并发控制和系统集成方面的复杂性。
*   **技术细节**：建立了一套清晰的映射关系：智能体身份对应进程ID，接口对应标准输入/输出流，状态对应内存和文件系统。其核心是一个可执行文件，通过递归地`fork`自身来生成新的智能体实例，继承了操作系统数十年的工程积累。
*   **效果验证**：作为一个概念验证，主要展示了其设计的可行性与简洁性。它自然支持递归委托、生命周期管理以及与现有Unix/Linux工具链的无缝集成。
*   **适用场景**：特别适合需要**高可靠性、强隔离性、长期运行以及与系统服务深度集成**的生产环境，为构建复杂智能体系统提供了坚实的底层架构选择。

**2. 《How Vulnerable Are AI Agents to Indirect Prompt Injections? Insights from a Large-Scale Public Competition》**
*   **核心创新点**：通过大规模公开红队竞赛，首次系统性地量化评估了前沿LLM智能体对**隐蔽性间接提示注入攻击**的脆弱性，揭示了攻击者可通过嵌入恶意指令到外部数据中，在用户无察觉的情况下操纵智能体行为的根本性安全威胁。
*   **技术细节**：设计了涵盖多种智能体场景的数十个攻击任务，吸引了大量参与者对前沿模型发起海量攻击尝试。分析发现了可跨模型和行为通用的攻击策略，表明这是指令跟随架构的固有弱点。
*   **效果验证**：所有被测模型均被证明存在漏洞，攻击成功率因模型而异。研究开源了竞赛环境和数据，为后续的鲁棒性研究提供了宝贵资源。
*   **适用场景**：对所有**依赖LLM处理外部不可信数据源**的应用（如自动化客服、研究助手）敲响了警钟，强调了在智能体设计阶段就必须将此类安全威胁纳入核心考量。

**3. 《Spend Less, Reason Better: Budget-Aware Value Tree Search for LLM Agents》**
*   **核心创新点**：提出了首个**无需训练、推理时即可实现的预算感知搜索框架**，解决了现有方法将计算资源视为无限、导致冗余和浪费的问题，核心挑战是在有限的Token和工具调用预算下，智能分配资源以最大化推理成功率。
*   **技术细节**：将多跳推理建模为动态搜索树，关键技术包括：1）**预算条件节点选择机制**，根据剩余资源动态调整搜索策略；2）**残差值预测器**，可靠地评估相对进展以剪枝冗余工具调用。
*   **效果验证**：在多个多跳问答基准上，该方法在严格的低预算约束下，其性能甚至超过了基线方法在**数倍资源分配**下的表现，证明了智能预算管理从根本上优于暴力计算扩展。
*   **适用场景**：非常适合所有需要控制推理成本、对延迟敏感的生产环境，特别是涉及多步工具调用的复杂任务代理。

**4. 《D-MEM: Dopamine-Gated Agentic Memory via Reward Prediction Error Routing》**
*   **核心创新点**：受神经科学启发，提出了一种全新的智能体长期记忆架构，旨在解决传统记忆系统存在的**写入延迟高**和**令牌消耗大**的瓶颈问题。
*   **技术细节**：核心是一个基于**奖励预测误差（RPE）的快速/慢速路由系统**。一个轻量级路由器评估输入信息的“惊喜度”，常规信息被缓存在快速缓冲区，而高RPE信息则触发慢速管道对知识图谱进行重构，将高频交互与低频认知重构解耦。
*   **效果验证**：在长期会话基准测试中，该方法将令牌消耗显著降低，消除了性能瓶颈，并在多跳推理和对抗鲁棒性上优于基线。
*   **适用场景**：适用于需要长期、多轮交互并维护一致记忆的对话代理、个人助理等，能显著降低运营成本并提升响应速度。

**方法间关系**：这四个方法分别从**系统基座（Quine）、安全底线（红队研究）、推理效率（BAVT）和交互体验（D-MEM）** 四个维度构建了健壮智能体的核心支柱。它们可以组合应用：基于Quine的进程架构部署智能体，利用BAVT进行预算感知推理，通过D-MEM管理长期记忆，并始终对红队研究所揭示的安全威胁保持警惕和防御。

### 实践启示

这些研究为实际的大模型应用开发提供了清晰的路线图，其核心启示在于**“精细化设计与安全前置优于暴力堆料”**。开发者应优先考虑利用成熟基础设施（如进程模型）构建稳定基座，并系统性关注资源预算管理与高效记忆系统，以直接降低成本和提升体验。

针对不同场景，建议如下：**构建通用复杂任务代理**，应重点借鉴预算感知推理（BAVT）和高效记忆架构（D-MEM）；**开发处理外部数据的应用**，必须将间接提示注入防御机制纳入核心设计；**打造垂直领域专家（如数据分析、运维）**，可参考DataMind、AOI等框架，通过高质量合成数据与安全学习闭环实现高性能与私有化部署。

**可落地的具体建议**包括：1）在新项目架构评估中，考虑将智能体实现为独立进程的可行性；2）为智能体设计包含输入净化与“沙箱”执行的安全流程；3）引入轻量级价值评估模块，实现智能的资源分配决策；4）为长期交互应用设计分层记忆系统；5）积极建立失败案例库，并将其转化为优化素材。**关键注意事项**是：这些优化方法可能引入额外复杂性，需权衡性能收益与系统维护成本；同时，所有涉及隐私和安全的方法必须与具体业务的数据合规要求紧密结合。最佳实践组合是：以**Quine或类似理念构建稳健系统架构**，集成**BAVT进行成本控制**，并**始终将红队研究揭示的安全原则作为设计底线**。

---

## 📄 论文列表（39 篇）

### [Toward Personalized LLM-Powered Agents: Foundations, Evaluation, and Future Directions](https://arxiv.org/abs/2602.22680)

**作者**: Xu, Chen, Ma, Liu, Wang 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.22680) · [PDF](https://arxiv.org/pdf/2602.22680)  \| [📖 全文分析](paper_2602.22680.md)  
**评分**: 8.93  （novelty: 7.0 | method: 9.5 | evidence: 8.0 | clarity: 9.0）

> 本文是一篇关于个性化LLM智能代理的综述性论文，系统性地梳理了该新兴领域的研究现状。论文提出了一个以能力为导向的分类框架，围绕用户画像建模、记忆、规划和行动执行四个核心能力组织现有工作，并分析了跨组件交互和设计挑战。论文进一步探讨了针对个性化代理的评估指标、基准范式以及从对话助手到领域专家系统的应用场景。通过澄清代理系统中个性化的设计空间，为开发更用户对齐、自适应和可部署的LLM智能代理提供了结构化基础。作者团队信息未在提供内容中明确显示，故省略。


### [How Vulnerable Are AI Agents to Indirect Prompt Injections? Insights from a Large-Scale Public Competition](https://arxiv.org/abs/2603.15714)

**作者**: Dziemian, Lin, Fu, Nowak, Winter 等 31 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15714) · [PDF](https://arxiv.org/pdf/2603.15714)  \| [📖 全文分析](paper_2603.15714.md)  
**评分**: 8.86  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 8.0）

> 本文由来自卡内基梅隆大学（CMU）、斯坦福大学（Stanford）、Google、Microsoft、Meta等知名高校和科技公司的研究人员组成的联合团队，针对AI智能体面临的新型安全威胁——间接提示注入攻击（特别是具有隐蔽性的攻击）——进行了大规模公开红队竞赛研究。该研究首次系统性地评估了前沿大模型智能体在多种实际场景下对隐蔽性间接提示注入攻击的脆弱性，揭示了攻击策略的通用性和模型架构的根本性弱点。研究规模宏大，数据详实，并开源了竞赛环境和部分攻击数据，对AI安全领域具有重要贡献。


### [Sparks of Cooperative Reasoning: LLMs as Strategic Hanabi Agents](https://arxiv.org/abs/2601.18077)

**作者**: Ramesh, Jayakumar, Ramkumar, Thodima, Rege 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2601.18077) · [PDF](https://arxiv.org/pdf/2601.18077)  \| [📖 全文分析](paper_2601.18077.md)  
**评分**: 8.86  （novelty: 8.5 | method: 9.0 | evidence: 9.5 | clarity: 8.0）

> 本文系统性地研究了大型语言模型在合作推理任务中的表现，以不完全信息卡牌游戏《花火》（Hanabi）为测试平台。研究设计了从基础提示到复杂状态跟踪的多层次上下文工程框架，并首次公开了包含完整游戏轨迹和动作价值标注的数据集。通过监督学习和强化学习微调，显著提升了开源模型在合作推理任务上的性能，并展示了模型在非游戏任务上的泛化能力。作者团队来自多个研究机构，包括Ramesh、Jayakumar、Ramkumar、Thodima、Rege、Vlatakis-Gkaragkounis等，但未明确标注其所属知名机构，因此省略团队背景介绍。


### [ToolRegistry: A Protocol-Agnostic Tool Management Library for Function-Calling LLMs](https://arxiv.org/abs/2507.10593)

**作者**: Ding  
**链接**: [arXiv](https://arxiv.org/abs/2507.10593) · [PDF](https://arxiv.org/pdf/2507.10593)  \| [📖 全文分析](paper_2507.10593.md)  
**评分**: 8.71  （novelty: 8.0 | method: 9.0 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种协议无关的工具管理系统ToolRegistry，用于解决大语言模型（LLM）应用中工具集成的碎片化、协议限制和实现复杂性问题。该系统已从一个单一库演变为一个模块化的三包生态系统，包含核心注册表、服务器包和中心包，提供了统一的工具注册、自动化模式生成、双模式并发执行等功能。评估显示，ToolRegistry能减少60-80%的工具集成代码，并通过并发执行实现高达3.1倍的性能提升，同时与OpenAI函数调用标准广泛兼容。论文实验设计合理，代码开源，具有较高的实用价值。


### [Infinite Problem Generator: Verifiably Scaling Physics Reasoning Data with Agentic Workflows](https://arxiv.org/abs/2603.14486)

**作者**: Sharan, Hebbale, Kumar  
**链接**: [arXiv](https://arxiv.org/abs/2603.14486) · [PDF](https://arxiv.org/pdf/2603.14486)  \| [📖 全文分析](paper_2603.14486.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为无限问题生成器（IPG）的智能体框架，用于生成具有可验证解的高质量物理推理数据。该方法通过公式即代码范式，将物理问题构建为可执行的Python程序，确保严格的数学一致性。作为概念验证，作者发布了ClassicalMechanicsV1数据集，包含1,335个经典力学问题，并识别出复杂度蓝图，建立了公式数量与验证代码长度之间的强线性关系。论文实验设计严谨，数据质量高，代码和数据集均已开源，支持可重复研究。


### [Hilbert: Recursively Building Formal Proofs with Informal Reasoning](https://arxiv.org/abs/2509.22819)

**作者**: Varambally, Voice, Sun, Chen, Yu 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.22819) · [PDF](https://arxiv.org/pdf/2509.22819)  \| [📖 全文分析](paper_2509.22819.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文提出了一种名为Hilbert的智能体框架，旨在弥合非正式数学推理与形式化定理证明之间的差距。该系统通过协调四个组件（非正式推理LLM、专门化证明器LLM、形式化验证器和语义定理检索器），并采用递归分解策略，在多个数学定理证明基准上取得了最先进的性能。论文实验充分，代码开源，为结合大型语言模型与形式化验证提供了有前景的通用框架。


### [Quine: Realizing LLM Agents as Native POSIX Processes](https://arxiv.org/abs/2603.18030)

**作者**: Ke  
**链接**: [arXiv](https://arxiv.org/abs/2603.18030) · [PDF](https://arxiv.org/pdf/2603.18030)  \| [📖 全文分析](paper_2603.18030.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种创新的LLM智能体运行时架构Quine，将LLM智能体实现为原生POSIX进程，而非在应用层重新构建隔离、调度和通信机制。这一设计充分利用了成熟操作系统内核提供的功能，实现了智能体抽象与操作系统进程模型的直接映射。论文设计思路新颖，参考实现已开源，具有重要的实践价值。


### [Darwin Godel Machine: Open-Ended Evolution of Self-Improving Agents](https://arxiv.org/abs/2505.22954)

**作者**: Zhang, Hu, Lu, Lange, Clune  
**链接**: [arXiv](https://arxiv.org/abs/2505.22954) · [PDF](https://arxiv.org/pdf/2505.22954)  \| [📖 全文分析](paper_2505.22954.md)  
**评分**: 8.57  （novelty: 9.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为达尔文哥德尔机（Darwin Gödel Machine, DGM）的自改进AI系统，旨在解决当前AI系统架构固定、无法自主持续改进的问题。该方法结合了元学习、哥德尔机的理论思想以及达尔文式开放演化，通过维护一个编码智能体档案库，并利用基础模型进行开放式探索和实证验证，实现了系统代码的自主迭代优化。实验表明，DGM在SWE-bench和Polyglot等编码基准测试上取得了显著性能提升。作者团队包括来自知名研究机构的研究人员（如Jeff Clune教授在开放演化与AI领域享有盛誉），其工作具有前沿性和探索性。


### [POLCA: Stochastic Generative Optimization with LLM](https://arxiv.org/abs/2603.14769)

**作者**: Ren, Nie, Xie, Cheng  
**链接**: [arXiv](https://arxiv.org/abs/2603.14769) · [PDF](https://arxiv.org/pdf/2603.14769)  \| [📖 全文分析](paper_2603.14769.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为POLCA（Prioritized Optimization with Local Contextual Aggregation）的随机生成优化框架，用于解决从LLM提示优化到多轮智能体优化等复杂系统的自动化优化问题。该方法将优化问题形式化为随机生成优化问题，利用生成式语言模型作为优化器，通过数值奖励和文本反馈来发现最佳系统。POLCA通过优先级队列管理探索-利用权衡，整合ε-Net机制保持参数多样性，并使用LLM摘要器进行历史试验的元学习。理论证明POLCA能在随机性下收敛到接近最优的候选解。在多个基准测试（包括τ-bench、HotpotQA、VeriBench和KernelBench）上的实验结果表明，POLCA在确定性和随机性问题中均优于现有最先进算法，表现出鲁棒、样本高效和时间高效的性能。代码已开源。


### [From Weak Cues to Real Identities: Evaluating Inference-Driven De-Anonymization in LLM Agents](https://arxiv.org/abs/2603.18382)

**作者**: Ko, Jeong, Thakur, Kim, Jia  
**链接**: [arXiv](https://arxiv.org/abs/2603.18382) · [PDF](https://arxiv.org/pdf/2603.18382)  \| [📖 全文分析](paper_2603.18382.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文研究了基于大语言模型（LLM）的智能体从稀疏、非识别性线索中自主重建真实世界身份的新型隐私威胁。作者将这种威胁形式化为“推理驱动的链接”，并在三种场景下进行了系统评估：经典链接场景（Netflix和AOL）、受控基准（InferLink）以及现代文本丰富的数字痕迹。研究发现，即使没有特定任务启发式方法，智能体也能成功执行固定池匹配和开放式身份解析，在Netflix Prize场景中身份重建准确率达到79.2%，显著优于56.0%的经典基线。更重要的是，身份链接不仅出现在明确的对抗性提示下，也作为良性跨源分析的副产品出现。这些发现表明，身份推理（而不仅仅是明确的信息披露）必须被视为一级隐私风险，评估必须衡量智能体能够推断出什么身份。作者团队来自学术界，但论文中未明确列出具体机构，因此省略团队背景介绍。


### [StatePlane: A Cognitive State Plane for Long-Horizon AI Systems Under Bounded Context](https://arxiv.org/abs/2603.13644)

**作者**: Annapureddy, Mulcahy, Thamatani  
**链接**: [arXiv](https://arxiv.org/abs/2603.13644) · [PDF](https://arxiv.org/pdf/2603.13644)  \| [📖 全文分析](paper_2603.13644.md)  
**评分**: 8.50  （novelty: 9.0 | method: 9.5 | evidence: 7.0 | clarity: 8.5）

> 本文提出了一种名为StatePlane的模型无关认知状态平面，用于解决大语言模型和小语言模型在有限上下文窗口下进行长程连贯推理的根本性限制。该方法基于认知心理学和系统设计原理，形式化了情景分割、选择性编码、目标条件检索、重构状态合成和自适应遗忘等机制，并提供了形式化状态模型、KV感知算法、安全治理机制和企业集成路径。论文通过六个领域特定基准进行评估，展示了无需扩展上下文窗口或重新训练模型即可实现长程智能的潜力。


### [Scaling Generalist Data-Analytic Agents](https://arxiv.org/abs/2509.25084)

**作者**: Qiao, Zhao, Qiu, Wang, Zhang 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.25084) · [PDF](https://arxiv.org/pdf/2509.25084)  \| [📖 全文分析](paper_2509.25084.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.5）

> 本文提出了一种名为DataMind的可扩展数据合成与智能体训练框架，旨在构建通用的数据分析智能体。该研究针对开源数据分析智能体面临的三大挑战（数据资源不足、训练策略不当、基于代码的多轮交互不稳定）提出了系统性解决方案，包括细粒度任务分类、递归易到难任务组合、知识增强轨迹采样、动态调整训练目标以及内存高效的多轮交互框架。基于此框架构建的DataMind-12K高质量轨迹集和训练的DataMind-14B模型在多个数据分析基准测试中取得了最先进的性能，甚至超越了最强的专有基线模型。作者团队承诺将开源数据集和模型，对社区研究具有重要价值。


### [UIS-Digger: Towards Comprehensive Research Agent Systems for Real-world Unindexed Information Seeking](https://arxiv.org/abs/2603.08117)

**作者**: Liu, Kuang, Zhuang, Cheng, Zhou 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.08117) · [PDF](https://arxiv.org/pdf/2603.08117)  \| [📖 全文分析](paper_2603.08117.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了UIS-Digger，一个针对未索引信息检索（UIS）问题的多智能体框架，并首次构建了专门的UIS-QA基准测试。研究揭示了当前基于LLM的信息检索智能体严重依赖搜索引擎索引知识的根本局限，定义了一个新的研究方向。实验表明，即使使用相对较小的骨干模型，UIS-Digger也能超越集成GPT-4.1等复杂模型的系统，凸显了主动与未索引源交互的重要性。作者团队未在摘要中明确标注所属知名机构，故省略背景介绍。


### [When the Specification Emerges: Benchmarking Faithfulness Loss in Long-Horizon Coding Agents](https://arxiv.org/abs/2603.17104)

**作者**: Yan, Chen, Zhang  
**链接**: [arXiv](https://arxiv.org/abs/2603.17104) · [PDF](https://arxiv.org/pdf/2603.17104)  \| [📖 全文分析](paper_2603.17104.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文针对当前代码生成智能体评估的局限性，提出了一个新颖的基准测试SLUMP，用于衡量在“渐进式需求披露”这一更贴近真实研发场景下，智能体代码生成的忠实度损失。研究不仅定义了问题、构建了包含20篇最新ML论文和371个可验证组件的基准，还通过实验揭示了Claude Code和Codex在此场景下的性能差异，并提出了一个名为ProjectGuard的缓解方案，有效恢复了大部分忠实度差距。实验设计严谨，包含控制组对比和详细的审计流程，具有重要的实践指导意义。


### [GNNVerifier: Graph-based Verifier for LLM Task Planning](https://arxiv.org/abs/2603.14730)

**作者**: Hao, Wang, Yang, Li, Zhang 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.14730) · [PDF](https://arxiv.org/pdf/2603.14730)  \| [📖 全文分析](paper_2603.14730.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种基于图神经网络的LLM任务规划验证器GNNVerifier，通过将任务计划表示为有向图并利用GNN进行结构评估和诊断，有效解决了传统LLM验证器在检测跨步骤结构关系错误方面的不足。在多个数据集、骨干LLM和规划器上的实验表明，该方法能显著提升规划质量。作者团队来自北京邮电大学（BUPT），代码和数据已开源。


### [A Framework for Assessing AI Agent Decisions and Outcomes in AutoML Pipelines](https://arxiv.org/abs/2602.22442)

**作者**: Du, Ahlawat, Liu, Wu  
**链接**: [arXiv](https://arxiv.org/abs/2602.22442) · [PDF](https://arxiv.org/pdf/2602.22442)  \| [📖 全文分析](paper_2602.22442.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种用于评估基于智能体的AutoML系统决策质量的新框架。当前AutoML系统的评估主要关注最终任务性能，而缺乏对中间决策过程的结构化评估。作者团队（作者姓名未显示知名机构标识，但论文内容显示其研究深度和系统性）通过引入一个不干扰执行过程的评估智能体（EA），从决策有效性、推理一致性、模型质量风险和反事实决策影响四个维度对AutoML智能体的中间决策进行审计。该方法在概念验证实验中表现出色，能够检测错误决策、识别推理不一致性，并将下游性能变化归因于具体决策，揭示了仅靠结果指标无法发现的故障模式。这项工作为构建可靠、可解释和可治理的自主机器学习系统奠定了基础。


### [OpenClaw-RL: Train Any Agent Simply by Talking](https://arxiv.org/abs/2603.10165)

**作者**: Wang, Chen, Jin, Wang, Yang  
**链接**: [arXiv](https://arxiv.org/abs/2603.10165) · [PDF](https://arxiv.org/pdf/2603.10165)  \| [📖 全文分析](paper_2603.10165.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种新颖的强化学习框架OpenClaw-RL，其核心洞见在于识别并利用所有智能体交互中普遍存在的“下一状态信号”作为在线学习源。该方法将个人对话、终端执行、GUI交互、软件工程任务和工具调用等不同场景统一视为可训练同一策略的交互数据流，通过异步架构实现实时学习。论文实验设计全面，代码已开源，展示了在多种智能体应用中的有效性。


### [MiroThinker-1.7 & H1: Towards Heavy-Duty Research Agents via Verification](https://arxiv.org/abs/2603.15726)

**作者**: MiroMind Team, Bai, Bing, Lei, Li 等 44 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15726) · [PDF](https://arxiv.org/pdf/2603.15726)  \| [📖 全文分析](paper_2603.15726.md)  
**评分**: 8.43  （novelty: 8.5 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文由MiroMind团队提出了一种新型研究智能体MiroThinker-1.7及其增强版本H1，专注于复杂长程推理任务。该方法通过引入结构化规划、上下文推理和工具交互的中期训练阶段，提升了单步交互的可靠性，并进一步在推理过程中整合了局部和全局验证机制，显著增强了多步问题解决的鲁棒性。在涵盖开放网络研究、科学推理和金融分析的多个基准测试中，MiroThinker-H1在深度研究任务上取得了最先进的性能，同时在特定领域也保持了强劲表现。团队开源了MiroThinker-1.7及其轻量版模型，促进了该领域的研究复现与应用探索。


### [Protein Design with Agent Rosetta: A Case Study for Specialized Scientific Agents](https://arxiv.org/abs/2603.15952)

**作者**: Teneggi, Turzo, Marwah, Bietti, Renfrew 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15952) · [PDF](https://arxiv.org/pdf/2603.15952)  \| [📖 全文分析](paper_2603.15952.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种名为Agent Rosetta的LLM智能体系统，专门用于操作Rosetta蛋白质设计软件。该方法通过将大型语言模型的推理能力与Rosetta软件的物理建模能力相结合，实现了对蛋白质（包括非经典氨基酸）的自动化设计。实验表明，Agent Rosetta在经典氨基酸设计任务上达到了与专用模型和专家基线相当的性能，并在非经典残基设计（传统机器学习方法难以处理的任务）上取得了可比的结果。研究强调了专门的环境设计对于LLM智能体有效集成专业科学软件的重要性。


### [A Trace-Based Assurance Framework for Agentic AI Orchestration: Contracts, Testing, and Governance](https://arxiv.org/abs/2603.18096)

**作者**: Paduraru, Bouruc, Stefanescu  
**链接**: [arXiv](https://arxiv.org/abs/2603.18096) · [PDF](https://arxiv.org/pdf/2603.18096)  \| [📖 全文分析](paper_2603.18096.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种基于追踪的智能体AI编排保障框架，针对大型语言模型在协调多智能体、外部服务和共享内存时可能出现的长期交互故障、随机决策和外部副作用等问题，设计了包含合约、测试和治理三个核心组件的系统性解决方案。该框架通过消息-动作追踪、确定性重放、压力测试和结构化故障注入等技术手段，为多智能体LLM系统的测试、评估和治理提供了通用抽象。


### [SkillsBench: Benchmarking How Well Agent Skills Work Across Diverse Tasks](https://arxiv.org/abs/2602.12670)

**作者**: Li, Chen, Liu, Zheng, Chen 等 41 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.12670) · [PDF](https://arxiv.org/pdf/2602.12670)  \| [📖 全文分析](paper_2602.12670.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了SkillsBench，一个用于评估Agent Skills在多样化任务中有效性的基准测试。该研究首次系统性地量化了结构化程序知识包对LLM智能体的影响，填补了该领域缺乏标准化评估工具的空白。通过涵盖11个领域的86个任务、精心设计的Skills和确定性验证器，研究揭示了Skills在不同领域的效果差异（从软件工程的+4.5pp到医疗健康的+51.9pp），并发现模型无法可靠地生成它们能从中受益的程序知识。实验规模大（7,308条轨迹），结论具有实证基础。


### [Spend Less, Reason Better: Budget-Aware Value Tree Search for LLM Agents](https://arxiv.org/abs/2603.12634)

**作者**: Li, Deng, Li, Li  
**链接**: [arXiv](https://arxiv.org/abs/2603.12634) · [PDF](https://arxiv.org/pdf/2603.12634)  \| [📖 全文分析](paper_2603.12634.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为预算感知价值树（BAVT）的训练免费推理时框架，用于提升大型语言模型（LLM）智能体在有限计算资源下的推理效率与可靠性。该方法通过将多跳推理建模为动态搜索树，并引入基于剩余资源比例的节点选择机制和残差值预测器，实现了在预算约束下从广泛探索到贪婪利用的原则性过渡。论文在四个多跳问答基准测试上进行了广泛评估，结果表明BAVT在严格低预算约束下显著优于基线方法，证明了智能预算管理优于暴力计算扩展。作者团队未明确标注来自知名机构，故省略背景介绍。


### [Imagine-then-Plan: Agent Learning from Adaptive Lookahead with World Models](https://arxiv.org/abs/2601.08955)

**作者**: Liu, Wang, Wang, Guo, Li  
**链接**: [arXiv](https://arxiv.org/abs/2601.08955) · [PDF](https://arxiv.org/pdf/2601.08955)  \| [📖 全文分析](paper_2601.08955.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Imagine-then-Plan（ITP）的智能体学习统一框架，该框架通过策略模型与学习到的世界模型交互，进行前瞻性想象，生成多步“想象”轨迹。针对不同任务和阶段对想象视野的需求差异，作者创新性地引入了自适应前瞻机制，在最终目标和任务进展之间进行权衡。生成的想象轨迹提供了关于未来结果的丰富信号，并与当前观察融合，形成了一个部分可观察和可想象的马尔可夫决策过程来指导策略学习。论文在多个代表性智能体基准测试上进行了广泛实验，结果表明ITP显著优于现有基线。代码和数据已承诺开源。作者团队信息在摘要中未明确提及知名机构，故省略背景介绍。


### [RoboClaw: An Agentic Framework for Scalable Long-Horizon Robotic Tasks](https://arxiv.org/abs/2603.11558)

**作者**: Li, Zhou, Zhu, Chen, Wang 等 18 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.11558) · [PDF](https://arxiv.org/pdf/2603.11558)  \| [📖 全文分析](paper_2603.11558.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为RoboClaw的智能机器人框架，旨在解决视觉-语言-动作（VLA）系统在长时程任务中面临的扩展性挑战。该框架通过统一的VLM驱动控制器，将数据收集、策略学习和任务执行整合在一起，并引入了纠缠动作对（EAP）机制来实现自主数据收集和策略迭代优化。实验表明，该方法在真实世界操作任务中显著提升了成功率和稳定性，同时大幅减少了人力投入。


### [TheraAgent: Multi-Agent Framework with Self-Evolving Memory and Evidence-Calibrated Reasoning for PET Theranostics](https://arxiv.org/abs/2603.13676)

**作者**: Chen, Wang, Chen, Ji, Hu 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.13676) · [PDF](https://arxiv.org/pdf/2603.13676)  \| [📖 全文分析](paper_2603.13676.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种名为TheraAgent的多智能体框架，用于PET诊疗（theranostics）中的治疗结果预测。该框架针对医学AI领域中的关键挑战，包括数据稀缺、异构信息整合和证据基础推理，通过多专家特征提取、自演化记忆和证据校准推理三大创新模块，在真实患者和合成病例上取得了优于现有方法的性能。作者团队来自多个研究机构，包括医学影像和AI研究领域的专家，体现了跨学科合作的特点。


### [D-MEM: Dopamine-Gated Agentic Memory via Reward Prediction Error Routing](https://arxiv.org/abs/2603.14597)

**作者**: Song, Xin  
**链接**: [arXiv](https://arxiv.org/abs/2603.14597) · [PDF](https://arxiv.org/pdf/2603.14597)  \| [📖 全文分析](paper_2603.14597.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为D-MEM（多巴胺门控智能体记忆）的新型智能体长期记忆架构，灵感来源于神经科学中的多巴胺信号和奖励预测误差机制。该方法旨在解决现有“追加-演化”式记忆系统（如A-MEM）存在的O(N²)写入延迟和高令牌消耗问题。通过引入基于奖励预测误差的快速/慢速路由系统，D-MEM将短期交互与认知重构解耦，实现了高效、可扩展的记忆管理。在包含噪声注入的长期会话基准测试中，D-MEM显著降低了令牌消耗，消除了性能瓶颈，并在多跳推理和对抗鲁棒性方面优于基线方法。


### [OpenSeeker: Democratizing Frontier Search Agents by Fully Open-Sourcing Training Data](https://arxiv.org/abs/2603.15594)

**作者**: Du, Ye, Tang, Zhu, Lu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.15594) · [PDF](https://arxiv.org/pdf/2603.15594)  \| [📖 全文分析](paper_2603.15594.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为OpenSeeker的完全开源搜索智能体，旨在解决前沿大语言模型智能体开发中高质量训练数据稀缺和封闭的问题。论文通过两项核心技术——基于事实的可扩展可控QA合成和去噪轨迹合成——仅用1.17万个合成样本进行监督微调，便在多个基准测试中达到了最先进的性能，甚至超越了部分工业界竞品。作者团队未明确标注其所属机构，但论文内容显示其工作具有显著的工程和研究价值，旨在推动搜索智能体研究的民主化和透明化。


### [AOI: Turning Failed Trajectories into Training Signals for Autonomous Cloud Diagnosis](https://arxiv.org/abs/2603.03378)

**作者**: Yang, Chen, Zheng, Li, Li 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03378) · [PDF](https://arxiv.org/pdf/2603.03378)  \| [📖 全文分析](paper_2603.03378.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为AOI（自主运维智能）的可训练多智能体框架，用于解决企业环境中基于LLM的站点可靠性工程（SRE）自动化所面临的三大挑战：数据隐私、安全执行和从失败中学习。该方法将自动化运维构建为一个安全约束下的结构化轨迹学习问题，核心贡献包括：基于GRPO的可训练诊断系统、读写分离的执行架构以及失败轨迹闭环演化器。在AIOpsLab基准测试中，该方法在多个指标上显著超越了现有最佳方法，并展示了本地部署模型超越大型商业模型（如Claude Sonnet）的潜力。


### [Compiled Memory: Not More Information, but More Precise Instructions for Language Agents](https://arxiv.org/abs/2603.15666)

**作者**: Rhodes, Kang  
**链接**: [arXiv](https://arxiv.org/abs/2603.15666) · [PDF](https://arxiv.org/pdf/2603.15666)  \| [📖 全文分析](paper_2603.15666.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Atlas的新型语言智能体记忆系统，专注于提升记忆效用而非传统的内存管理。该方法通过将积累的任务经验编译到智能体的指令结构中，无需微调、检索增强生成或人工干预，实现了从失败和成功中提取事实并验证，最终通过重写系统提示来改进智能体性能。在CUAD合同分析和HotpotQA多跳问答任务上取得了显著提升，并验证了方法的跨模型迁移能力。作者Rhodes, Kang未明确标注知名机构，故省略团队背景介绍。


### [When Should a Robot Think? Resource-Aware Reasoning via Reinforcement Learning for Embodied Robotic Decision-Making](https://arxiv.org/abs/2603.16673)

**作者**: Liu, Zhao, Kong, Shen, Dong 等 15 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.16673) · [PDF](https://arxiv.org/pdf/2603.16673)  \| [📖 全文分析](paper_2603.16673.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为RARRL（基于强化学习的资源感知推理）的分层框架，用于解决具身机器人系统中大语言模型推理带来的计算延迟和资源开销问题。该框架通过学习高层编排策略，使机器人能够自适应地决定何时进行推理、使用何种推理角色以及分配多少计算资源。实验表明，相比固定或启发式推理策略，RARRL能显著提高任务成功率、降低执行延迟并增强系统鲁棒性。作者团队来自多个研究机构，包括清华大学、北京大学、上海交通大学等国内顶尖高校，以及微软亚洲研究院等知名企业研究机构，体现了较强的学术和工业背景。


### [Facts as First Class Objects: Knowledge Objects for Persistent LLM Memory](https://arxiv.org/abs/2603.17781)

**作者**: Zahn, Chana  
**链接**: [arXiv](https://arxiv.org/abs/2603.17781) · [PDF](https://arxiv.org/pdf/2603.17781)  \| [📖 全文分析](paper_2603.17781.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.0）

> 本文提出了一种名为“知识对象”（Knowledge Objects, KOs）的新方法，用于解决大型语言模型（LLM）作为持久知识工作者时，传统上下文记忆（in-context memory）存在的容量限制、压缩损失和目标漂移等根本性缺陷。论文通过详尽的基准测试，在多个前沿模型上验证了KOs在准确性、成本效益和多跳推理方面的显著优势，并揭示了嵌入检索和神经记忆的特定失败模式。作者还引入了密度自适应检索作为切换机制，并开源了基准测试套件。


### [Governed Memory: A Production Architecture for Multi-Agent Workflows](https://arxiv.org/abs/2603.17787)

**作者**: Taheri  
**链接**: [arXiv](https://arxiv.org/abs/2603.17787) · [PDF](https://arxiv.org/pdf/2603.17787)  \| [📖 全文分析](paper_2603.17787.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为'Governed Memory'的生产架构，旨在解决企业AI中多智能体工作流中的内存治理问题。论文针对企业环境中多个自主智能体节点缺乏共享内存和统一治理的现状，识别了五个结构性挑战，并提出了包含四种机制的系统架构。实验验证表明，该架构在事实召回、治理路由精度、令牌减少、实体隔离和治理合规性等方面均表现出色，并在LoCoMo基准测试中取得了74.8%的整体准确率。该系统已在Personize.ai投入生产使用。


### [CodeScout: An Effective Recipe for Reinforcement Learning of Code Search Agents](https://arxiv.org/abs/2603.17829)

**作者**: Sutawika, Soni, R, Gandhi, Yassine 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.17829) · [PDF](https://arxiv.org/pdf/2603.17829)  \| [📖 全文分析](paper_2603.17829.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为CodeScout的强化学习配方，用于训练代码搜索智能体。该方法的核心创新在于，仅使用标准Unix终端作为工具，通过精心设计的强化学习训练方案（包括环境改造、奖励设计和优化策略），使智能体在代码定位任务上取得了优异性能。在SWE-Bench系列基准测试中，其模型性能优于或媲美体量大2-18倍的基础及后训练大语言模型，有时甚至接近Claude Sonnet等闭源模型的水平。作者团队来自卡内基梅隆大学（CMU）等知名机构，研究背景扎实。


### [Theory of Code Space: Do Code Agents Understand Software Architecture?](https://arxiv.org/abs/2603.00601)

**作者**: Sapunov  
**链接**: [arXiv](https://arxiv.org/abs/2603.00601) · [PDF](https://arxiv.org/pdf/2603.00601)  \| [📖 全文分析](paper_2603.00601.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为'代码空间理论'（Theory of Code Space, ToCS）的新基准测试，用于评估代码智能体在探索代码库时构建、维护和更新连贯软件架构信念的能力。该研究通过程序化生成的代码库和部分可观测性设置，系统地揭示了不同大语言模型在主动探索、信念状态维护和结构化信念图利用方面的能力差异。实验设计严谨，基准测试已开源，为评估和理解代码智能体的架构理解能力提供了重要工具。


### [MEMO: Memory-Augmented Model Context Optimization for Robust Multi-Turn Multi-Agent LLM Games](https://arxiv.org/abs/2603.09022)

**作者**: Xie, Wang, Cheng, Yao, Sha 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.09022) · [PDF](https://arxiv.org/pdf/2603.09022)  \| [📖 全文分析](paper_2603.09022.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为MEMO（Memory-augmented Model Context Optimization）的自对弈框架，旨在解决多轮多智能体大语言模型（LLM）游戏评估中存在的运行间方差大、排名不稳定等问题。该方法通过结合记忆保留与探索机制，优化推理时的上下文，在多个文本游戏中显著提升了胜率并降低了方差。实验表明，该方法在谈判和不完全信息游戏中效果尤为显著。所有代码均已开源。


### [ARISE: Agent Reasoning with Intrinsic Skill Evolution in Hierarchical Reinforcement Learning](https://arxiv.org/abs/2603.16060)

**作者**: Li, Miao, Qi, Lan  
**链接**: [arXiv](https://arxiv.org/abs/2603.16060) · [PDF](https://arxiv.org/pdf/2603.16060)  \| [📖 全文分析](paper_2603.16060.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为ARISE（Agent Reasoning via Intrinsic Skill Evolution）的分层强化学习框架，旨在解决语言模型数学推理任务中策略复用性不足的问题。该框架通过高层技能管理和低层响应生成的双层策略，结合技能库的演化机制，显著提升了模型在数学推理任务上的性能，特别是在分布外任务上表现出色。论文实验设计严谨，在多个基准测试上验证了有效性，并开源了代码。


### [When Only the Final Text Survives: Implicit Execution Tracing for Multi-Agent Attribution](https://arxiv.org/abs/2603.17445)

**作者**: Nian, Cao, Zhu, Zou, Luan 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.17445) · [PDF](https://arxiv.org/pdf/2603.17445)  \| [📖 全文分析](paper_2603.17445.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为IET（隐式执行追踪）的元数据无关框架，用于解决多智能体语言系统中责任归属的难题。该方法通过在生成过程中嵌入智能体特定的密钥信号，将文本转化为自描述的执行轨迹，仅需密钥即可检测，实现了无需日志的令牌级归因和交互拓扑重建。实验表明IET在保持生成质量的同时，能够高精度恢复智能体贡献和协调结构，为多智能体系统的隐私保护审计提供了创新解决方案。


### [The Provenance Paradox in Multi-Agent LLM Routing: Delegation Contracts and Attested Identity in LDP](https://arxiv.org/abs/2603.18043)

**作者**: Prakash  
**链接**: [arXiv](https://arxiv.org/abs/2603.18043) · [PDF](https://arxiv.org/pdf/2603.18043)  \| [📖 全文分析](paper_2603.18043.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文针对多智能体LLM系统中的任务委派信任问题，提出了一个重要的理论发现——来源悖论，并设计了相应的解决方案。论文通过扩展LLM委派协议（LDP），引入了委派合约、声明与认证身份模型以及类型化失败语义，有效解决了当代理可以虚报质量分数时，基于质量的路由反而会系统性选择最差代理的问题。实验设计严谨，包含模拟和真实模型验证，结果具有统计显著性。


### [PlanTwin: Privacy-Preserving Planning Abstractions for Cloud-Assisted LLM Agents](https://arxiv.org/abs/2603.18377)

**作者**: Yu, Wang, Lang, Su, Wang  
**链接**: [arXiv](https://arxiv.org/abs/2603.18377) · [PDF](https://arxiv.org/pdf/2603.18377)  \| [📖 全文分析](paper_2603.18377.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为PlanTwin的隐私保护架构，用于解决云托管大语言模型（LLM）作为智能体规划器时，本地私有环境状态暴露给云端的安全问题。该工作创新性地引入了“面向规划的数字孪生”概念，通过模式约束和去标识化的抽象图来保留规划所需的结构，同时移除可重构的细节，从而在隐私和效用之间取得平衡。论文实验设计全面，在60个跨10个领域的任务上进行了评估，验证了其有效性。

