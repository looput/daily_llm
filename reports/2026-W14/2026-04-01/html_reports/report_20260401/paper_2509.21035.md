# CLAUSE: Agentic Neuro-Symbolic Knowledge Graph Reasoning via Dynamic Learnable Context Engineering

**arXiv**: [2509.21035](https://arxiv.org/abs/2509.21035) · [PDF](https://arxiv.org/pdf/2509.21035)  
**领域**: Agent  
**作者**: Zhao, Dai, Zhuo, Xiu, Niyato  
**综合评分**: 8.19  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为CLAUSE的智能神经符号知识图谱推理框架，通过动态可学习上下文工程将上下文构建视为知识图谱上的序列决策过程。该方法在HotpotQA、MetaQA和FactKG等多个数据集上实现了更高的精确匹配率，同时显著降低了子图增长和端到端延迟。作者团队来自学术界，但未明确标注具体知名机构。

---

## 详细分析

> **社区热度**: ⭐ 6 (来自 papers.cool)

## 问题定义

论文将多跳知识图谱问答（KGQA）的**上下文构建过程**本身视为一个**序列决策问题**，目标是在**显式的延迟、成本和准确度约束**下，为每个查询动态地、可学习地决定：

- 在图谱上扩展哪些边、沿哪些路径继续或回退  
- 保留哪些证据、何时停止扩展与整理  

从而取代传统静态 k-hop 扩展或“多想一想”式提示，解决三大痛点：

1. **过检索**：固定 k-hop 会引入大量无关三元组，膨胀提示、抬高延迟。  
2. **不可控的 runtime**：交互步数、边编辑次数、token 用量均缺乏可学习的、带预算的调控手段。  
3. **无法显式权衡**：部署时需要“准、快、省”三者兼顾，但现有方法只能手动调旋钮（hop 深度、degree 上限、top-k），无法按查询粒度自动适应。  

为此，作者提出 **CLAUSE**——一个三智能体神经符号框架，把子图编辑、路径探索、证据筛选统一建模为**带预算的约束马尔可夫决策过程（CMDP）**，并用新算法 **LC-MAPPO** 联合优化准确度与三项资源成本（边编辑数、交互步数、选中 token 数），使得在推理阶段无需重训即可通过“预算帽”或“价格”旋钮实现**可预测的准确度-延迟-成本权衡**。

## 相关工作

论文在第 2 章将相关研究归为 5 条主线，并指出它们与 CLAUSE 的核心差异。以下按 markdown 列表归纳，并给出关键代表工作。

- **符号/神经符号多跳 KGQA**
  - 路径跟随：MINERVA、NeuralLP、TensorLog、RNNLogic  
  - 图感知阅读器：QAGNN、GraftNet、PullNet  
  - 共性：预定义 hop 深度或度阈值，**无显式步数/token 预算**；与 CLAUSE 的“预算感知可学习停止”形成对照。

- **检索增强生成（RAG）与图 RAG**
  - 稠密检索：DPR、Hybrid RAG、LightRAG  
  - 图结构检索：GraphRAG、Think-on-Graph 2.0  
  - 共性：依赖**固定 hop 或手工调度**；未把“子图编辑-路径探索-证据筛选”统一纳入**带约束的序列决策**。

- **链式思维（CoT）与自反思提示**
  - CoT、Self-Ask、Self-RAG、ReAct、Graph-of-Thoughts  
  - 共性：延长每步推理文本，但**不改变可见证据集合**；对端到端延迟仅有间接影响，**无显式步数/价格信号**。

- **智能体 LLM（Agentic LLM）**
  - AutoGen、KG-Agent、Voyager、Reflexion  
  - 共性：多步工具调用带来**隐式高交互成本**；缺乏**按查询的边/步/token 预算**与可学习的停止规则。

- **多智能体强化学习与约束 RL**
  - CTDE 框架：COMA、QMIX、MAPPO  
  - 单惩罚约束：RCPO、Reward-Constrained PPO  
  - 偏好优化：DPO、GRPO（仅静态偏好，无状态转移）  
  - 共性：要么**无多资源约束**，要么用**单一乘子**混合异构成本；均未实现**边、步、token 三通道独立对偶变量**的在线更新。

定位总结  
CLAUSE 首次把“上下文构造”本身视为**可学习的、带多资源预算的序列决策**，用三智能体联合优化子图编辑、路径探索与证据筛选，并通过 LC-MAPPO 实现**显式准确度-延迟-成本权衡**，与上述各线工作形成区别。

## 解决方案

论文将“多跳 KGQA 上下文构造”重新建模为**带预算的序列决策问题**，并给出三层解法：框架、算法、部署机制。要点如下（无第一人称，按 markdown 分点）。

- **问题建模：约束型 MDP**
  - 状态 $s_t = \langle q, G_t, F_t, C_t, \mathbf{b}_t \rangle$：问题、当前子图、前沿、候选池、剩余预算  
  - 动作空间：{EDIT, TRAVERSE, CURATE}，每类再细分 ADD/DELETE/CONTINUE/BACKTRACK/SELECT/STOP  
  - 目标：最大化答案奖励 $R_{\text{acc}}$，同时满足  
    $$ \mathbb{E}[C_{\text{edge}}] \le \beta_{\text{edge}},\quad \mathbb{E}[C_{\text{lat}}] \le \beta_{\text{lat}},\quad \mathbb{E}[C_{\text{tok}}] \le \beta_{\text{tok}}$$

- **框架：CLAUSE 三智能体神经符号控制器**
  1. Subgraph Architect  
     - 可逆、带价格的边编辑；轻量打分器融合实体/关系/邻居/度信号  
     - 采用“增益-价格”规则：仅当 $s(e|q,G_t) - \lambda_{\text{edge}}c_{\text{edge}} > 0$ 且预算足够时才 ADD/DELETE  
  2. Path Navigator  
     - 维护路径前缀，编码〈问题, 当前节点, 候选边, 前缀摘要〉  
     - 输出 {STOP, CONTINUE, BACKTRACK}，每 hop 增加 $C_{\text{lat}}$；继续仅当期望塑形价值高于 $\lambda_{\text{lat}}$  
  3. Context Curator  
     - 对文本化节点/边/路径做**列表式冗余感知重排**，带显式 STOP 头  
     - 优化目标：$\max R_{\text{task}}(S)$ s.t. $\sum_{c\in S}\text{tok}(c)\le \beta_{\text{tok}}$，用 $\lambda_{\text{tok}}$ 决定是否再选一条

- **训练算法：LC-MAPPO**
  - Centralized Training with Decentralized Execution (CTDE)  
    - 中心化 critic 同时估计**任务价值头** $Q_{\text{task}}$ 与**三成本头** $Q_{\text{edge}}, Q_{\text{lat}}, Q_{\text{tok}}$  
  - 单步塑形回报  
    $$ r'_t = r^{\text{acc}}_t - \lambda_{\text{edge}}c^{\text{edge}}_t - \lambda_{\text{lat}}c^{\text{lat}}_t - \lambda_{\text{tok}}c^{\text{tok}}_t $$  
  - 多对偶变量投影上升  
    $$ \lambda_k \leftarrow \Big[\lambda_k + \eta\,(\mathbb{E}[C_k]-\beta_k)\Big]_+,\; k\in\{\text{edge,lat,tok}\} $$  
    支持 PID 稳定；推理时可用硬预算 $(\beta)$ 或价格 $(\lambda)$ 两种模式，无需重训

- **部署控制**
  - 同一套 checkpoint 支持“cap 模式”(硬上限) 与“price 模式”(软权衡)  
  - 每步动作与停止理由均为离散符号，可输出审计轨迹：加了哪条边、走了哪步、为何停

- **效果**
  - 在 HotpotQA、MetaQA、FactKG 上 EM@1 领先，同时**边增长↓、延迟↓、token 量↓**  
  - MetaQA-2hop 相对最强基线 GraphRAG：EM +39.3%，延迟 −18.6%，边增长 −40.9%  
  - 消融实验表明：去掉任一智能体或固定 λ 都会同时损害准确度与效率，验证三组件与对偶更新缺一不可

综上，论文通过“约束 MDP + 三智能体协同 + 多资源对偶上升”把传统静态 k-hop 扩展转化为**可学习的、预算感知的上下文组装流程**，在推理阶段给出**可预测的准确度-延迟-成本权衡**。

## 实验验证

实验围绕“准确度–延迟–成本”三条线展开，覆盖 3 个数据集、3 类基线、4 组消融与 1 个案例可视化。结果均以“单检查点、零重训”方式获得。

1. 主实验：EM@1、延迟、边预算、token 用量  
   数据集：HotpotQA(distractor)、FactKG、MetaQA(1/2/3-hop)  
   基线家族：  
   - 纯 LLM：GPT-OSS-120B、LLaMA3.3-70B、Qwen3-32B  
   - RAG：Vanilla RAG、Hybrid RAG、LightRAG、GraphRAG  
   - 智能体：ReAct、Graph-of-Thoughts、AutoGen、KG-Agent  
   结果：  
   - CLAUSE 在所有设定下 EM@1 最高（HotpotQA 71.7，MetaQA-2hop 87.3，FactKG 84.2）  
   - 延迟归一化后 ≤1.48×，普遍低于同类智能体（AutoGen 2.43×）  
   - 边预算 0.74–0.90×，token 用量低于 RAG/Agent 平均值（图 2）

2. 约束算法对比  
   任务：MetaQA-2hop，固定边预算 0.5、延迟预算 0.7  
   对手：MAPPO、Fixed-Penalty PPO、RCPO  
   指标：可行率、延迟违规率、延迟成本、对偶变量活跃度  
   结果：  
   - 可行率提升 191%（0.340 vs 0.117）  
   - 延迟违规率降低 34%，延迟成本降低 12%  
   - 对偶变量值 0.004，远高于 RCPO 的 0.001，显示多资源独立约束生效

3. 消融实验（表 4）  
   设定：MetaQA-2hop，统一 reader，指标归一化到 CLAUSE=1.0×  
   变体：  
   - w/o Subgraph Architect → EM 74.8，延迟 1.32×，边 1.44×  
   - w/o Path Navigator → EM 82.1，延迟 1.18×，边 1.22×  
   - w/o Context Curator → EM 80.6，延迟 1.24×，边 1.07×  
   - MAPPO(无对偶) → EM 85.0，边 1.28×  
   - 固定 λ → EM 84.6，延迟 1.06×，边 1.15×  
   结论：任一组件或自适应对偶缺失都会同时损害准确度与效率

4. 案例可视化（图 4）  
   问题：“Who co-starred with Brian Backer?”  
   轨迹：  
   - Architect 以 Brian Backer 为锚点，添加电影 Moving Violations 及其 3 位演员，边预算耗尽后 STOP  
   - Navigator 发现 2-hop 共演模式，置信度饱和后 STOP  
   - Curator 在 512 token 预算内仅选 2 条共演证据（36 token）后 STOP  
   端到端延迟 238.6 ms，LLM 给出正确答案 Jennifer Tilly & John Murray

5. 额外灵敏度分析（正文 5.1 末）  
   连续调整 βlat 与 βtok 可得到清晰的 Pareto 前沿：  
   - 固定 token 预算，把资源从“每步推理”转向“更多交互步”可提升 EM  
   - 收紧步数预算几乎不丢准确度，验证 learned STOP 的有效性

## 未来工作

- **动态预算预测**  
  当前需用户显式指定 $(\beta_{\text{edge}}, \beta_{\text{lat}}, \beta_{\text{tok}})$ 或 $(\lambda_{\text{edge}}, \lambda_{\text{lat}}, \lambda_{\text{tok}})$。可训练一个**问题感知预算预测器**，使系统根据查询复杂度、领域或 SLA 自动输出推荐预算，实现“零旋钮”部署。

- **层次化或连续价格调度**  
  实验采用全局静态对偶变量。可引入**逐轮次衰减**或**分层价格**（如实体层、关系层、路径层不同 $\lambda$），让早期探索更激进、后期更保守，进一步压缩延迟。

- **异构图与多模态扩展**  
  现有状态仅包含三元组符号。若将**图像、表格、文本段落**统一为异构节点，Architect 需扩展跨模态可逆编辑动作；Navigator 需定义跨模态相似度，Curator 需处理混合模态 token 计数，可验证框架在富媒体问答上的通用性。

- **在线持续学习**  
  目前训练完成后对偶固定。可探索**在线 dual-ascent** 或**元学习初始化**，使系统在真实流量中持续更新 $\lambda$，自动适应概念漂移或硬件成本变化，而无需重训策略网络。

- **可解释性增强**  
  轨迹仅提供离散动作序列。可加入**事后因果归因**（如 edge-Shapley、step-Shapley）或**反事实解释**：“若预算减少 10%，哪条边/步会被首先舍弃？” 帮助运维人员审计与调试。

- **更细粒度资源模型**  
  实际部署中，**API 调用费、GPU 显存、缓存命中率**均影响总成本。可将这些真实计量纳入 $C_{\text{edge}}, C_{\text{lat}}, C_{\text{tok}}$ 的即时成本信号，训练**硬件感知策略**，实现云账单直接优化。

- **与其他 RL 范式结合**  
  LC-MAPPO 采用 CTDE 与对偶上升。可尝试：  
  - **分层 RL**（高层发预算，底层执行）  
  - **离线约束 RL**（利用历史日志不额外交互）  
  - **人类偏好注入**（用 DPO-style 正则项替代原始 $R_{\text{acc}}$）  
  比较它们在样本效率与约束满足上的差异。

- **跨语言与低资源场景**  
  当前实体链接与打分依赖英文表面形式。若将**多语言实体编码**或**音译别名**引入 $\phi_{\text{ent}}$，并在低资源语言上测试，可验证预算机制是否仍能抑制过检索，避免语言漂移导致的成本爆炸。

- **对抗鲁棒性**  
  图中可能存在**对抗边**或**虚假关系**。可构建**对抗 CMDP**：对手在预算内注入最坏情况边，主智能体需学习鲁棒策略；研究 $\lambda$ 的在线调整能否自动“降价”屏蔽恶意边。

- **开源与基准**  
  发布含**可复现预算接口**的端到端 pipeline，并建立**准确度-延迟-成本三维排行榜**，推动社区在相同硬预算下比较新方法，形成面向“经济高效 QA”的新基准。

## 总结

- **问题**  
  多跳知识图谱问答需在**严格延迟与 token 成本**下提供高准确答案；静态 k-hop 扩展或“多想一想”提示会过检索、膨胀上下文，且缺乏可学习的预算控制。

- **思路**  
  把“上下文组装”本身视为**序列决策**：每步决定加/删哪条边、沿哪条路径继续或回退、保留哪段证据、何时停止，并在** episode 级对边编辑数、交互步数、选中 token 数**设显式预算或价格。

- **方法：CLAUSE 框架**  
  1. Subgraph Architect —— 可逆、价格感知的边编辑，增益>价格才执行  
  2. Path Navigator —— 预算内探索/回退/停止，输出人类可读路径  
  3. Context Curator —— 列表式冗余感知重排，带 learned STOP，硬限 token  
  三智能体用 **LC-MAPPO** 联合训练：中心化 critic 估计任务价值与三成本头，单步回报按 Lagrangian 塑形，多对偶变量在线更新；推理时同一 checkpoint 支持硬预算或平滑价格两种模式，无需重训。

- **结果**  
  HotpotQA、MetaQA、FactKG 上 EM@1 全面领先，同时边增长 ↓18–26%、延迟 ↓10–35%、token 用量低于 RAG/Agent 平均值；约束消融显示缺任一组件或固定对偶均显著掉点；案例给出可审计轨迹与 238 ms 端到端延迟。

- **贡献**  
  1. 将 KGQA 重新表述为**预算驱动的可学习上下文构造**  
  2. 提出三智能体神经符号控制器 CLAUSE，替代 k-hop/Top-k 启发  
  3. 设计 LC-MAPPO 算法，实现多资源独立对偶上升与去中心化执行  
  4. 实验验证在**同等或更低成本**下获得更高准确度与可预测 runtime，提供部署级“准-快-省”旋钮。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
