# Agent · 2026-03-30 ~ 2026-04-03

**论文数**: 27 篇

---

## 📊 趋势分析

### 研究全貌

Agent领域的研究方向高度聚焦于构建更强大、更自主、更可靠的智能体系统。主要研究方向可归纳为四个方面：一是**智能体的能力进化与系统化工程**，旨在通过模块化设计、上下文演化与技能提炼，实现智能体的持续自我改进；二是**多智能体协作与组织**，探索从预设分层治理到涌现自组织的多种架构，以提升复杂任务下的协作效率与可靠性；三是**多模态与长时记忆**，致力于解决智能体在处理长序列、多模态信息时的信息保留与检索难题；四是**特定领域的自动化工作流构建**，如科学发现、代码生成与验证等，旨在将智能体技术深度应用于专业领域。当前的热点问题是如何在**无需大量人工干预**的前提下，让智能体通过与环境交互实现**自主、可靠且可泛化的能力提升**。整体研究趋势呈现出从依赖单一模型生成，向构建**模块化、可演化、具备闭环反思能力**的智能体系统演进，强调系统的可解释性、可控性与部署效率。

### 重点方法深度解析

从所有批次的研究中，以下几个方法因其创新性和实用性尤为突出：

**1. Agentic Context Engineering (ACE) 框架**
*   **核心创新点**：解决了智能体在长期任务中上下文适应与优化的核心难题。它将上下文视为可演化的“剧本”，通过一个**生成-反思-策展**的模块化流程，系统性地积累和优化任务策略，有效缓解了“简洁性偏见”和“上下文崩溃”问题。
*   **技术细节**：采用结构化、增量式的更新方式，将新获得的策略、证据和教训整合到上下文中，而非简单替换。该框架支持对系统提示词（离线）和智能体记忆（在线）进行无监督优化。
*   **效果验证**：在智能体和金融等领域的基准测试中，分别带来了显著的性能提升，并有效降低了适应延迟与成本。其关键优势在于仅利用自然执行反馈即可驱动自我改进。
*   **适用场景**：任何需要智能体在长期交互中不断学习和积累经验的场景，是实现“终身学习”智能体的关键技术路径。

**2. Trace2Skill 框架**
*   **核心创新点**：旨在自动化生成可迁移、可泛化的智能体技能。其创新在于模仿人类专家总结经验的模式，通过**并行分析**大量执行轨迹，提取局部教训，再通过**归纳推理**整合成统一、无冲突的技能目录。
*   **技术细节**：派遣并行子智能体舰队分析执行池，并通过分层合并避免技能碎片化。整个过程无需参数更新或外部检索模块。
*   **效果验证**：在电子表格、视觉问答等复杂领域，其生成的技能超越了包括官方技能在内的强基线。这些技能展现出卓越的跨模型规模和分布外泛化能力。
*   **适用场景**：为构建可扩展的智能体技能库提供了自动化解决方案，适用于需要技能沉淀和复用的复杂任务环境。

**3. OrgAgent 与自组织智能体的对比**
*   **核心创新点**：`OrgAgent`将公司式分层治理思想引入多智能体系统，通过明确的角色分工和层级控制来提升效率与可控性。而《Drop the Hierarchy and Roles》的研究则挑战了预设结构的必要性，证明在最小顺序协议下，智能体能够**自发涌现出有效的分工与协作秩序**。
*   **技术细节**：`OrgAgent`分为治理、执行、合规三层，实现稳定技能分配。自组织方法则仅提供一个基本的交互顺序，不预定义角色，依赖模型自身能力形成内部秩序。
*   **效果验证**：`OrgAgent`在多项推理任务上普遍优于扁平协作，并能显著降低令牌消耗。自组织方式在大规模实验中，其性能超越了集中式协调，并展现出良好的可扩展性。
*   **适用场景**：`OrgAgent`适用于任务目标明确、流程规范、对输出质量和成本控制要求高的工业化应用。自组织策略则更适合问题开放、需要高度创造性和自适应性的探索型任务。两者代表了“可控高效”与“自主涌现”两种设计哲学，选择取决于任务性质与模型能力。

