# A Comedy of Estimators: On KL Regularization in RL Training of LLMs

**arXiv**: [2512.21852](https://arxiv.org/abs/2512.21852) · [PDF](https://arxiv.org/pdf/2512.21852)  
**领域**: RLHF  
**作者**: Shah, Obando-Ceron, Jain, Bartoldson, Kailkhura, Mittal, Berseth, Castro 等 13 人  
**综合评分**: 8.57  （novelty: 9.0 · method: 9.0 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文由来自Meta GenAI、Google、Mila（蒙特利尔学习算法研究所）等知名研究机构的团队合作完成，作者包括Bengio、Courville等深度学习领域的知名学者。该论文系统性地研究了强化学习训练大语言模型时KL正则化项估计器的配置问题，揭示了梯度偏差对训练稳定性和模型性能的影响，并通过实验验证了无偏梯度估计器配置的优越性。研究填补了该领域的重要空白，具有重要的理论和实践价值。

---

## 详细分析

> **社区热度**: ⭐ 15 (来自 papers.cool)

## 问题定义

论文旨在系统澄清并解决“在大型语言模型（LLM）强化学习（RL）后训练中，如何正确实现 KL 正则化”这一被忽视却影响深远的问题。具体而言，作者指出：

1. 现有开源与学术代码中，对反向 KL 散度 $KL(\pi_\theta\|\pi_{\text{ref}})$ 的估计方式及其在目标函数中的“摆放位置”（reward 项 vs. loss 项）存在多种不一致实现；
2. 这些实现多数并未给出真实序列级 KL 正则化目标的**无偏梯度**，导致优化过程与宣称目标不一致，引发训练不稳定甚至崩溃；
3. 尚无工作系统评估不同 KL 估计器与摆放策略对下游任务（分布内/分布外）性能的影响，尤其在可验证奖励（RLVR）场景。

因此，论文从理论与实验两方面：

- 解析四种常见配置（K1/K3 估计器 × reward/loss 摆放）在 on-policy 设定下的梯度偏差；
- 在 Qwen2.5-7B、Llama-3.1-8B-Instruct、Qwen3-4B-Instruct-2507 上开展大规模 RL 微调，量化不同配置对数学推理、科学问答等任务的稳定性与泛化性能；
- 探讨高异步（off-policy）训练场景下 KL 正则化的作用，并验证“将 KL 同时加入 reward 与 loss”可恢复无偏梯度的简单修正方案。

最终给出明确建议：**默认采用 K1-in-reward 或其它可保证无偏梯度的配置**，以避免训练事故并提升分布外泛化。

## 相关工作

论文在附录 A（Related work）与正文引文中系统回顾了与 KL 正则化、LLM 强化学习后训练相关的研究，可归纳为以下几条主线：

1. **KL 正则化在 RLHF 中的奠基性工作**  
   - Christiano et al. 2017、Ziegler et al. 2019、Stiennon et al. 2020 首次将 $KL(\pi_\theta\|\pi_{\text{ref}})$ 作为惩罚项引入奖励函数，防止语言模型偏离初始分布。  
   - Ouyang et al. 2022（InstructGPT）将这一框架规模化，成为后续 RLHF 工程实现的模板。

2. **面向推理任务的 RL 后训练**  
   - Shao et al. 2024、Guo et al. 2025（DeepSeek-R1）提出 GRPO，将 KL 项直接加入损失函数而非奖励，显著降低方差，被后续数学/代码推理工作广泛沿用。  
   - Trung et al. 2024（ReFT）、Lambert et al. 2025（Tulu 3）等把“可验证奖励”（RLVR）确立为提升推理能力的主流范式。

3. **KL 估计与梯度偏差理论分析**  
   - Schulman 2020 博客提出 K3（Schulman）估计器，强调低方差但并未讨论梯度偏差。  
   - Tang & Munos 2025、Zhang et al. 2025 首次指出 K3-in-loss 的梯度并非目标函数真实梯度，给出 token-level 修正思路，但未系统比较序列级配置。  
   - Amini et al. 2025 提出更精确的 KL 估计技巧，仍聚焦估计精度而非梯度偏差。

4. **知识蒸馏与 KL 的再解读**  
   - Wu et al. 2024 把 KL 正则化重新解释为“在线知识蒸馏”，分析前向/反向 KL 对保留先验知识的影响。  
   - Vassoyan et al. 2025 主张在关键 token 上“忽略 KL 惩罚”以增强探索，与本文“稳定训练”目标互补。

5. **异步/离策略 RL 训练框架**  
   - Noukhovitch et al. 2024、Bartoldson et al. 2025 提出高异步 RLHF 流水线，发现 KL 正则化可缓解离策略带来的不稳定，但未探讨不同估计器差异。

6. **开源工具链中的实现差异**  
   - von Werra et al. 2020（TRL）、Hu et al. 2024（OpenRLHF）、Sheng et al. 2025（VeRL）、Cui et al. 2025（Implicit PRM）等库在 KL 项默认估计器与摆放位置上存在分歧，成为本文实证对比的直接动机。

综上，本文首次把“KL 估计器选择 × 摆放方式 × 梯度偏差”作为一个系统问题提出，并通过大规模实验验证其对训练稳定性与分布外泛化的决定性影响，填补了上述研究在工程实践与理论一致性之间的空白。

## 解决方案

论文采用“先拆解梯度偏差、再大规模验证、最后给出可执行方案”的三段式路线，系统解决了“KL 正则化到底该怎么实现”的问题。

1. 理论拆解：把“序列级反向 KL 梯度”写成路径导数 + 得分函数两项  
   $$  
   \nabla_\theta \mathbb{E}_{y\sim\pi_\theta}\bigl[\widehat{KL}\bigr]  
   = \underbrace{\mathbb{E}\bigl[\sum_t \nabla_\theta \widehat{KL}_t\bigr]}_{\text{path-wise}}  
   + \underbrace{\mathbb{E}\bigl[\bigl(\sum_t \widehat{KL}_t\bigr)\nabla_\theta\log\pi_\theta(y|x)\bigr]}_{\text{score function}}  
   $$  
   据此导出四种常见配置的“期望梯度偏差”闭合表达式，证明：  
   - K1-in-reward：两项恰好抵消掉多余项 → 无偏  
   - K1-in-loss：path-wise 项期望为 0 → 等效于 β=0，但引入额外方差  
   - K3-in-reward / K3-in-loss：均残留非零偏置项 → 有偏

2. 合成环境验证：在可计算真梯度的二元自回归模型上，蒙特卡洛测量不同配置随序列长度 T 的偏差与方差，与理论预测完全吻合，确认偏差来源。

3. LLM 大规模 RL 实验  
   a) 严格 on-policy 设置（batch-size=mini-batch-size，ω=1）  
      – Qwen2.5-7B、Llama-3.1-8B-Instruct 在 MATH 7500 题上训练，β∈{0,0.05,0.1,0.3,1}  
      – 结果：  
        * K1-in-loss 高方差 → 训练曲线震荡甚至崩溃  
        * K3-in-reward 高偏差 → 训练直接崩溃  
        * K3-in-loss 虽稳定但分布外任务平均落后 K1-in-reward 3–19 %  
   b) 高异步 off-policy 设置（async-level=10）  
      – Qwen2.5-7B / Qwen3-4B-Instruct-2507 在 MATH/Countdown 上训练  
      – 结果：无 KL 时训练不稳定；K1-in-reward 与 K3-in-loss 均能提供稳定信号，且前者仍保持最高性能。

