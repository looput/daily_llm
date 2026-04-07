# SKILL0: In-Context Agentic Reinforcement Learning for Skill Internalization

**arXiv**: [2604.02268](https://arxiv.org/abs/2604.02268) · [PDF](https://arxiv.org/pdf/2604.02268)  
**领域**: Agent  
**作者**: Lu, Yao, Wu, Han, Gu, Cai, Lu, Xiao 等 10 人  
**综合评分**: 8.51  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 7.5）

---

## 摘要

> 本文由浙江大学REAL实验室团队提出了一种名为SKILL0的上下文强化学习框架，旨在实现技能内化，使LLM智能体能够将外部技能知识内部化为模型参数，从而实现零样本自主行为。该方法在ALFWorld和Search-QA等智能体任务上取得了显著性能提升（分别提升9.7%和6.6%），同时保持了高效的上下文使用（每步少于0.5k token）。论文创新性强，实验设计充分，代码已开源。

---

## 详细分析

> **社区热度**: ⭐ 16 (来自 papers.cool)

## 问题定义

这篇论文试图解决**技能增强型LLM智能体在推理阶段对外部技能库的依赖问题**。

具体而言，现有范式采用**推理时技能增强**（inference-time skill augmentation）：在每一步决策时从外部技能库检索相关技能并注入提示上下文。论文指出该范式存在三个根本性局限：

1. **检索噪声**：无关或误导性的技能指导会腐蚀智能体的决策上下文；
2. **token开销**：注入的技能内容在多轮交互中产生累积性计算成本，限制可扩展性；
3. **知识外置**：模型仅"执行"上下文中的技能描述，而非真正"习得"技能——能力 residing in the context, not in the model。

针对上述局限，论文提出核心研究问题：**能否将技能内化（internalize）到模型参数中，实现无需运行时检索的零样本自主行为？**

为此，论文提出**SKILL0**框架，通过**上下文强化学习**（In-Context Reinforcement Learning）和**动态课程学习**（Dynamic Curriculum），在训练阶段以渐进式撤销的方式提供技能上下文，迫使策略将外部程序性知识转化为内在能力，最终在推理阶段实现完全零技能依赖的自主决策。

## 相关工作

该论文的相关研究主要分为以下两个方向：

### 2.1 LLM Agents（大型语言模型智能体）

近期研究通过指令调优的LLM，使自主智能体能够在广泛的动态开放世界环境中运行，包括：
- **代码生成**（Jimenez et al., 2023; Wang et al., 2026a）
- **GUI自动化**（Ye et al., 2025; Liu et al., 2026b）
- **游戏**（Shridhar et al., 2020）
- **具身控制**（Wang et al., 2023）

随着面向LLM的强化学习技术兴起（Yu et al., 2025; Zheng et al., 2025; Yao et al., 2026b; Chen et al., 2026），**Agentic RL**已成为为LLM智能体配备鲁棒决策能力的关键后训练方法（Lu et al., 2026, 2025; Feng et al., 2025）。

### 2.2 Agentic Skills（智能体技能）

**早期记忆方法**：直接将原始轨迹存储到外部数据库中，作为经验回放的参考（Zhao et al., 2024; Shinn et al., 2024）。然而，这类原始轨迹往往冗长、冗余且嘈杂，直接注入上下文窗口的效率低下（Chhikara et al., 2025）。

**基于技能的抽象**：
- **技能定义**：作为从历史轨迹中提炼的可重用、抽象化、结构化的行为原语（Xu & Yan, 2026; Li et al., 2026a; He et al., 2026）
- **情景记忆**：技能作为智能体在决策时可咨询的情景记忆形式（Li et al., 2026b; Liu et al., 2026a; Liang et al., 2026）
- **RL中的技能应用**：研究表明技能能在强化学习框架内提供高效指导（Xia et al., 2026; Wang et al., 2025a; Jiao et al., 2026）

**现有局限**：尽管上述进展在技能提取、组织和检索方面取得成效，**但技能能否被内化到模型参数中这一问题仍未被充分探索**。

### 对比基线方法（实验部分）

论文在实验中还与以下具体方法进行了对比：
- **提示式方法**：ReAct（Yao et al., 2022）、Reflexion（Shinn et al., 2024）
- **记忆增强学习**：Mem0（Chhikara et al., 2025）、ExpeL（Zhao et al., 2024）、MemP（Fang et al., 2025）、MemRL（Zhang et al., 2026）、SimpleMem（Liu et al., 2026c）
- **技能增强RL**：SkillRL（Xia et al., 2026）
- **视觉上下文压缩**：AgentOCR（Feng et al., 2026）
- **搜索增强QA**：Search-o1、Search-R1（Jin et al., 2025）、ZeroSearch（Sun et al., 2025）、StepSearch（Wang et al., 2025b）

## 解决方案

论文通过提出 **SKILL0** 框架解决技能内化问题，核心方法论包含以下三个关键组件：

## 1. 上下文强化学习（In-Context Reinforcement Learning, ICRL）

ICRL 通过"训练时提供技能上下文，推理时完全移除"的范式，强制策略将外部知识转化为内在能力：

- **训练阶段**：智能体在策略 $\pi_\theta(a_t, c_t \mid I, V_t)$ 中接收视觉化的技能上下文 $V_t = \text{Enc}(h_t, S; c_t)$，其中 $S$ 为选中的技能子集，$c_t$ 为自生成的压缩比率
- **推理阶段**：完全零技能依赖（$S = \emptyset$），实现零样本自主决策

为激励高效压缩与技能内化，采用复合奖励函数：

$$
\tilde{r}_t = r_t + \lambda \cdot r_t^{\text{comp}}, \quad r_t^{\text{comp}} = 
\begin{cases} 
\ln(c_t), & \text{if } I_{\text{succ}}(\tau) = 1 \\
0, & \text{otherwise}
\end{cases}
$$

训练目标采用带有重要性采样的 PPO 变体：

$$
\mathcal{L}_{\text{SKILL0}}(\theta) = \mathbb{E}_{\tau_i \sim \pi_{\theta_{\text{old}}}(q), q \sim \mathcal{D}} \left[ \frac{1}{G} \sum_{i=1}^{G} \frac{1}{|\tau_i|} \sum_{t=1}^{|\tau_i|} \text{clip}(r_{i,t}(\theta), A_i, \epsilon) - \beta \cdot D_{\text{KL}}[\pi_\theta \| \pi_{\text{ref}}] \right]
$$

## 2. 动态课程学习（Dynamic Curriculum）

通过两阶段策略实现从"依赖技能"到"自主执行"的平滑过渡：

### (a) 离线相关性驱动的技能分组（Relevance-Driven Skill Grouping）
- 按任务类别将技能库 $\text{SkillBank} = \{S_k\}_{k=1}^N$ 划分为 $N$ 个验证子任务 $\{T_k\}_{k=1}^N$
- 每个技能文件 $S_k$ 对应专属验证集 $T_k$，用于评估其对该任务类别的效用

### (b) 在线帮助性驱动的动态课程（Helpfulness-Driven Dynamic Curriculum）
将训练过程分为 $N_S$ 个阶段，技能预算 $M(s)$ 线性衰减：

$$
|S(s)| \leq M(s) = \left\lceil N \cdot \frac{N_S - s}{N_S - 1} \right\rceil
$$

每 $d$ 步执行以下三步骤：
1. **帮助性评估**：计算 $\Delta_k = \text{Acc}_k^{\text{w/ skill}} - \text{Acc}_k^{\text{w/o skill}}$，量化策略对 $S_k$ 的依赖程度
2. **过滤与排序**：仅保留 $\Delta_k > 0$ 的技能，并按帮助性降序排列
3. **预算约束选择**：选取前 $M(s)$ 个技能，直至最终阶段 $M(N_S) = 0$

## 3. 视觉上下文渲染（Visual Context Rendering）

为解决技能与历史交互的 token 开销问题，引入光学压缩机制：

- 将文本交互历史 $h_t$ 和技能 $S$ 渲染为紧凑 RGB 图像
- 通过视觉编码器压缩为嵌入 $V_t \in \mathbb{R}^d$，每步 token 成本降至 0.5k 以下
- 策略同时输出动作 $a_t$ 和下一步压缩比率 $c_t$，实现自适应上下文管理

通过这种"渐进式撤离"（progressive withdrawal）机制，SKILL0 确保策略分布 $\pi_\theta(\cdot \mid I, V_t^{(s)})$ 随阶段 $s$ 平滑演化，避免上下文空间的突变分布偏移，最终实现知识从上下文到参数的完全内化。

## 实验验证

论文在 **ALFWorld** 和 **Search-based QA** 两个基准测试上进行了 extensive experiments，涵盖性能对比、训练动态分析与消融研究。

### 1. 实验设置

**基准测试**
- **ALFWorld**：基于文本的具身AI环境，包含 3,827 个任务实例，涵盖 6 类家庭活动（Pick and Place, Look at Obj in Light, Pick Clean then Place, Pick Heat then Place, Pick Cool then Place, Pick Two Obj and Place）
- **Search-based QA**：包含单跳 QA（NQ, TriviaQA, PopQA）和多跳 QA（HotpotQA, 2Wiki, MuSiQue, Bamboogle）

**对比基线**
- **基础模型**：Qwen2.5-(VL)-3B/7B-Instruct 的 Zero-Shot 与 Few-Shot 设置
- **RL 方法**：GRPO、AgentOCR（视觉上下文压缩）、EvolveR
- **技能增强方法**：SkillRL（推理时注入技能）
- **其他方法**：ReAct、Reflexion、Mem0、ExpeL、MemP、MemRL、SimpleMem（ALFWorld）；Search-o1、Search-R1、ZeroSearch、StepSearch（Search-QA）

**实现细节**
- 使用 Qwen2.5-VL 系列模型，在 4×H800 GPU 上训练最多 180 步
- 课程阶段数 $N_S = 3$，验证间隔 $d = 10$
- 视觉编码压缩率 $c_t$ 由策略自生成，平衡效率与性能

### 2. 主要结果

**任务性能**（见 Table 1）

| 方法 | ALFWorld (3B) | ALFWorld (7B) | Search-QA (3B) | Search-QA (7B) |
|------|---------------|---------------|----------------|----------------|
| AgentOCR | 78.2 | 81.2 | 34.2 | 40.1 |
| SkillRL | 82.4 | 89.9 | 38.9 | 47.1 |
| **SKILL0** | **87.9** | **89.8** | **40.8** | **44.4** |

- 在 **ALFWorld** 上，SKILL0 (3B) 较 AgentOCR 提升 **+9.7%**，较 GRPO 提升 **+8.0%**；7B 模型达到 89.8%，超越所有对比基线
- 在 **Search-QA** 上，SKILL0 (3B) 较 AgentOCR 提升 **+6.6%**，在 Bamboogle（分布外）上达到 63.7%，显著优于 SkillRL 的 58.1%
- 与推理时技能增强的 SkillRL 相比，SKILL0 在无技能注入的情况下达到相近或更优性能，验证了技能内化的有效性

**Token 效率**
- SKILL0 维持极低的上下文开销：ALFWorld 每步 **0.38k tokens**，Search-QA 每步 **0.18k tokens**
- 相比 SkillRL（2.21k/0.87k）降低 **>5×** 的推理成本，同时保持更高成功率

### 3. 训练动态分析

**奖励曲线**（Figure 3, 4）
- 在 Qwen2.5-VL-3B 和 7B 上，SKILL0 的奖励曲线在整个训练过程中持续高于 AgentOCR 基线，显示更稳定的策略优化

**验证性能演化**（Figure 5）
- **w/ skill vs w/o skill**：训练初期，使用技能验证的准确率显著高于无技能验证；随着训练进行，两者差距逐渐缩小，最终无技能验证性能追上，直观展示知识从上下文向参数的内化过程
- **方法对比**：在严格公平的无技能推理设置下，SKILL0 持续优于 GRPO 和 SkillRL，且收敛到更高上限

**帮助性指标**（Figure 6）
- 定义技能帮助性 $\Delta_k = \text{Acc}_k^{\text{w/ skill}} - \text{Acc}_k^{\text{w/o skill}}$
- 训练呈现"上升-下降"轨迹：初期 $\Delta_k$ 较低（策略未学会利用技能），中期达到峰值（策略依赖技能指导），后期收敛至零（技能知识已内化，移除不影响性能）

### 4. 消融实验

**技能预算 $M$ 的影响**（Figure 7, 8, Table 4）
- 对比固定预算 $[6,6,6]$、静态低预算 $[3,3,3]$、无预算 $[0,0,0]$ 与线性衰减 $[6,3,0]$
- 线性衰减策略在移除技能后性能提升 **+1.6%**，而固定满预算导致性能崩溃 **-13.3%**，证明渐进式撤离对避免过拟合至关重要

**动态课程组件**（Table 2）
- **w/o Filter**（保留所有预算内技能）：性能下降 2.7%，因引入无关技能噪声
- **w/o Rank**（随机选择技能）：性能暴跌 13.7%，验证按帮助性排序的必要性

**验证间隔 $d$**（Table 3）
- 测试 $d \in \{5, 10, 20\}$，$d=10$ 在计算开销与性能间取得最优平衡（ALFWorld 87.9%，Search-QA 48.9%）

**扩展对比**（Table 5, 6）
- 在 ALFWorld 上超越记忆增强方法（ExpeL 46.3%，MemRL 21.4%）和闭源模型（GPT-4o 48.0%，Gemini-2.5-Pro 60.3%）
- 在 Search-QA 上超越检索增强方法（RAG 30.4%，Search-R1 38.5%），特别是在分布外数据集上展现强泛化能力

## 未来工作

基于论文的局限性与实验观察，以下方向值得进一步探索：

### 1. 自动化技能库构建与质量优化
当前框架依赖预构建的初始 SkillBank，其质量直接影响内化效果。可探索：
- **自动技能挖掘**：从原始交互轨迹中自动提炼、验证并结构化技能，减少人工设计依赖
- **噪声鲁棒性**：研究初始技能库含噪声或冲突时的内化机制，开发去噪训练策略
- **技能嵌入表示**：将技能编码为连续向量（而非文本），通过 $S_k \in \mathbb{R}^d$ 的梯度更新实现更细粒度的知识内化

### 2. 跨领域自适应分组机制
离线相关性驱动的分组 $\{T_k\}_{k=1}^N$ 需针对新领域重新划分。可探索：
- **在线元学习**：通过元梯度 $\nabla_\phi \mathcal{L}_{\text{val}}$ 动态调整技能与验证任务的关联，实现跨领域即时适配
- **层次化技能组织**：构建多层级技能本体（从通用原子技能到领域复合技能），利用 $\Delta_k^{(l)}$ 在层级 $l$ 上评估帮助性，支持细粒度课程控制

### 3. 自适应课程策略
当前采用线性预算衰减 $M(s) = \lceil N \cdot \frac{N_S-s}{N_S-1} \rceil$ 与贪心选择。可探索：
- **基于不确定性的退火**：根据策略熵 $\mathcal{H}(\pi_\theta(\cdot|I, V_t))$ 或价值函数方差动态调整预算，在高不确定性区域保留更多技能支持
- **非单调课程**：允许预算 $M(s)$ 在特定阶段暂时回升，帮助智能体克服局部最优或复杂子任务瓶颈

### 4. 持续学习与灾难性遗忘缓解
技能内化后，模型可能遗忘旧技能以学习新技能。可探索：
- **弹性权重固化（EWC）结合**：在 RL 目标中加入参数正则项 $\sum_i \frac{\lambda}{2} F_i (\theta_i - \theta_i^*)^2$，其中 $F_i$ 为 Fisher 信息矩阵，保护已内化技能对应的参数
- **模块化架构**：采用 Mixture-of-Experts（MoE）结构，将不同技能路由至特定专家子网络，实现技能参数的物理隔离与动态加载

### 5. 多智能体协作中的技能传播
扩展至多智能体场景：
- **技能蒸馏协议**：设计智能体间的通信机制，通过 $\mathcal{L}_{\text{distill}} = D_{\text{KL}}[\pi_{\text{student}} \| \pi_{\text{teacher}}^{\text{skill}}]$ 实现技能知识的跨智能体迁移
- **集体智慧内化**：研究去中心化环境下，多智能体经验聚合后的群体技能如何被单个模型内化

### 6. 可解释性机制
内化后的技能缺乏显式可解释性。可探索：
- **技能探针（Skill Probing）**：训练线性分类器 $f_{\text{probe}}(h_{\text{hidden}}) \to S_k$，检测特定技能知识在模型隐层状态中的编码位置
- **反事实技能分析**：通过干预实验 $P(\text{success} | \text{do}(S_k = \emptyset))$ 量化单个技能对决策路径的因果贡献

### 7. 与大规模模型的协同
当前实验基于 3B/7B 模型。在更大规模（72B+）模型上：
- **涌现能力研究**：观察技能内化是否触发模型的涌现推理模式，如自发形成元认知策略（自我监控何时需要"回忆"已内化技能）
- **上下文压缩的极限**：研究超大规模模型中视觉上下文编码 $V_t$ 的最优压缩率 $c_t^*$ 与模型容量的关系

### 8. 混合推理范式
完全零样本与完全检索增强之间的连续谱：
- **自适应检索触发**：训练策略输出二值决策变量 $z_t \in \{0,1\}$，根据当前状态置信度动态选择是否检索外部技能，形成"内化为主、检索为辅"的混合模式
- **技能工作记忆**：允许推理时临时加载极少 token 的技能提示（如 $<$0.1k），模拟人类"工作记忆"与"长期记忆"的交互

### 9. 视觉编码器的端到端优化
当前视觉编码器 $\text{Enc}$ 为固定组件。可探索：
- **可微渲染**：将文本到图像的渲染过程纳入端到端训练，通过 $\nabla_{c_t} \mathcal{L}$ 优化压缩策略，使视觉表示更适配特定技能类型
- **多模态技能**：扩展至包含视频演示、GUI 截图序列的技能内化，利用时序视觉编码器处理动态技能示范

## 总结

该论文针对大型语言模型（LLM）智能体对**推理时技能增强**（inference-time skill augmentation）的固有依赖问题，提出了**SKILL0**——首个以**技能内化**（skill internalization）为显式训练目标的强化学习框架。

### 1. 研究动机与问题

现有范式在每一步推理时从外部检索并注入技能描述，存在三重局限：
- **检索噪声**引入无关指导，腐蚀决策上下文；
- **Token开销**随多轮交互累积，限制可扩展性；
- **知识外置**：模型仅"执行"而非"习得"技能，能力 residing in context rather than parameters。

核心问题：**能否将结构化程序知识固化到模型参数中，实现无需运行时检索的零样本自主行为？**

### 2. SKILL0 框架

SKILL0 通过**上下文强化学习**（In-Context Reinforcement Learning, ICRL）与**动态课程学习**（Dynamic Curriculum）实现"训练时依赖技能，推理时零技能"（Skills at training, zero at inference）：

#### 2.1 上下文强化学习（ICRL）
- **训练阶段**：策略 $\pi_\theta(a_t, c_t \mid I, V_t)$ 接收视觉化技能上下文 $V_t = \text{Enc}(h_t, S; c_t)$，其中 $S$ 为选中技能子集，$c_t$ 为自适应压缩率；
- **推理阶段**：完全移除技能上下文（$S = \emptyset$），强制模型依赖内化知识；
- **复合奖励**：联合优化任务成功与压缩效率
$$
\tilde{r}_t = r_t + \lambda \cdot \mathbb{I}_{\text{succ}}(\tau) \cdot \ln(c_t)
$$

#### 2.2 动态课程学习
- **离线阶段**：按领域将技能库 $\text{SkillBank} = \{S_k\}_{k=1}^N$ 分组，关联验证子任务 $\{T_k\}_{k=1}^N$；
- **在线阶段**：分 $N_S$ 个阶段线性衰减技能预算
$$
|S(s)| \leq M(s) = \left\lceil N \cdot \frac{N_S - s}{N_S - 1} \right\rceil
$$
- **帮助性驱动选择**：每 $d$ 步评估 $\Delta_k = \text{Acc}_k^{\text{w/ skill}} - \text{Acc}_k^{\text{w/o skill}}$，仅保留 $\Delta_k > 0$ 且按降序选取前 $M(s)$ 个技能，直至 $M(N_S) = 0$。

### 3. 实验验证

在 **ALFWorld**（具身AI）与 **Search-QA**（搜索增强问答）上的实验表明：

- **性能提升**：相比 AgentOCR 基线，SKILL0 在 ALFWorld 上提升 **+9.7%**（87.9% vs 78.2%），在 Search-QA 上提升 **+6.6%**（40.8% vs 34.2%），与推理时技能增强的 SkillRL 相当或更优；
- **极致效率**：通过视觉上下文压缩，推理时每步仅 **<0.5k tokens**，较 SkillRL 降低 **>5×** 计算开销；
- **内化验证**：训练过程中技能帮助性 $\Delta_k$ 呈现"低→高→零"的演化轨迹，证明知识成功从上下文迁移至模型参数。

### 4. 主要贡献

- **范式创新**：首次将技能内化作为显式优化目标，突破"检索-注入"的传统范式；
- **方法论**：提出 ICRL 与动态课程的协同机制，实现从"上下文依赖"到"参数固化"的平滑过渡；
- **实践价值**：在保持高性能的同时，消除推理时外部依赖，为可扩展的自主智能体提供新路径。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
