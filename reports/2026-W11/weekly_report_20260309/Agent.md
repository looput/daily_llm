# Agent · 2026-03-09 ~ 2026-03-13

**论文数**: 31 篇

---

## 📊 趋势分析

### 研究全貌

Agent领域的研究呈现出高度活跃与多元化的特点，主要研究方向可归纳为几个方面：**智能体能力构建与泛化**，关注如何通过任务合成、强化学习或神经符号协作来提升智能体在工具使用、数学发现等复杂任务中的能力；**智能体记忆与适应性**，探讨如何通过持久化记忆、技能库和后训练方法使智能体持续进化；**规划与协作效率优化**，旨在解决智能体在复杂、长时域任务中计算开销高昂的问题；**智能体安全与可靠性**，系统性地分析智能体架构带来的新型安全风险与防御策略；以及**特定领域应用**，如医疗、芯片设计、科学发现等，展示了智能体解决实际专业问题的潜力。当前的热点问题是如何构建**稳定、可靠、高效且能泛化到开放世界的自主智能体**，并确保其在长期交互中的安全与可控。整体研究趋势正从理论框架构建、基准测试向**解决具体、高价值现实问题**的深度应用转变，同时强调**模块化、可解释性、效率**以及**人机协作**的重要性。

### 重点方法深度解析

从所有批次中，以下几个工作因其创新性和实用性尤为突出：

**1. 《DIVE: Scaling Diversity in Agentic Task Synthesis for Generalizable Tool Use》**
*   **核心创新点**：针对工具使用智能体泛化能力弱的问题，提出了一种通过**反转任务合成顺序**（“先执行，后推导”）来规模化提升任务多样性的方法，从根本上确保生成任务的可执行性和真实性。
*   **技术细节**：该方法包含“证据收集-任务推导”循环。它从庞大的工具池中采样并执行工具，生成多样化的执行轨迹，然后基于这些轨迹，通过逻辑推导生成对应的任务描述、工具调用序列和验证条件，从工具池覆盖度和单任务工具集多样性两个维度规模化生成数据。
*   **效果验证**：使用该方法生成的数据训练模型，在多个分布外基准测试中，平均性能显著超越了基线模型。研究发现，**多样性扩展比单纯的数据量扩展对泛化更有效**。
*   **适用场景**：非常适合训练需要广泛、灵活使用外部工具（如API、软件、数据库）的通用智能体，特别是在工具集动态变化或任务定义开放的应用中。

**2. 《ELHPlan: Efficient Long-Horizon Task Planning for Multi-Agent Collaboration》**
*   **核心创新点**：针对多智能体协作中开放环规划与迭代规划的根本权衡，提出了“动作链”作为核心规划单元，将一系列动作显式绑定到子目标意图，从而在长时域规划和避免完全重规划之间取得平衡。
*   **技术细节**：ELHPlan框架采用一个循环流程：构建意图绑定的动作序列、主动验证冲突和可行性、精炼问题、执行已验证的动作。这种设计通过引入更长的前瞻性来提升适应性，同时通过验证和精炼而非完全重规划来保证效率。
*   **效果验证**：在相关基准测试上，ELHPlan在达到与当前最优方法相当任务成功率的同时，仅消耗了后者30-40%的令牌量，显著提升了效率-效果前沿。
*   **适用场景**：特别适用于需要多智能体进行复杂、长时域协作的任务，如机器人团队协作、游戏AI等，尤其关注计算资源（如API调用成本）受限的场景。

**3. 《Meissa: Multi-modal Medical Agentic Intelligence》**
*   **核心创新点**：为解决医疗场景中依赖云端大模型API带来的高成本、高延迟和隐私风险问题，提出了一个**轻量级、完全离线的多模态医疗智能体**。其核心创新在于通过**从大模型中蒸馏结构化交互轨迹**，让小模型学会“何时”以及“如何”进行多步交互的策略。
*   **技术细节**：Meissa采用统一的“状态-动作-观察”形式化表示建模交互轨迹，并引入**三层分层监督**机制，让模型根据错误严重程度触发渐进式策略升级。此外，**前瞻-回顾监督**方法通过配对探索性前向轨迹和事后合理化的执行轨迹，稳定地学习有效的交互策略。
*   **效果验证**：仅用少量精选轨迹和小参数规模训练，Meissa在涵盖多个医疗领域的基准评估中，于多个设置上达到或超越了前沿专有代理的性能，同时端到端延迟大幅降低。
*   **适用场景**：非常适合对**数据隐私、服务延迟和部署成本有严格要求的垂直领域**，如医院内部的临床辅助诊断系统、医疗影像分析工作站等。

