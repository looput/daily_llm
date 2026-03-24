# Demystifying Group Relative Policy Optimization: Its Policy Gradient is a U-Statistic

**arXiv**: [2603.01162](https://arxiv.org/abs/2603.01162) · [PDF](https://arxiv.org/pdf/2603.01162)  
**领域**: RLHF  
**作者**: Zhou, Ye, Xu, Zhu, Yang, Gong, Shi  
**综合评分**: 8.08  （novelty: 9.0 · method: 9.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文对Group Relative Policy Optimization（GRPO）这一在DeepSeekMath和DeepSeek-R1等大型语言模型推理能力扩展中发挥核心作用的方法进行了深入的理论分析。作者团队（Zhou, Ye, Xu, Zhu, Yang, Gong, Shi）通过将GRPO的策略梯度解释为经典的U-统计量，建立了一个统一的理论框架，揭示了其渐近最优性和普适的缩放规律。这项工作填补了GRPO理论研究的空白，为算法设计和参数选择提供了坚实的理论指导。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

这篇论文试图解决**Group Relative Policy Optimization (GRPO)** 算法的理论基础与统计性质分析问题。GRPO作为DeepSeek-R1和DeepSeekMath等大规模推理模型的核心训练方法，虽然在实践中表现出色，但其理论机制尚未得到充分研究。

具体而言，论文针对以下四个核心问题提供了系统性的理论解答：

### 1. **GRPO有效性的理论根源** (Q1: Why is GRPO so effective?)
论文通过建立GRPO与经典**U-统计量 (U-statistics)** 的等价关系，证明GRPO的策略梯度本质上是一个二阶U-统计量。基于Hoeffding分解，论文证明了GRPO具有**Oracle性质**（即渐近等价于拥有真实价值函数访问权限的Oracle算法）和**最优性**（在广泛的策略梯度算法类中渐近最小化均方误差）。

### 2. **组均值作为Critic近似的合理性** (Q2: Rationale for group mean)
论文利用U-统计量理论解释了为何使用组均值（group mean）替代传统的Critic网络是统计上合理的。通过Hoeffding分解，论文证明组均值 baseline 对应于U-统计量的一阶投影项，这使其方差结构优于无 baseline 的Vanilla策略梯度，并在样本量增大时趋近于Oracle baseline。

### 3. **有限样本与渐近收敛分析** (Q3: Finite-sample and asymptotic analyses)
- **有限样本性质**：论文推导了GRPO策略梯度的均方误差（MSE）上界（Theorem 2 & Proposition 3）以及次优性间隙（suboptimality gap）的有限样本误差界（Lemma 6）。
- **渐近分布**：在不假设参数可识别性的情况下（这对过参数化的LLM至关重要），论文建立了参数估计的一致性以及次优性间隙的渐近分布（Theorem 8），证明其收敛于加权卡方分布。

### 4. **最优组大小选择** (Q4: Optimal group size)
论文建立了一个**通用缩放定律 (universal scaling law)**，明确了最优组大小 $G^*$ 的计算公式：
$$G^* = \sqrt{\frac{c_3}{c_1}}$$
其中 $c_1, c_3$ 为取决于数据和模型架构的常数。该定律表明最优组大小仅与训练数据和模型结构相关，而与训练预算、迭代次数无关，为超参数选择提供了原则性指导。

综上所述，论文通过**U-统计量理论框架**，首次为GRPO提供了严格的统计理论支撑，填补了该算法在有限样本理论、渐近分析和超参数优化方面的理论空白。

## 相关工作

根据论文第2节（Related Works）的内容，相关研究主要分布在以下三个领域：

## 1. 强化学习（Reinforcement Learning）

### 经典与深度强化学习
- **基础算法**：包括表格型Q-learning [Watkins and Dayan, 1992]、拟合Q迭代 [Ernst et al., 2005]、策略梯度方法REINFORCE [Williams, 1992]、Actor-Critic架构 [Konda and Tsitsiklis, 1999]
- **信赖域方法**：TRPO [Schulman et al., 2015] 及其后继者PPO [Schulman et al., 2017]，后者广泛应用于机器人学和LLM微调
- **深度RL里程碑**：DQN [Mnih et al., 2015]、AlphaGo [Silver et al., 2016]，以及后续的双Q学习 [Van Hasselt et al., 2016]、分布式RL [Dabney et al., 2018]

### 离线强化学习（Offline RL）
- **悲观原则**：Swaminathan and Joachims [2015a,b]、Kumar et al. [2019]、Jin et al. [2021]、Rashidinejad et al. [2021]
- **离策略评估**：Thomas et al. [2015]、Jiang and Li [2016]、Liu et al. [2018]、Kallus and Uehara [2022]

### 基于人类反馈的强化学习（RLHF）
- **开创性工作**：Ouyang et al. [2022]、Christiano et al. [2017]
- **理论分析**：参数估计的渐近分布 [Liu et al., 2024]；次优性间隙和遗憾的非渐近界 [Chowdhury et al., 2024; Zhong et al., 2024; Aminian et al., 2025; Ye et al., 2025]

### 与统计学的交叉
- **动态治疗规则（DTRs）**：Q-learning [Qian and Murphy, 2011; Song et al., 2015]、A-learning [Murphy, 2003; Robins, 2004; Shi et al., 2018]、基于策略的方法 [Zhang et al., 2013; Zhao et al., 2015]
- **长时域MDP**：Ertefaie and Strawderman [2018]、Luckett et al. [2020]、Liao et al. [2022]、Li et al. [2024a]

## 2. 可验证奖励的强化学习（RLVR）

### GRPO的发展脉络
- **原始RLVR**：Lambert et al. [2025] 提出使用PPO和客观验证器替代人类偏好
- **GRPO突破**：DeepSeekMath [Shao et al., 2024] 首次引入无Critic的组相对策略优化；DeepSeek-R1 [Guo et al., 2025] 将其推广并验证；Qwen2.5 [Yang et al., 2025] 等开源模型跟进

### GRPO的后续改进（三类）
1. **梯度估计器改进**：
   - Baseline修改或替换 [Hao et al., 2025; Xiao et al., 2025a; Zeng et al., 2025; Li et al., 2024b; Ahmadian et al., 2024; Hu et al., 2025]
   - 重要性采样修正或移除 [Zheng et al., 2025a; Pang and Jin, 2025; Chu et al., 2025]
   - 奖励归一化 [Xiong et al., 2025; Liu et al., 2025c; Xiao et al., 2025a]

2. **优化目标变体**：
   - 熵正则化防止熵坍缩 [Zhang et al., 2025b; Cheng et al., 2025; Chen et al., 2025]
   - 长度/难度感知目标 [Zhang and Zuo, 2025; Dai et al., 2025]
   - 风险敏感目标 [Ren et al., 2026]
   - 自适应推理格式选择 [Wu et al., 2025a]

3. **训练效率提升**：
   - 利用离线数据 [Yan et al., 2025]
   - 轨迹重用 [Li et al., 2025a; Zhan et al., 2026]
   - 并行推理加速 [Zheng et al., 2025b]
   - 单样本输出降低计算成本 [Xu and Ding, 2026]

### GRPO的理论分析（现有局限）
- **偏差分析**：Liu et al. [2025c]、Yang et al. [2026] 研究策略梯度和优势函数的偏差
- **梯度界**：Pang and Jin [2025] 对KL正则化目标的梯度平方上界进行刻画
- **目标函数**：Davis and Recht [2025] 证明GRPO优化的是arcsin变换的期望奖励；Vojnovic and Yun [2025] 表征GRPO的目标函数
- **离线策略性质**：Yao et al. [2026] 讨论GRPO的离线策略特性

## 3. U-统计量理论

### 基础理论
- **开创性工作**：Hoeffding [1948] 引入U-统计量并建立渐近理论
- **Hoeffding分解**：将U-统计量分解为正交分量，建立与样本均值的渐近等价性

### 应用领域
- **概率论**：U-过程 [Nolan and Pollard, 1987]
- **半参数统计**：高阶影响函数 [Liu et al., 2017]
- **计量经济学**：最大秩相关估计 [Han, 1987; Sherman, 1993]
- **精准医疗**：一致性辅助学习（Concordance-assisted learning）用于估计最优个体化治疗规则 [Fan et al., 2017; Liang et al., 2018]

## 解决方案

论文通过构建一个**基于U-统计量理论的统一分析框架**来解决GRPO的理论刻画问题。具体技术路线如下：

## 1. 核心洞察：识别GRPO的U-统计量本质

论文首先建立了一个关键的理论连接（**Lemma 1**）：

> **GRPO的策略梯度估计器本质上是一个二阶U-统计量。**

具体而言，对于固定提示$x$，GRPO的组相对梯度估计器可表示为：
$$\hat{g}_{\text{GRPO}}(x;\theta) = \binom{G}{2}^{-1} \sum_{1\leq i<j\leq G} h\left((Y^{(i)},Z^{(i)}), (Y^{(j)},Z^{(j)})\right)$$

其中对称核函数为：
$$h(\cdot,\cdot) = \frac{1}{2}\left[\nabla_\theta \log \pi_\theta(Y^{(i)}|x) - \nabla_\theta \log \pi_\theta(Y^{(j)}|x)\right](Z^{(i)} - Z^{(j)})$$

这一识别是后续所有理论分析的基石，它将GRPO的组均值baseline（leave-one-out group mean）转化为经典的U-统计量形式。

## 2. 技术工具：Hoeffding分解

利用U-统计量的经典**Hoeffding分解**，论文将梯度估计器分解为三个正交分量：

$$\hat{g}_{\text{GRPO}} = \underbrace{g(\theta)}_{\text{真实梯度}} + \underbrace{\text{一阶项}}_{O_p(G^{-1/2})} + \underbrace{\text{二阶退化项}}_{O_p(G^{-1})}$$

其中：
- **一阶项**恰好对应于使用真实价值函数$V^{\pi_\theta}(x)$作为baseline的Oracle梯度估计器
- **二阶项**以更快的速率收敛，是高阶残差

这一分解直接导出了GRPO的**Oracle性质**（当$G\to\infty$时，GRPO的MSE趋近于Oracle算法）和**最优性**（在广泛的baseline函数类中方差最小）。

## 3. 分层理论分析

论文从两个层面建立理论保证：

### 层面一：梯度估计（有限样本性质）
通过U-统计量的方差结构，论文推导了**均方误差（MSE）的精确刻画**（**Theorem 2** & **Proposition 3**）：

$$\text{MSE}(\hat{g}_{\text{GRPO}}(\theta)) = \underbrace{\frac{\mathbb{E}\|g(X;\theta)-g(\theta)\|^2}{B}}_{\text{提示采样方差}} + \underbrace{\frac{\text{trace}[\Sigma_{\text{oracle}}(\theta)]}{BG}}_{\text{Oracle方差}} + \underbrace{O\left(\frac{1}{BG^2}\right)}_{\text{高阶残差}}$$

### 层面二：策略优化（学习保证）
基于MSE结果，论文进一步分析**次优性间隙（Suboptimality Gap）**：
- 建立有限样本误差界（**Lemma 6**），显示收敛速率依赖于梯度估计器的MSE
- 在不假设参数可识别性（对过参数化LLM至关重要）的情况下，证明参数估计的一致性并推导次优性间隙的**渐近分布**（**Theorem 8**）：
$$n\Delta(\pi_{\theta_n}) \xrightarrow{d} \sum_{k=1}^r w_k \chi^2_{1,k}$$

## 4. 最优性理论

论文通过比较三种算法（Vanilla/GRPO-type/Oracle）建立GRPO的优势：

| 算法 | Baseline形式 | 渐近MSE |
|------|-------------|---------|
| Vanilla | $C=0$ | 次优（高方差）|
| GRPO-type | $\bar{Z}^{(b,-g)}$（组均值） | 趋近Oracle |
| Oracle | $V^{\pi_\theta}(X)$ | 最优下界 |

**关键结论**：
- **Oracle性质**（**Corollary 4 & 9**）：当$G$足够大时，GRPO的梯度估计和策略性能渐近等价于拥有真实价值函数的Oracle算法
- **最优性**（**Corollary 5 & 10**）：GRPO在所有仅依赖提示$X$的baseline函数类中，渐近最小化MSE和次优性间隙

## 5. 实用扩展

论文还将理论扩展至实际GRPO实现中的三个关键细节（补充材料Section A）：
- **奖励标准化**（Reward normalization）
- **重要性采样**（Importance sampling for off-policy updates）
- **KL散度惩罚**（KL divergence penalty）

通过建立相应的U-统计量表示（**Lemma 11**），证明这些实际技巧不会改变GRPO的核心统计性质。

## 6. 缩放定律（Scaling Law）

基于MSE分解，论文推导了**最优组大小**的闭式解（**Theorem 7**）：

$$G^* = \sqrt{\frac{c_3}{c_1}}$$

其中$c_1, c_3$为取决于数据分布和模型架构的常数。这一定律的**普适性**在于：最优组大小与训练预算、迭代次数无关，仅由任务固有特性决定，为超参数选择提供了原则性指导。

---

**总结**：论文通过将GRPO识别为U-统计量，利用经典统计理论中的Hoeffding分解工具，首次建立了该算法的有限样本误差界、渐近分布理论和最优性保证，填补了大规模语言模型推理训练算法的理论空白。

## 实验验证

论文在第5节（Section 5）进行了两组实验，旨在验证理论发现：**(i) GRPO梯度估计器的Oracle性质和最优性；(ii) 最优组大小（Group Size）的普适性**。

## 1. 梯度评估的Oracle性质验证（Section 5.1）

**实验目标**：验证GRPO梯度估计器的均方误差（MSE）确实优于Vanilla REINFORCE，并随组大小$G$增加而趋近于Oracle估计器（Corollary 4 & 5）。

**实验设置**：
- **数据集**：构建了一个包含500道题目的合成算术数据集，涵盖五类中等难度整数运算（两步加减、三步加减、单步乘法、整数除法、带括号运算）
- **评估对象**：三种策略梯度估计器：
  - **Vanilla**：无baseline（$C=0$）
  - **GRPO-type**：使用leave-one-out组均值baseline（公式4）
  - **Oracle**：使用真实价值函数$V^{\pi_\theta}(x)$作为baseline
- **模型配置**：基于Qwen/Qwen2.5-0.5B的三个不同版本，代表不同推理能力水平：
  - **Base model**：基础预训练模型（推理能力最弱）
  - **Instruct model**：指令微调版本
  - **ICL model**：Instruct模型结合上下文学习（in-context learning），提供少样本示例（推理能力最强）
- **组大小**：$G \in \{4, 8, 16, 32, 64\}$
- **评估指标**：通过蒙特卡洛模拟估计MSE及95%置信区间

**主要发现**（见图4）：
- **优势验证**：在所有模型配置和组大小下，GRPO-type估计器的MSE显著低于Vanilla估计器，验证了其方差缩减的有效性
- **Oracle性质**：当$G=8$时，GRPO-type的MSE已接近Oracle估计器；当$G$增至32或64时，两者几乎无法区分，证实了渐近等价性
- **模型能力影响**：所有估计器的MSE随模型推理能力增强而降低（ICL < Instruct < Base），因为更强的模型输出更确定，随机性更小

## 2. 最优组大小的普适性验证（Section 5.2）

**实验目标**：验证理论推导的最优组大小$G^* = \sqrt{c_3/c_1}$的**普适性**——即$G^*$不随训练迭代次数$n$或总采样预算$N$变化，仅取决于数据和模型架构（Theorem 7）。

### 实验2a：GSM8K数据集（验证与迭代次数$n$的无关性）

**实验设置**：
- **数据集**：GSM8K（数学推理基准）
- **模型**：Qwen2.5-1.5B Instruct
- **固定预算**：每轮迭代总采样数$N = B \times G = 1024$（固定）
- **组大小**：$G \in \{4, 8, 16, 32, 64, 128\}$（对应调整批次大小$B$以保持$N$不变）
- **训练迭代**：在多个中间检查点（steps 200, 300, 400, 600, 800）及最终步骤记录测试准确率
- **重复性**：每组配置独立运行5次，报告均值及95%置信区间

**主要发现**（见图5）：
- **最优组大小稳定**：除step 200外，所有训练步骤的最优组大小均为$G^* = 32$，验证了$G^*$与迭代次数$n$的无关性
- **性能曲线**：测试准确率随$G$先增后减，形成倒U型曲线——小$G$时二阶残差项主导，大$G$时提示采样方差（第一项）因批次$B$过小而过大的权衡关系得到实证支持

### 实验2b：MATH数据集（验证与采样预算$N$的关系）

**实验设置**：
- **数据集**：MATH（更具挑战性的数学推理基准）
- **模型**：更大的Qwen2.5-Math-7B模型
- **采样预算**：$N \in \{1024, 2048, 4096\}$（每轮迭代每提示的采样总数）
- **组大小**：同上，$G \in \{4, 8, 16, 32, 64, 128\}$

**主要发现**（见Table 2）：
- **预算依赖性**：当$N=1024$或$2048$时，最优组大小为$G^*=64$；当$N$增至$4096$时，最优组大小移至$G=128$
- **模型规模影响**：与GSM8K（1.5B模型，$G^*=32$）相比，7B模型在相同预算下需要更大的$G^*$（64），符合理论预测——模型容量和数据复杂度影响常数$c_1, c_3$，从而改变$G^*$
- **提示数量限制**：作者指出$G^*$随$N$增加而增大的现象可能源于固定提示数量的有限性（未在理论分析中显式建模）

## 实验总结

这两组实验共同验证了论文的理论预测：
1. **统计效率**：GRPO通过组均值baseline有效降低梯度方差，且在大组大小下达到理论最优性能
2. **超参数选择**：最优组大小$G^*$是任务和模型固有的超参数，不随训练时长或计算预算变化，为实际部署提供了稳定的调参指导

## 未来工作

基于论文的理论框架和实验验证，以下几个方向值得进一步探索：

## 1. 最优组大小的实际估计与自适应选择
论文在定理7中推导了最优组大小的解析式 $G^* = \sqrt{c_3/c_1}$，但明确指出**实际估计这些常数并验证估计的$G^*$**超出了当前工作范围。未来可以：
- 开发在线估计$c_1, c_3$的方法（基于参考模型的少量样本估计）
- 设计**自适应组大小选择策略**，根据训练过程中观察到的梯度方差动态调整$G$
- 探索分层组大小：针对不同难度或类型的提示使用异质性组大小

## 2. 提示数据集规模的理论刻画
在MATH实验（表2）中，作者观察到当总采样预算$N$增加时，最优组大小$G^*$有增大趋势，怀疑这与**有限的提示数量**（固定提示集不随$B,G$变化）有关。当前理论假设提示分布$f(X)$是固定的，未来可：
- 将有限提示集（finite prompt set）的采样方差显式纳入理论框架
- 分析提示数量、批次大小$B$和组大小$G$之间的三重权衡关系

## 3. 复杂实际场景的理论扩展
虽然论文在附录中处理了奖励标准化、重要性采样和KL惩罚，但以下实际细节仍需理论分析：
- **长度惩罚与格式奖励**：当奖励函数包含输出长度惩罚（如鼓励简洁推理）时的统计性质
- **多模态奖励**：奖励信号来自多个验证器（如代码执行+单元测试+格式检查）的U-统计量扩展
- **动态温度调度**：探索阶段（高温度）与利用阶段（低温度）对组大小选择的影响

## 4. 计算-统计权衡的量化分析
论文主要关注统计效率（MSE、次优性间隙），但GRPO的核心动机是**计算效率**（避免训练Critic网络）。未来可建立：
- 计算成本（FLOPs）与统计精度的帕累托前沿分析
- 在固定GPU内存预算下，$B$与$G$的最优分配策略（考虑KV-cache限制）

## 5. 多轮交互与工具使用的理论
论文将推理建模为单轮bandit问题（$T=1$）。对于**多轮交互场景**（如工具调用、多步验证、自我修正）：
- 将U-统计量理论扩展至序贯决策（sequential decision making）框架
- 分析跨步骤的组相对优势估计（inter-step relative advantages）

## 6. 高阶U-统计量与核设计
论文建立了GRPO与**二阶U-统计量**的等价性。可探索：
- **高阶U-统计量**（$m>2$）是否通过更高阶的Hoeffding分解进一步降低方差
- 设计非对称核函数或自适应核，针对特定任务结构优化

## 7. 非渐近高概率界与鲁棒性
当前分析主要基于MSE和渐近分布。更强的**非渐近高概率收敛界**（high probability bounds）将有助于：
- 理解GRPO在分布偏移（distribution shift）下的鲁棒性
- 建立样本复杂度与泛化误差的定量关系

## 8. 与其他RLVR变体的理论比较
论文主要对比了Vanilla和Oracle算法。未来可系统比较：
- GRPO与Dr-GRPO、GPG等无Critic变体的统计效率差异
- 组相对优势vs.其他baseline（如学习得到的baseline、历史平均）的方差缩减理论

这些方向既延续了论文的U-统计量理论框架，又回应了实际部署中的关键工程挑战。

## 总结

这篇论文系统性地解构了**Group Relative Policy Optimization (GRPO)** —— 驱动DeepSeek-R1、DeepSeekMath等大规模推理模型的核心强化学习算法 —— 的统计理论基础，填补了该算法在形式化分析方面的空白。

## 1. 研究背景与问题
GRPO通过**消除Critic网络**、使用**组均值（group mean）作为baseline**来降低计算成本，在可验证奖励的强化学习（RLVR）中展现出卓越性能。然而，其理论机制长期不明：为何组均值近似有效？其收敛性如何？最优超参数如何选择？

## 2. 核心理论框架：U-统计量视角
论文的关键洞察在于识别出**GRPO的策略梯度估计器本质上是一个二阶U-统计量**（Lemma 1）。通过经典的**Hoeffding分解**，该估计器被解析为：
- **零阶项**：真实策略梯度
- **一阶项**：对应使用真实价值函数的Oracle估计器（主导方差，$O_p(G^{-1/2})$）
- **二阶退化项**：高阶残差（$O_p(G^{-1})$）

这一分解为所有后续理论分析奠定了数学基础。

## 3. 主要理论贡献

### 梯度估计层面
- **均方误差（MSE）刻画**（Theorem 2 & Proposition 3）：精确分解了批次大小$B$和组大小$G$对梯度估计方差的影响
- **Oracle性质**（Corollary 4）：当$G \to \infty$时，GRPO的MSE渐近等价于拥有真实价值函数的Oracle算法
- **最优性**（Corollary 5）：在所有仅依赖提示的baseline函数类中，GRPO渐近最小化MSE

### 策略优化层面
- **有限样本保证**（Lemma 6）：在Polyak-Łojasiewicz条件下，建立了次优性间隙（suboptimality gap）的显式误差界
- **渐近分布**（Theorem 8）：在不假设参数可识别性（对过参数化LLM至关重要）的情况下，证明$n\Delta(\pi_{\theta_n})$收敛于加权卡方分布$\sum_{k=1}^r w_k\chi^2_{1,k}$
- **策略最优性**（Corollary 9 & 10）：验证了GRPO学习到的策略在渐近意义下等价于Oracle算法，且优于REINFORCE等基线

### 实用缩放定律
- **最优组大小公式**（Theorem 7）：推导了$G^* = \sqrt{c_3/c_1}$，证明最优组大小仅取决于数据和模型架构，与训练预算、迭代次数无关，为超参数选择提供了普适性指导

## 4. 实验验证
- **梯度估计验证**：在合成算术数据集上，证实GRPO的MSE显著低于Vanilla REINFORCE，且随$G$增大趋近于Oracle估计器（Figure 4）
- **最优组大小普适性**：在GSM8K和MATH数据集上，验证了$G^*$在不同训练迭代和模型规模下的稳定性（Figure 5 & Table 2）

## 5. 结论与意义
论文通过U-统计量理论框架，首次为GRPO提供了严格的统计保证，解释了其计算效率与统计效率兼得的根源：组均值baseline通过U-统计量的一阶投影实现了方差最优缩减。所建立的缩放定律为大规模语言模型推理训练的超参数调优提供了原则性指导。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
