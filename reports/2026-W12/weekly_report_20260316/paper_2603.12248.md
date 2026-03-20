# Matching Features, Not Tokens: Energy-Based Fine-Tuning of Language Models

**arXiv**: [2603.12248](https://arxiv.org/abs/2603.12248) · [PDF](https://arxiv.org/pdf/2603.12248)  
**领域**: SFT  
**作者**: Jelassi, Kwun, Zhao, Li, Fusi, Du, Kakade, Domingo-Enrich  
**综合评分**: 8.36  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文由来自斯坦福大学、普林斯顿大学、微软研究院等知名机构的学者团队（作者包括来自Stanford的Jelassi、Kakade，来自Microsoft Research的Fusi、Du等）提出了一种基于能量模型的微调方法（EBFT），通过特征匹配而非传统的交叉熵训练来优化语言模型的序列级行为。该方法在问答编程、非结构化编程和翻译任务上均表现出色，超越了监督微调（SFT）并取得了更低的验证交叉熵。

---

## 详细分析

> **社区热度**: ⭐ 3 (来自 papers.cool)

## 问题定义

这篇论文旨在解决语言模型微调中的**分布偏移（distribution shift）**与**序列级行为优化**之间的根本张力，具体包括以下核心问题：

### 1. 教师强制（Teacher Forcing）导致的暴露偏差（Exposure Bias）
标准交叉熵（CE）训练在真实前缀（ground-truth prefixes）上优化下一token预测，但在部署时模型必须基于自身生成的、可能包含错误的前缀进行条件生成。这种差异导致：
- 生成长度增加时，条件熵（conditional entropy）单调上升（Braverman et al., 2019）
- 模型在token级匹配数据分布的同时，在序列级（sequence-level）偏离真实分布

### 2. 现有强化学习方法（RLVR）的局限性
虽然基于可验证奖励的强化学习（RLVR）通过优化序列级奖励缓解了分布偏移，但其存在以下缺陷：
- **依赖外部验证器**：需要任务特定的验证器（如单元测试）或偏好模型，无法应用于无明确正确性信号的开放式任务（如非结构化代码生成）
- **分布校准退化**：优化标量奖励会牺牲模型的分布校准（distributional calibration），表现为验证集交叉熵（perplexity）显著上升，特征匹配损失（feature-matching loss）恶化

### 3. 序列级统计量对齐的缺失
现有方法缺乏对**rollout分布**（模型实际生成分布）与**数据分布**在语义特征空间中对齐的直接监督：
- 基于token重叠或自似然（self-likelihood）的代理奖励（surrogate rewards）无法保证分布校准
- 需要一种不依赖特定任务奖励、又能提供密集语义反馈（dense semantic feedback）的序列级目标

### 解决方案概述
论文提出**基于能量的微调（Energy-Based Fine-Tuning, EBFT）**，通过**特征匹配目标（feature-matching objective）**直接优化：
$$L_{\text{FM}}(\theta) := \mathbb{E}_{c\sim p} \left[ \left\| \mathbb{E}_{\hat{y}\sim p_\theta(\cdot|c)}[\phi(c:\hat{y})] - \mathbb{E}_{y\sim p(\cdot|c)}[\phi(c:y)] \right\|^2 \right]$$

该方法在问答编程、非结构化编程和翻译任务中实现了：
- 比SFT更低的验证交叉熵（尽管SFT直接优化该目标）
- 比RLVR更好的特征匹配损失（全程序列长度）
- 无需任务特定验证器即可匹配或超越RLVR的下游准确率

## 相关工作

根据论文第5节及相关引用，相关研究可分为以下几个方向：

### 1. 序列级训练与暴露偏差缓解
- **暴露偏差的早期研究**：Bengio et al. (2015) 提出Scheduled Sampling，通过逐渐从模型分布中采样来缓解教师强制（teacher forcing）导致的分布偏移；Lamb et al. (2016) 的Professor Forcing通过判别器强制生成序列与真实序列的动态相似。
- **分布偏移的量化**：Braverman et al. (2019) 测量了生成token的条件熵随序列位置的增长，揭示了低困惑度（perplexity）不保证序列级校准的问题。

### 2. 基于强化学习的微调（RL Fine-tuning）
- **RLHF与DPO**：Christiano et al. (2017) 和 Ouyang et al. (2022) 通过KL正则化优化序列级人类偏好奖励；Rafailov et al. (2023) 的DPO可被解释为在类似正则化下的隐式奖励最大化。
- **早期序列级训练**：Ranzato et al. (2016) 和 Edunov et al. (2018) 结合交叉熵与REINFORCE梯度，但仍依赖token级监督信号。

### 3. 预训练与早阶段强化学习
- **RLP/RPT/RLPT**：Hatamizadeh et al. (2025)、Dong et al. (2025) 和 Li et al. (2025) 将RL引入预训练阶段，使用推理轨迹、信息增益或下一段预测作为奖励。但这些奖励仍源自next-token似然或预训练流上的正确性，而非独立的语义目标。
- **FlowRL**：Zhu et al. (2025) 通过匹配完整奖励分布鼓励多样性，但仍基于似然或任务特定信号定义奖励。

### 4. 对抗与判别器方法
- **GAPT**：Wu et al. (2025) 使用共同演化的判别器（co-evolving discriminator）缓解交互生成中的奖励黑客攻击（reward hacking）。
- **RARO**：Cai & Provilkov (2025) 在逆强化学习框架中使用相对判别器，从无验证器的专家演示中恢复隐式奖励。

这些方法均通过对抗训练学习**非平稳的标量奖励**，而EBFT使用固定的向量值特征匹配目标，无需学习奖励模型。

### 5. 替代生成框架与基于嵌入的方法
- **能量基扩散语言模型（EDLM）**：Xu et al. (2024b) 在序列级操作，但专注于直接建模数据分布，而非定义特征空间对齐目标。
- **BERTScore等嵌入相似性**：Zhang et al. (2019) 等广泛用于评估，偶尔通过RL优化用于指标驱动的微调（如Rennie et al., 2017），但未作为教师强制token预测的通用替代。

### 6. 部分Rollout与代理奖励方法
- **Quiet-STaR** (Zelikman et al., 2024)：在训练时引入部分rollout，但使用启发式代理奖励（模型自身对数概率或token重叠相似性），无法保证分布校准。

### 与本文方法的关键区别
现有方法要么：
1. **依赖标量奖励**（RLHF、GAPT、FlowRL），导致分布锐化（distribution sharpening）和交叉熵退化；
2. **奖励源自token似然**（RLP、RPT、Quiet-STaR），无法提供独立的语义反馈；
3. **需要任务特定验证器**（RLVR），无法应用于非结构化代码等开放域任务。

EBFT通过**特征匹配**直接对齐rollout分布与数据分布的高阶统计量，无需学习奖励模型或验证器，在优化序列级行为的同时保持语言模型的分布校准。

## 解决方案

论文通过提出**能量基微调（Energy-Based Fine-Tuning, EBFT）**框架解决上述问题，核心机制是将序列级分布对齐转化为可优化的特征匹配目标。具体解决方案包含以下组件：

### 1. 特征匹配目标函数
定义**特征匹配损失（Feature-Matching Loss）**直接衡量模型rollout分布与数据分布在高维特征空间中的一阶矩差异：

$$L_{\text{FM}}(\theta) := \mathbb{E}_{c\sim p} \left[ \left\| \mathbb{E}_{\hat{y}\sim p_\theta(\cdot|c)}[\phi(c:\hat{y})] - \mathbb{E}_{y\sim p(\cdot|c)}[\phi(c:y)] \right\|^2 \right]$$

其中$\phi: \mathcal{V}^* \to \mathbb{R}^d$为冻结的特征网络（frozen feature network）。由于真实数据矩未知，实际优化其无偏估计——**条件特征匹配损失**：

$$L_{\text{CFM}}(\theta) := \mathbb{E}_{c\sim p} \left[ \left\| \mathbb{E}_{\hat{y}\sim p_\theta(\cdot|c)}[\phi_c(\hat{y})] - \phi_c(y) \right\|^2 \right]$$

当特征映射$\phi$足够丰富时，该损失为零等价于模型分布与数据分布一致（严格真评分规则）。

### 2. REINFORCE梯度估计与奖励设计
通过策略梯度方法优化$L_{\text{CFM}}$，推导出每样本的REINFORCE梯度：

$$\nabla_\theta L_{\text{CFM}}(\theta; c, y) = -\mathbb{E}_{\hat{y}\sim p_\theta(\cdot|c)} \left[ \nabla_\theta \log p_\theta(\hat{y}|c) \cdot r(\hat{y}, c) \right]$$

其中奖励函数$r(\hat{y}, c)$分解为**对齐项**与**多样性项**：

$$r(\hat{y}, c) = \underbrace{2\phi_c(\hat{y})^\top\phi_c(y)}_{\text{alignment}} - \underbrace{2\phi_c(\hat{y})^\top\mathbb{E}_{\tilde{y}\sim p_\theta(\cdot|c)}[\phi_c(\tilde{y})]}_{\text{diversity}}$$

实践中通过采样$n$个rollout $\{\hat{y}_j\}_{j=1}^n$计算无偏估计：
$$r_j = 2\phi_c(\hat{y}_j)^\top\phi_c(y) - \frac{2}{n-1}\sum_{j'\neq j} \phi_c(\hat{y}_j)^\top\phi_c(\hat{y}_{j'})$$