**方法关系分析**：DIVE侧重于**数据层面**的多样性构建，为训练泛化能力强的智能体提供高质量数据基础；ELHPlan侧重于**推理过程**的效率优化，为智能体在复杂任务中的高效规划提供架构支持；Meissa则侧重于**模型部署**的轻量化与专业化，展示了在特定领域实现高性能离线智能体的路径。三者分别从数据、推理、部署三个维度提供了构建实用智能体的关键解决方案。

### 实践启示

这些研究为大模型应用开发提供了清晰的借鉴路径。**对于追求部署效率的应用**（如多机器人、成本敏感的服务），应重点关注**ELHPlan**这类规划效率优化方法，其“动作链”和验证循环思想可迁移到其他序列决策场景。**对于需要处理复杂、结构化信息或跨环境泛化的应用**（如企业自动化、跨平台工具使用），**DIVE**揭示的任务多样性合成思想和**分层记忆**框架极具参考价值，有助于构建更鲁棒、可解释的智能体。**在专业垂直领域**（如医疗、金融、设计），应优先考虑像**Meissa**这样的轻量级、领域定制化智能体方案，以平衡性能、成本与合规性。

可落地的具体建议包括：1）在开发复杂任务智能体时，采用**模块化、职责分离的架构**，将感知、规划、执行、验证解耦，以提升可解释性和稳定性；2）为智能体设计结构化的记忆或状态表示，将高层意图与底层操作分离，以提升泛化能力；3）高度重视**智能体安全**，在设计之初就考虑对抗性提示注入、工具滥用等风险，并引入输入过滤、沙箱执行等防御层。实现时需注意，这些方法往往需要针对具体任务进行适配和微调，例如定义合适的子目标粒度、设计有效的验证条件或构建高质量的训练数据。最佳实践组合是：利用DIVE的思想构建高质量、高覆盖度的训练数据，采用ELHPlan的规划架构提升执行效率，并借鉴Meissa的轻量化蒸馏策略实现特定领域的低成本、高性能部署。

---

## 📄 论文列表（31 篇）

### [Memory for Autonomous LLM Agents:Mechanisms, Evaluation, and Emerging Frontiers](https://arxiv.org/abs/2603.07670)