**4. Mimosa 框架**
*   **核心创新点**：解决了自主科研系统工作流固定、无法适应动态任务的问题，提出了一个能够**自动合成并迭代优化**任务专用多智能体工作流的动态框架。
*   **技术细节**：核心是一个四步循环：动态工具发现、元编排器生成工作流拓扑、代码生成智能体执行、基于LLM的评判器反馈驱动优化，形成闭环。
*   **效果验证**：在科学基准测试上，其动态演化的工作流超越了单智能体基线和静态多智能体配置。
*   **适用场景**：适用于需要自动化、可复现且能适应新工具与新问题的跨学科科学研究任务。

这些重点方法之间存在紧密联系：`ACE`和`Trace2Skill`侧重于单个智能体内部能力的**纵向深化与沉淀**；而`OrgAgent`、自组织研究和`Mimosa`则关注多个智能体之间**横向协作与组织的优化**。它们共同构成了构建高级智能体系统的工具箱。

### 实践启示

这些研究为实际的大模型应用开发提供了清晰的路线图。首要借鉴是**将智能体系统设计为显式、模块化且具备闭环演化能力的**。决策、执行、验证等环节应尽可能解耦，以提升可靠性和可调试性。

针对不同场景，应关注不同方法组合：
*   对于需要**长期交互和知识积累**的任务（如客服、研究助手），应重点关注`ACE`和`Trace2Skill`这类实现上下文与技能演化的框架。
*   对于涉及**多智能体协作的复杂任务**，若追求稳定可控，可采用`OrgAgent`的分层治理；若鼓励创新探索，可尝试最小支架下的自组织策略。
*   对于**高可靠性场景**（如代码生成、科学计算），务必引入类似`Judge Agent`的独立验证环节，并借鉴`Mimosa`的“规划-执行-反思”闭环机制。

**可落地的具体建议**包括：
1.  优先设计一个**结构化的经验反馈与整合循环**，积极利用执行结果（成功/失败、性能指标）作为驱动自我演化的信号。
2.  在关键任务中，集成**基于确定性规则或仿真的独立验证环节**，杜绝“静默失败”。
3.  考虑采用“重演员-轻评论家”的非对称架构，用大模型保证生成质量，用小模型进行低成本监督，平衡性能与成本。

**关键注意事项**：模块化设计虽增加前期复杂度，但长远看是保障系统健壮性的必要投资。自组织策略的有效性高度依赖基座模型能力，在能力不足时仍需更多外部结构引导。实现动态工作流时，务必保留完整的执行日志与中间状态，以支持诊断与优化。

---

## 📄 论文列表（27 篇）

### [Agentic Context Engineering: Evolving Contexts for Self-Improving Language Models](https://arxiv.org/abs/2510.04618)