4. 修正方案验证  
   在 on-policy 场景，把 KL 项**同时**加入 reward 与 loss（β/2 各一份），可让任意估计器都恢复无偏梯度。实验显示：  
   - K1-reward+loss、K3-reward+loss 的训练曲线与最终指标几乎重合，且一致优于单用 K3-in-loss。

5. 工程落地清单  
   给出 VeRL、OpenRLHF、SkyRL 等主流库一行级参数配置，确保社区可直接切换到“无偏默认”。

通过以上步骤，论文不仅回答了“为什么有的 KL 实现会炸”，也给出了“怎样改一定更稳、更强”的可复现方案。

## 实验验证

论文共设计并执行了 **4 组互补实验**，覆盖“合成验证 → 严格 on-policy → 高异步 off-policy → 偏差修正”全链条，具体如清单所示：

1. 合成模型验证实验（§3.3）  
   目的：在可计算真梯度的最小环境中，直接测量不同 KL 配置的“期望梯度偏差”与方差。  
   - 构造二元自回归分布 Aθ、Bϕ，序列长度 T=5–20。  
   - 200 轮蒙特卡洛，每轮 1000 条样本，分别采集 K1-in-reward、K1-in-loss、K3-in-reward、K3-in-loss 的梯度估计。  
   结果：  
   - K1-reward 偏差≈0，与理论吻合；其余三种配置均出现显著非零偏差，且 K3-reward 偏差最大。  
   - 方差分析显示 K1-loss 虽偏差较低但方差高，解释后续 LLM 训练震荡。