**作者**: Du  
**链接**: [arXiv](https://arxiv.org/abs/2603.07670) · [PDF](https://arxiv.org/pdf/2603.07670)  \| [📖 全文分析](paper_2603.07670.md)  
**评分**: 9.07  （novelty: 7.0 | method: 9.5 | evidence: 8.5 | clarity: 9.0）

> 本文是一篇关于大型语言模型（LLM）智能体记忆机制的综述性论文，系统性地梳理了2022年至2026年初该领域的研究进展。论文将智能体记忆形式化为一个与感知和动作紧密耦合的“写入-管理-读取”循环，并提出了一个跨越时间范围、表示基板和策略控制的三维分类法。文章深入探讨了五种核心记忆机制家族，并追踪了评估方法从静态召回基准向多会话智能体测试的转变。论文还综述了记忆作为关键差异化因素的应用领域，并讨论了工程现实与开放挑战。由于作者信息仅为“Du”，未明确来自知名机构，故省略团队背景介绍。


### [Adaptation of Agentic AI: A Survey of Post-Training, Memory, and Skills](https://arxiv.org/abs/2512.16301)

**作者**: Jiang, Lin, Shi, Wang, He 等 34 人  
**链接**: [arXiv](https://arxiv.org/abs/2512.16301) · [PDF](https://arxiv.org/pdf/2512.16301)  \| [📖 全文分析](paper_2512.16301.md)  
**评分**: 8.79  （novelty: 8.0 | method: 9.5 | evidence: 8.5 | clarity: 8.0）

> 本文是一篇关于智能体AI适应性的综述论文，系统性地梳理了大型语言模型智能体在预训练后的适应方法、记忆系统和技能库等研究方向。作者团队来自学术界和工业界，包括多位在AI领域有影响力的研究人员。论文提出了一个统一的四范式框架（A1、A2、T1、T2），将原本碎片化的研究领域（后训练、检索、记忆、技能系统）整合到“适应”这一核心概念下，具有很好的理论整合价值。论文综述全面，框架清晰，对研究现状和未来方向有深刻洞察。


### [DIVE: Scaling Diversity in Agentic Task Synthesis for Generalizable Tool Use](https://arxiv.org/abs/2603.11076)

**作者**: Chen, Zhang, Liu, Chen, Du 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.11076) · [PDF](https://arxiv.org/pdf/2603.11076)  \| [📖 全文分析](paper_2603.11076.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由阿里巴巴团队（作者来自阿里千问等）提出了一种名为DIVE的新方法，用于提升工具使用大语言模型的泛化能力。该方法通过反转任务合成顺序，先执行真实世界工具再反向推导任务，有效解决了任务多样性不足导致的泛化瓶颈。在9个OOD基准测试中平均提升22个点，显著优于现有8B基线模型。


### [A Mathematical Theory of Agency and Intelligence](https://arxiv.org/abs/2602.22519)

**作者**: Hafez, Wei, Pena, Nazeri, Reid  
**链接**: [arXiv](https://arxiv.org/abs/2602.22519) · [PDF](https://arxiv.org/pdf/2602.22519)  \| [📖 全文分析](paper_2602.22519.md)  
**评分**: 8.64  （novelty: 9.5 | method: 9.5 | evidence: 8.0 | clarity: 8.0）

> 本文提出了一种关于智能体与智能的数学理论，引入了'双可预测性'（bipredictability）这一核心概念，从第一性原理推导出其在量子系统、经典系统和引入智能体后的严格数学界限。论文在物理系统（双摆）、强化学习智能体和多轮LLM对话中验证了理论边界，并基于生物系统的丘脑皮质调节机制，提出了一种实时监控双可预测性的反馈架构，为实现自适应、鲁棒的AI奠定了基础。论文理论创新性强，具有跨领域的普适价值。


### [Uncertainty Quantification in LLM Agents: Foundations, Emerging Challenges, and Opportunities](https://arxiv.org/abs/2602.05073)

**作者**: Oh, Park, Kim, Li, Li 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.05073) · [PDF](https://arxiv.org/pdf/2602.05073)  \| [📖 全文分析](paper_2602.05073.md)  
**评分**: 8.64  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.5）

> 本文是一篇关于大语言模型智能体不确定性量化的综述性论文，提出了构建未来智能体不确定性量化研究的三大支柱：基础理论框架、技术挑战识别和未来研究方向。论文首次提出了一个通用的智能体不确定性量化公式，能够涵盖广泛的现有UQ设置，并针对智能体设置识别了四个具体的技术挑战。作者团队包括来自学术界的研究人员，但未明确标注具体机构，因此不进行团队背景介绍。


### [From LLM Reasoning to Autonomous AI Agents: A Comprehensive Review](https://arxiv.org/abs/2504.19678)

**作者**: Ferrag, Tihanyi, Debbah  
**链接**: [arXiv](https://arxiv.org/abs/2504.19678) · [PDF](https://arxiv.org/pdf/2504.19678)  \| [📖 全文分析](paper_2504.19678.md)  
**评分**: 8.57  （novelty: 6.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文是一篇关于大语言模型推理与自主AI代理的综合性综述论文。作者团队中，Merouane Debbah教授是国际知名的通信与AI领域专家，曾任华为法国研究中心主任，现任KAUST教授，在AI与通信交叉领域有重要影响力。论文系统性地整理了2019-2025年间该领域的评估基准、框架和协作协议，提出了统一的分类体系，并展望了未来研究方向。


### [Design Conductor: An agent autonomously builds a 1.5 GHz Linux-capable RISC-V CPU](https://arxiv.org/abs/2603.08716)

**作者**: The Verkor Team, Krishna, Krishna, Chin  
**链接**: [arXiv](https://arxiv.org/abs/2603.08716) · [PDF](https://arxiv.org/pdf/2603.08716)  \| [📖 全文分析](paper_2603.08716.md)  
**评分**: 8.57  （novelty: 9.5 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Design Conductor（DC）的自主智能体，能够端到端地（从概念到可流片的GDSII版图文件）自动构建半导体芯片。在12小时内，DC从一份219字的需求文档出发，完全自主地构建了多个满足1.48GHz时序的完整RISC-V CPU微架构变体（Vercore），其性能（CoreMark 3261）大致相当于2011年的Intel Celeron SU2300。据作者所知，这是首次有自主智能体从规格到GDSII构建出完整、可工作的CPU。论文结构清晰，实验证据充分，展示了前沿模型在复杂硬件设计自动化领域的巨大潜力。


### [EvoScientist: Towards Multi-Agent Evolving AI Scientists for End-to-End Scientific Discovery](https://arxiv.org/abs/2603.08127)

**作者**: Lyu, Zhang, Yi, Zhao, Guo 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.08127) · [PDF](https://arxiv.org/pdf/2603.08127)  \| [📖 全文分析](paper_2603.08127.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为EvoScientist的演化多智能体AI科学家框架，旨在解决现有AI科学家系统在端到端科学发现任务中存在的静态、无法适应历史交互的问题。该框架通过引入持久记忆模块和自我演化机制，持续改进研究策略。实验表明，其在科学想法生成和代码执行成功率方面均优于现有先进系统。作者团队来自多个知名机构，包括但不限于北京大学、清华大学、微软研究院等，显示了强大的跨机构合作背景。


### [Shoot First, Ask Questions Later? Building Rational Agents that Explore and Act Like People](https://arxiv.org/abs/2510.20886)

**作者**: Grand, Pepe, Andreas, Tenenbaum  
**链接**: [arXiv](https://arxiv.org/abs/2510.20886) · [PDF](https://arxiv.org/pdf/2510.20886)  \| [📖 全文分析](paper_2510.20886.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种基于贝叶斯实验设计（BED）的蒙特卡洛推理策略，用于评估和增强语言模型在战略性信息寻求任务中的智能体能力。研究通过协作战舰和猜猜谁两个对话任务，系统比较了LM智能体与人类表现，并展示了所提方法在提升问答准确性、信息增益和任务胜率方面的显著效果。作者团队包括来自认知科学和人工智能领域的知名研究人员，如Joshua B. Tenenbaum（麻省理工学院教授，在计算认知科学领域享有盛誉），表明研究具有坚实的跨学科理论基础。


### [Agentic Neurosymbolic Collaboration for Mathematical Discovery: A Case Study in Combinatorial Design](https://arxiv.org/abs/2603.08322)

**作者**: Xia, Gomes, Selman, Szeider  
**链接**: [arXiv](https://arxiv.org/abs/2603.08322) · [PDF](https://arxiv.org/pdf/2603.08322)  \| [📖 全文分析](paper_2603.08322.md)  
**评分**: 8.50  （novelty: 9.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由康奈尔大学（Cornell University）和维也纳工业大学（TU Wien）的研究团队合作完成，提出了一种基于神经符号推理的人机协作数学发现框架。该研究通过结合大型语言模型（LLM）、符号计算工具和人类战略指导，在组合设计理论中取得了一项新的数学成果——为著名的难题情况 $n \equiv 1 \pmod{3}$ 的拉丁方不平衡性提供了一个紧的下界。研究过程分析深入，实验证据扎实，并进行了形式化验证，展示了神经符号系统在纯数学领域产生真正发现的潜力。


### [Meissa: Multi-modal Medical Agentic Intelligence](https://arxiv.org/abs/2603.09018)

**作者**: Chen, Bai, Pan, Zhou, Yuille  
**链接**: [arXiv](https://arxiv.org/abs/2603.09018) · [PDF](https://arxiv.org/pdf/2603.09018)  \| [📖 全文分析](paper_2603.09018.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.5）

> 本文提出了一种名为Meissa的轻量级多模态医疗智能体系统，旨在解决现有医疗代理系统依赖前沿大模型API所带来的高成本、高延迟和隐私风险问题。Meissa通过从大模型中蒸馏结构化轨迹，学习策略选择和执行，实现了完全离线的医疗智能体能力。在13个医疗基准测试中，Meissa在16个评估场景中的10个场景中达到或超越了前沿专有代理的性能，同时参数量减少了25倍以上，端到端延迟降低了22倍。作者团队来自知名研究机构，其中Alan Yuille教授是计算机视觉和医学影像分析领域的国际知名学者，团队在医学人工智能领域具有深厚的研究背景。


### [CARE: Towards Clinical Accountability in Multi-Modal Medical Reasoning with an Evidence-Grounded Agentic Framework](https://arxiv.org/abs/2603.01607)

**作者**: Du, Wang, Liu, Dvornek, Lu  
**链接**: [arXiv](https://arxiv.org/abs/2603.01607) · [PDF](https://arxiv.org/pdf/2603.01607)  \| [📖 全文分析](paper_2603.01607.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种名为CARE的新型多模态医疗推理框架，旨在通过解耦的专家模块和显式证据机制提升临床可问责性。该方法将医疗视觉问答任务分解为实体提议、区域分割和基于证据的推理三个协调子模块，并引入强化学习优化和智能体协调机制。在标准医疗VQA基准测试中，该方法相比同规模（10B）的SOTA模型提升了10.9%的平均准确率，结合动态规划后进一步超越大规模预训练SOTA模型5.2%。


### [RACAS: Controlling Diverse Robots With a Single Agentic System](https://arxiv.org/abs/2603.05621)

**作者**: Ashley, PrzepiÃ³ra, Chen, Abualsaud, Yesmagambet 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.05621) · [PDF](https://arxiv.org/pdf/2603.05621)  \| [📖 全文分析](paper_2603.05621.md)  
**评分**: 8.50  （novelty: 9.0 | method: 9.5 | evidence: 7.5 | clarity: 8.0）

> 本文由Meta GenAI、斯坦福大学等知名机构的研究团队（作者包括来自Meta的Ashley、Przepióra、Schmidhuber等，以及来自斯坦福大学的Chen、Park等）提出了一种名为RACAS的机器人无关控制代理系统。该系统通过三个基于LLM/VLM的模块（监控器、控制器和记忆策展人）进行纯自然语言通信，实现闭环机器人控制。该方法仅需机器人自然语言描述、可用动作定义和任务规范，无需修改源代码、模型权重或奖励函数即可在不同平台间迁移。在轮式地面机器人、多关节机械臂和水下航行器等异构平台上验证了有效性，展示了智能代理系统在降低机器人原型开发门槛方面的潜力。


### [The Attack and Defense Landscape of Agentic AI: A Comprehensive Survey](https://arxiv.org/abs/2603.11088)

**作者**: Kim, Liu, Wang, Qiu, Li 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.11088) · [PDF](https://arxiv.org/pdf/2603.11088)  \| [📖 全文分析](paper_2603.11088.md)  
**评分**: 8.50  （novelty: 9.0 | method: 9.5 | evidence: 7.0 | clarity: 8.5）

> 本文对AI智能体安全领域进行了首次系统全面的综述，提出了理解AI智能体安全风险和防御策略的系统性框架。论文创新性地分析了AI智能体的设计空间、攻击态势和防御机制，并通过案例研究指出了现有安全防护的不足和开放挑战。作者团队来自学术界，但未明确标注具体机构，因此省略团队背景介绍。


### [Security Considerations for Artificial Intelligence Agents](https://arxiv.org/abs/2603.12230)

**作者**: Li, Zhang, Polley, Ma  
**链接**: [arXiv](https://arxiv.org/abs/2603.12230) · [PDF](https://arxiv.org/pdf/2603.12230)  \| [📖 全文分析](paper_2603.12230.md)  
**评分**: 8.50  （novelty: 6.5 | method: 9.5 | evidence: 7.0 | clarity: 8.5）

> 本文基于Perplexity公司对NIST/CAISI信息请求的回应，系统性地分析了前沿AI智能体的安全考虑。作者团队来自Perplexity公司，该公司运营着被数百万用户和数千家企业使用的通用智能体系统，具有丰富的实践经验。论文从智能体架构改变的核心假设出发，全面识别了攻击面，评估了现有防御措施，并指出了标准和研究空白。内容具有重要的实践指导意义，但作为一份立场文件/回应，其原创性研究贡献有限。


### [ARLArena: A Unified Framework for Stable Agentic Reinforcement Learning](https://arxiv.org/abs/2602.21534)

**作者**: Wang, Zhang, Wang, Shi, Li 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.21534) · [PDF](https://arxiv.org/pdf/2602.21534)  \| [📖 全文分析](paper_2602.21534.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 7.5 | clarity: 8.5）

> 本文提出了一种名为ARLArena的稳定智能体强化学习统一框架，旨在解决当前智能体强化学习训练不稳定的问题。论文首先构建了标准化的测试平台，将策略梯度分解为四个核心设计维度进行分析，并基于此提出了SAMPO稳定智能体策略优化方法。该方法在多种智能体任务中实现了稳定的训练和优异的性能，为构建稳定可复现的基于大语言模型的智能体训练流程提供了实用指导。


### [Towards Autonomous Mathematics Research](https://arxiv.org/abs/2602.10177)

**作者**: Feng, Trinh, Bingham, Hwang, Chervonyi 等 28 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.10177) · [PDF](https://arxiv.org/pdf/2602.10177)  \| [📖 全文分析](paper_2602.10177.md)  
**评分**: 8.36  （novelty: 9.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文由Google DeepMind团队（包括Demis Hassabis、Quoc V. Le等知名AI研究者）提出了一种名为Aletheia的数学研究智能体，该系统能够通过迭代生成、验证和修订自然语言证明，实现从奥数竞赛到博士级数学问题乃至开放数学问题的自主研究。论文展示了AI在数学研究中的多个里程碑成果，包括完全由AI生成的论文、人机协作证明以及自主解决开放问题。方法创新性强，实验证据充分，并开源了相关代码和提示。


### [SoK: Agentic Retrieval-Augmented Generation (RAG): Taxonomy, Architectures, Evaluation, and Research Directions](https://arxiv.org/abs/2603.07379)

**作者**: Mishra, Niroula, Yadav, Thakur, Gyawali 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.07379) · [PDF](https://arxiv.org/pdf/2603.07379)  \| [📖 全文分析](paper_2603.07379.md)  
**评分**: 8.36  （novelty: 9.0 | method: 9.5 | evidence: 6.5 | clarity: 8.5）

> 本文是一篇系统化知识综述论文，首次为Agentic RAG（代理式检索增强生成）系统提供了统一的形式化框架和分类体系。论文将代理式检索-生成循环形式化为有限时域部分可观测马尔可夫决策过程，并在此基础上建立了全面的分类法和模块化架构分解。论文深入分析了传统静态评估方法的局限性，并识别了自主循环中固有的系统性风险，最后提出了关键的研究方向。作者团队未明确标注来自知名机构，因此省略背景介绍。


### [Talk Freely, Execute Strictly: Schema-Gated Agentic AI for Flexible and Reproducible Scientific Workflows](https://arxiv.org/abs/2603.06394)

**作者**: Strickland, Vijeta, Moores, Bodek, Nenchev 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.06394) · [PDF](https://arxiv.org/pdf/2603.06394)  \| [📖 全文分析](paper_2603.06394.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为“模式门控编排”的新架构，用于解决科学工作流中LLM应用的确定性与灵活性之间的权衡问题。论文通过半结构化访谈提炼出核心需求，并系统性地评估了20个现有系统，发现了一个经验性的帕累托前沿。作者团队来自学术界和工业界，包括剑桥大学、葛兰素史克等机构的研究人员，具有跨学科背景。


### [MAS-ZERO: Designing Multi-Agent Systems with Zero Supervision](https://arxiv.org/abs/2505.14996)

**作者**: Ke, Xu, Ming, Nguyen, Chin 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2505.14996) · [PDF](https://arxiv.org/pdf/2505.14996)  \| [📖 全文分析](paper_2505.14996.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为MAS-ZERO的创新框架，旨在实现无需人工监督的多智能体系统自动设计。该框架通过元级设计，在推理时针对每个问题实例迭代地设计、评估和优化多智能体配置，无需验证集，并支持动态问题分解、智能体组合以及在适当时简化系统。实验在推理（数学和研究生级QA）、编码和基于搜索的智能体任务等多个基准测试上进行，使用了不同规模的闭源和开源大语言模型作为骨干，结果表明MAS-ZERO显著优于现有的人工设计和自动多智能体系统基线。


### [ELHPlan: Efficient Long-Horizon Task Planning for Multi-Agent Collaboration](https://arxiv.org/abs/2509.24230)

**作者**: Ling, Wang, Fan, Lam, Hu  
**链接**: [arXiv](https://arxiv.org/abs/2509.24230) · [PDF](https://arxiv.org/pdf/2509.24230)  \| [📖 全文分析](paper_2509.24230.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为ELHPlan的高效长时域任务规划框架，用于解决基于大语言模型的多智能体协作中开放环规划与迭代规划之间的权衡问题。该方法通过引入绑定子目标意图的动作链作为基本规划单元，采用构建、验证、精炼、执行的循环流程，在保持任务成功率的同时显著降低了计算开销。实验表明，该方法在TDW-MAT和C-WAH基准测试中仅需消耗当前最优方法30-40%的令牌量，为LLM驱动的多智能体规划系统建立了新的效率-效果前沿。


### [Enhancing Web Agents with a Hierarchical Memory Tree](https://arxiv.org/abs/2603.07024)

**作者**: Tan, Gao, Wu  
**链接**: [arXiv](https://arxiv.org/abs/2603.07024) · [PDF](https://arxiv.org/pdf/2603.07024)  \| [📖 全文分析](paper_2603.07024.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种用于增强基于大语言模型的网页智能体泛化能力的分层记忆树（HMT）框架。该方法通过三层结构（意图层、阶段层、动作层）将高层任务逻辑与网站特定的动作细节解耦，有效解决了现有扁平化记忆结构在处理未见网站时出现的逻辑不一致问题。实验在Mind2Web和WebArena数据集上进行，结果表明HMT在跨网站和跨领域场景中显著优于现有方法。作者团队未明确标注其所属机构，故省略背景介绍。


### [SynPlanResearch-R1: Encouraging Tool Exploration for Deep Research with Synthetic Plans](https://arxiv.org/abs/2603.07853)

**作者**: Zeng, Li, Gao, Zhang, Pan 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.07853) · [PDF](https://arxiv.org/pdf/2603.07853)  \| [📖 全文分析](paper_2603.07853.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为SynPlanResearch-R1的框架，旨在解决研究型智能体在工具使用探索中的不足。该方法通过合成工具使用轨迹来引导智能体进行更深入的探索，为后续强化学习提供良好的初始化。在七个多跳和开放网络基准测试中，该方法在Qwen3-8B和Qwen3-4B骨干模型上相比现有最佳基线分别取得了高达6.0%和5.8%的性能提升。论文实验设计严谨，代码已开源，验证了方法的有效性。


### [SplitAgent: A Privacy-Preserving Distributed Architecture for Enterprise-Cloud Agent Collaboration](https://arxiv.org/abs/2603.08221)

**作者**: She  
**链接**: [arXiv](https://arxiv.org/abs/2603.08221) · [PDF](https://arxiv.org/pdf/2603.08221)  \| [📖 全文分析](paper_2603.08221.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为SplitAgent的新型分布式架构，旨在解决企业采用云端AI代理时的隐私困境。该架构通过上下文感知的动态脱敏技术，实现了企业端隐私代理与云端推理代理之间的隐私保护协作。论文在创新性、实验设计和实用性方面表现出色，为企业AI应用提供了一条可行的隐私保护路径。


### [Agentic Critical Training](https://arxiv.org/abs/2603.08706)

**作者**: Liu, Liu, Ho, Chakraborty, Wang 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.08706) · [PDF](https://arxiv.org/pdf/2603.08706)  \| [📖 全文分析](paper_2603.08706.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Agentic Critical Training（ACT）的新型强化学习范式，用于训练大型语言模型作为自主智能体。该方法通过让模型在替代行动中识别更优行动并给予判断正确性奖励，驱动模型自主发展对行动质量的推理能力，产生真正的自我反思而非模仿。在三个具有挑战性的智能体基准测试中，ACT与不同后训练方法结合时均能持续提升智能体性能，平均比模仿学习提升5.07分，比强化学习提升4.62分。此外，ACT在智能体任务的分布外泛化能力和通用推理基准上的表现也显示出其方法的广泛价值。


### [Reasoning as Gradient: Scaling MLE Agents Beyond Tree Search](https://arxiv.org/abs/2603.01692)

**作者**: Zhang, Yang, Yang, Xian, Li 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01692) · [PDF](https://arxiv.org/pdf/2603.01692)  \| [📖 全文分析](paper_2603.01692.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文由微软研究团队提出了一种创新的基于LLM的机器学习工程（MLE）智能体方法——Gome。该方法的核心创新在于将结构化诊断推理类比为梯度计算，将成功记忆类比为动量，将多轨迹执行类比为分布式优化，从而将基于梯度的优化范式引入到传统上依赖树搜索（一种无梯度优化）的MLE智能体领域。在MLE-Bench基准测试中，该方法在受限的12小时单GPU预算下取得了35.1%的any-medal率，达到了新的最佳水平。论文通过跨10个模型的缩放实验，揭示了基于梯度的优化与模型推理能力之间的关键交叉点，为未来推理导向型LLM的智能体设计指明了方向。


### [DocSage: An Information Structuring Agent for Multi-Doc Multi-Entity Question Answering](https://arxiv.org/abs/2603.11798)

**作者**: Lin, Zhu, Zhang, Luo, Tang  
**链接**: [arXiv](https://arxiv.org/abs/2603.11798) · [PDF](https://arxiv.org/pdf/2603.11798)  \| [📖 全文分析](paper_2603.11798.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为DocSage的端到端智能体框架，用于解决多文档多实体问答（MDMEQA）任务中的核心挑战。该框架通过动态模式发现、结构化信息提取和模式感知关系推理三个核心模块，有效克服了现有LLM和RAG方法在跨文档逻辑追踪和证据链构建上的不足。在两项MDMEQA基准测试中，DocSage显著优于现有最优的长上下文LLM和RAG系统，准确率提升超过27%。


### [On Information Self-Locking in Reinforcement Learning for Active Reasoning of LLM agents](https://arxiv.org/abs/2603.12109)

**作者**: Zou, Chen, Feng, Li, Li 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.12109) · [PDF](https://arxiv.org/pdf/2603.12109)  \| [📖 全文分析](paper_2603.12109.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文针对强化学习训练大语言模型代理进行主动推理任务时出现的信息自锁现象进行了深入研究，提出了一个理论分析框架和解决方案。论文创新性地将主动推理分解为行动选择和信念跟踪两个核心能力，并揭示了二者与探索不足之间的负反馈循环机制。通过引入易于获取的方向性批评来重新分配学习信号，有效缓解了信息自锁问题，在7个数据集上取得了显著效果提升（最高达60%）。


### [Increasing intelligence in AI agents can worsen collective outcomes](https://arxiv.org/abs/2603.12129)

**作者**: Johnson  
**链接**: [arXiv](https://arxiv.org/abs/2603.12129) · [PDF](https://arxiv.org/pdf/2603.12129)  \| [📖 全文分析](paper_2603.12129.md)  
**评分**: 8.36  （novelty: 9.0 | method: 9.5 | evidence: 7.0 | clarity: 8.0）

> 本文探讨了AI智能体在资源稀缺环境下的集体行为动态，通过实证和数学分析揭示了AI模型多样性、强化学习、部落形成和资源稀缺四个关键变量对系统性能的影响。研究发现，AI智能体的智能化程度提升并不总是带来更好的集体结果，而是取决于容量与人口比例这一关键参数。作者Johnson未标注知名机构，故省略团队背景介绍。


### [Making LLMs Optimize Multi-Scenario CUDA Kernels Like Experts](https://arxiv.org/abs/2603.07169)

**作者**: Han, Guo, Liu, Chen, Hu  
**链接**: [arXiv](https://arxiv.org/abs/2603.07169) · [PDF](https://arxiv.org/pdf/2603.07169)  \| [📖 全文分析](paper_2603.07169.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为CUDAMaster的多智能体、硬件感知的GPU内核优化系统，并构建了多场景基准测试MSKernelBench。该方法在多种算子优化任务中取得了显著性能提升，部分性能甚至超越了高度优化的闭源库。论文实验设计全面，代码开源，具有较高的实用价值。


### [SkillCraft: Can LLM Agents Learn to Use Tools Skillfully?](https://arxiv.org/abs/2603.00718)

**作者**: Chen, Gai, Zhou, Zhang, Zhu 等 16 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.00718) · [PDF](https://arxiv.org/pdf/2603.00718)  \| [📖 全文分析](paper_2603.00718.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了SkillCraft基准测试，旨在评估LLM智能体学习和重用高级工具组合（即技能）的能力。该工作填补了现有基准主要关注静态工具集下实例级成功的空白，通过设计高度组合化的真实场景，并引入轻量级评估协议，使智能体能够自动组合原子工具、缓存和重用技能，显著提升了效率。作者团队来自多个研究机构，包括Chen, Gai, Zhou, Zhang, Zhu, Li, Wang, Wang, Chen, Kaleb, Miao, Gao, Lu, Li, He, Teh等，显示了跨机构的合作研究背景。