并采用**RLOO（REINFORCE Leave-One-Out）基线**降低方差。

### 3. 特征网络架构
- **初始化**：从预训练模型复制，训练期间保持冻结
- **特征提取**：取模型25%、50%、75%深度的中间层激活，经L2归一化后拼接
- **设计原理**：浅层捕获低层信息，深层偏向next-token预测，中层承载语义与结构信息

### 4. 白化（Whitening）与几何校正
为解决特征方向相关性导致的优化病态，引入**白化特征匹配**：

$$\tilde{\phi}_c(z) = (\hat{\Sigma}_c^\dagger)^{1/2}\phi_c(z), \quad \hat{\Sigma}_c = \frac{1}{n}\sum_{j=1}^n \phi_c(\hat{y}_j)\phi_c(\hat{y}_j)^\top$$

其中$\dagger$表示Moore-Penrose伪逆。白化后的奖励函数为：
$$r_j = 2\frac{\tilde{\phi}_c(\hat{y}_j)^\top\tilde{\phi}_c(y)}{\|\tilde{\phi}_c(\hat{y}_j)\|\|\tilde{\phi}_c(y)\|} - \frac{2}{n-1}\sum_{j'\neq j} \tilde{\phi}_c(\hat{y}_j)^\top\tilde{\phi}_c(\hat{y}_{j'})$$