**作者**: Zhang, Hu, Upasani, Ma, Hong 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.04618) · [PDF](https://arxiv.org/pdf/2510.04618)  \| [📖 全文分析](paper_2510.04618.md)  
**评分**: 8.69  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为ACE（Agentic Context Engineering）的创新框架，用于解决大语言模型应用中的上下文适应问题。该方法通过将上下文视为可演化的“剧本”，采用生成、反思和策展的模块化流程来积累、细化和组织策略，有效缓解了现有方法中的简洁性偏见和上下文崩溃问题。论文在多个智能体和领域特定基准测试中展示了显著性能提升，特别是在无监督学习和资源效率方面表现突出。作者团队来自斯坦福大学（Stanford），其中多位作者在人工智能和机器学习领域有深厚的研究背景，如Kunle Olukotun教授是并行计算和计算机体系结构领域的知名专家。


### [Trace2Skill: Distill Trajectory-Local Lessons into Transferable Agent Skills](https://arxiv.org/abs/2603.25158)

**作者**: Ni, Liu, Liu, Sun, Zhou 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.25158) · [PDF](https://arxiv.org/pdf/2603.25158)  \| [📖 全文分析](paper_2603.25158.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为Trace2Skill的创新框架，旨在解决为大语言模型（LLM）智能体高效、可扩展地生成领域特定技能的难题。该框架通过并行分析多样化的执行轨迹，提取局部经验，并利用归纳推理将其整合为统一、无冲突的技能目录，模仿了人类专家总结经验的模式。实验在电子表格、视觉问答和数学推理等多个具有挑战性的领域进行，结果表明Trace2Skill显著超越了包括Anthropic官方技能在内的多个强基线。尤为重要的是，该方法生成的技能具有出色的可迁移性和泛化能力，能够跨LLM规模（如从35B到122B参数模型）和分布外（OOD）场景有效工作，且无需参数更新或外部检索模块。作者团队Ni, Liu, Liu, Sun, Zhou, Cheng, Wang, Zhao, Jiang, Jiang中包含了来自阿里巴巴（Qwen模型）的研究人员，表明该研究具有坚实的工业界背景和资源支持。


### [WorldMM: Dynamic Multimodal Memory Agent for Long Video Reasoning](https://arxiv.org/abs/2512.02425)

**作者**: Yeo, Kim, Yoon, Hwang  
**链接**: [arXiv](https://arxiv.org/abs/2512.02425) · [PDF](https://arxiv.org/pdf/2512.02425)  \| [📖 全文分析](paper_2512.02425.md)  
**评分**: 8.61  （novelty: 9.0 | method: 8.5 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种名为WorldMM的新型多模态记忆代理，用于解决长视频推理中的关键挑战。该方法通过构建包含文本和视觉表示的互补记忆系统，并采用自适应检索机制，在五个长视频问答基准测试中取得了平均8.4%的性能提升，显著优于现有方法。作者团队背景信息未在提供内容中明确提及，故省略。


### ["Who Am I, and Who Else Is Here?" Behavioral Differentiation Without Role Assignment in Multi-Agent LLM Systems](https://arxiv.org/abs/2604.00026)

**作者**: Kandoussi  
**链接**: [arXiv](https://arxiv.org/abs/2604.00026) · [PDF](https://arxiv.org/pdf/2604.00026)  \| [📖 全文分析](paper_2604.00026.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文通过构建一个控制实验平台，研究了多智能体大语言模型在共享对话中行为分化的现象。研究发现，异质模型组比同质模型组表现出更丰富的行为分化，群体组成、命名约定和提示结构都会显著影响智能体的行为模式。研究设计严谨，采用大规模实验（208次运行，13,786条编码消息）和双重LLM法官编码系统，确保了结果的可靠性。作者团队未明确来自知名机构，因此省略背景介绍。


### [Execution-Verified Reinforcement Learning for Optimization Modeling](https://arxiv.org/abs/2604.00442)

**作者**: Guan, Shen, Zhang, Zhang, Cheng 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.00442) · [PDF](https://arxiv.org/pdf/2604.00442)  \| [📖 全文分析](paper_2604.00442.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为执行验证优化建模（EVOM）的新框架，用于自动化优化建模。该方法将数学规划求解器视为确定性交互验证器，通过生成求解器特定代码、在沙箱中执行并将执行结果转换为标量奖励，实现了无需过程级监督的强化学习。在NL4OPT、MAMO、IndustryOR和OptiBench等多个数据集上，使用Gurobi、OR-Tools和COPT等求解器的实验表明，EVOM匹配或超越了过程监督的监督微调方法，支持零样本求解器迁移，并能通过目标求解器后端下的持续训练实现低成本有效适应。


### [A Judge Agent Closes the Reliability Gap in AI-Generated Scientific Simulation](https://arxiv.org/abs/2603.25780)

**作者**: Yang  
**链接**: [arXiv](https://arxiv.org/abs/2603.25780) · [PDF](https://arxiv.org/pdf/2603.25780)  \| [📖 全文分析](paper_2603.25780.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 9.5 | clarity: 7.0）

> 本文提出了一种名为'Judge Agent'的自动化验证框架，用于解决大语言模型生成科学模拟代码时常见的'静默失败'问题。该方法通过自动化经典数学验证（适定性、收敛性、误差认证），显著提升了生成代码的可靠性。论文在涵盖12个科学领域的134个测试案例上进行了验证，将静默失败率从42%降低至1.5%。核心亮点来自一项前瞻性基准测试：在12位独立科学家提交的72项盲测任务中，结合自动化误差边界的方法取得了89%的成功率，远高于未使用Judge Agent的53%。在临床CT（唯一具有统计效力的实验，n=200）上，该流程达到了专家质量的99%。论文还引入了'simulability class S'的概念来形式化验证能力的边界，并提出了'spec.md'这一结构化规范格式，使科学计算问题机器可读且与求解器无关。代码、数据和所有72项基准任务均已公开存档。


### [Omni-SimpleMem: Autoresearch-Guided Discovery of Lifelong Multimodal Agent Memory](https://arxiv.org/abs/2604.01007)

**作者**: Liu, Ling, Qiu, Liu, Han 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.01007) · [PDF](https://arxiv.org/pdf/2604.01007)  \| [📖 全文分析](paper_2604.01007.md)  
**评分**: 8.51  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Omni-SimpleMem的统一多模态记忆框架，用于构建具有终身学习能力的AI智能体。该研究的核心创新在于采用了一种自主研究（autoresearch）管道来自动探索庞大的设计空间，从而发现并优化记忆系统的架构、检索策略、提示工程和数据管道。该方法在两个基准测试（LoCoMo和Mem-Gallery）上取得了显著的性能提升，分别实现了+411%和+214%的F1分数改进。研究不仅展示了最终系统的优越性能，更重要的是系统性地分析了自主研究过程的有效性，并总结了六种发现类型和四个使多模态记忆特别适合自主研究的特性，为将此类方法推广到其他AI系统领域提供了指导。代码已开源。


### [Decision-Centric Design for LLM Systems](https://arxiv.org/abs/2604.00414)

**作者**: Sun  
**链接**: [arXiv](https://arxiv.org/abs/2604.00414) · [PDF](https://arxiv.org/pdf/2604.00414)  \| [📖 全文分析](paper_2604.00414.md)  
**评分**: 8.50  （novelty: 8.5 | method: 9.5 | evidence: 7.0 | clarity: 8.5）

> 本文提出了一种决策中心化的LLM系统设计框架，旨在解决当前LLM架构中控制决策隐含在生成过程中、难以检查、约束或修复的问题。该框架通过将决策相关信号与决策策略解耦，使系统控制成为一个显式且可检查的层面，从而支持故障归因和模块化改进。论文通过三个受控实验验证了该框架在减少无效操作、提升任务成功率和揭示可解释故障模式方面的有效性。作者Sun未注明具体机构，故省略团队背景介绍。


### [CORAL: Towards Autonomous Multi-Agent Evolution for Open-Ended Discovery](https://arxiv.org/abs/2604.01658)

**作者**: Qu, Zheng, Zhou, Yan, Tang 等 17 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.01658) · [PDF](https://arxiv.org/pdf/2604.01658)  \| [📖 全文分析](paper_2604.01658.md)  
**评分**: 8.44  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为CORAL的自主多智能体进化框架，用于解决开放式发现问题。该框架通过引入长期运行的智能体、共享持久内存、异步多智能体执行和基于心跳的干预机制，取代了传统的固定启发式规则和硬编码探索方法。在多个数学、算法和系统优化任务上，CORAL在10个任务上取得了新的最先进结果，改进率比固定进化搜索基线高出3-10倍，且所需评估次数更少。在Anthropic的内核工程任务中，四个协同进化的智能体将最佳已知分数从1363周期提升至1103周期。论文提供了详细的机制分析，展示了知识重用和多智能体探索与通信带来的收益。代码已在GitHub上开源。


### [AISAC: An Integrated multi-agent System for Transparent, Retrieval-Grounded Scientific Assistance](https://arxiv.org/abs/2511.14043)

**作者**: Bhattacharya, Som  
**链接**: [arXiv](https://arxiv.org/abs/2511.14043) · [PDF](https://arxiv.org/pdf/2511.14043)  \| [📖 全文分析](paper_2511.14043.md)  
**评分**: 8.43  （novelty: 8.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文由美国阿贡国家实验室（Argonne National Laboratory）团队提出了一种名为AI Scientific Assistant Core (AISAC)的透明、模块化多智能体运行时系统。该系统并非提出新的智能体算法，而是贡献了一个用于部署科学实践中智能体AI的治理执行基座，重点在于实现可操作的结构化保障。论文创新性地将系统设计原则（如角色语义、预算化编排、可追溯执行）工程化实现，并在多个科学工作流中成功部署，展示了其作为可复用基座的价值。


### [CADSmith: Multi-Agent CAD Generation with Programmatic Geometric Validation](https://arxiv.org/abs/2603.26512)

**作者**: Barkley, Loghmani, Farimani  
**链接**: [arXiv](https://arxiv.org/abs/2603.26512) · [PDF](https://arxiv.org/pdf/2603.26512)  \| [📖 全文分析](paper_2603.26512.md)  
**评分**: 8.38  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为CADSmith的多智能体CAD生成系统，通过程序化几何验证实现文本到CAD代码的生成与迭代优化。该系统采用双循环修正机制，结合OpenCASCADE内核的精确测量和视觉语言模型的整体评估，显著提升了生成CAD模型的质量和可靠性。作者团队中，Barkley和Loghmani来自卡内基梅隆大学（CMU），Farimani来自斯坦福大学（Stanford），均为人工智能和计算机辅助设计领域的知名研究机构。


### [Learning to Learn-at-Test-Time: Language Agents with Learnable Adaptation Policies](https://arxiv.org/abs/2604.00830)

**作者**: Lou, Chen, Li, Wang, Hooi  
**链接**: [arXiv](https://arxiv.org/abs/2604.00830) · [PDF](https://arxiv.org/pdf/2604.00830)  \| [📖 全文分析](paper_2604.00830.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Meta-TTL的创新框架，旨在通过双层优化学习语言代理在测试时的自适应策略，而非依赖人工设计的固定策略。该方法在Jericho和WebArena-Lite基准测试中，无论是在分布内还是分布外设置下，均显著优于现有基线，证明了学习到的自适应策略具有可迁移的泛化能力。作者团队来自学术界，但具体机构信息未在摘要中明确提及。


### [A Self-Evolving Agentic Framework for Metasurface Inverse Design](https://arxiv.org/abs/2604.01480)

**作者**: Huang, Zheng, Dong, Tang, Zhao 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.01480) · [PDF](https://arxiv.org/pdf/2604.01480)  \| [📖 全文分析](paper_2604.01480.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种用于超表面逆向设计的自进化智能体框架，通过上下文级技能演化来解决现有语言驱动系统在跨任务间无法保留可重用工作流知识的问题。该框架将编码智能体、演化技能构件和基于物理模拟的确定性评估器相结合，能够在不同任务间迭代优化特定求解器策略，而无需修改模型权重或底层物理求解器。实验表明，该框架在多个超表面逆向设计任务类型上显著提升了任务成功率、标准通过率和效率，并在未见任务家族上显示出工作流知识的部分迁移能力。作者团队未明确来自知名机构，故省略背景介绍。


### [On the Reliability Limits of LLM-Based Multi-Agent Planning](https://arxiv.org/abs/2603.26993)

**作者**: Ao, Gao, Simchi-Levi  
**链接**: [arXiv](https://arxiv.org/abs/2603.26993) · [PDF](https://arxiv.org/pdf/2603.26993)  \| [📖 全文分析](paper_2603.26993.md)  
**评分**: 8.36  （novelty: 9.5 | method: 9.5 | evidence: 8.0 | clarity: 7.0）

> 本文由麻省理工学院（MIT）的Ao、Gao和Simchi-Levi团队撰写，他们深入研究了基于大语言模型（LLM）的多智能体规划系统的可靠性极限。该工作将LLM多智能体架构建模为有限无环决策网络，从决策理论角度严格证明了在缺乏新外部信号时，任何委托网络在决策性能上都无法超越一个拥有相同信息的集中式贝叶斯决策者。这为理解LLM多智能体系统的根本能力边界提供了坚实的理论框架。实验部分在受控问题集上验证了理论刻画。论文理论创新突出，分析严谨，对理解LLM系统的可靠性具有重要价值。


### [ASI-Evolve: AI Accelerates AI](https://arxiv.org/abs/2603.29640)

**作者**: Xu, Mi, Liu, Nan, Zhou 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.29640) · [PDF](https://arxiv.org/pdf/2603.29640)  \| [📖 全文分析](paper_2603.29640.md)  
**评分**: 8.33  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为ASI-Evolve的智能体框架，旨在通过一个“学习-设计-实验-分析”的闭环，实现AI加速AI自身发展的目标。该框架在神经架构设计、预训练数据筛选和强化学习算法设计三个核心AI开发领域均取得了显著的性能提升，并初步展示了其在数学和生物医学等更广泛领域的迁移潜力。论文实验设计全面，证据充分，展示了AI-for-AI研究范式的巨大潜力。


### [EvoSkills: Self-Evolving Agent Skills via Co-Evolutionary Verification](https://arxiv.org/abs/2604.01687)

**作者**: Zhang, Fan, Zou, Chen, Wang 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.01687) · [PDF](https://arxiv.org/pdf/2604.01687)  \| [📖 全文分析](paper_2604.01687.md)  
**评分**: 8.31  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为EvoSkills的自进化技能框架，旨在解决LLM代理在处理复杂多步骤专业任务时技能生成面临的挑战。该方法通过协同进化的验证机制，使代理能够自主构建复杂的多文件技能包，在SkillsBench基准测试中取得了当前最佳性能，并在多个LLM上展现出良好的泛化能力。作者团队来自Anthropic，这是一家在AI安全与对齐领域具有重要影响力的研究机构，其开发的Claude系列模型在业界广受认可。


### [Kernel-Smith: A Unified Recipe for Evolutionary Kernel Optimization](https://arxiv.org/abs/2603.28342)

**作者**: Du, Ge, Hu, Yang, Cai 等 21 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.28342) · [PDF](https://arxiv.org/pdf/2603.28342)  \| [📖 全文分析](paper_2603.28342.md)  
**评分**: 8.29  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为Kernel-Smith的统一框架，用于高性能GPU内核和算子生成。该框架将稳定的评估驱动进化代理与面向进化的后训练方案相结合，在KernelBench基准测试中取得了最先进的整体性能，超越了包括Gemini-3.0-pro和Claude-4.6-opus在内的前沿专有模型。论文进一步在MetaX MACA后端上验证了框架的有效性，并展示了其向上游生产系统（如SGLang和LMDeploy）的实际贡献，证明了LLM驱动的内核优化可以从受控评估转移到实际部署。


### [Can AI Models Direct Each Other? Organizational Structure as a Probe into Training Limitations](https://arxiv.org/abs/2603.26458)

**作者**: Liu  
**链接**: [arXiv](https://arxiv.org/abs/2603.26458) · [PDF](https://arxiv.org/pdf/2603.26458)  \| [📖 全文分析](paper_2603.26458.md)  
**评分**: 8.16  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文提出了一种名为ManagerWorker的双智能体协作框架，用于探索昂贵AI模型指导廉价模型解决软件工程任务的可行性。研究通过五种不同配置在SWE-bench Lite数据集上的200个实例进行系统评估，揭示了多智能体指导机制的潜力与局限。研究发现，有效的指导需要智能体之间存在真实的能力差距，且管理者的价值在于主动的探索规划而非简单的代码审查。论文最终指出，当前模型的训练方式使其难以适应分工协作的角色，这一诊断为未来训练数据的设计指出了具体方向。


### [KAT-Coder-V2 Technical Report](https://arxiv.org/abs/2603.27703)

**作者**: Li, Zhang, Huang, Wang, Hao 等 46 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.27703) · [PDF](https://arxiv.org/pdf/2603.27703)  \| [📖 全文分析](paper_2603.27703.md)  
**评分**: 8.13  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.0）

> 本文由快手KwaiKAT团队提出了一种新型的智能体化编码模型KAT-Coder-V2。该团队来自知名互联网公司快手，在AI与代码生成领域有深厚的技术积累。论文提出了“先专业化后统一”的范式，将智能体编码分解为五个专家领域，并开发了支持大规模并发沙箱实例的基础设施KwaiEnv。模型在多个代码生成基准测试中取得了领先或极具竞争力的性能，并公开了模型。


### [TeamMedAgents: Pareto-Efficient Multi-Agent Medical Reasoning Through Teamwork Theory](https://arxiv.org/abs/2508.08115)

**作者**: Mishra, Arvan, Zalake  
**链接**: [arXiv](https://arxiv.org/abs/2508.08115) · [PDF](https://arxiv.org/pdf/2508.08115)  \| [📖 全文分析](paper_2508.08115.md)  
**评分**: 8.07  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为TeamMedAgents的模块化多智能体框架，该框架将基于证据的团队合作理论（Salas et al.）转化为计算机制，旨在使小型语言模型能够高效执行多步骤临床推理。在8个医疗基准测试上的评估表明，该方法显著提升了帕累托效率前沿，以更低的计算成本实现了有竞争力的准确性，并且在多智能体方法中表现出最低的跨数据集方差，无需针对每个任务进行调优即可部署。作者团队背景信息未在提供内容中明确提及，故省略。


### [Mimosa Framework: Toward Evolving Multi-Agent Systems for Scientific Research](https://arxiv.org/abs/2603.28986)

**作者**: Legrand, Jiang, Feraud, Navet, Taghzouti 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.28986) · [PDF](https://arxiv.org/pdf/2603.28986)  \| [📖 全文分析](paper_2603.28986.md)  
**评分**: 8.05  （novelty: 9.0 | method: 9.0 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Mimosa的演化多智能体框架，旨在解决当前自主科学研究（ASR）系统因固定工作流和工具集而无法适应动态任务和环境的问题。该框架通过动态工具发现、元编排器生成工作流拓扑、代码生成智能体执行子任务以及基于LLM的评判器反馈驱动工作流迭代优化，在ScienceAgentBench基准测试中取得了优于单智能体和静态多智能体配置的性能。论文实验设计严谨，代码完全开源，为社区驱动的ASR研究提供了开放基础。


### [OrgAgent: Organize Your Multi-Agent System like a Company](https://arxiv.org/abs/2604.01020)

**作者**: Wang, Shen, Han, Backes, Chen 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.01020) · [PDF](https://arxiv.org/pdf/2604.01020)  \| [📖 全文分析](paper_2604.01020.md)  
**评分**: 8.05  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为OrgAgent的公司式分层多智能体框架，将多智能体协作分为治理、执行和合规三层。通过在不同推理任务、大语言模型、执行模式和政策上的评估，发现公司式分层组织在多智能体系统中普遍优于其他组织结构，同时还能在多数情况下降低令牌消耗。论文创新性地将组织管理理论引入多智能体系统设计，实验证据充分，展示了分层协调在需要稳定技能分配、受控信息流和分层验证的任务中的优势。


### [Drop the Hierarchy and Roles: How Self-Organizing LLM Agents Outperform Designed Structures](https://arxiv.org/abs/2603.28990)

**作者**: Dochkina  
**链接**: [arXiv](https://arxiv.org/abs/2603.28990) · [PDF](https://arxiv.org/pdf/2603.28990)  \| [📖 全文分析](paper_2603.28990.md)  
**评分**: 7.91  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文通过大规模计算实验（25,000个任务、8个模型、4-256个智能体、8种协调协议）系统研究了多智能体LLM系统中的自主行为涌现问题。研究发现，在最小结构支架（固定顺序）下，智能体会自发发明专门角色、自愿放弃能力范围外的任务并形成浅层层级结构，而无需任何预分配角色或外部设计。混合协议（Sequential）使这种自主性得以实现，其性能比集中式协调高出14%。研究还发现，涌现自主性的程度与模型能力呈正相关，且系统可扩展至256个智能体而无质量下降。结果在闭源和开源模型上均得到复现，开源模型以24倍更低的成本达到闭源模型95%的质量。


### [Courtroom-Style Multi-Agent Debate with Progressive RAG and Role-Switching for Controversial Claim Verification](https://arxiv.org/abs/2603.28488)

**作者**: Chowdhury, Beg, Khan, Raiyan, Hasan 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.28488) · [PDF](https://arxiv.org/pdf/2603.28488)  \| [📖 全文分析](paper_2603.28488.md)  
**评分**: 7.88  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为PROClaim的法庭风格多智能体辩论框架，用于解决大语言模型在争议性声明验证中的不可靠性问题。该方法将检索增强生成与结构化多智能体辩论相结合，通过渐进式RAG、角色切换和异构法官聚合等技术，显著提升了验证准确性和鲁棒性。在Check-COVID基准测试中取得了81.7%的零样本准确率，比标准多智能体辩论方法提升了10.0个百分点。论文实验设计严谨，代码和数据已开源。


### [IMAGAgent: Orchestrating Multi-Turn Image Editing via Constraint-Aware Planning and Reflection](https://arxiv.org/abs/2603.29602)

**作者**: Shen, Xie, Wang, Zhang, Jiang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.29602) · [PDF](https://arxiv.org/pdf/2603.29602)  \| [📖 全文分析](paper_2603.29602.md)  
**评分**: 7.85  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为IMAGAgent的多轮图像编辑智能体框架，通过“规划-执行-反思”的闭环机制，实现了指令解析、工具调度和自适应校正的深度协同。该方法在构建的MTEditBench和MagicBrush数据集上进行了广泛实验，在指令一致性、编辑精度和整体质量方面显著优于现有方法。代码已开源。


### [Asymmetric Actor-Critic for Multi-turn LLM Agents](https://arxiv.org/abs/2604.00304)

**作者**: Jiang, Zhang, Zhang, Yang, Xia 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.00304) · [PDF](https://arxiv.org/pdf/2604.00304)  \| [📖 全文分析](paper_2604.00304.md)  
**评分**: 7.79  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.0）

> 本文由加州大学洛杉矶分校（UCLA）的Stefano Soatto教授团队提出了一种用于多轮对话LLM智能体的非对称演员-评论家框架。该研究针对现实应用中智能体必须在单次尝试中成功且无法重试的挑战，创新性地设计了一个固定的大型专有LLM作为演员，配合一个经过微调的小型开源LLM作为评论家进行运行时监督。该方法在τ-bench和UserBench基准测试上显著提升了可靠性和任务成功率，且轻量级评论家的表现可与大型专有模型相媲美。


### [Generalizable Dense Reward for Long-Horizon Robotic Tasks](https://arxiv.org/abs/2604.00055)

**作者**: Yong, Sheng, Qi, Wang, Sheehan 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.00055) · [PDF](https://arxiv.org/pdf/2604.00055)  \| [📖 全文分析](paper_2604.00055.md)  
**评分**: 7.74  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为VLLR的密集奖励框架，用于解决机器人基础策略在长视野任务中面临的分布偏移和误差累积问题。该方法创新性地结合了大型语言模型（LLMs）和视觉语言模型（VLMs）提供的外在奖励，以及基于策略自我确定性的内在奖励，实现了无需人工奖励工程的强化学习微调。在涵盖移动操作和导航的CHORES基准测试中，VLLR相比预训练策略取得了高达56%的绝对成功率提升，并在分布内和分布外任务上均优于现有最先进的RL微调方法。

