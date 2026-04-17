# Agent · 2026年04月14日

**论文数**: 17 篇

---

## 📊 趋势分析

### 研究全貌

本批次的Agent领域论文呈现出鲜明的“工程化”与“精细化”趋势，研究焦点从构建基础的多智能体系统转向解决其在实际应用中暴露出的效率、可靠性与协调性问题。主要研究方向可归纳为以下几类：**智能体系统效率与成本分析**，探讨在同等计算预算下，单智能体与多智能体架构的性能对比与理论依据；**智能体协作与协调协议**，旨在为独立主体拥有的智能体提供结构化、可验证的协作框架；**智能体能力优化与训练方法**，包括工具调用、检索增强生成、持续学习等能力的系统性提升；以及**面向特定复杂任务（如科学发现、代码分析、图表生成）的专用智能体框架**。当前的热点问题集中体现在如何平衡智能体系统的计算开销、上下文利用与协调成本，以及如何通过细粒度的过程监督与验证机制来提升其决策的可靠性与可解释性。整体研究趋势表明，该领域正从追求架构的复杂性转向追求方法的实用性与可控性，强调在真实场景下的性能、效率与稳定性。

### 重点方法深度解析

在这些论文中，以下几项工作因其深刻的洞察力、严谨的方法论和显著的实用价值而尤为突出：