该变体近似优化局部$\chi^2$散度，在$p_\theta \approx p$时与KL散度等价。

### 5. 高效并行采样（Strided Block-Parallel Rollouts）
为解决on-policy采样的计算瓶颈，实现**步长分块并行解码**：
- 从长序列中以步长$s$提取$B = \lfloor(T-G)/s\rfloor$个嵌套前缀
- 使用自定义注意力掩码（custom attention mask）在单次前向传播中并行生成所有前缀的续写
- 摊销前缀计算成本，支持批量特征网络推理

### 6. 与能量基模型的理论联系
在KL正则化视角下，特征匹配目标隐式定义能量函数：

$$\min_\rho \mathbb{E}_{c\sim p} \left[ \left\| \mathbb{E}_{\rho(\cdot|c)}[\phi_c(y)] - \mathbb{E}_{p(\cdot|c)}[\phi_c(y)] \right\|^2 + \frac{1}{\beta}D_{\text{KL}}(\rho(\cdot|c)\|q(\cdot|c)) \right]$$

其最优策略为基分布的**指数倾斜（exponential tilt）**：
$$\rho^*(y|c) \propto q(y|c)\exp\left(-\chi_c^\top\phi_c(y)\right)$$

其中$\chi_c$为上下文依赖的倾斜向量。EBFT通过直接优化生成器参数实现该能量基模型的最大似然训练，无需显式学习能量函数参数。

### 7. 可选的交叉熵正则化
实践中采用混合目标：
$$\mathcal{L}(\theta) = L_{\text{FM}}(\theta) + \gamma L_{\text{CE}}(\theta)$$

系数$\gamma$控制分布校准强度：$\gamma=0$时纯特征匹配已能降低交叉熵（通过白化近似$\chi^2$优化），增大$\gamma$可加速收敛而不损害下游性能。

## 实验验证

论文在**编程**（问答式与非结构化）和**翻译**三类任务上进行了系统性实验，涵盖从1.5B到7B参数的模型。以下是实验设计的详细分解：

## 1. 实验任务与数据集

