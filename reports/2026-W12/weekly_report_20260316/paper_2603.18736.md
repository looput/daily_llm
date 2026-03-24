# CausalRM: Causal-Theoretic Reward Modeling for RLHF from Observational User Feedbacks

**arXiv**: [2603.18736](https://arxiv.org/abs/2603.18736) · [PDF](https://arxiv.org/pdf/2603.18736)  
**领域**: RLHF  
**作者**: Wang, Pan, Chen, Zheng, Chu, Li, Lu, Liu 等 10 人  
**综合评分**: 7.85  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文提出了一种基于因果理论的奖励建模框架CausalRM，用于从观测性用户反馈（如点击、复制、点赞）中学习无偏的奖励模型，以替代传统RLHF中依赖昂贵人工标注反馈的方法。该方法通过显式建模标注错误生成过程引入噪声感知的代理损失项，并使用倾向得分对训练样本进行重加权以消除用户偏好偏差。在多个LLM骨干网络和基准数据集上的广泛实验验证了CausalRM的有效性，在WildGuardMix和HarmBench等下游RLHF任务上取得了显著性能提升（分别提升49.2%和32.7%）。代码已开源。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

这篇论文试图解决**从观测性用户反馈（observational user feedback）中学习奖励模型（Reward Modeling）用于RLHF（基于人类反馈的强化学习）时面临的双重挑战**。

具体而言，论文针对以下两个核心问题提出了系统性的解决方案：

### 1. 观测性反馈的噪声问题（Annotation Errors）
观测性反馈（如用户点击、复制、点赞等）往往包含**用户标注错误**。由于用户注意力不集中、冲动交互或恶意系统操控等因素，观测到的反馈 $r_i$ 可能偏离用户的真实偏好 $r^*_i$（例如，用户为了修改内容而复制文本，系统却将其记录为正面反馈）。这种标签噪声会导致奖励模型学习到错误的偏好信号。

### 2. 观测性反馈的选择偏倚问题（Preference Bias / Selection Bias）
观测性反馈是**有选择性地提供**的——用户倾向于仅对他们有强烈感受的响应提供反馈（无论是极度满意还是极度不满），而中性响应则被欠代表。这导致训练数据分布 $O$ 与推理阶段需要评估的完整数据分布 $D$ 之间存在**分布偏移**（distribution shift），使得在观测数据上训练的奖励模型在推断时产生偏倚的估计。

### 论文提出的解决方案
为应对上述挑战，论文提出了 **CausalRM**（Causal-Theoretic Reward Modeling）框架：
- **针对噪声**：引入**噪声感知的替代损失函数**（noise-aware surrogate loss），通过显式建模标注错误生成过程（假阳性率和假阴性率），使修正后的损失在期望上等价于无噪声条件下的原始损失。
- **针对偏倚**：采用**倾向得分重加权**（propensity score reweighting）策略，利用用户反馈概率（propensity score）对训练样本进行逆概率加权，消除用户偏好偏倚，使模型能够从偏倚的观测数据中恢复出对全量数据分布的无偏估计。

最终，CausalRM 旨在从低成本、可大规模获取但存在噪声和偏倚的观测性反馈中，学习出能够准确反映真实用户偏好的奖励模型，从而提升下游RLHF任务的性能。

## 相关工作

根据论文内容，相关研究主要分布在以下几个方向：

## 1. RLHF 与奖励建模基础
- **标准RLHF流程**：Ouyang et al. (2022) 提出了现代RLHF的两阶段范式（奖励建模+策略优化），该范式被 ChatGPT、Gemini、DeepSeek 等系统广泛采用。
- **偏好建模**：Bradley & Terry (1952) 提出的 Bradley-Terry 模型被广泛用于成对比较数据的建模，通过 $p(x^+ \succ x^-) = \sigma(\hat{r}(x^+) - \hat{r}(x^-))$ 估计偏好概率。

## 2. 处理选择偏倚的方法（Debias-based Methods）
这类方法主要针对**用户偏好偏倚**（feedback并非随机缺失，而是与用户偏好相关）：
- **IPS (Inverse Propensity Scoring)**：Rosenbaum & Rubin (1983) 提出通过逆倾向得分重加权来纠正选择偏倚。
- **MTIPS**：Zhang et al. (2020) 将多任务学习与IPS结合。
- **CVIB**：Wang et al. (2020) 使用信息论方法进行反事实学习。
- **DR (Doubly Robust)**：Robins et al. (1994) 提出双重稳健估计器，结合倾向得分和结果插补。
- **MTDR/SDR**：Zhang et al. (2020) 和 Li et al. (2023) 分别提出了多任务DR和稳定化DR方法用于推荐系统去偏。

## 3. 处理标签噪声的方法（Denoise-based Methods）
这类方法主要针对**标注错误**（annotation errors）：
- **F-correction**：Patrini et al. (2017a) 通过后验概率校正损失函数。
- **Co-teaching**：Han et al. (2018) 通过双网络互相筛选干净样本。
- **CoDis**：Xia et al. (2023) 挖掘高差异样本来对抗噪声标签。
- **LabelWave**：Yuan et al. (2025) 提出无需验证数据的早停策略。
- **Robust DivideMix**：Zhang et al. (2024a) 使用混合模型分割干净与噪声样本。
- **SelectMix**：Liu et al. (2025a) 通过目标样本混合增强鲁棒性。
- **ILDE**：Liao et al. (2025) 实例依赖的标签分布估计方法。

## 4. 带噪声偏好的RLHF相关研究
- **Chowdhury et al. (2024)** 和 **Liang et al. (2024)** 研究了从含噪反馈中训练LLM的问题，但这些工作主要针对**DPO (Direct Preference Optimization)** 范式，直接优化策略模型而**绕过奖励建模**，因此不适用于本文关注的奖励建模场景。

## 与现有工作的区别
现有方法的核心局限在于**仅针对单一挑战**：
- 去偏方法（IPS/DR等）假设标签无噪声，无法处理用户标注错误；
- 去噪方法（Co-teaching/ILDE等）假设数据分布无偏倚，无法处理用户选择性反馈导致的分布偏移。

CausalRM 首次**统一处理**了观测性反馈中的**标注错误**和**偏好偏倚**两大挑战，通过噪声感知的替代损失（noise-aware surrogate loss）和因果重加权（causal reweighting）的协同作用，实现了对真实用户偏好的无偏估计。

## 解决方案

论文通过提出 **CausalRM**（Causal-Theoretic Reward Modeling）框架，系统性地解决了观测性奖励建模中的**标注错误**（Challenge ❶）和**用户偏好偏倚**（Challenge ❷）问题。其核心思想是将奖励建模重新表述为**因果推断问题**，通过显式建模反馈生成机制和反事实查询，从有偏、带噪的观测数据中恢复真实用户偏好。

以下是具体解决方案的技术细节：

---

### 1. 问题形式化：潜在结果框架
论文采用潜在结果（Potential Outcome）框架形式化问题：
- **Unit** $x_i$：提示-响应对（prompt-response pair）
- **Feedback** $r_i \in \{0,1\}$：观测到的用户反馈（含噪）
- **Preference** $r^*_i$：真实的潜在用户偏好（无噪）
- **Treatment** $o_i \in \{0,1\}$：反馈是否被观测到的指示变量（受用户偏好影响）

理想学习目标为在所有候选响应 $D$ 上最小化与真实偏好的误差：
$$L_{\text{ideal}} = \frac{1}{|D|} \sum_{x_i \in D} \ell(\hat{r}_\theta(x_i), r^*_i)$$

但由于 $r^*_i$ 不可观测且 $o_i$ 存在选择机制，实际只能基于观测集 $O = \{x_i \mid o_i=1\}$ 进行训练。CausalRM 通过以下两个模块分别纠正**观测性偏倚**和**标注噪声**。

---

### 2. 解决标注错误：噪声感知的替代损失（Noise-aware Surrogate Loss）

针对 Challenge ❶（$r_i \neq r^*_i$），论文显式建模标注错误生成过程，定义四种错误概率：
- $\rho_{11} = P(r_i=1 \mid r^*_i=1)$（真阳性率）
- $\rho_{00} = P(r_i=0 \mid r^*_i=0)$（真阴性率）  
- $\rho_{01} = P(r_i=0 \mid r^*_i=1)$（假阴性率）
- $\rho_{10} = P(r_i=1 \mid r^*_i=0)$（假阳性率）

**核心思想**：构造一个**替代损失函数** $\tilde{\ell}$，使得在噪声分布下的期望等于无噪条件下的原始损失：
$$\mathbb{E}_{r_i \mid r^*_i}[\tilde{\ell}(\hat{r}_\theta(x_i), r_i)] = \ell(\hat{r}_\theta(x_i), r^*_i)$$

通过求解线性方程组，得到替代损失的解析形式：
$$
\begin{aligned}
\tilde{\ell}(\hat{r}_\theta(x_i), 1) &= \frac{(1-\rho_{10})\ell(\hat{r}_\theta(x_i), 1) - \rho_{01}\ell(\hat{r}_\theta(x_i), 0)}{1-\rho_{01}-\rho_{10}} \\
\tilde{\ell}(\hat{r}_\theta(x_i), 0) &= \frac{(1-\rho_{01})\ell(\hat{r}_\theta(x_i), 0) - \rho_{10}\ell(\hat{r}_\theta(x_i), 1)}{1-\rho_{01}-\rho_{10}}
\end{aligned}
$$

对于每个样本，根据观测标签 $r_i$ 计算：
$$\tilde{\ell}_i = r_i \cdot \tilde{\ell}(\hat{r}_\theta(x_i), 1) + (1-r_i) \cdot \tilde{\ell}(\hat{r}_\theta(x_i), 0)$$

**噪声率估计**：基于**弱可分性假设**（存在锚点单位几乎必然为正或负），通过预训练代理模型识别 $P(r_i=1 \mid x_i)$ 的最大/最小值点，估计：
$$\hat{\rho}_{01} = 1 - P(r_{\max}=1 \mid x_{\max}), \quad \hat{\rho}_{10} = P(r_{\min}=1 \mid x_{\min})$$

---

### 3. 解决用户偏好偏倚：因果重加权目标（Causally Reweighted Objective）

针对 Challenge ❷（$O \neq D$ 的分布偏移），论文引入**倾向得分**（Propensity Score）$p_i = P(o_i=1 \mid x_i)$，表示用户对特定响应提供反馈的概率。

#### CausalRM-IPS（逆概率加权）
通过逆倾向得分重加权观测样本，构建IPS估计器：
$$L^*_{\text{IPS}}(\phi, \theta; \rho_{01}, \rho_{10}) = \frac{1}{|D|} \sum_{i=1}^{|D|} \frac{o_i}{\hat{p}_\phi(x_i)} \tilde{\ell}(\hat{r}_\theta(x_i), r_i)$$

其中 $\hat{p}_\phi(x_i)$ 是倾向得分的估计模型。该目标通过对低倾向（难得反馈）样本赋予更高权重，纠正用户选择性反馈导致的分布偏倚。

#### CausalRM-DR（双重稳健估计）
为解决IPS在倾向得分估计不准或数值较小时的方差问题，引入**误差插补模型** $\hat{\varepsilon}_\psi(x_i)$ 预测替代损失，构建DR估计器：
$$L^*_{\text{DR}} = \frac{1}{|D|} \sum_{i=1}^{|D|} \left[ \hat{\varepsilon}_\psi(x_i) + \frac{o_i}{\hat{p}_\phi(x_i)} \left( \tilde{\ell}(\hat{r}_\theta(x_i), r_i) - \hat{\varepsilon}_\psi(x_i) \right) \right]$$

**双重稳健性**：只要倾向得分**或**误差插补中至少一项准确，$L^*_{\text{DR}}$ 就是 $L_{\text{ideal}}$ 的无偏估计。同时，DR估计器通过引入插补项降低了IPS的高方差问题（理论证明见 Appendix A）。

---

### 4. 完整训练流程（Algorithm 1）

CausalRM 采用三阶段训练流程：

1. **表示提取**：使用LLM骨干网络提取提示-响对的语义嵌入 $x_i$；
2. **辅助模型训练**：
   - 训练倾向得分估计器 $\hat{p}_\phi$：最小化 $L_{\text{prop}}(\phi) = \frac{1}{|D|} \sum (o_i - \hat{p}_\phi(x_i))^2$；
   - 训练代理奖励模型 $\hat{r}_\zeta$：使用IPS加权MSE在观测数据上训练，用于识别锚点以估计噪声率；
3. **主模型训练**（CausalRM-IPS或CausalRM-DR）：
   - 基于代理模型识别 $x_{\min}$ 和 $x_{\max}$，计算 $\hat{\rho}_{01}, \hat{\rho}_{10}$；
   - 使用 $L^*_{\text{IPS}}$ 或 $L^*_{\text{DR}}$ 训练最终奖励模型 $\hat{r}_\theta$，其中DR变体还需同步更新误差插补模型 $\hat{\varepsilon}_\psi$。

---

### 5. 理论保证
论文证明（Appendix A）：
- **定理1**：当噪声率和倾向得分准确估计时，$L^*_{\text{IPS}}$ 是 $L_{\text{ideal}}$ 的无偏估计；
- **定理2**：当噪声率准确且（倾向得分**或**误差插补）准确时，$L^*_{\text{DR}}$ 是无偏估计（双重稳健性）；
- **定理3**：在温和条件下，$L^*_{\text{DR}}$ 的方差小于 $L^*_{\text{IPS}}$。

通过上述设计，CausalRM 实现了从含噪、有偏的观测性反馈中**无偏地恢复**真实用户偏好，为下游RLHF提供准确的奖励信号。

## 实验验证

论文进行了系统性的实验验证，围绕**6个核心研究问题（RQs）**展开，涵盖奖励建模性能、消融分析、理论验证、超参数敏感性、跨架构泛化性以及下游RLHF任务评估。

---

### 1. 实验设置（4.1节）
- **数据集**：HelpSteer（有用性评分）、UltraFeedback（综合评分）、PKU-SafeRLHF（安全等级）
- **数据生成**：通过两阶段模拟观测性反馈——(1) 注入用户偏好偏倚（倾向得分 $p_i \propto \alpha^{\max(r^*)-r^*_i}$）；(2) 注入标注错误（假阴性率 $\rho_{01}$、假阳性率 $\rho_{10}$）
- **基线方法**：
  - **去偏方法**：IPS、MTIPS、CVIB、DR、MTDR、SDR
  - **去噪方法**：F-correction、Co-Teaching、CoDis、LabelWave、Robust DivideMix、SelectMix、ILDE
  - **朴素方法**：Naive（直接使用标准MSE）
- **评估指标**：均方误差（MSE）、平均绝对误差（MAE）、决定系数（$R^2$）

---

### 2. 主要性能对比（4.2节，对应RQ1）
在**两种噪声场景**（Scenario 1: $\rho_{01}=0.2, \rho_{10}=0.1$；Scenario 2: $\rho_{01}=0.1, \rho_{10}=0.2$）下比较各方法：
- **结果**：CausalRM-IPS 和 CausalRM-DR 在所有数据集和场景下均显著优于基线。例如，在PKU-SafeRLHF上，CausalRM-DR的$R^2$达到0.779（Scenario 1），相比Naive的0.559提升显著。
- **关键发现**：单纯去偏（如SDR）或单纯去噪（如ILDE）的方法均不及CausalRM，证明必须同时处理两种挑战。

---

### 3. 消融研究（4.3节，对应RQ2）
验证CausalRM各组件的独立贡献：
- **IPS系列**：
  - Naive：无去偏无去噪
  - CausalRM-IPS$^\dagger$：仅去偏（倾向加权），无去噪（标准MSE损失）
  - CausalRM-IPS$^\ddagger$：仅去噪（噪声感知损失），无去偏（均匀加权）
  - **CausalRM-IPS**：完整方法（去偏+去噪）
- **DR系列**：类似地设置CausalRM-DR$^\dagger$（仅去偏）和CausalRM-DR$^\ddagger$（仅去噪）
- **结论**：两个组件均带来性能提升，且联合使用时产生协同效应。

---

### 4. 半合成数据集验证（4.4节，对应RQ3）
为验证**理论无偏性**，在PKU-SafeRLHF上构造四种合成奖励预测场景：
- **ROTATE**：系统性偏移（循环移位分数）
- **SKEW**：异方差高斯噪声
- **ONE**：极端偏斜（翻转负样本）
- **FOUR**：细微过优化（翻转次高分数）

**结果**：
- CausalRM-IPS和CausalRM-DR的估计值与理想目标$L_{\text{ideal}}$的差异（$\Delta$）接近零
- 基线方法（如Naive、IPS、F-correction）均表现出显著偏差
- 验证了Theorem 1和Theorem 2的理论保证

---

### 5. 超参数敏感性分析（4.5节，对应RQ4）
#### 模型超参数
- **学习率$\eta$**：在$[5\times10^{-5}, 2\times10^{-3}]$范围内测试，CausalRM在较大学习率（如$5\times10^{-4}$）下表现最优且稳定
- **批次大小$B$**：在$[64, 1024]$范围内，CausalRM性能稳定（$R^2$保持在0.73-0.78），无需精细调参

#### 数据生成超参数
- **偏倚强度$\alpha$**（图4、图5）：当$\alpha$减小（偏倚增强）时，Naive方法性能急剧下降，而CausalRM保持鲁棒
- **噪声率$\rho$**（图4、图5）：随着$\rho$增大（0.01→0.2），所有方法性能下降，但CausalRM下降幅度最小

---

### 6. 跨架构泛化性（4.6节，对应RQ5）
测试CausalRM在不同LLM骨干上的适用性：
- **模型规模**：Qwen2.5（7B、14B、72B）和LLaMA2（7B、13B、70B）
- **结果**：CausalRM在所有规模和架构上均显著优于Naive基线，例如：
  - Qwen2.5-72B：MSE从0.083降至0.059（CausalRM-DR）
  - LLaMA2-70B：$R^2$从0.634提升至0.691（CausalRM-DR）
- **结论**：CausalRM是与模型架构无关的通用目标函数。

---

### 7. 下游RLHF安全对齐（4.7节，对应RQ6）
验证奖励模型在实际RLHF流程中的效用：
- **设置**：使用训练好的奖励模型，通过GRPO算法微调Qwen2.5-7B和LLaMA2-7B策略模型
- **评估基准**：HarmBench、WildGuardMix、DAN（使用DeepSeek-V3作为评判）
- **结果**：
  - **WildGuardMix**：CausalRM-DR取得49.2%的相对提升（Qwen2.5-7B）
  - **HarmBench**：CausalRM-DR取得32.7%的相对提升（Qwen2.5-7B）
  - 在LLaMA2-7B上同样一致优于SDR（去偏）和ILDE（去噪）等强基线
- **定性案例**（Table 8）：CausalRM训练的策略能坚定拒绝有害请求（如毒品制作），而基线方法常出现"奖励劫持"（表面拒绝但后续提供有害信息）。

---

### 8. 附录补充实验
- **扩展半合成实验**：在HelpSteer和UltraFeedback上重复验证理论无偏性（Appendix .3，Table 6-7）
- **R²敏感性**：展示在不同$\alpha$和$\rho$下的$R^2$变化曲线（Figure 6-7），证明CausalRM在强偏倚和高噪声下仍保持高相关性
- **计算开销**：CausalRM仅引入轻量级的MLP头（倾向模型、误差插补模型），训练 overhead 可控。

## 未来工作

基于论文第5节（Conclusion）中"Limitations & Future Works"的阐述以及技术框架的延伸，以下是值得进一步探索的研究方向：

### 1. 模型架构的联合建模
当前CausalRM主要关注**学习目标函数**的改进（即如何设计无偏的损失函数），而模型架构仍采用简单的MLP（多层感知机）头。未来可探索**端到端的联合架构设计**：
- 采用**混合专家模型（Mixture of Experts, MoE）**等先进架构，同时建模用户偏好（preference）和反馈倾向（propensity），实现特征层面的深度解耦与交互。
- 设计统一的多任务框架，使倾向估计、噪声率估计和奖励预测共享表示的同时保持任务特异性。

### 2. 观测性与实验性数据的协同
本工作假设仅使用**观测性反馈**（observational feedback），但现实中通常可以低成本获取少量**实验性反馈**（experimental feedback，如人工标注的高质量数据）。未来研究方向包括：
- **半监督或主动学习策略**：如何利用少量无偏的实验性数据来校准噪声率估计（$\rho_{01}, \rho_{10}$）或倾向得分模型，进一步提升观测性数据下的学习效果。
- **安全融合机制**：设计防止实验数据（小规模）被观测数据（大规模但有偏）"淹没"的训练策略，实现质量与规模的权衡。

### 3. 更复杂的噪声与偏倚机制
当前假设标注错误由**恒定**的假阳性/阴性率（$\rho_{01}, \rho_{10}$）刻画，实际场景可能更复杂：
- **实例依赖噪声（Instance-dependent Noise）**：错误概率可能与响应特征相关（如用户更容易在长文本上犯错）。
- **时间动态性**：用户偏好和反馈模式随时间演变（concept drift），需要在线或持续学习（continual learning）机制来动态更新噪声率和倾向得分。
- **选择性标签的复杂机制**：当前假设倾向得分仅依赖响应质量，实际中可能受用户画像、上下文场景等多维因素影响。

### 4. 多模态与细粒度反馈的扩展
当前聚焦二元或标量反馈（点击、点赞），未来可扩展至：
- **多属性偏好**：同时建模有用性（helpfulness）、安全性（safety）、正确性（correctness）等多个维度的观测性反馈，处理多维偏倚和噪声的耦合。
- **细粒度反馈信号**：利用编辑行为（用户如何修改AI回复）、停留时间、多轮交互历史等**连续型或结构化反馈**，设计相应的去噪与去偏策略。

### 5. 因果推断方法的深化
- **未观察混杂因素（Unmeasured Confounding）**：当前假设所有影响反馈和偏好的变量都可观测，实际中存在潜在混杂（如用户情绪状态）。
- **反事实推理的强化**：利用生成模型（如扩散模型或LLM）合成反事实样本（"如果用户看到不同回复，会如何反馈"），增强对缺失反馈（$o_i=0$）的插补能力。

### 6. 下游RLHF的稳定性
- **奖励劫持（Reward Hacking）的鲁棒性**：尽管CausalRM提升了奖励信号的准确性，但在复杂强化学习优化过程中仍可能发生奖励劫持。研究如何结合CausalRM与防劫持技术（如奖励模型集成、对抗训练）是重要方向。
- **多轮对话中的信用分配**：当前针对单轮响应，未来需扩展到多轮对话场景，处理延迟反馈（delayed feedback）和长期信用分配问题。

## 总结

本文提出 **CausalRM**（Causal-Theoretic Reward Modeling），一种从**观测性用户反馈**（observational user feedback，如点击、复制、点赞）中学习无偏奖励模型的框架，旨在解决传统RLHF依赖昂贵实验性标注数据（experimental feedback）的可扩展性瓶颈。

### 核心问题
观测性反馈虽廉价且丰富，但存在两大固有缺陷：
1. **标注错误（Annotation Errors）**：用户因注意力不集中或交互习惯，提供的反馈 $r_i$ 可能偏离真实偏好 $r^*_i$（如为修改而复制文本被记为正面反馈）；
2. **用户偏好偏倚（Preference Bias）**：用户仅对极端偏好（极满意或极不满）的响应提供反馈，中性响应被系统性缺失，导致训练分布 $O$ 与推理全分布 $D$ 存在偏移。

### 方法论
CausalRM 通过因果推断视角，提出双重纠正机制：

**1. 噪声感知的替代损失（Noise-aware Surrogate Loss）**  
显式建模标注错误过程（假阳性率 $\rho_{10}$、假阴性率 $\rho_{01}$），构造替代损失 $\tilde{\ell}$ 满足 $\mathbb{E}_{r_i|r^*_i}[\tilde{\ell}(\hat{r}_\theta(x_i), r_i)] = \ell(\hat{r}_\theta(x_i), r^*_i)$。通过锚点估计技术从数据中估计噪声率，使损失在期望上等价于无噪条件下的理想损失。

**2. 因果重加权目标（Causally Reweighted Objective）**  
引入**倾向得分**（propensity score）$p_i = P(o_i=1|x_i)$ 刻画反馈概率，提出：
- **CausalRM-IPS**：逆概率加权（IPS）消除分布偏移；
- **CausalRM-DR**：双重稳健估计（DR），结合误差插补模型，在倾向得分或插补模型之一准确时仍保持无偏，且方差更低。

### 理论贡献
证明在给定准确噪声率与倾向得分条件下，所提估计器是理想学习目标 $L_{\text{ideal}}$ 的**无偏估计**（Theorem 1-2），且DR估计器方差小于IPS（Theorem 3）。

### 实验验证
在 HelpSteer、UltraFeedback、PKU-SafeRLHF 上的实验表明：
- CausalRM 显著优于仅去偏（如IPS、SDR）或仅去噪（如ILDE）的基线，在PKU-SafeRLHF上 $R^2$ 提升至0.779；
- 在下游RLHF安全对齐任务中，使用CausalRM奖励模型训练的Qwen2.5-7B在WildGuardMix上取得**49.2%**的性能提升，有效抑制奖励劫持（reward hacking）。

### 局限与未来方向
当前局限在于聚焦目标函数改进而未探索先进架构（如MoE）的联合建模，且未利用可能获取的少量实验性数据。未来可结合观测与实验数据的协同学习，以及处理更复杂的实例依赖噪声和未观察混杂因素。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