**1. 《Single-Agent LLMs Outperform Multi-Agent Systems on Multi-Hop Reasoning Under Equal Thinking Token Budgets》 [URL](https://arxiv.org/abs/2604.02460)**
*   **核心创新点**：该研究挑战了“多智能体系统（MAS）必然优于单智能体系统（SAS）”的流行观点。它通过信息论（数据处理不等式）论证，在固定的推理token预算和完美的上下文利用下，SAS更具信息效率。其核心是提供了一个受控的、公平的比较框架，将性能差异归因于计算资源与上下文利用效率，而非架构本身。
*   **技术细节**：作者设计了一个严格的实证研究，在三个模型系列（Qwen3, DeepSeek-R1, Gemini 2.5）上，将SAS与多种MAS架构在完全相同的推理token预算下进行比较。研究揭示了现有评估中的潜在偏差，例如API预算控制的不精确和基准测试中未考虑的上下文效应，这些都可能夸大MAS的优势。
*   **效果验证**：在多跳推理任务上，当推理token预算相同时，SAS始终匹配或优于MAS。例如，在HotpotQA等任务中，SAS在相同预算下实现了更高的准确率。
*   **适用场景**：这项研究为资源受限场景下的智能体架构选型提供了关键指导。它表明，对于许多多跳推理任务，与其盲目增加智能体数量，不如优先优化单个智能体的上下文利用能力和推理效率。

**2. 《MPAC: A Multi-Principal Agent Coordination Protocol for Interoperable Multi-Agent Collaboration》 [URL](https://arxiv.org/abs/2604.09744)**
*   **核心创新点**：针对现有协议（如MCP、A2A）仅适用于单一控制主体的局限，MPAC填补了独立主体（如不同个人、组织）拥有的智能体之间协调共享状态的空白。
*   **技术细节**：MPAC是一个包含五层明确语义的应用层协议：会话、意图、操作、冲突和治理。其关键技术包括：将意图声明作为行动的前提条件；将冲突定义为可操作的一等结构化对象；支持可插拔的治理层进行人工仲裁。协议规范了21种消息类型、状态机、Lamport时钟因果水印以及共享状态上的乐观并发控制。
*   **效果验证**：在一个受控的三智能体代码审查基准测试中，与串行化的人工协调基线相比，MPAC将协调开销降低了95%，并将实际执行速度提升了4.8倍，且不压缩模型调用时间。
*   **适用场景**：非常适合需要跨组织、跨团队协作的场景，例如不同工程师的编码代理共同编辑同一代码库、家庭成员共同规划行程，或企业间代理进行联合决策谈判。其实用性体现在提供了完整的开源规范、两种语言的参考实现和详尽的测试套件。

**3. 《HiL-Bench (Human-in-Loop Benchmark): Do Agents Know When to Ask for Help?》 [URL](https://arxiv.org/abs/2604.09408)**
*   **核心创新点**：该研究敏锐地指出，当前前沿智能体的瓶颈往往不是原始能力，而是“判断力”——即知道何时应自主行动，何时应向人类求助。它创建了一个专门评估这种“选择性升级”能力的新型基准。
*   **技术细节**：HiL-Bench的核心是Ask-F1指标，它是问题精确率和阻塞点召回率的调和平均数，旨在平衡“过度求助”和“沉默猜测”。任务中嵌入了经过人工验证的“阻塞点”（如缺失信息、模糊请求），这些阻塞点只有在逐步探索中才会显现，无法预先检查。
*   **效果验证**：评估揭示了所有前沿模型都存在巨大的“判断鸿沟”：当需要自行决定是否求助时，没有模型能恢复其拥有完整信息时的一小部分性能。更重要的是，研究证明通过基于Ask-F1指标的强化学习训练，模型的求助判断能力是可训练的，且这种能力可以跨领域迁移。
*   **适用场景**：对于任何部署在生产环境、需要与人类协作或处理模糊需求的智能体系统（如编程助手、客服代理）都至关重要。它提供了一种量化评估和提升智能体“自知之明”与协作意识的方法。

**4. 《UniToolCall: Unifying Tool-Use Representation, Data, and Evaluation for LLM Agents》 [URL](https://arxiv.org/abs/2604.11557)**
*   **核心创新点**：针对工具调用领域存在的交互表示不一致、数据分布结构被忽视以及评估基准不兼容这三大问题，提出了一个端到端的统一框架。
*   **技术细节**：框架构建了包含超过22,000个工具的大规模工具池，并通过结合10个标准化公共数据集与结构可控的合成轨迹，构建了包含390,000+实例的混合训练语料。它明确建模了单跳/多跳、单轮/多轮等交互模式，并引入了“锚点链接”机制来强制执行跨轮依赖。此外，它将7个公共基准统一为“查询-行动-观察-答案”（QAOA）表示，支持函数调用、轮次和对话级别的细粒度评估。
*   **效果验证**：在Qwen3-8B模型上使用其数据集进行微调，在干扰项众多的Hybrid-20设置下，实现了93.0%的单轮严格精确率，超越了包括GPT、Gemini和Claude在内的商业模型。
*   **适用场景**：适用于任何需要复杂工具链调用和多轮交互的任务，如自动化工作流、复杂信息查询等。其统一的数据和评估标准为工具学习领域的标准化和可复现性做出了重要贡献。

### 实践启示

这批研究为构建实用、可靠的大模型智能体系统提供了清晰的路线图。首要启示是**避免盲目追求多智能体架构的复杂性**，应优先评估单智能体在优化上下文利用后的性能，这往往是成本效益更高的选择。其次，在需要跨主体协作的场景下，**采用结构化的协调协议（如MPAC）** 远比依赖非结构化的聊天协调更高效、更可控。对于关键任务应用，**引入细粒度的过程奖励（如HiPRAG）或执行验证（如Verify Before You Fix）是提升可靠性的关键。对于工具调用等核心能力，**采用统一的数据、表示和评估框架（如UniToolCall）** 是提升性能和泛化能力的有效途径。在实现时，需特别注意**公平的性能比较**（控制计算预算）、**评估基准的潜在偏差**，以及**规则设计应以负面约束为主**（禁止做什么），而非正面指令（应该做什么），后者可能适得其反。

---

## 📄 论文列表（17 篇）

### [Single-Agent LLMs Outperform Multi-Agent Systems on Multi-Hop Reasoning Under Equal Thinking Token Budgets](https://arxiv.org/abs/2604.02460)

**作者**: Tran, Kiela  
**链接**: [arXiv](https://arxiv.org/abs/2604.02460) · [PDF](https://arxiv.org/pdf/2604.02460)  \| [📖 全文分析](paper_2604.02460.md)  
**评分**: 8.81  （novelty: 9.0 | method: 9.5 | evidence: 9.0 | clarity: 8.5）

> 本文对多智能体大语言模型系统（MAS）与单智能体系统（SAS）在多跳推理任务上的性能进行了深入的比较研究。作者通过信息论（数据处理不等式）提出了一个理论观点，认为在固定的推理token预算和完美的上下文利用下，单智能体系统更具信息效率。研究通过一个受控的实证研究，在三个模型系列（Qwen3、DeepSeek-R1-Distill-Llama和Gemini 2.5）上验证了这一预测，发现当推理token预算相同时，SAS在多跳推理任务上始终匹配或优于MAS。论文还揭示了现有评估方法（如API预算控制和标准基准测试）中可能夸大MAS优势的潜在问题。


### [MPAC: A Multi-Principal Agent Coordination Protocol for Interoperable Multi-Agent Collaboration](https://arxiv.org/abs/2604.09744)

**作者**: Qian, Fang, Li  
**链接**: [arXiv](https://arxiv.org/abs/2604.09744) · [PDF](https://arxiv.org/pdf/2604.09744)  \| [📖 全文分析](paper_2604.09744.md)  
**评分**: 8.71  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 7.5）

> 本文提出了一种名为MPAC（多主体代理协调协议）的新型应用层协议，旨在解决独立主体拥有的多个AI代理在需要协调共享状态时的协作问题。论文针对现有协议（如MCP和A2A）在单主体假设下的局限性，设计了一个包含会话、意图、操作、冲突和治理五层明确语义的协调框架。作者团队未在摘要中明确提及所属知名机构，因此省略背景介绍。该工作创新性地将意图声明作为行动前提，将冲突作为一等结构化对象处理，并支持可插拔的治理层进行人工仲裁。论文提供了完整的协议规范、两种参考实现、详尽的测试和演示，并通过受控基准测试展示了显著的协调开销降低和速度提升。


### [UniToolCall: Unifying Tool-Use Representation, Data, and Evaluation for LLM Agents](https://arxiv.org/abs/2604.11557)

**作者**: Liang, Chen, Ge, Wu, Wu 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.11557) · [PDF](https://arxiv.org/pdf/2604.11557)  \| [📖 全文分析](paper_2604.11557.md)  
**评分**: 8.52  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 7.5）

> 本文由阿里巴巴团队（Qwen系列模型开发者）提出了一种名为UniToolCall的统一框架，旨在解决大语言模型工具调用领域存在的交互表示不一致、数据分布结构被忽视以及评估基准不兼容等问题。该框架通过构建大规模工具池、混合训练语料库以及统一的评估表示，显著提升了模型在复杂工具调用场景下的性能，在多个基准测试中超越了GPT、Gemini和Claude等商业模型。


### [EvoDiagram: Agentic Editable Diagram Creation via Design Expertise Evolution](https://arxiv.org/abs/2604.09568)

**作者**: Wang, Ding, Tao, Zhan, Ma 等 16 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.09568) · [PDF](https://arxiv.org/pdf/2604.09568)  \| [📖 全文分析](paper_2604.09568.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为EvoDiagram的智能体框架，用于生成对象级可编辑图表。该方法通过引入中间画布模式来弥合像素级模型与代码合成之间的表示鸿沟，并采用协调的多智能体系统将语义意图与渲染逻辑解耦。论文还提出了设计知识演化机制，将执行轨迹提炼为领域指南的分层记忆，使智能体能够自适应地检索上下文感知的专业知识。此外，作者团队发布了CanvasBench基准数据集和评估指标。实验表明，EvoDiagram在生成可编辑、结构一致且美观的图表方面表现出色。


### [HiPRAG: Hierarchical Process Rewards for Efficient Agentic Retrieval Augmented Generation](https://arxiv.org/abs/2510.07794)

**作者**: Wu, Zhang, Wan, Zhao, He 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.07794) · [PDF](https://arxiv.org/pdf/2510.07794)  \| [📖 全文分析](paper_2510.07794.md)  
**评分**: 8.46  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为HiPRAG（Hierarchical Process Rewards for Efficient agentic RAG）的训练方法，旨在解决智能体检索增强生成（Agentic RAG）中普遍存在的搜索效率低下问题（如过度搜索和搜索不足）。该方法通过在强化学习框架中引入细粒度的、基于知识的流程奖励，对智能体的推理轨迹进行分解和评估，从而优化搜索决策过程。实验在Qwen2.5和Llama-3.2模型上进行，覆盖七个不同的问答基准测试，结果表明该方法在提升准确率的同时，显著降低了过度搜索和搜索不足的发生率，并展现出良好的泛化能力。


### [Agentic Exploration of PDE Spaces using Latent Foundation Models for Parameterized Simulations](https://arxiv.org/abs/2604.09584)

**作者**: Vishwasrao, Giral, Golestanian, Tonti, Ramo 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.09584) · [PDF](https://arxiv.org/pdf/2604.09584)  \| [📖 全文分析](paper_2604.09584.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文由来自加州大学圣地亚哥分校、斯坦福大学、麻省理工学院、宾夕法尼亚大学、马德里理工大学等知名高校的研究团队提出了一种创新的科学发现范式。该方法将多智能体大语言模型与潜在基础模型相结合，用于自动探索偏微分方程控制的物理系统参数空间。该方法创新性地解决了连续高维物理场探索的难题，实验设计系统且规模较大，展示了在流体力学领域的有效应用。


### [HiL-Bench (Human-in-Loop Benchmark): Do Agents Know When to Ask for Help?](https://arxiv.org/abs/2604.09408)

**作者**: Elfeki, Trinh, Luu, Luo, Hunt 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.09408) · [PDF](https://arxiv.org/pdf/2604.09408)  \| [📖 全文分析](paper_2604.09408.md)  
**评分**: 8.42  （novelty: 9.5 | method: 9.0 | evidence: 8.5 | clarity: 8.5）

> 本文提出了一种名为HiL-Bench（Human-in-the-Loop Benchmark）的新型基准测试，旨在评估AI智能体在面临不完整或模糊任务规范时，是否具备“选择性升级”（即判断何时应自主行动、何时应向人类求助）的关键能力。该研究揭示了当前前沿模型普遍存在的“判断鸿沟”，并证明通过基于Ask-F1指标的强化学习训练，可以提升模型的求助判断能力，且该能力具有跨领域迁移性。作者团队来自学术界和工业界，包括Meta GenAI等机构的研究人员。


### [Pioneer Agent: Continual Improvement of Small Language Models in Production](https://arxiv.org/abs/2604.09791)

**作者**: Atreja, White, Nayak, Zhang, Princis 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.09791) · [PDF](https://arxiv.org/pdf/2604.09791)  \| [📖 全文分析](paper_2604.09791.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Pioneer Agent的闭环系统，用于自动化小语言模型在生产环境中的持续改进生命周期。该系统包含冷启动和生产两种模式，能够自动完成数据获取、评估集构建、迭代训练、错误诊断和针对性再训练等任务。作者团队来自学术界和工业界，但未明确标注具体机构，因此省略团队背景介绍。论文在多个基准测试上展示了显著性能提升，方法创新性强，实验设计全面。


### [Instructing LLMs to Negotiate using Reinforcement Learning with Verifiable Rewards](https://arxiv.org/abs/2604.09855)

**作者**: Liu, Chen, Xiao, Lei, Zhang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.09855) · [PDF](https://arxiv.org/pdf/2604.09855)  \| [📖 全文分析](paper_2604.09855.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种基于可验证奖励的强化学习（RLVR）框架，用于训练大型语言模型（LLM）进行双边价格谈判。该方法通过将奖励信号直接锚定在经济盈余最大化和严格遵守私人预算约束上，揭示了智能体从幼稚议价到发展出复杂说服技巧的四阶段战略演化过程。实验表明，经过训练的30B参数智能体在提取盈余方面显著优于规模大十倍的尖端模型，并能泛化到训练中未见过的更强对手和敌对卖家角色。作者团队来自学术界，其中David Simchi-Levi教授是麻省理工学院（MIT）的知名学者，在运筹学、供应链管理和算法博弈论领域享有盛誉。


### [Verify Before You Fix: Agentic Execution Grounding for Trustworthy Cross-Language Code Analysis](https://arxiv.org/abs/2604.10800)

**作者**: Gajjar  
**链接**: [arXiv](https://arxiv.org/abs/2604.10800) · [PDF](https://arxiv.org/pdf/2604.10800)  \| [📖 全文分析](paper_2604.10800.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种基于LLM驱动的、面向可信跨语言代码分析的智能体执行验证框架。该框架围绕三个核心推理阶段构建：混合结构-语义检测、执行验证的智能体验证以及验证感知的迭代修复，并遵循一个严格的不变量：在没有基于执行确认可利用性之前，不采取任何修复行动。通过通用抽象语法树（uAST）将Java、Python和C++代码归一化为共享结构模式，并结合GraphSAGE与Qwen2.5-Coder-1.5B嵌入的混合融合，实现了跨语言泛化。实验表明，该框架在跨语言漏洞检测和修复方面取得了显著效果，验证了执行验证的闭环推理是构建可信LLM驱动智能体AI的有效机制。


### [Mem$^2$Evolve: Towards Self-Evolving Agents via Co-Evolutionary Capability Expansion and Experience Distillation](https://arxiv.org/abs/2604.10923)

**作者**: Cheng, Liu, Shan, Wang, Zhu 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.10923) · [PDF](https://arxiv.org/pdf/2604.10923)  \| [📖 全文分析](paper_2604.10923.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Mem²Evolve的新型自进化智能体框架，通过协同进化的能力扩展与经验蒸馏，解决了现有方法将经验积累与资产（工具或专家智能体）动态创建过程割裂的问题。该框架整合了经验记忆与资产记忆，利用积累的经验指导新资产的创建，同时通过新资产获取新经验，实现双向协同进化。在6个任务类别和8个基准测试上的广泛实验表明，其性能显著优于标准大语言模型、仅通过经验进化的智能体以及仅通过资产创建进化的智能体。作者团队来自北京航空航天大学（BUAA），在人工智能与自然语言处理领域具有扎实的研究基础。


### [FM-Agent: Scaling Formal Methods to Large Systems via LLM-Based Hoare-Style Reasoning](https://arxiv.org/abs/2604.11556)

**作者**: Ding, Wang, Chen  
**链接**: [arXiv](https://arxiv.org/abs/2604.11556) · [PDF](https://arxiv.org/pdf/2604.11556)  \| [📖 全文分析](paper_2604.11556.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为FM-Agent的创新框架，首次实现了面向大规模系统的自动化组合推理。该框架通过利用大语言模型（LLM）引入了一种自顶向下的范式，能够自动生成函数级规范，并将霍尔式推理推广至自然语言规范，从而有效验证由LLM生成的大规模代码系统的正确性。在评估中，FM-Agent成功对高达14.3万行代码的系统进行了推理，并发现了522个开发者测试未覆盖的新缺陷。


### [AIRA_2: Overcoming Bottlenecks in AI Research Agents](https://arxiv.org/abs/2603.26499)

**作者**: Hambardzumyan, Baldwin, Toledo, Hazra, Kuchnik 等 25 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.26499) · [PDF](https://arxiv.org/pdf/2603.26499)  \| [📖 全文分析](paper_2603.26499.md)  
**评分**: 8.31  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文由Meta AI、Mila、斯坦福大学、多伦多大学等知名机构的研究人员组成的团队提出了一种名为AIRA₂的新型AI研究智能体架构。该研究系统性地识别并解决了现有AI研究智能体的三个结构性性能瓶颈，通过异步多GPU执行、隐藏一致性评估协议和动态范围ReAct智能体等创新设计，显著提升了研究智能体的性能和效率。在MLE-bench-30和AIRS-Bench两个基准测试中均取得了超越现有基线和人类专家水平的优异表现。


### [Diagnosing Retrieval vs. Utilization Bottlenecks in LLM Agent Memory](https://arxiv.org/abs/2603.02473)

**作者**: Yuan, Su, Yao  
**链接**: [arXiv](https://arxiv.org/abs/2603.02473) · [PDF](https://arxiv.org/pdf/2603.02473)  \| [📖 全文分析](paper_2603.02473.md)  
**评分**: 8.18  （novelty: 8.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种诊断框架，用于分析LLM智能体记忆系统中信息写入策略与检索方法对性能的相对影响。通过3×3实验设计（三种写入策略×三种检索方法），研究发现检索方法是影响性能的主导因素，而无需LLM调用的原始分块存储策略与更复杂的写入方法表现相当甚至更优。论文实验设计严谨，代码已开源，为优化LLM智能体记忆系统提供了重要的实证依据。


### [Agent-Dice: Disentangling Knowledge Updates via Geometric Consensus for Agent Continual Learning](https://arxiv.org/abs/2601.03641)

**作者**: Wu, Lou, Ma, Li, Liu 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2601.03641) · [PDF](https://arxiv.org/pdf/2601.03641)  \| [📖 全文分析](paper_2601.03641.md)  
**评分**: 8.13  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Agent-Dice的参数融合框架，旨在解决基于大语言模型（LLM）的智能体在持续学习（Continual Learning）中面临的稳定性-可塑性困境。该方法通过几何共识评估，将知识更新解耦为两个阶段：几何共识过滤以修剪冲突梯度，以及基于曲率的重要性加权以增强共享语义。论文提供了严格的理论分析，并在GUI智能体和工具使用智能体领域进行了广泛的实验验证，证明了其出色的持续学习性能，且计算开销和参数更新量最小。代码已开源。


### [Do Agent Rules Shape or Distort? Guardrails Beat Guidance in Coding Agents](https://arxiv.org/abs/2604.11088)

**作者**: Zhang, Wang, Cui, Qiu, Li 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.11088) · [PDF](https://arxiv.org/pdf/2604.11088)  \| [📖 全文分析](paper_2604.11088.md)  
**评分**: 7.91  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文首次对AI编程代理中广泛使用的自然语言指令文件（如CLAUDE.md、.cursorrules）进行了大规模实证研究。通过从GitHub收集679个文件（包含25,532条规则），并在SWE-bench Verified基准上进行了超过5,000次代理运行测试，研究发现规则能提升性能7-14个百分点，但随机规则与专家精心设计的规则效果相当，表明规则主要通过上下文启动而非具体指令发挥作用。研究还发现负面约束（“不要重构无关代码”）是唯一单独有益的规则类型，而正面指令（“遵循代码风格”）反而有害。这些发现揭示了隐藏的可靠性风险，并为安全的代理配置提供了明确原则：约束代理不能做什么，而非规定应该做什么。


### [Escaping the Context Bottleneck: Active Context Curation for LLM Agents via Reinforcement Learning](https://arxiv.org/abs/2604.11462)

**作者**: Li, Lyu, Yang, Shan, Yang 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.11462) · [PDF](https://arxiv.org/pdf/2604.11462)  \| [📖 全文分析](paper_2604.11462.md)  
**评分**: 7.91  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种创新的框架来解决LLM在长视野任务中的“上下文瓶颈”问题。通过将上下文管理与任务执行解耦，并引入一个轻量级的强化学习策略模型ContextCurator，该框架能够主动管理工作记忆，在减少环境噪声的同时保留关键的推理锚点。在WebArena和DeepSearch基准测试中，该方法在提升任务成功率的同时显著降低了token消耗，展示了其有效性和效率。作者团队未明确标注所属机构，因此省略背景介绍。