2. 严格 on-policy LLM 微调实验（§4.1）  
   目的：排除 off-policy 干扰，孤立考察“梯度偏差”对训练稳定性与下游性能的影响。  
   - 模型：Qwen2.5-7B、Llama-3.1-8B-Instruct  
   - 数据：MATH 训练集 7500 题，G=5 rollout，batch-size=mini-batch-size=256（ω≡1）  
   - 变量：KL 配置 × β∈{0,0.05,0.1,0.3,1}  
   - 评测：  
     – 分布内：MATH500、MATH2  
     – 分布外：MMLU-college physics/chemistry/biology  
   结果：  
   - K1-loss 在 β≥0.3 时训练曲线剧烈震荡甚至 Pass@1 跌至 0.1 以下；Llama 模型在 β=0.05 亦不稳定。  
   - K3-reward 所有 β 均出现“部分或完全崩溃”，验证高偏差危害。  
   - K3-loss 训练平稳，但平均准确率系统性地低于 K1-reward（Qwen 分布外任务最高差距 19 %）。  
   - K1-reward 在 β=0.05/0.1 下取得最佳综合性能，且随 β 增大单调下降，符合无偏正则化预期。

3. 高异步 off-policy 实验（§4.2）  
   目的：验证 KL 配置在工程常用“高延迟异步”流水线中的稳定性与最终效果。  
   - 框架：Dr. GRPO，async-level=10（采样与更新相隔 10 个步骤）  
   - 设置 A：Qwen2.5-7B + MATH，512 batch，16 rollout，β=0.005  
   - 设置 B：Qwen3-4B-Instruct-2507 + Countdown 任务，同规模超参  
   结果：  
   - 无 KL 正则化时，训练早期即出现大幅波动，最终 MATH500 准确率低于 0.4。  
   - K1-reward 与 K3-loss 均能提供稳定提升，其中 K1-reward 终盘领先 3–5 个百分点，与 on-policy 结论一致。  
   - K1-loss 与 K3-reward 再次表现出明显震荡或早停，证实偏差危害与 on-policy 场景相同。

4. 无偏修正方案实验（§4.3）  
   目的：验证“把 KL 同时放入 reward 与 loss”是否真能在任意估计器上恢复无偏梯度并提升性能。  
   - 模型：Qwen2.5-7B，完全 on-policy，β=0.1，训练 150 步  
   - 配置：K1-reward+loss、K3-reward+loss、K1-reward（基准）、K3-loss（基准）  
   结果：  
   - 两种“双放”配置训练曲线几乎重合，且 Pass@1 始终高于单用 K3-loss。  
   - 分布外任务平均相对提升 6–8 %，证明偏差修正带来的泛化增益可重复。

通过上述实验，论文从最小合成环境到 4B–8B 级模型、从理想 on-policy 到高异步 off-policy，全方位验证了“梯度偏差 → 训练稳定性/最终性能”因果链，并给出了可直接落地的无偏配置。

## 未来工作

以下问题仍待后续工作深入挖掘，按“理论-算法-系统-评测”四个层面列出：

---

### 理论层面
1. **序列级 off-policy 无偏估计器**  
   现有重要性采样仅修正 token-level 梯度，无法给出序列级 $KL(\pi_\theta\|\pi_{\text{ref}})$ 的无偏梯度。能否设计轻量级 $\omega_s$ 估计或 Metropolis-Hastings 类校正，使 async-level 任意大时仍保持无偏？

2. **前向 vs 反向 KL 的 task-dependent 最优性**  
   本文证实 K3-in-loss 等效于“前向 KL 蒸馏”。是否存在可量化的任务特征（解空间大小、奖励稀疏度）决定反向 KL 或前向 KL 更优？

3. **非平稳参考策略的理论保证**  
   工程上常定期把 $\pi_{\text{ref}}$ 替换为最新 checkpoint（“ref-reset”）。该非平稳性对收敛点与偏差的影响尚无分析。

---

### 算法层面
4. **自适应 β 与 token-level β_t**  
   全局常数 β 无法区分“关键 token”与“填充 token”。能否基于 advantage 或梯度幅度的在线统计量，实现 per-token、per-step 的 β 自适应？

5. **方差-偏差权衡的新估计器**  
   K1 无偏但方差高，K3 方差低却有偏。能否借鉴控制变量或多重重要性采样，构造“第三估计器”同时实现低方差与零偏差？