### 编程任务
- **Q&A编程（可验证设置）**：使用OpenCodeInstruct的100k子集，包含自然语言编程提示与参考答案
  - 评估基准：HumanEval、MBPP（分布内），MultiPL-E（跨语言迁移，含C++/JavaScript/Rust等8种语言）
- **非结构化编程（不可验证设置）**：使用SwallowCode的40k子集，仅包含原始Python代码无显式指令
  - 评估基准：HumanEval、MBPP（均为分布外，因训练数据无提示/测试用例）

### 翻译任务
- 使用ALMA-HumanParallel的100k子集（人工精选平行句对）
- 评估基准：
  - **WMT'22**：主评测基准（新闻/通用领域）
  - **MTNT**：噪声Reddit评论（拼写错误/俚语/代码切换，分布外鲁棒性测试）
  - **OpenSubtitles**：电影/TV对话（非正式风格，分布外）

## 2. 对比方法（Baselines）

| 方法 | 说明 | 适用场景 |
|------|------|----------|
| **SFT** | 标准交叉熵训练（监督微调） | 所有任务 |
| **RLVR** | 基于可验证奖励的强化学习（代码通过率/BLEU） | 仅Q&A编程与翻译（需验证器） |
| **EBFT** | 本文方法，含白化与REINFORCE梯度 | 所有任务（含不可验证场景） |

所有方法均从预训练模型初始化（Qwen2.5-1.5B用于编程，Llama-3.2-1B用于翻译），训练2个epoch。

## 3. 核心评估指标

- **下游任务性能**：
  - 编程：greedy准确率（temp=0）、pass@1/4/16（temp=0.6）
  - 翻译：COMET分数（主指标）、BLEU（附录）；greedy与best-of-1/4/16
- **分布校准指标**：
  - **验证集交叉熵（CE）**：在1k样本held-out数据上计算
  - **条件特征匹配损失（CFM）**：$\mathbb{E}_c[\|\mathbb{E}_{\hat{y}}[\phi_c(\hat{y})] - \phi_c(y)\|^2]$
  - **特征匹配损失（FM）**：衡量模型与数据分布的矩差异

## 4. 主要实验结果

### 核心发现（表1与图3、5）
- **EBFT vs SFT**：在HumanEval上greedy准确率从0.483→0.548（Q&A），pass@16从0.728→0.771；翻译COMET greedy从0.717→0.725
- **EBFT vs RLVR**：下游性能相当或更优（如MTNT翻译0.737 vs 0.705），但RLVR导致CE显著恶化（如翻译任务1.870→2.454，而EBFT降至1.670）
- **不可验证场景**：非结构化代码上RLVR不可用，EBFT仍显著优于SFT（pass@1: 0.524 vs 0.467）

### 训练动态（图3、5）
- **CE矛盾现象**：EBFT的验证CE低于SFT（尽管SFT直接优化CE），而RLVR的CE随训练持续上升
- **特征匹配**：EBFT在所有完成长度（1-64 tokens）上实现最低CFM损失；RLVR的CFM损失甚至高于基线模型

## 5. 消融实验（Ablations）

### 超参数敏感性（图6、11-14）
- **CE正则系数γ**（$\mathcal{L} = L_{\text{FM}} + \gamma L_{\text{CE}}$）：
  - $\gamma=0$（纯特征匹配）已能降低CE并优于SFT
  - 增大$\gamma$加速CE下降，但不影响下游准确率或CFM损失
- **对齐偏置α**（缩放多样性项）：
  - $\alpha=1$（标准设置）最稳定
  - $\alpha<1$（模式寻求）在$\gamma=0$时导致CE发散，需$\gamma>0$稳定训练

### 特征网络设计（图7）
- **白化（Whitening）**：移除白化导致CFM损失与下游性能显著下降
- **池化策略**：last-token特征优于mean-pooling
- **网络规模**：使用7B参数特征网络（冻结）替代1.5B，性能无显著提升，说明无需额外缩放特征网络
- **随机初始化**：随机权重特征网络性能略有下降，但预训练表示更有利

### 模型规模扩展（图8）
在Qwen2.5-1.5B/3B/7B上测试EBFT（$\gamma=0$）：
- 所有规模均显示一致的改进模式：HumanEval greedy准确率分别从0.35→0.49（1.5B）、0.37→0.60（3B）、0.55→0.69（7B）
- 验证CE与CFM损失随规模增大单调下降，无性能饱和迹象

