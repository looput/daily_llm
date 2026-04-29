# AgentHER: Hindsight Experience Replay for LLM Agent Trajectory Relabeling

**arXiv**: [2603.21357](https://arxiv.org/abs/2603.21357) · [PDF](https://arxiv.org/pdf/2603.21357)  
**领域**: Agent  
**作者**: Ding  
**综合评分**: 7.88  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为AgentHER的创新框架，将强化学习中的后见经验回放（HER）原理应用于大语言模型智能体的自然语言轨迹重标注，有效利用失败轨迹进行离线数据增强。该方法在WebArena和ToolBench等多个基准测试中显著提升了模型性能，并实现了2倍的数据效率。作者Ding未明确标注所属机构，因此省略团队背景介绍。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

这篇论文试图解决**LLM代理训练中的数据浪费问题**——具体而言，是失败轨迹（failed trajectories）被系统性丢弃而导致的训练信号损失。

### 核心问题剖析

- **高失败率与数据丢弃的悖论**：当前LLM代理在真实任务中失败率极高（WebArena上GPT-4o成功率不足15%，ToolBench上pass@1低于55%），但标准训练流程仅保留成功轨迹，将60–75%的失败数据视为无效而直接废弃。

- **失败轨迹的潜在价值**：这些被丢弃的轨迹并非随机噪声，而是包含丰富、连贯的执行过程，往往产生正确的中间结果。例如，一个寻找"$5/kg以下铜线"失败的代理，可能实际完成了"$5.30/kg铜线供应商的全面对比"——这对重设后的目标而言是完美的示范。

- **传统监督微调（SFT）的局限**：仅使用成功样本（Success-only SFT）不仅浪费大部分收集经验，还限制了模型接触多样化任务变体的机会，导致数据效率低下。

### 解决思路

论文将强化学习中的**Hindsight Experience Replay（HER）**原则提升至自然语言代理场景，提出**AgentHER**框架。其核心洞察是：相对于原始目标$A$失败的轨迹，往往是相对于某个可实现替代目标$B$的正确演示。通过四阶段管道（失败分类→结果提取→LLM引导的提示重标记→数据打包），AgentHER将废弃的失败轨迹自动转换为高质量的SFT、DPO和ShareGPT格式训练数据，无需额外环境交互或人工标注即可实现约3.7倍的有效数据扩展。

## 相关工作

论文的相关研究主要涵盖以下几个维度：

### 1. 后见经验回放（HER）与目标条件强化学习
- **Andrychowicz et al. (2017)**：提出HER核心思想，通过将实际达到的状态替换为目标来利用失败轨迹，解决稀疏奖励RL问题。
- **Kaelbling (1993); Sutton et al. (1999); Mnih et al. (2015)**：目标条件RL的基础理论工作。

**关键区别**：AgentHER将HER从连续状态空间提升至自然语言代理场景，需要理解多步工具交互中实际完成的内容，并合成满足该轨迹的自然语言提示——这是传统RL formulation所不具备的能力。

### 2. 失败重用与偏好学习
- **Peng et al. (2023)**：基于GPT-4的指令微调。
- **Gulcehre et al. (2023)**：ReST（Reinforced Self-Training），通过过滤和重加权利用失败数据。
- **Hosseini et al. (2024)**：V-STaR，训练验证器用于自学习推理。

**关键区别**：现有NLP中的失败重用工作仅选择或重新加权$(goal, trajectory)$对，**从不生成新的可实现目标**；而AgentHER的核心创新在于反向推导（reverse-engineering）步骤——合成轨迹实际满足的新目标。

### 3. LLM代理训练与自我改进
- **ReAct (Yao et al., 2023b;a)** 与 **Toolformer (Schick et al., 2023)**：确立推理-行动代理范式。
- **FireAct (Chen et al., 2023)** 与 **AgentTuning (Zeng et al., 2024)**：基于成功轨迹的微调。
- **Reflexion (Shinn et al., 2023)**：通过在线口头强化学习改进。
- **ExpeL (Zhao et al., 2024)**：基于规则提取的经验学习。
- **Trial-and-Error (Song et al., 2024)**：探索错误驱动的恢复机制。

**关键区别**：上述方法均依赖成功样本或在线交互；AgentHER则**离线转换已有失败**，无需额外环境交互。

### 4. 同期互补工作
- **ECHO (Hu et al., 2025)**：体验整合 via 后见优化（Experience Consolidation via Hindsight Optimization），针对**在线**LM代理的推理时框架，在草稿本记忆中维护优化的轨迹描述以提高样本效率。

**关键区别**：AgentHER针对**离线**训练数据增强，仅重标记目标而保持轨迹不变，输出SFT/DPO数据集用于微调而非推理时记忆。两者为互补方法。

### 5. 验证与评估技术
- **Self-Instruct (Wang et al., 2023)**、**STaR (Zelikman et al., 2022)**、**DPO (Rafailov et al., 2023)**：针对单轮任务的自举方法。
- **CRITIC (Gou et al., 2024)**、**Lightman et al. (2024)**、**Du et al. (2024)**、**Liang et al. (2023)**：多智能体辩论与逐步验证技术，为AgentHER的多法官验证机制提供基础。

### 6. 错误分类体系
- **Lu et al. (2024)**：MQM（Multidimensional Quality Metrics）风格错误分析，为AgentHER的失败严重程度加权（failure-severity weighting）机制提供理论依据。

## 解决方案

论文通过提出**AgentHER**框架解决该问题，这是一个将Hindsight Experience Replay（HER）原则适配到自然语言代理轨迹的四阶段自动化管道。核心方法论如下：

### 1. 问题形式化
定义失败轨迹语料库 $\mathcal{F} = \{(g_i, \tau_i, f_i)\}_{i=1}^N$，其中 $g_i$ 为原始目标，$\tau_i = \{(z_t^i, a_t^i, o_t^i)\}_{t=1}^{T_i}$ 为思维-动作-观察序列。目标是合成**后见目标** $\hat{g}_i$，使得 $\tau_i$ 成为 $\hat{g}_i$ 的有效正样本，从而将失败轨迹转换为训练数据对 $(\hat{g}_i, \tau_i)$。

**有效后见目标的定义**（Definition 3.1）：
- (a) $\hat{g}$ 的每个事实声明必须由观察 $\{o_t\}$ 支持；
- (b) LLM法官赋予置信度 $c(\hat{g}, \tau) \geq \theta$，确认 $\tau$ 是 $\hat{g}$ 的成功演示。

### 2. 四阶段处理管道

#### Stage 1: 失败检测器（Failure Detector）
对每条轨迹分类并过滤不可恢复的样本：
- **失败类型** $\phi$：不完整（INCOMPLETE）、约束违反（CONSTRAINT VIOLATION）、错误结果（WRONG RESULT）、工具错误（TOOL ERROR）、幻觉（HALLUCINATION）、离题（OFF TOPIC）
- **可恢复标志** $r \in \{0,1\}$：判断轨迹是否包含实质性观察可供重标记
- **严重程度权重** $w \in [0,1]$：基于MQM风格错误分析，区分重大错误（$w<0.3$，如幻觉观察）与轻微错误（$w \in [0.3,1.0]$，如约束违反）

**实现模式**：
- 基于规则：关键词词库匹配，$w = \min(1.0, 0.3 + 0.1 \cdot h)$（$h$为匹配术语数）
- LLM法官：JSON模式提示返回 $(\phi, v, r, w)$

#### Stage 2: 结果提取器（Outcome Extractor）
生成**ReplayOutcome**：轨迹实际达成的事实摘要，作为后续重标记的事实锚点。
- **基于规则**：每个非平凡、非错误观察视为一项成就（截断至200字符）；数字令牌通过正则提取
- **LLM模式**：处理隐式结果、去重，并严格遵守事实性——仅包含观察证据支持的事实

#### Stage 3: 提示重标记器（Prompt Relabeler）
基于ReplayOutcome和原始提示风格，LLM合成新的自然语言目标：
$$(\hat{g}, b_{\text{valid}}, r_{\text{rationale}}, c) \leftarrow \text{Relabeler}(\text{outcome}, g_{\text{orig}}; M)$$

其中 $b_{\text{valid}} \in \{0,1\}$ 为有效性标志，$c \in [0,1]$ 为置信度。需满足四大约束：
1. $\hat{g}$ 读起来像自然用户请求
2. $\hat{g}$ 的每个断言均被观察满足
3. $\hat{g}$ 不引用原始失败提示
4. 复杂度与原始提示匹配

**多法官验证机制**：
- 首次尝试通过 $b_{\text{valid}}=1$ 且 $c \geq \theta$ 后，调用**第二独立LLM**（temperature=0）验证
- 仅当第二法官也确认 $c_2 \geq \theta$ 时接受该重标记
- 最多重试 $K=3$ 次；若无多法官通过但单次法官置信度 $\geq 0.8\theta$，则保留为最佳 effort 回退

#### Stage 4: 数据增强器（Data Augmenter）
将验证后的 $(\hat{g}, \tau)$ 序列化为多种训练格式：

- **SFT（监督微调）**：两轮对话 $[(\text{user}, \hat{g}), (\text{assistant}, \tilde{a})]$，从 $\tau$ 重构思维链。启用严重程度加权时，损失按 $w$ 缩放。

- **DPO（直接偏好优化）**：
  - 偏好对：选择 $(\hat{g}, \tau)$，拒绝 $(g_{\text{orig}}, \tau)$
  - **目标维度上的DPO**：固定轨迹 $\tau$，对比两个目标描述（而非标准DPO中固定提示对比两个回复）
  - 严重程度加权损失：
    $$\mathcal{L}_{\text{DPO}}^w = -\mathbb{E}_{(\hat{g},\tau,g_{\text{orig}})\sim \mathcal{D}^+} \left[ w_i \cdot \log \sigma \left( \beta \left( \log \frac{\pi_\theta(\tau|\hat{g})}{\pi_{\text{ref}}(\tau|\hat{g})} - \log \frac{\pi_\theta(\tau|g_{\text{orig}})}{\pi_{\text{ref}}(\tau|g_{\text{orig}})} \right) \right) \right]$$
    其中 $w_i$ 仅调节梯度幅度，不改变二元偏好方向。

- **ShareGPT格式**：多轮对话格式，兼容LLaMA-Factory、ms-swift等框架。

### 3. 关键创新机制

| 机制 | 作用 | 效果 |
|------|------|------|
| **严重程度加权** | 基于 $w$ 过滤 $w<0.3$ 的灾难性失败，并在DPO中按 $w$ 缩放损失 | 将标签噪声从5.9%降至2.3% |
| **多法官验证** | 要求两个独立LLM一致确认 $c \geq \theta$ | 将精确率从94.1%提升至97.7%，下游任务提升+0.7–0.9 pp |
| **置信度门控** | 阈值 $\theta^*=0.5$ 过滤边界样本 | 在噪声与召回率间取得最优平衡 |

### 4. 理论保证
**命题3.1（无偏性）**：在完美法官假设下（$c(\hat{g},\tau)=1 \Leftrightarrow \tau$ 是 $\hat{g}$ 的有效演示），每个被接受的 $(\hat{g}_i, \tau_i)$ 都是来自oracle目标条件策略 $\pi_G^*$ 支持集的正确样本。

**推论3.1.1**：在不完美法官（精确率 $p=0.977$）下，相对于SFT-Success的预期增益下界为 $p \cdot \Delta_{\text{perfect}} - (1-p) \cdot \varepsilon$，当 $\varepsilon \leq 42 \cdot \Delta_{\text{perfect}}$ 时保证正向收益，多法官机制显著强于单法官边界（$\varepsilon \leq 16 \cdot \Delta_{\text{perfect}}$）。

## 实验验证

论文在**WebArena**和**ToolBench**两个基准上进行了系统性实验，涵盖四个模型家族（GPT-4o、Qwen2.5-72B/7B、LLaMA-3.1-8B），主要实验包括：

## 1. 实验设置

### 数据集与数据收集
- **WebArena**：812个组合式网页自动化任务（购物、Reddit、GitLab、地图、维基百科）。收集3,000条GPT-3.5-turbo的失败轨迹和500条验证成功轨迹。AgentHER接受率：单法官78.0%（2,341/3,000），多法官73.2%（2,197/3,000）。
- **ToolBench**：16,464个工具使用任务（49个API类别，G1/G2/G3划分）。收集5,000条失败轨迹和2,000条成功轨迹（均来自官方训练集），测试使用官方测试集。AgentHER接受率82.5%（4,123/5,000）。

### 模型与微调
- **闭源**：GPT-4o（OpenAI微调API，默认超参数）
- **开源**：Qwen2.5-72B/7B、LLaMA-3.1-8B
- **训练配置**：LoRA（rank=16, α=32），3 epochs，8×A100-80G，AdamW+cosine学习率调度，DPO β=0.1

### 基线方法
- **Base**：零样本（zero-shot）
- **SFT-Random**：等量控制组，使用与AgentHER相同数量的失败轨迹但不重标记（直接作为负样本或随机配对）
- **Rejection-Sampling**：按置信度c>0.5过滤失败轨迹但不重标记
- **SFT-Success**：仅使用成功轨迹（主要对比目标，代表当前最佳离线训练流程）
- **Reflexion**：推理时在线方法（仅作参考，不直接可比）

## 2. 主实验结果（Table 1）

| 场景 | 方法 | GPT-4o | Qwen-72B | Qwen-7B | LLaMA-8B |
|------|------|--------|----------|---------|----------|
| **WebArena** | Base | 14.3 | 21.4 | 8.6 | 6.8 |
| | SFT-Success | 23.4 | 30.8 | 18.9 | 17.3 |
| | **AgentHER-SJ** | 29.7 | 38.1 | 27.0 | 25.3 |
| | **AgentHER-MJ** | **30.5** | **38.9** | **27.8** | **26.1** |
| | **Δ(MJ vs Success)** | **+7.1** | **+8.1** | **+8.9** | **+8.8** |
| **ToolBench** | Base | 53.2 | 60.1 | 44.6 | 42.1 |
| | SFT-Success | 67.8 | 75.4 | 61.2 | 58.3 |
| | **AgentHER-MJ** | **75.6** | **83.7** | **72.9** | **69.4** |
| | **Δ(MJ vs Success)** | **+7.8** | **+8.3** | **+11.7** | **+11.1** |

**关键发现**：
- 多法官（MJ）比单法官（SJ）提升+0.7–1.5 pp，验证标签噪声降低（5.9%→2.3%）的下游效益
- 小模型（7B/8B）收益最大（ToolBench上+11.1–11.7 pp），因其世界知识较弱，更受益于多样化任务子类型覆盖
- 跨基准迁移：WebArena训练→ToolBench零样本测试，AgentHER-MJ比SFT-Success高+9.5 pp，证明学习的是泛化行为而非任务记忆

## 3. 数据效率与扩展分析（Figure 3）

- **数据效率**（图3a）：AgentHER-SJ仅用50%成功样本即可匹配SFT-Success的100%性能；AgentHER-MJ在所有数据点均超越SFT-Success
- **失败量扩展**（图3b）：AgentHER随失败轨迹数量增加呈对数线性扩展（0.5k→5k），而SFT-Success无法利用额外失败数据
- **阈值敏感性**（图3c）：最优阈值θ*=0.5，在两种变体中均稳健

## 4. 模型尺寸扩展（Figure 4）

在Qwen2.5系列（1.5B至72B）上的测试显示：
- 所有规模均单调提升
- AgentHER-MJ增益从1.5B的+5.8 pp增长至14B峰值的+9.2 pp，72B时为+8.1 pp（大模型部分饱和）
- 1.5B模型性能翻倍（6.8%→12.6%），验证方法适用于边缘部署

## 5. 消融实验（Table 2）

以Qwen2.5-7B在WebArena上测试各组件贡献：

| 配置 | 成功率(%) | Δ | 噪声率(%) |
|------|-----------|-----|-----------|
| **Full AgentHER-MJ** | **27.8** | — | **2.3** |
| w/o Multi-judge (仅单法官) | 27.0 | -0.8 | 5.9 |
| w/o Severity weighting (w=1) | 26.4 | -1.4 | 4.1 |
| w/ Rule-based Extractor (Stage 2降级) | 26.0 | -1.8 | 5.9 |
| w/ LLM Detector (Stage 1升级) | 28.7 | +0.9 | 2.1 |
| w/o Failure Detection (接受所有) | 25.3 | -2.5 | 7.3 |
| **w/o Confidence Filter (θ=0)** | **23.7** | **-4.1** | **14.8** |
| SFT only (无DPO) | 25.4 | -2.4 | 2.3 |
| Naive Relabeling (随机提示) | 21.8 | -6.0 | n/a |
| SFT-Success (无AgentHER) | 18.9 | -8.9 | — |

**关键结论**：
- 置信度过滤最关键（-4.1 pp），将噪声从14.8%降至可控水平
- DPO偏好信号比纯SFT提升+2.4 pp
- 随机重标记（Naive）有害（-6.0 pp），证明目标逆向工程不可替代

## 6. 深入分析

### 按失败类型分析（Figure 5）
- **INCOMPLETE**（不完整）：+11.2 pp（最大收益，含丰富正确观察但目标未完全达成）
- **CONSTRAINT VIOLATION**（约束违反）：+9.8 pp
- **TOOL ERROR**（工具错误）：+2.1 pp（崩溃轨迹信号最少）
- 前两类占WebArena失败的~63%，处于AgentHER"最佳点"

### 法官可靠性（Section 5.2）
- 人工评估200个样本（3名NLP博士标注，Fleiss' κ=0.82）：
  - 单法官精确率：94.1%（159/169）
  - 多法官精确率：**97.7%**（127/130）
  - 被过滤样本中38.7%实际有效，验证置信度门控偏向保守

### 目标分布分析（Appendix E）
- AgentHER将覆盖的语义任务簇从11个扩展至14个（共18个）
- 独特覆盖3个长尾簇，Jensen-Shannon散度0.31，熵从1.83增至2.47 nats

### 迭代部署（Table 3）
- **Round 1**（GPT-3.5收集失败）：27.8%
- **Round 2**（使用Round 1模型收集新失败）：29.4%（+1.6 pp）
- **Round 3**：29.9%（+0.5 pp，总计+11.0 pp超过SFT-Success）
- 接受率逐轮下降（73.2%→70.0%→68.0%），反映模型能力增强后失败模式更难重标记

## 未来工作

论文在结论部分明确指出了以下**四个当前局限**与**三个未来探索方向**：

### 当前局限与改进空间

1. **任务集泄露问题（Task-set overlap）**  
   WebArena的当前协议使用相同的812个任务环境进行失败数据收集和模型评估，导致微调后的模型在训练阶段已接触页面HTML结构、API响应模式与导航惯例，构成一种任务集泄露（task-set leakage）。尽管通过跨基准迁移实验（WebArena→ToolBench）部分缓解了该担忧，但构建**完全独立的 held-out 测试分区**仍是必要的下一步工作，以提供完全干净的比较。

2. **数据量不对称的精确控制**  
   虽然通过SFT-Random（等量失败数据但不重标记）控制了数据量差异，但仍低于**完全匹配的oracle比较**（即使用与AgentHER接受样本数完全相等的成功样本进行训练）。未来需严格匹配数据规模以隔离重标记机制的真实增益。

3. **边界样本的主动学习**  
   当前置信度阈值θ=0.5在达到97.7%精确率的同时，丢弃了约38.7%的边界对（borderline pairs）。对这些高不确定性样本引入**主动学习（active learning）**——例如迭代查询人工标注或强化学习优化阈值——是自然的扩展方向，可进一步提升数据利用率。

4. **理论边界的 tighten**  
   理论保证（Proposition 3.1）假设存在完美法官（perfect judge）。需在**噪声预言机模型（noisy-oracle model）**下收紧该边界，以闭合理论预测与实验观测之间的差距，为超参数选择提供更严格的理论指导。

### 未来研究方向

1. **在线AgentHER（Online AgentHER）**  
   将重标记与训练过程与部署环境**并发执行**（relabeling and training concurrently with deployment），使交互式代理能在持续运行中实时利用新产生的失败轨迹进行自我改进，加速迭代周期。

2. **多模态轨迹扩展**  
   将管道扩展至**多模态轨迹**（网页截图、GUI界面交互、视觉观察），覆盖真实世界部署中的主流形态。当前AgentHER仅处理文本观察（thought-action-observation），视觉模态的引入需要新的结果提取与验证机制。

3. **端到端学习的后见模块**  
   将当前基于LLM prompt engineering的"后见目标合成"步骤本身转化为**可学习的模块**（learned hindsight module），通过端到端训练直接最大化下游任务多样性或策略性能，而非仅匹配现有提示风格。这可能涉及训练专门的目标生成网络或利用对抗训练探索 harder negatives。

### 其他潜在探索点

- **跨领域泛化**：在更多样化的代理环境（如操作系统控制、科学实验模拟、具身智能）中验证AgentHER的鲁棒性，特别是那些具有更长轨迹（long-horizon）和稀疏反馈的任务。
- **专门化法官模型**：用轻量级、针对特定领域微调的验证模型替代通用LLM法官，降低推理成本并可能提高判断一致性。
- **与推理时方法融合**：将AgentHER的离线数据增强与ECHO等在线推理时记忆机制结合，构建"训练-推理"协同的后见优化闭环。

## 总结

**AgentHER: 面向LLM代理轨迹重标记的后见经验回放**

### 核心贡献
该论文提出**AgentHER**，首次将强化学习中的**Hindsight Experience Replay（HER）**原则系统性地适配至自然语言代理场景，通过自动化目标重标记（goal relabeling）将失败的代理轨迹转换为高质量训练数据，而非直接丢弃。该方法在WebArena与ToolBench基准上实现**+7.1–11.7 pp**的性能提升，并将数据效率提高**2倍**（仅用50%成功样本即可匹配全量成功样本的基线性能）。

### 研究背景与问题
当前LLM代理在真实世界任务中面临**高失败率困境**：GPT-4o在WebArena上成功率不足15%，ToolBench的pass@1低于55%。然而，标准训练流程（SFT-Success）仅保留成功轨迹，将60–75%的失败数据视为噪声直接丢弃。这些被浪费的轨迹往往包含丰富、连贯的正确执行过程（如完成价格比较但超出预算限制），只是与原始目标不匹配。

### AgentHER框架
论文提出四阶段自动化管道，将失败轨迹语料库 $\mathcal{F} = \{(g_i, \tau_i, f_i)\}$ 转换为有效训练对 $(\hat{g}_i, \tau_i)$：

1. **失败检测器（Stage 1）**：基于规则或LLM对轨迹分类（不完整、约束违反、工具错误等），赋予**可恢复标志** $r$ 与**严重程度权重** $w \in [0,1]$，过滤灾难性失败（$w<0.3$）。
2. **结果提取器（Stage 2）**：提取ReplayOutcome——轨迹实际达成的事实摘要（价格数据、实体名称等），作为重标记的事实锚点。
3. **提示重标记器（Stage 3）**：LLM基于Outcome合成**后见目标** $\hat{g}$，满足：(a) 所有断言由观察支持；(b) 不引用原始失败提示；(c) 置信度 $c \geq \theta$。**多法官验证**要求两个独立LLM一致确认，将标签噪声从5.9%降至2.3%。
4. **数据增强器（Stage 4）**：生成SFT（带 severity weighting 的损失缩放）、DPO（固定轨迹 $\tau$，对比目标 $\hat{g}$ 与 $g_{\text{orig}}$ 的偏好对）及ShareGPT格式数据。

### 关键技术机制
- **严重程度加权**：受MQM错误分析启发，按 $w$ 调节DPO梯度幅度，降低边界样本影响。
- **置信度门控**：阈值 $\theta^*=0.5$ 在召回率与精度间取得最优平衡。
- **目标维度DPO**：与传统DPO（固定提示，对比两个回复）相反，AgentHER固定轨迹，对比两个目标描述，利用相同的对数似然比推导偏好损失。

### 实验验证
在四个模型家族（GPT-4o、Qwen2.5-72B/7B、LLaMA-3.1-8B，覆盖1.5B至72B参数）上的系统性实验表明：

- **主性能**：AgentHER-MJ（多法官）相比SFT-Success在WebArena上提升+7.1–8.9 pp，在ToolBench上提升+7.8–11.7 pp；小模型（7B/8B）收益最大（+11.1–11.7 pp）。
- **数据效率**：AgentHER-SJ在仅用50%成功样本时即匹配SFT-Success的100%性能，有效训练数据扩展约3.7倍。
- **跨基准迁移**：WebArena训练→ToolBench零样本测试，AgentHER比基线高+9.5 pp，证明学习的是泛化行为而非任务记忆。
- **迭代部署**：三轮迭代收集-重标记-再训练持续增益，总计+11.0 pp，尽管接受率随模型增强而递减（73.2%→68.0%）。

### 理论贡献
论文证明：在完美法官假设下，AgentHER生成的 $(\hat{g}, \tau)$ 对是oracle目标条件策略 $\pi_G^*$ 支持集上的无偏样本；在不完美法官（精度 $p=0.977$）下，增益下界为 $p \cdot \Delta_{\text{perfect}} - (1-p) \cdot \varepsilon$，只要单条噪声样本的边际危害 $\varepsilon \leq 42 \cdot \Delta_{\text{perfect}}$ 即可保证正向收益。

### 局限与未来方向
当前局限包括任务集泄露（训练与评估使用相同环境集合）、数据量不对称的完全控制，以及理论边界对完美法官的依赖。未来方向涵盖**在线AgentHER**（部署时实时重标记）、**多模态轨迹**（网页截图、GUI交互）的扩展，以及将后见目标合成步骤本身训练为**端到端可学习模块**。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