6. **与并行化策略梯度（如 DPPO、SPPO）的耦合**  
   当引入张量并行、序列并行时，KL 项需跨设备规约。估计器与摆放策略是否会影响通信复杂度或数值稳定性？

---

### 系统层面
7. **千亿级模型的 KL 正则化开销**  
   8B 模型下 K1-in-reward 仅增加 <2% 显存。当参数扩展至 100B+、序列长度 32k+ 时，显存与延迟代价是否仍可接受？

8. **流水线异步中的“延迟 KL”**  
   采样与更新相隔数百步时，$\pi_{\text{old}}$ 与 $\pi_\theta$ 分布漂移显著。能否缓存旧 log-prob 或采用 checkpoint-averaged $\pi_{\text{ref}}$ 缓解？

9. **多任务/多目标 RL 的 KL 共享机制**  
   同时优化数学、代码、对话任务时，是维护“全局 $\pi_{\text{ref}}$”还是“任务专属 $\pi_{\text{ref}}$”？不同策略对遗忘与迁移的影响待验证。

---

### 评测与现象层面
10. **长链推理（>8k tokens）的 KL 累积效应**  
    序列长度增加后，token-level 误差累积可能导致 KL 估计漂移。需测量 Pass@1 随长度的衰减曲线，并校准 β。

11. **多语言、多模态场景的适用性**  
    本文仅关注英文数学/科学。KL 配置在低资源语言或图文混合模型上是否仍保持“无偏=更稳”规律？

12. **与人类偏好对齐的交互影响**  
    在 RLHF 阶段使用 K1-reward，再在 RLVR 阶段使用 K3-loss，是否会出现“偏差叠加”或“隐式蒸馏”现象？需要端到端追踪。

---

### 可立即动手的小课题
- 把 K1-reward 移植到已有开源库（DeepSpeed-Chat、OpenRLHF）的 70B 模型，观察是否再现“β>0.3 崩溃”现象。  
- 在相同算力预算下，比较“K1-reward + 低 β”与“K3-loss + 高 β”的 Pareto 前沿，验证无偏配置是否同时节省算力。

这些方向一旦突破，可直接提升大模型后训练的稳定性、泛化性与资源效率。

## 总结

**论文核心概要**

| 主题 | 要点 |
|---|---|
| **研究动机** | 大规模 LLM 强化学习后训练普遍使用 KL 正则化，但开源代码中对反向 KL 的估计方式与摆放位置（reward vs loss）不一致，且多数实现梯度有偏，缺乏系统评估。 |
| **理论贡献** | 给出序列级反向 KL 梯度的“路径导数+得分函数”分解式：<br>$$\nabla_\theta \mathbb{E}_{y\sim\pi_\theta}[\widehat{KL}]=\mathbb{E}\Bigl[\sum_t \nabla_\theta \widehat{KL}_t\Bigr]+\mathbb{E}\Bigl[\Bigl(\sum_t \widehat{KL}_t\Bigr)\nabla_\theta\log\pi_\theta(y|x)\Bigr]$$<br>并证明四种常见配置的梯度偏差：<br>• K1-in-reward：无偏<br>• K1-in-loss：期望为 0（等效 β=0）<br>• K3-in-reward / loss：均残留非零偏置 |
| **合成验证** | 在可解析的二元自回归模型上蒙特卡洛测量，偏差与方差结果与理论完全吻合。 |
| **LLM 实验** | 1. 严格 on-policy（Qwen2.5-7B、Llama-3.1-8B-Instruct + MATH）<br>– K1-in-loss 高方差→训练震荡；K3-in-reward 高偏差→崩溃；K3-in-loss 稳定但分布外性能落后 K1-reward 最高 19 %。<br>2. 高异步 off-policy（Qwen3-4B-Instruct-2507 + Countdown）<br>– 无 KL 时训练不稳定；K1-reward 与 K3-loss 均能提供稳定信号，前者仍领先。<br>3. 偏差修正：将 KL 同时放入 reward 与 loss，任意估计器均可恢复无偏梯度，实测性能一致优于单用 K3-loss。 |
| **工程输出** | 提供 VeRL、OpenRLHF、SkyRL 等库的一行参数配置，社区可直接采用“K1-in-reward”作为默认无偏实现。 |
| **结论** | 使用无偏梯度配置（K1-in-reward 或 KL 双放）可显著提升训练稳定性与分布外泛化；有偏配置即使训练未崩溃，也系统性地落后。 |



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