### 预热初始化（Warm-start）
对比从基模型直接训练 vs 先SFT 1 epoch再EBFT/RLVR：
- **EBFT**：预热与否性能相近，对初始化质量鲁棒
- **RLVR**：严重依赖预热（无预热时CE与下游性能显著恶化）

## 6. 定性分析（第4.3节与附录H.2-H.3）

### 代码生成（HumanEval）
- **SFT**：常遗漏细微提示要求（如统计重叠子串时步进长度错误）
- **RLVR**：生成逻辑合理但不可执行（调用未定义函数如`is_prime`，或混入自然语言解释）
- **EBFT**：生成自包含、可执行代码，严格遵循提示语义（如正确处理子串重叠）

### 翻译（MTNT）
- **基模型/RLVR**：常出现"非翻译"现象（重复源语言）或多语言标签漂移（生成"Spanish: ... Português: ..."列表）
- **RLVR额外问题**：生成长度截断（mid-word truncation）
- **EBFT**：保持单语言输出，语义忠实（如保留否定词"no more"）

## 7. 实现细节
- **框架**：基于OpenRLHF构建
- **计算资源**：80GB H100 GPU
- **效率**：单epoch SFT约0.5小时（1×H100）；EBFT约36小时（2×H100，未使用vLLM优化）；RLVR约28小时（2×H100，使用vLLM）
- **关键超参**：rollout长度$G=8$（代码）或$4$（翻译），每提示采样$n=4$，温度$0.6$

## 未来工作

基于论文结论与实验局限性，以下方向值得进一步探索：

### 1. 计算效率与工程优化
EBFT基于on-policy rollout，单epoch训练时间（36小时，2×H100）显著高于SFT（0.5小时）。**优化方向包括**：
- 集成vLLM等高效推理引擎加速rollout生成
- 开发更高效的块并行解码（block-parallel decoding）变体，减少前缀重复计算
- 探索off-policy或重要性采样（importance sampling）变体以降低采样开销

### 2. 模型规模与rollout horizon扩展
当前实验限于7B参数模型与短horizon（$G \leq 8$ tokens）。**开放问题包括**：
- 在70B+参数模型上验证EBFT的有效性（是否保持单调收益）
- 扩展至更长rollout horizon（如$G=64$或完整序列长度），验证长程依赖下的校准保持能力
- 研究horizon长度与特征网络深度之间的最优配比

### 3. 自适应与可学习特征网络
当前方法依赖**冻结的预训练特征网络**：
- **可学习特征网络**：允许特征提取器与生成器协同适应，可能提升对齐精度（需防范特征崩溃）
- **动态特征选择**：根据任务或上下文自适应选择特征层（如使用注意力机制加权不同深度层）
- **跨架构特征网络**：探索使用不同架构（如编码器-only模型）作为特征提取器，与生成器（解码器-only）解耦

### 4. 特征丰富性的理论刻画
论文假设"足够丰富的特征映射"可使特征匹配等价于分布匹配，但**缺乏对"丰富性"的定量刻画**：
- 建立特征维度$d$、模型容量与分布匹配精度之间的理论界限
- 研究特征白化（whitening）对优化景观（optimization landscape）的精确影响
- 分析特征匹配与最大似然估计在多模态分布（multi-modal distributions）下的等价条件

### 5. 与RLVR的混合训练范式
EBFT与RLVR在验证集交叉熵上呈现**相反趋势**（EBFT降低，RLVR升高）：
- 设计阶段性训练策略：先用EBFT保持分布校准，再用RLVR针对特定任务优化
- 开发联合目标函数，将特征匹配损失作为RLVR的正则化项，缓解奖励黑客（reward hacking）导致的分布偏移
- 在RLVR训练中加入EBFT的多样性奖励项，改善pass@k指标中的样本多样性

### 6. 非文本模态与多模态扩展
- **代码-执行信号融合**：在EBFT框架中融入编译器反馈或执行轨迹特征，而非仅文本特征
- **视觉-语言任务**：将图像编码器特征纳入匹配目标，用于图像描述或视觉问答的微调
- **语音-文本对齐**：探索语音单元（speech units）与文本特征的对齐训练

### 7. 特征匹配在预训练阶段的应用
当前EBFT仅用于微调阶段：
- 探索在中期训练（mid-training）或持续预训练（continual pre-training）中应用特征匹配，以改善基础模型的序列级校准
- 研究特征匹配与next-token预测在预训练中的最优混合比例

