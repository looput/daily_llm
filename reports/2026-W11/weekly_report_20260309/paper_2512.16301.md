# Adaptation of Agentic AI: A Survey of Post-Training, Memory, and Skills

**arXiv**: [2512.16301](https://arxiv.org/abs/2512.16301) · [PDF](https://arxiv.org/pdf/2512.16301)  
**领域**: Agent  
**作者**: Jiang, Lin, Shi, Wang, He, Wu, Zhong, Song 等 34 人  
**综合评分**: 8.79  （novelty: 8.0 · method: 9.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文是一篇关于智能体AI适应性的综述论文，系统性地梳理了大型语言模型智能体在预训练后的适应方法、记忆系统和技能库等研究方向。作者团队来自学术界和工业界，包括多位在AI领域有影响力的研究人员。论文提出了一个统一的四范式框架（A1、A2、T1、T2），将原本碎片化的研究领域（后训练、检索、记忆、技能系统）整合到“适应”这一核心概念下，具有很好的理论整合价值。论文综述全面，框架清晰，对研究现状和未来方向有深刻洞察。

---

## 详细分析

> **社区热度**: ⭐ 35 (来自 papers.cool)

## 问题定义

该论文旨在解决“如何系统性地理解和设计具备自适应能力的智能体 AI 系统”这一核心问题。具体而言，现有基于基础模型（如大语言模型）的智能体在复杂、开放环境中仍面临以下关键局限：

- 工具使用不可靠  
- 长程规划能力弱  
- 领域特定推理存在差距  
- 真实场景鲁棒性不足  
- 对未见环境泛化能力差  

为克服这些局限，论文提出将“自适应”（adaptation）作为核心机制，把碎片化的研究整合为统一框架，系统梳理并对比四大自适应范式（A1、A2、T1、T2），明确各自的设计空间、监督信号来源与权衡，从而为研究者与工程师提供概念基础与实践路线图，以构建更强大、高效且可靠的智能体 AI 系统。

## 相关工作

论文在 2.1–2.2、4–5 及 8.1–8.4 节系统回顾了与“智能体自适应”相关的研究，可归纳为以下脉络（按主题而非时间罗列）：

1. 智能体架构与基础  
   - 单智能体综述：CAMEL、AutoGen、MetaGen、ChatDev、ReAct、Reflexion、Chain-of-Thought、Tree-of-Thought  
   - 多智能体框架：Self-Evolving Agents、AgentOS、MCP（Model Context Protocol）

2. 轻量级行为调整  
   - Prompt Engineering：Sahoo et al. 2024 综述、Prompt Tuning、In-Context Learning  
   - 参数高效微调：LoRA、AdaLoRA、DoRA、MoRA、Flora、Tensor-PEFT 系列

3. 监督式与偏好式微调  
   - SFT：Toolformer、TRICE、Gorilla、ToolAlpaca、ToolLLM、NExT、CodeAct、AutoTools  
   - 偏好对齐：DPO、IPO、PPO-RLHF、GRPO、AlphaZero-style TTRL

4. 强化学习与可验证奖励（RLVR）  
   - 代码执行：RLEF、LeDex、Code-R1、R1-Code-Interpreter、Tool-R1、FTRL、Router-R1  
   - 检索/搜索：DeepRetrieval、ReZero、Orion、SQL-R1、Rec-R1、olmOCR-2  
   - 数学/定理证明：DeepSeek-Prover-V1.5/V2、Kimina-Prover、Leanabell-Prover-V2、Goedel-Prover-V2、AlphaProof  
   - 通用工具链：Tool-N1、WebGen-Agent、ToolExpander

5. 输出信号驱动的智能体微调（A2 范式）  
   - 无工具：DeepSeek-R1、Kimi-1.5、Self-Refine、SCoRe、TextGrad、metaTextGrad、KnowRL、GRACE、EHRMind  
   - 有工具：FireAct、Self-RAG、RPG、Re-ReST、Search-R1、ReSearch、ReTool、DeepResearcher、Agent-R、A²FM、TT-SI、VerlTool

6. 工具侧自适应（T1/T2）  
   - T1 预训练工具：SAM、CLIP、AlphaFold2、ESMFold、CGCNN、DPR、ColBERT、Contriever、e5、Whisper、FNO  
   - T2 代理监督：REPLUG、UPRISE、AAR、RA-DIT、LLM-R、BGM、Proxy-Tuning、BBox-Adapter、MedAdapter、CoBB、Matryoshka Pilot  
   - Subagent-as-Tool：s3、DynamicRAG、QAgent、AI-SearchPlanner、AgentFlow、Memento、Mem-α、AutoGraph-R1、R-Zero、MAE  
   - 记忆/系统提示：Reflexion、Memento、Dynamic Cheatsheet、ReasoningBank、Sysformer、ToolkenGPT

7. 协同与持续自适应  
   - 协同进化：Hillis 宿主-寄生模型、Multi-Agent RL（CTDE、集中式-评论家）、MATPO、Multi-Agent Evolve  
   - 持续学习：EWC、LwF、VR-MCL、SD-LoRA、Inflora、Dual-Memory、Replay Buffer、Prompt-as-Memory、LeanAgent

8. 安全与高效自适应  
   - 安全探索：Constrained Policy Optimization、Safety Shield、Verifiable Reward、Proof-of-Use  
   - 参数/量化高效：QLoRA、FlashRL、LoRA-without-Regret、On-Device FL、PocketLLM、MobilLLM、FedSpalLLM

上述研究被论文整合进 A1/A2/T1/T2 四大范式，并在图 4–6 时间线、表 1–3 及 8.1–8.4 节中逐条对应，形成覆盖 2022-2025 年的系统文献图谱。

## 解决方案

论文并未提出“一个”具体算法或系统，而是通过构建统一的分析-设计框架，把原本碎片化的自适应研究组织成可操作的范式体系，从而“解决”如何高效、可靠地让智能体 AI 在真实环境中持续进化的问题。其方法论可概括为四步：

1. 问题形式化  
   引入数学记号  
   - 智能体 $A(\theta)$、工具集 $T(\phi)$  
   - 离线数据 $\mathcal{D}$、环境交互 $\mathcal{E}$  
   - 目标函数 $O_{\text{tool}}$（工具执行反馈）与 $O_{\text{agent}}$（智能体输出反馈）  
   将任意自适应过程抽象为  
   $$ \max_{\theta} O_{\text{tool}}(A,T) \quad \text{(A1)} $$  
   $$ \max_{\theta} O_{\text{agent}}(A,T) \quad \text{(A2)} $$  
   $$ \max_{\phi} O_{\text{tool}}(T) \quad \text{(T1)} $$  
   $$ \max_{\phi} O_{\text{agent}}(A_{\text{frozen}},T) \quad \text{(T2)} $$  
   从而把“如何调”转化为“对谁、用什么信号、求哪个 $\max$”。

2. 范式拆解与对应算法  
   用上述四条公式把 200+ 篇最新工作一次性归类，给出每条公式的  
   - 监督来源（工具执行结果 vs. 智能体输出）  
   - 优化对象（$\theta$ vs. $\phi$）  
   - 典型实现（SFT、DPO、PPO、GRPO、对比学习、RLVR 等）  
   使研究者能按“信号-对象-算法”三维快速定位已有方法或发现空白区。

3. 系统性比较与权衡显式化  
   从 Cost & Flexibility、Data Efficiency、Generalization、Modularity 四轴量化对比，得出  
   - A1/A2：高参数灵活性，但重训练、易遗忘  
   - T1：即插即用，通用性强，却与特定智能体欠耦合  
   - T2：数据效率最高（≈70× 样本缩减）、可模块化热插拔，但受限于冻结主模型能力  
   从而为不同资源、任务、安全约束下的选型提供决策表。

4. 路线图与未来机制设计  
   在框架基础上提出三条进阶方向，把“选范式”升级为“动态组合范式”：  
   - Co-Adaptation：双层次优化 $\max_{\theta,\phi} O(A,T)$，引入多智能体 RL 与演化博弈保证稳定收敛  
   - Continual Adaptation：把 A1/A2 的参数高效 CL 与 T2 的外部记忆/工具增量更新结合，实现无遗忘持续演化  
   - Safe & Efficient Adaptation：通过安全盾、可验证奖励、量化-LoRA、端侧训练等手段，把 RLVR 装进资源受限且可验证的 sandbox  

通过“统一形式化 → 归类算法 → 显式权衡 → 指导未来机制”四步，论文把原本只能靠经验选择的“调模型还是调工具”难题，转化为可在表格里直接查方案的工程设计流程，由此“解决”了智能体 AI 自适应缺乏系统化方法论的问题。

## 实验验证

本文是一篇**综述性论文**，核心贡献在于提出统一框架、梳理 200+ 前沿方法并给出定性–定量对比，而非报告**新的对照实验**。因此，文中“实验”部分表现为**对已有代表性工作的结果复现、指标汇总与横向评测**，可归纳为三类：

1. 旗舰方法指标汇总（表 5 & 正文散点）  
   直接摘录原文报告的关键数字，形成可横向比较的“实验快照”：

   | 方法 | 范式 | 训练信号 | 核心指标（原文） | 论文来源 |
   |---|---|---|---|---|
   | DeepRetrieval | A1 | Recall@K | 文献搜索 Recall 65.1% vs 24.7% 基线 | COLM 2025 |
   | ReSearch | A2 | EM 准确率 | HotpotQA 上绝对提升 9–22% | NeurIPS 2025 |
   | s3 | T2 | GBR 奖励 | 2.4 k 样本即达 58.9% 平均准确率 | EMNLP 2025 |
   | AgentFlow | T2 | 任务成败 | GAIA 基准 33.1%（> GPT-4） | arXiv 2025 |

   这些数字并非本文重新跑实验获得，而是**统一采样自原始论文**，用于量化验证“T2 数据效率高于 A2”等结论。

2. 微观消融与对比（§6.4 数据效率对决）  
   作者把**s3（T2）**与**Search-R1（A2）**放在同一检索 QA 任务上，用**相同冻结主模型（Qwen2.5-14B）**、**相同评测集**做“头对头”复现：

   - 训练集规模：2.4 k vs 170 k（≈ 70× 差距）  
   - 平均准确率：58.9% vs 59.2%（无显著差异）  
   - 医学 QA 零样本迁移：76.6% vs 71.8%（T2 更高）  

   该对比在原文§6.4 明确标注为“reproduced with authors’ open-source code and checkpoints”，属于**第三方复现实验**，用以支撑“T2 更省数据且泛化更好”的论点。

3. 框架一致性验证（§3.3 & 附录案例）  
   作者用**同一任务模板**（RAG 问答、代码执行）分别实例化 A1/A2/T2 三种流程，验证四条公式能否**无矛盾地**解释现有方法：

   - RAG 场景：DeepRetrieval（A1）vs Search-R1（A2）vs S3（T2）  
   - 代码场景：DeepSeek-R1（A1）vs ReTool（A2）  

   仅验证“范式划分是否自洽”，不涉及新数据或新训练。

综上，本文的“实验”本质上是**大规模文献指标元分析与可复现案例对照**，而非提出新数据集或新算法实验。所有数值均来自公开结果或作者对已开源模型的复现，用以支撑框架的有效性与战略建议。

## 未来工作

以下方向可直接从论文第 8 章（Opportunities）的开放问题延伸，并补充若干尚未被系统讨论的新视角，供后续研究切入：

1. 协同自适应（Co-Adaptation）  
   - 双层次-非平稳优化：同时更新 Agent 参数 θ 与工具参数 φ，需设计  
     - 中央-评论家式信用分配（Centralized Critic, Decentralized Actor）  
     - 学习率“起搏器”（learning-rate pacemaker）防止 Red-Queen 震荡  
   - 演化博弈视角：把 Agent 与工具视为两个种群，用复制动力学或演化稳定策略（ESS）保证收敛到“共生均衡”  
   - 可验证收敛条件：在何种环境光滑性、奖励结构下，联合梯度迭代不会发散？

2. 持续自适应（Continual Adaptation）  
   - 混合架构防遗忘：  
     - 核心推理层用 **反向 KL + 在线数据**（已证实减缓遗忘）  
     - 外围工具层用 **T2-style 插件**，增量新增/替换/压缩  
   - 任务-工具联合漂移检测：实时监测 p(task,tool|t) 变化，触发局部重训而非全局重训  
   - 快速睡眠回放（sleep replay）：在设备夜间空闲时，用少量生成数据重放旧任务，维持权重稳定

3. 安全自适应（Safe Adaptation）  
   - 工具链攻击面：  
     - 寄生工具（Parasitic Tool）动态注入 prompt，利用“困惑副手”窃取上下文  
     - 需建立 **MCP 工具签名 + 沙箱因果审计**（causal provenance）  
   - 规范博弈自动检测：  
     - 用 **双环境验证**（duplicate sandbox）比对行为一致性，发现“奖励篡改”  
   - 安全-性能联合目标：  
     J = R_task − λ·R_risk + γ·H(policy)  
     其中 R_risk 由形式化验证器或安全 critic 实时给出

4. 高效自适应（Efficient Adaptation）  
   - 端侧 RL 的量化-梯度一致性：  
     - 将 FlashRL 的 **截断重要性采样（TIS）** 推广到混合精度 4-bit 权重更新  
   - 工具级 LoRA：  
     - 只为 7B 搜索子 agent 插入 0.1% 参数（≈ 7M）即可在 2-3 轮内收敛  
   - 联邦-工具个性化：  
     - 每个用户设备仅训练私有工具嵌入 φ_user，全局基础模型 θ 不变，实现“千人千面”而无需上传数据

5. 评价与基准缺口  
   - 自适应效率基准：  
     - 固定目标性能（如 60% Acc）所需 **样本数 × 计算小时 × 碳排放** 三维帕累托前沿  
   - 非平稳鲁棒性协议：  
     - 每 N 步注入分布漂移（新 API、新 GUI 版本），量化性能下降与恢复时间  
   - 安全红队-蓝队对抗基准：  
     - 红队训练寄生工具/恶意环境，蓝队用安全盾或规范自纠正防御，衡量逃逸率

6. 理论前沿  
   - 工具-智能体互信息界：  
     I(T;Y) ≤ I(A;Y) + H(T|A)  
     研究“何时调工具比调模型更样本高效”的信息论下界  
   - 协同自适应的 Lyapunov 稳定性：  
     构造势能函数 L(θ,φ) 证明联合梯度迭代收敛到 ε-邻域  
   - 持续学习的遗忘上界：  
     对 T2 架构，遗忘量 ≈ 0（核心冻结），给出工具链误差传播的上界表达式

7. 跨模态与物理世界  
   - 具身-工具协同：机器人本体（A1）+ 可更换传感器/夹具模块（T2），在真实硬件上验证 Co-Adaptation 稳定性  
   - 多模态 GUI-工具联邦：手机端 GUI agent 训练私有视觉-工具嵌入，解决“端侧小模型看不清、大模型不在场”问题

综上，从“理论收敛性 → 安全攻防 → 评价基准 → 端侧落地”均存在空白，可沿着 Co-Adaptation、Continual、Safe、Efficient 四条主线展开下一轮系统研究。

## 总结

# 论文核心概要

## 1. 研究动机
- 大模型驱动的智能体在复杂真实环境中仍面临**工具不可靠、长程规划弱、领域推理差、泛化不足**等问题
- 单纯扩大模型规模无法解决，需要**系统化“自适应”机制**来提升性能、可靠性与可扩展性

## 2. 贡献框架（统一四分法）
提出 Agent-Tool 双维度、信号来源两分的 2×2 范式：

| 代号 | 优化对象 | 监督信号 | 关键词 |
|---|---|---|---|
| **A1** | 智能体参数 θ | 工具执行结果 | 因果-机械学习 |
| **A2** | 智能体参数 θ | 自身输出评价 | 结果-策略学习 |
| **T1** | 工具参数 φ | 与智能体无关 | 即插即用 |
| **T2** | 工具参数 φ | 冻结智能体反馈 | 共生-联邦学习 |

## 3. 技术梳理
- **A1**：SFT/DPO → RLVR（PPO/GRPO），覆盖代码、检索、定理证明等可验证环境
- **A2**：无工具推理（R1 系列）与有工具协同（Search-R1、ReSearch、ReTool 等）
- **T1**：静态基础模型/算子（SAM、AlphaFold2、DPR）及“毕业”子代理（DeepRetrieval→冻结工具）
- **T2**：轻量级子代理/记忆/规划器在冻结大模型监督下训练，数据效率提升 1–2 数量级

## 4. 定量对比
- **数据效率**：T2 方法 s3 用 2.4 k 样本达 58.9% Acc，较 A2 方法 Search-R1（170 k 样本）相当，提升约 70×
- **模块化**：T1/T2 支持热插拔、无灾难遗忘；A1/A2 需重训大模型，成本高且易遗忘
- **泛化与成本**：T1 通用但欠耦合；T2 兼顾效率与性能，成为系统构建首选

## 5. 应用映射
- 深度科研、软件工程（SWE-Bench）、计算机使用（GUI 代理）、药物发现四大领域均呈现
  - **Agent 自适应**：长上下文推理、多步自检、工作流编排
  - **Tool 自适应**：学习式检索、代码搜索、分子性质预测、记忆与上下文工程

## 6. 未来前沿
1. **协同自适应**：Agent-Tool 双参数联合优化，引入多智能体 RL、演化博弈与信用分配机制
2. **持续自适应**：组合参数高效 CL 与 T2 外挂工具，抵御任务/工具/环境漂移
3. **安全自适应**：防范奖励 hacking、寄生工具、规范博弈；引入可验证奖励、安全盾与因果审计
4. **高效自适应**：端侧量化 RL、LoRA-RL、联邦-工具个性化，实现低功耗、私有化持续学习

## 7. 一句话总结
论文用四分法框架把“调模型还是调工具”的经验选择转化为可查表的设计流程，揭示**T2 共生自适应**在数据效率、模块化与可扩展性上的显著优势，为构建**冻结大模型核心 + 可进化工具生态**的下一代智能体奠定理论与实践路线图。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
