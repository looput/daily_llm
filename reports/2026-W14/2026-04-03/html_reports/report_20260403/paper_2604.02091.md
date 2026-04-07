# Optimizing RAG Rerankers with LLM Feedback via Reinforcement Learning

**arXiv**: [2604.02091](https://arxiv.org/abs/2604.02091) · [PDF](https://arxiv.org/pdf/2604.02091)  
**领域**: RLHF  
**作者**: Wu, Shen, Wang, Zhou, Wu, Dai, Xia  
**综合评分**: 7.74  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为ReRanking Preference Optimization (RRPO)的强化学习框架，用于优化检索增强生成中的重排序模型。该方法通过将重排序建模为序列决策过程，直接利用LLM的生成质量反馈进行优化，无需昂贵的人工标注。在知识密集型基准测试上的广泛实验表明，RRPO显著优于包括RankZephyr在内的强大基线模型。分析还显示该框架具有很好的通用性，能无缝适配不同的LLM阅读器，与查询扩展模块正交集成，并在有噪声监督下保持鲁棒性。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

这篇论文试图解决**检索增强生成（RAG）系统中重排序器（reranker）与下游大语言模型（LLM）生成质量之间的根本错位问题**。

具体而言，现有方法存在以下关键局限：

- **优化目标与下游任务解耦**：当前重排序模型通常在孤立的监督学习范式下进行优化，依赖静态的人工标注相关性标签（如基于NDCG等信息检索指标）。这种优化过程与RAG流水线的下游生成阶段完全分离，导致重排序器学习的"相关性"概念并不等同于LLM生成精确答案所需的"有用性"。

- **上下文效用与主题相关性的偏差**：传统信息检索指标偏好的文档（如覆盖广泛主题的长篇文章）未必是对LLM生成答案最有帮助的文档（如包含关键事实的简洁条目）。这种偏差使得重排序器可能选择对最终答案生成贡献有限的文档，即使它们在主题上高度相关。

- **昂贵的标注成本**：构建高质量的人工标注相关性数据集成本高昂且难以扩展，限制了重排序模型在特定任务或领域中的适配能力。

为此，论文提出**ReRanking Preference Optimization (RRPO)**框架，通过以下机制解决上述问题：

- **端到端对齐**：将重排序形式化为顺序决策过程（Markov Decision Process），利用下游LLM的生成质量作为奖励信号，直接优化文档集合对生成任务的实际效用（context utility），而非仅优化静态相关性标签。

- **稳定的强化学习训练**：引入参考锚定的确定性基线（reference-anchored deterministic baseline），避免训练单独的评论网络（critic network），从而稳定训练过程并降低计算复杂度。

- **可扩展的监督信号**：利用LLM自身的反馈作为可扩展的监督来源，消除对昂贵人工标注的依赖，使重排序器能够针对特定读者模型（reader model）的需求进行自适应优化。

## 相关工作

根据论文第2节（Related Work），相关研究可分为以下三个主要方向：

### 1. 检索增强生成（Retrieval-Augmented Generation）
该方向研究如何通过整合外部信息来增强LLM能力，缓解幻觉和知识过时问题：

- **早期框架**：侧重于动态知识供应，用于问答和对话系统（如Lewis et al., 2020; Gao et al., 2023; Karpukhin et al., 2020; Izacard et al., 2023; Shuster et al., 2021）。
- **生成过程优化**：包括指令微调生成器以更好地利用上下文（Ma et al., 2024; Zhu et al., 2024; Muennighoff et al., 2024; Liu et al., 2024; Lin et al., 2023），以及引入自反思机制如Self-RAG（Asai et al., 2024）。
- **复杂检索策略**：开发迭代、自适应或主动检索方法以处理复杂查询（Trivedi et al., 2022a; Jiang et al., 2023; Jeong et al., 2024; Xu et al., 2024）。

### 2. RAG中的重排序器（Rerankers in RAG）
该方向关注如何在检索和生成之间引入重排序阶段来优化文档质量：

- **传统方法**：基于BERT的编码器进行点式（pointwise）或成对（pairwise）相关性估计（Nogueira and Cho, 2019; Nogueira et al., 2019）。
- **LLM-based方法**：
  - 零样本列表式提示（zero-shot listwise prompting）（Sun et al., 2023）
  - 离散提示优化（Cho et al., 2023）
  - 在排名数据上微调开源LLM，如RankZephyr（Pradeep et al., 2023; Ma et al., 2024）
- **效率与无监督方法**：无监督风险最小化（Yuan et al., 2024）和利用注意力机制提高效率（Reddy et al., 2024; Chen et al., 2024）。
- **上下文感知与智能体范式**：
  - EBCAR：利用嵌入捕获跨段落交互（Yuan et al., 2025）
  - REARANK：将重排序形式化为推理智能体（Zhang et al., 2025a）

### 3. 用于RAG的强化学习（Reinforcement Learning for RAG）
该方向探索使用RL来对齐RAG组件与最终任务性能：

- **RL应用**：优化查询重写（Wang et al., 2023b）、通过ReAct结合推理轨迹（Yao et al., 2023）、以及促进多步浏览（Nakano et al., 2021）。
- **偏好优化方法**：近期趋势是通过偏好优化（如DPO）将检索与生成对齐，包括DynamicRAG（Sun et al., 2025）、KnowPO（Zhang et al., 2025b）和DPARAG（Dong et al., 2025）。
- **多智能体架构**：探索多智能体强化学习框架（Chen et al., 2025）。

**与现有工作的区别**：与上述基于DPO的方法或复杂多智能体框架不同，RRPO采用**顺序决策制定（sequential decision-making）**公式，显式捕获文档集合的组合效用（combinatorial utility），而非仅进行单步偏好对齐。

## 解决方案

论文通过提出 **ReRanking Preference Optimization (RRPO)** 框架解决该问题，核心思路是将重排序形式化为**顺序决策过程**，利用下游LLM的生成质量作为奖励信号直接优化，而非依赖静态相关性标签。具体解决方案包括以下关键组件：

### 1. 任务形式化为马尔可夫决策过程（MDP）
将文档选择定义为有限范围的MDP $(\mathcal{S}, \mathcal{A}, \mathcal{P}, \mathcal{R})$：

- **状态** ($s_t$)：时刻$t$尚未被选中的候选文档集合，初始状态 $s_1 = \{d_1, d_2, \ldots, d_N\}$
- **动作** ($a_t$)：从当前状态 $s_t$ 中选择文档 $d_{c_t}$，动作空间为 $\mathcal{A}_t = \{\text{select } d_i \mid d_i \in s_t\}$
- **转移** ($\mathcal{P}$)：确定性转移，$s_{t+1} = s_t \setminus \{d_{c_t}\}$
- **奖励** ($r_t$)：基于已选文档 $\{d_{c_1}, \ldots, d_{c_t}\}$ 生成的答案质量，由LLM评估器 $\mathcal{R}_{\text{lm}}$ 计算：
$$r_t = \mathcal{R}_{\text{lm}}(\text{ans}, \text{response}_t)$$

其中 $\text{response}_t = \text{ReaderLLM}(\text{INST}, q, d_{c_1}, \ldots, d_{c_t})$

### 2. 策略网络与概率归一化
使用参数化的点式重排序模型 $f_\theta$ 实现智能体策略 $\pi_\theta(a_t|s_t)$：

- **初始相关性分数**：$\text{score}_i = f_\theta(q, d_i)$
- **初始概率分布**（SoftMax）：
$$p_i = \frac{\exp(\text{score}_i)}{\sum_{j=1}^N \exp(\text{score}_j)}$$

- **条件选择概率**：在时刻$t$选择文档 $d_{c_t}$ 的概率为其在当前剩余文档中的归一化概率：
$$\pi_\theta(a_t = \text{select } d_{c_t} | s_t) = \frac{p_{c_t}}{\sum_{d_j \in s_t} p_j} = \frac{p_{c_t}}{1 - \sum_{l=1}^{t-1} p_{c_l}}$$

### 3. 带约束的策略优化目标
为避免策略梯度更新过大导致训练震荡，采用改进的PPO目标函数：

$$J(\theta) = \mathbb{E}_{\tau \sim \pi_\theta} \left[ \frac{1}{k} \sum_{t=1}^k \min\left( \rho_t(\theta)\hat{A}_t, \text{clip}(\rho_t(\theta), 1-\epsilon, 1+\epsilon)\hat{A}_t \right) - \beta D_{\text{KL}}(\pi_\theta \| \pi_{\text{ref}}) \right]$$

其中：
- $\rho_t(\theta) = \frac{\pi_\theta(a_t|s_t)}{\pi_{\text{ref}}(a_t|s_t)}$ 为重要性采样比率
- $\pi_{\text{ref}}$ 为固定的参考策略（通常使用预训练重排序模型）
- $\hat{A}_t$ 为估计的优势函数
- $\epsilon$ 为裁剪系数，$\beta$ 为KL散度惩罚系数

### 4. 参考锚定的确定性基线（核心创新）
为解决LLM奖励离散、方差大导致训练不稳定的问题，**放弃训练单独的价值网络（Critic）**，改为使用参考模型的贪婪 rollout 作为确定性基线：

- **基线计算**：在状态 $s_t$ 下，使用参考策略 $\pi_{\text{ref}}$ 贪婪选择 $t$ 个文档（每步选择概率最大的文档），构成集合 $\{d'_{c_1}, \ldots, d'_{c_t}\}$
- **价值函数**：
$$V(s_t) = \mathcal{R}_{\text{lm}}(\text{ans}, \text{ReaderLLM}(\text{INST}, q, d'_{c_1}, \ldots, d'_{c_t}))$$

- **GAE优势估计**：结合广义优势估计（Generalized Advantage Estimation）计算原始优势：
$$A_t = \sum_{j=t}^{k-1} (\gamma\lambda)^{j-t} \delta_j, \quad \text{其中 } \delta_t = r_t + \gamma V(s_{t+1}) - V(s_t)$$

- **优势归一化**：在滑动窗口内标准化优势以进一步稳定训练：
$$\hat{A}_t = \frac{A_t - \text{mean}(A_{\text{window}})}{\text{std}(A_{\text{window}}) + \epsilon_{\text{norm}}}$$

### 5. 训练稳定策略
- **KL散度估计器**：采用Ouyang et al. (2022) 的估计器计算策略与参考策略间的KL散度
- **LLM响应回放缓存**：通过设置temperature=0确保确定性生成，缓存历史LLM响应以减少约50%的推理调用
- **混合精度与内核加速**：使用Flash Attention和Automatic Mixed Precision (AMP)加速训练，vLLM加速推理

通过上述机制，RRPO实现了**无需人工标注**、**直接对齐LLM生成质量**、**训练稳定**的重排序优化，将优化目标从传统的"主题相关性"转变为"上下文效用"（context utility）。

## 实验验证

论文进行了系统性的实验验证，涵盖基准性能测试、消融分析以及多维度的泛化性与鲁棒性评估。具体实验内容如下：

### 1. 主实验（Main Results）
在标准知识密集型基准上验证RRPO的有效性：
- **数据集**：HotpotQA（多跳问答）和 AmbigNQ（消歧问答）
- **基线**：包括Naive RAG (BM25)、多种预训练重排序器（jina-reranker、bge-reranker、gte-reranker、qwen3-reranker）及其RRPO优化版本，以及高级RAG方法（FLARE、DRAGIN）
- **配置**：使用BM25检索Top-100文档，经重排序后选择Top-$k$（HotpotQA: $k=3$；AmbigNQ: $k=5$）输入Qwen2.5-7B阅读器
- **评估指标**：EM（Exact Match）和 F1

结果表明，RRPO在所有重排序器架构上均带来一致提升，显著优于强监督基线和先进的RAG策略。

### 2. 消融实验（Ablation Study）
探究训练交互深度 $k_{\text{train}}$ 对性能的影响：
- **设置**：在HotpotQA上对比 $k_{\text{train}} \in \{1, 3, 5\}$ 三种配置
- **发现**：$k_{\text{train}}=3$ 取得最优结果，平衡了单文档训练（$k=1$）的信息不足与多文档训练（$k=5$）的噪声及注意力分散问题

### 3. 泛化性与鲁棒性综合分析（Extensive Analysis）
该部分验证RRPO在五个关键维度的性能：

- **复杂多跳推理泛化**：在2WikiMultiHopQA和MusiQue（需跨文档信息整合的复杂多跳数据集）上测试。RRPO在2WikiMultiHopQA上 consistently 优于基线，在MusiQue（低召回率场景）上仍保持正向增益，证明其不仅过拟合特定查询模式，而是学习到证据链的语义鲁棒性。

- **跨LLM阅读器迁移**：验证重排序策略在不同规模、架构的LLM上的通用性：
  - 同系列不同规模：Qwen2.5-3B、7B、14B
  - 不同架构：Llama-3.1-8B、GLM-4-9B
  - 闭源专有模型：GPT-4o、Claude-3.5-Sonnet、Gemini-2.5-Flash
  结果显示RRPO训练的排序策略无需针对特定阅读器微调即可实现跨模型迁移，甚至增强闭源模型性能。

- **与SOTA列表式重排序器对比**：与RankZephyr（7B参数、基于LLM的列表式重排序器）在Top-3设置下比较。RRPO在HotpotQA和AmbigNQ上均优于RankZephyr，表明针对特定阅读器效用优化的策略优于通用LLM推理能力。

- **与高级RAG管道正交集成**：测试RRPO与Query2Doc（查询扩展技术）的叠加效果。结果表明Query2Doc提升召回率后，RRPO仍能在此基础上进一步提升精确率，证实两者在"扩展搜索边界"与"优化上下文效用"上的正交性。

- **嘈杂监督下的训练鲁棒性**：使用较小的Qwen2.5-3B作为奖励监督器（RRPO-3B）训练重排序器，并在不同规模阅读器上评估。结果显示即使使用较小、噪声较多的监督器，RRPO仍能获得稳定提升，验证其标签效率与训练成本优势。

### 4. 提示模板泛化实验（Appendix D）
验证重排序器是否过拟合于特定提示模板：
- 使用与训练时不同的Prompt Template A和Template B（结构和风格差异显著）在HotpotQA上评估
- 结果：RRPO在所有模板变体上均保持对基线的显著优势，证明其学习的是文档内在价值而非特定语言模式

### 5. 更具挑战性的多跳数据集（Appendix E）
在2WikiMultiHopQA和MusiQue上的补充结果：
- 2WikiMultiHopQA：Top-1和Top-3设置下的详细性能
- MusiQue：在极低基线性能（BM25召回率低）的困难场景下，RRPO仍能提供正向增益

### 6. 替代RL架构对比（Appendix G）
验证RRPO设计选择的必要性，对比：
- **Listwise Bandit**：将Top-$k$选择建模为单动作多臂赌博机
- **Standard PPO**：使用参数化价值网络（Critic）替代确定性基线

结果表明，标准方法在Top-1指标上有提升，但在Top-3列表优化上表现不稳定（甚至劣于基线），而RRPO的顺序决策与参考锚定基线能一致地优化整个文档集合的效用。

### 7. 统计显著性检验（Appendix H）
对HotpotQA和AmbigNQ的F1分数进行配对t检验（paired t-test）：
- 零假设：RRPO与基线无显著差异
- 结果：$p < 0.01$，拒绝零假设，证实性能提升具有统计显著性

### 8. 训练加速策略（Appendix I）
报告了实现高效训练的技术实现：
- Flash Attention与自动混合精度（AMP）优化
- 检索结果缓存（跨epoch复用）
- LLM响应回放缓存（确定性生成，减少约50%推理调用）
- 分布式训练与梯度累积优化

## 未来工作

基于论文的局限性分析与实验发现，以下方向值得进一步探索：

### 1. 突破初始检索召回率的瓶颈
论文指出，RRPO作为重排序模型，其性能上限受限于初始检索模块提供的候选集质量。若Top-N候选文档中相关文档召回率过低，无论重排序策略如何优化，均无法弥补信息缺失。未来可探索：
- **联合优化检索与重排序**：将RRPO的强化学习框架扩展至初始检索器，实现从索引到排序的端到端优化
- **动态候选集扩展**：在训练或推理阶段，根据策略网络的置信度动态触发额外的检索轮次，以补充候选池

### 2. 自适应上下文深度决策
当前框架假设固定的文档选择数量 $k$（训练时 $k_{\text{train}}$ 与推理时 $k_{\text{eval}}$ 固定）。然而，不同查询的信息需求具有内在差异性：
- **动态停止机制**：引入"停止动作"（stop action），使策略能够根据当前已选文档的累积效用，自主决定何时终止选择（即动态 $k$），避免信息不足或上下文窗口浪费
- **分层文档组织**：探索将RRPO与分层RAG结合，在段落（passage）与文档（document）两级分别应用效用优化

### 3. 细粒度奖励与信用分配机制
当前奖励 $\mathcal{R}_{\text{lm}}$ 基于最终答案的匹配度（如EM/F1），属于稀疏的延迟奖励，且难以解释单篇文档对生成的具体贡献：
- **中间步骤监督**：在多跳推理场景中，利用思维链（Chain-of-Thought）的中间推理步骤作为过程奖励（process reward），实现更精确的信用分配
- **反事实效用估计**：训练辅助模型预测"移除特定文档后生成质量的变化"，从而显式量化每篇文档的边际贡献（marginal utility）

### 4. 跨模态与复杂任务扩展
论文实验集中于知识密集型问答，RRPO的效用优化范式可向更复杂场景迁移：
- **多模态RAG**：将框架扩展至图像、表格等非文本模态，定义跨模态的上下文效用度量（如视觉问答中的图像选择）
- **长上下文与代码生成**：在代码检索增强生成或长文档摘要中，探索文档组合对长序列生成连贯性的影响，设计相应的效用评估指标

### 5. 参考锚定策略的理论深化
论文提出的参考锚定确定性基线在实践中表现稳健，但其理论特性尚待阐明：
- **方差分析**：从理论上量化该基线相对于标准参数化Critic的方差减少比率，以及在非平稳奖励分布下的收敛保证
- **参考策略选择**：研究参考策略 $\pi_{\text{ref}}$ 与目标策略 $\pi_\theta$ 的初始性能差距对优化动态的影响，以及自适应调整 $\beta$（KL惩罚系数）的策略

### 6. 高效训练与蒸馏
尽管论文采用了缓存与回放缓存等加速策略，基于LLM反馈的RL训练成本仍较高：
- **离线强化学习**：探索无需在线LLM交互的离线RRPO变体，利用预先收集的偏好数据集进行训练，降低对API调用或推理计算的依赖
- **策略蒸馏**：研究如何将RRPO优化后的策略蒸馏为更轻量级的监督模型，在保持对齐质量的同时实现推理加速

### 7. 多智能体与对抗鲁棒性
- **对抗性噪声处理**：研究RRPO在初始检索结果包含对抗性构造的误导性文档时的鲁棒性，以及通过对抗训练增强策略稳定性的方法
- **多阅读器共识机制**：当存在多个异构阅读器（如开源模型与专有API混合）时，探索利用多智能体强化学习聚合多样化反馈信号的策略

## 总结

本文提出 **ReRanking Preference Optimization (RRPO)**，一种基于强化学习的重排序优化框架，旨在解决检索增强生成（RAG）系统中传统重排序器与下游大语言模型（LLM）生成质量之间的根本错位问题。

### 核心问题
现有重排序模型通常在孤立的监督学习范式下优化，依赖静态的人工标注相关性标签（如NDCG），与下游LLM的生成过程完全解耦。这导致**主题相关性**与**上下文效用**之间的偏差：被信息检索指标判定为高相关性的文档，未必能为LLM生成精确答案提供实际价值。

### 方法框架
**RRPO** 将文档重排序形式化为有限范围的马尔可夫决策过程（MDP）：

- **状态**（$s_t$）：当前未被选中的候选文档集合
- **动作**（$a_t$）：从剩余文档中选择一篇
- **奖励**（$r_t$）：基于已选文档生成的答案与标准答案的匹配度（由LLM评估器 $\mathcal{R}_{\text{lm}}$ 计算），直接反映文档对生成质量的贡献

**关键技术创新**包括：
1. **顺序决策建模**：通过逐步选择文档并获取中间奖励，显式优化文档集合的组合效用
2. **参考锚定确定性基线**：摒弃传统PPO中难以训练的价值网络（Critic），改用固定参考策略（$\pi_{\text{ref}}$）的贪婪rollout作为确定性基线 $V(s_t)$，结合广义优势估计（GAE）稳定训练：
   $$V(s_t) = \mathcal{R}_{\text{lm}}(\text{ans}, \text{ReaderLLM}(\text{INST}, q, d'_{c_1}, \ldots, d'_{c_t}))$$
3. **约束优化目标**：采用PPO-clip机制与KL散度惩罚，限制策略更新幅度：
   $$J(\theta) = \mathbb{E}_{\tau\sim\pi_\theta}\left[\frac{1}{k}\sum_{t=1}^k \min\left(\rho_t(\theta)\hat{A}_t, \text{clip}(\rho_t(\theta), 1-\epsilon, 1+\epsilon)\hat{A}_t\right) - \beta D_{\text{KL}}(\pi_\theta\|\pi_{\text{ref}})\right]$$

### 实验发现
在HotpotQA、AmbigNQ、2WikiMultiHopQA和MusiQue等知识密集型基准上的实验表明：

- **性能提升**：RRPO显著优于强监督基线（如gte-reranker）和先进RAG方法（如FLARE、DRAGIN），F1分数提升1-2个百分点，且优于SOTA列表式重排序器RankZephyr
- **跨模型泛化**：训练后的策略可无缝迁移至不同架构（Llama、GLM）和规模的LLM（3B至14B），甚至增强闭源模型（GPT-4o、Claude-3.5-Sonnet）的性能
- **正交集成**：与Query2Doc等查询扩展技术结合时产生叠加增益，证明其在精确率优化上的独特价值
- **训练鲁棒性**：即使使用较小（3B参数）、噪声较多的LLM作为监督器，仍能学习到有效的排序策略，显著降低训练成本

### 主要贡献
- **端到端对齐**：首次通过强化学习将重排序器直接与LLM生成质量对齐，消除对昂贵人工标注的依赖
- **训练稳定性**：参考锚定基线机制有效解决了LLM反馈高方差、离散性带来的训练不稳定问题
- **实用性与可扩展性**：作为即插即用模块，兼容现有RAG管道，展现出强大的跨域泛化与成本效率优势



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