### 8. 评估协议与诊断工具
- 开发更细粒度的特征匹配诊断工具，定位模型在哪些语义维度（如语法结构 vs. 事实内容）存在校准不足
- 建立特征匹配损失与下游任务性能之间的预测性关系，用于早期训练停止或模型选择

## 总结

这篇论文提出**能量基微调（Energy-Based Fine-Tuning, EBFT）**，一种针对大语言模型的序列级微调方法，旨在解决标准交叉熵（CE）训练中的分布偏移问题，同时克服强化学习微调（RLVR）对验证器的依赖及分布校准退化问题。

### 核心问题
- **暴露偏差（Exposure Bias）**：CE训练在真实前缀（teacher forcing）上优化下一token预测，而部署时模型需基于自身生成的、可能错误的前缀继续生成，导致序列级行为偏离数据分布。
- **RLVR的局限性**：基于可验证奖励的强化学习虽能优化序列级行为，但需任务特定验证器（如单元测试），无法应用于开放式任务；且优化标量奖励会显著恶化验证集困惑度（perplexity），损害语言模型的分布校准。

### 方法：EBFT
EBFT通过**特征匹配（Feature Matching）**直接对齐模型rollout分布与数据分布在高维特征空间中的统计量：

**1. 特征匹配目标**  
定义损失函数：
$$L_{\text{FM}}(\theta) := \mathbb{E}_{c\sim p} \left[ \left\| \mathbb{E}_{\hat{y}\sim p_\theta(\cdot|c)}[\phi(c:\hat{y})] - \mathbb{E}_{y\sim p(\cdot|c)}[\phi(c:y)] \right\|^2 \right]$$
其中 $\phi$ 为冻结的特征网络（从预训练模型复制，取25%、50%、75%深度层激活）。实际优化其无偏估计——条件特征匹配损失 $L_{\text{CFM}}$。

**2. REINFORCE梯度估计**  
通过策略梯度优化，导出奖励函数：
$$r(\hat{y}, c) = \underbrace{2\phi_c(\hat{y})^\top\phi_c(y)}_{\text{对齐项}} - \underbrace{2\phi_c(\hat{y})^\top\mathbb{E}_{\tilde{y}\sim p_\theta(\cdot|c)}[\phi_c(\tilde{y})]}_{\text{多样性项}}$$
使用RLOO（REINFORCE Leave-One-Out）基线降低方差。

**3. 白化（Whitening）**  
通过伪逆变换 $\tilde{\phi}_c(z) = (\hat{\Sigma}_c^\dagger)^{1/2}\phi_c(z)$ 校正特征几何，近似优化$\chi^2$散度，改善优化条件。

**4. 高效实现**  
采用**步长分块并行解码（strided block-parallel rollouts）**，单次前向传播生成多个嵌套前缀的续写，摊销计算成本。

### 实验结果
在Q&A编程（OpenCodeInstruct）、非结构化编程（SwallowCode）和翻译（ALMA）任务上的实验表明：

- **下游性能**：EBFT匹配或超越RLVR（如HumanEval greedy准确率0.548 vs 0.535），显著优于SFT（0.483）；在非结构化代码（无验证器场景）上仍大幅领先SFT。
- **分布校准**：EBFT实现最低的验证集交叉熵（如翻译任务1.670 vs SFT的1.782和RLVR的2.454）和特征匹配损失；RLVR则显著恶化这两项指标。
- **跨长度泛化**：尽管仅在8-token horizon上训练，EBFT在1-64 token长度上均保持最低特征匹配损失。

### 理论视角
在KL正则化框架下，特征匹配目标隐式定义能量函数，其最优策略为基分布的**指数倾斜（exponential tilt）**：
$$\rho^*(y|c) \propto q(y|c)\exp\left(-\chi_c^\top\phi_c(y)\right)$$
EBFT通过直接优化生成器参数实现该能量基模型的最大似然训练，无需显式学习能量参数。

### 贡献总结
EBFT提供了一种**无需任务特定验证器**的序列级微调方法，通过密集语义反馈（特征匹配）同时实现：
- 优于SFT的下游任务性能
- 优于RLVR的分布校准（低困惑度）
- 适用于非可验证的开放式任务（如非结构化代码生成）



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
