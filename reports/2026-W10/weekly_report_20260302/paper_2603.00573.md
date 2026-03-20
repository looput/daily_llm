# CoMoL: Efficient Mixture of LoRA Experts via Dynamic Core Space Merging

**arXiv**: [2603.00573](https://arxiv.org/abs/2603.00573) · [PDF](https://arxiv.org/pdf/2603.00573)  
**领域**: SFT  
**作者**: Cao, Fan, Wang, Lin, Zhao, Yan, Zhang, Shao 等 11 人  
**综合评分**: 8.36  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为CoMoL（Core Space Mixture of LoRA）的新型MoE-LoRA框架，旨在解决现有参数高效微调方法中专家数量膨胀、参数效率低和适应粒度粗的问题。通过引入核心空间专家和核心空间路由两个关键组件，并结合软合并策略，该方法在保持模型适应性的同时显著提升了参数效率。实验表明，CoMoL在多个任务上均优于现有方法。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

这篇论文主要试图解决现有基于混合专家（MoE）的低秩适应（LoRA）方法（即MoE-LoRA）在参数高效微调（PEFT）中面临的两个核心局限性：

**1. 参数效率受限（Limited Parameter Efficiency）**
- 现有MoE-LoRA架构通过引入路由网络和多个LoRA专家来增强模型容量，但这导致可训练参数数量随专家数量线性增长（通常为标准LoRA的$N$倍）
- 大多数专家参与后续计算，造成显著的参数冗余和计算开销，违背了PEFT减少训练成本的核心原则

**2. 粗粒度适应（Coarse-grained Adaptation）**
- 现有软合并（soft-merging）方法（如SMEAR）在实例级别（instance-level）而非令牌级别（token-level）进行路由决策，即对整个输入样本使用相同的合并专家
- 这种基于实例特征的路由机制无法捕捉输入序列中不同令牌所需的细粒度、动态化的特征表示，限制了模型的表达能力

为解决上述问题，论文提出了**CoMoL（Core Space Mixture of LoRA）**框架，通过以下关键设计实现高效且细粒度的模型适应：
- **核心空间专家**：将专家参数存储在紧凑的$r \times r$核心矩阵中，而非完整的LoRA矩阵对，将参数复杂度从$O(N \cdot (m+n)r)$降低至$O((m+n)r + N \cdot r^2)$
- **核心空间路由**：在相同的低秩核心空间内执行令牌级别的动态路由和软合并，实现细粒度的输入自适应适应
- **低秩投影路由**：将路由网络投影到与LoRA矩阵相同的低秩空间，进一步减少参数开销，使总参数量与标准LoRA相当

## 相关工作

根据论文第5节（Related work）及相关章节的梳理，该研究领域的相关工作主要可分为以下几类：

### 1. 基础方法
- **Standard LoRA** (Hu et al., 2021)：通过低秩矩阵分解 $W + \Delta W = W + BA$ 实现参数高效微调，其中 $B \in \mathbb{R}^{m \times r}, A \in \mathbb{R}^{r \times n}$，为后续MoE-LoRA方法奠定基础。

### 2. 稀疏MoE-LoRA（Sparse MoE-LoRA）
这类方法采用top-k稀疏路由机制，仅激活部分专家：
- **SiRA** (Zhu et al., 2023)：采用带容量限制的top-k路由和门控dropout来稳定路由并缓解过拟合
- **MoELoRA** (Luo et al., 2024)：通过对比学习促进专家专业化，缓解随机路由问题
- **MoLA** (Gao et al., 2024)：分析层间冗余，在不同层分配不同数量的LoRA专家，发现底层冗余度更高
- **AdaMoLE** (Liu and Luo, 2024)：引入基于阈值网络的令牌级自适应专家激活机制
- **SparseMoA** (Cao et al., 2025)：异构混合适配器方法

### 3. 软加权MoE-LoRA（Soft-weighted MoE-LoRA）
这类方法用连续混合替代硬top-k选择，聚合所有专家的输出：
- **MoLoRA** (Zadouri et al., 2023)：采用令牌级软路由，为每个令牌组合专家输出
- **LoRAMoE** (Dou et al., 2024)：使用线性路由网络集成LoRA专家，缓解世界知识遗忘
- **MOLE** (Wu et al., 2024)：仅训练每层路由器，实现多个训练后LoRA的高效组合
- **HydraLoRA** (Tian et al., 2024)：通过非对称设计（共享矩阵）提高参数效率

### 4. 软合并MoE方法（Soft-merging MoE methods）
这类方法在计算前通过路由先合并专家参数，而非聚合专家输出，从而降低计算开销：
- **AdaMix** (Wang et al., 2022)：采用随机路由的适配器混合方法，在实例级别操作
- **SMEAR** (Muqeeth et al., 2024)：通过学习的软路由合并专家参数，在实例级别（instance-level）操作，牺牲令牌级动态性换取计算效率
- **MoV** (Zadouri et al., 2023)：令牌级专家参数合并方法，但仅限于IA3专家（使用可训练向量缩放隐藏状态），无法适用于LoRA专家

### 5. 其他相关方法
- **DenseLoRA** (Mu et al., 2025)：密集低秩适应方法
- **FlyLoRA** (Zou et al.)：通过隐式秩级混合专家提升任务解耦和参数效率，但在不同模型家族上表现出容量局限性

### 关键区别
与上述方法相比，CoMoL的核心创新在于：**首次实现了在令牌级别（token-level）对LoRA专家进行核心空间（core space）内的软合并**，既保留了细粒度的输入自适应能力，又将参数和计算复杂度控制在接近标准LoRA的水平。

## 解决方案

论文通过提出**Core Space Mixture of LoRA (CoMoL)**框架解决上述问题，核心技术包括**核心空间专家**与**核心空间路由**两个关键组件。具体解决方案如下：

### 1. 核心空间专家：解决参数冗余问题

传统MoE-LoRA为每个专家存储完整的低秩矩阵对 $\{B_i \in \mathbb{R}^{m \times r}, A_i \in \mathbb{R}^{r \times n}\}$，导致参数随专家数量 $N$ 线性增长（复杂度 $O(N \cdot (m+n)r)$）。

CoMoL通过**奇异值分解（SVD）重参数化**，将专家特定知识压缩到紧凑的**核心矩阵**中：

$$
B = U_B \Sigma_B V_B^\top, \quad A = U_A \Sigma_A V_A^\top
$$

$$
\Delta W = U_B \underbrace{(\Sigma_B V_B^\top U_A \Sigma_A)}_{M \in \mathbb{R}^{r \times r}} V_A^\top = U_B M V_A^\top
$$

其中：
- $U_B \in \mathbb{R}^{m \times r}$ 和 $V_A^\top \in \mathbb{R}^{r \times n}$ 为跨专家共享的奇异子空间（捕捉特征方向）
- $M \in \mathbb{R}^{r \times r}$ 为**核心矩阵**（捕捉各专家特定的耦合强度与权重分配）

**参数效率提升**：每个专家仅需存储核心矩阵 $M_i$，参数复杂度降至 $O((m+n)r + N \cdot r^2)$。由于 $r \ll m,n$，当 $N$ 增加时，参数总量接近常数，与标准LoRA相当。

### 2. 核心空间内软合并：实现细粒度令牌级适应

为解决SMEAR等方法的**实例级（instance-level）粗粒度路由**问题，CoMoL在核心空间内执行**令牌级（token-level）软合并**：

对于输入令牌 $x$，传统MoE-LoRA在输出层聚合专家结果：
$$
h = W x + \sum_{i=1}^N G(x)_i \cdot U_B M_i V_A^\top x
$$

CoMoL利用矩阵乘法分配律，在核心空间内先合并专家参数：
$$
h = W x + U_B \underbrace{\left(\sum_{i=1}^N G(x)_i M_i\right)}_{M_{\text{merged}} \in \mathbb{R}^{r \times r}} V_A^\top x
$$

**关键优势**：
- **细粒度**：$G(x)_i$ 为每个令牌独立计算的路由权重，实现令牌级动态适应
- **计算高效**：高维投影 $U_B$ 和 $V_A^\top$ 每个令牌仅计算一次，核心矩阵合并计算复杂度仅为 $O(r^2)$，远低于输出层聚合的 $O(n)$

### 3. 核心空间路由：降低路由网络参数开销

传统MoE-LoRA的路由网络 $W_g \in \mathbb{R}^{N \times n}$ 参数随模型维度 $n$ 线性增长，成为新的参数瓶颈。

CoMoL提出**低秩投影路由**：
- 复用LoRA的低秩投影结果 $\hat{x} = V_A^\top x \in \mathbb{R}^r$ 作为路由输入
- 路由网络参数降至 $W_g \in \mathbb{R}^{N \times r}$，复杂度从 $O(N \cdot n)$ 降至 $O(N \cdot r)$

路由过程定义为：
$$
\hat{x} = V_A^\top x \\
h = W x + U_B \left(\sum_{i=1}^N G(\hat{x})_i M_i\right) \hat{x}
$$

### 4. 整体架构流程

综合上述组件，CoMoL的完整前向传播流程为：

1. **投影**：计算低秩表示 $\hat{x} = V_A^\top x$
2. **路由**：在核心空间计算路由权重 $G(\hat{x}) = \text{softmax}(W_g \hat{x})$
3. **合并**：软合并核心矩阵 $M_{\text{merged}} = \sum_{i=1}^N G(\hat{x})_i M_i$
4. **计算**：通过共享投影矩阵计算输出 $h = W x + U_B M_{\text{merged}} \hat{x}$

通过这一设计，CoMoL在保持**与标准LoRA相当参数量**（约 $1.0\times$）的同时，实现了**令牌级细粒度路由**和**专家知识的高效聚合**，突破了现有MoE-LoRA方法的参数效率与适应粒度瓶颈。

## 实验验证

论文在第4节（Experiments）中进行了系统性的实验验证，涵盖数学推理、代码生成以及不同配置下的扩展性分析。具体实验设置如下：

### 1. 数学推理任务（Mathematical Reasoning）

**实验设置**
- **骨干模型**：Qwen3-8B 与 Qwen3-14B（验证不同参数规模下的性能稳定性）
- **微调数据**：Math14k（GSM8K与AQuA的训练子集，包含思维链推理路径）
- **评估基准**：GSM8K、SVAMP、MultiArith、AddSub、AQuA、SingleEq共6个数学推理数据集
- **评估指标**：准确率（Accuracy）
- **对比基线**：Standard LoRA、MoLoRA（软加权）、HydraLoRA（软加权）、MoLA（稀疏）、AdaMoLE（稀疏）、SparseMoA（稀疏）、FlyLoRA，以及消融版本**CoMoL w/o CR**（移除核心空间路由）

**主要结果**
- 在Qwen3-8B上，CoMoL平均准确率达84.48%，超越标准LoRA 1.7个百分点，且在AddSub和AQuA上取得SOTA性能（表2）
- 在Qwen3-14B上，CoMoL平均准确率86.34%，同样超越所有基线（表3）
- 参数量控制在25.16M（Qwen3-8B）和35.82M（Qwen3-14B），与标准LoRA（24.77M / 35.39M）相当，远低于MoLoRA等方法的4倍以上参数量

### 2. 代码生成任务（Code Generation）

**实验设置**
- **骨干模型**：Qwen3-8B与Llama3.1-8B（验证跨架构鲁棒性）
- **微调数据**：CodeAlpaca-20k
- **评估基准**：HumanEval
- **评估指标**：pass@k（$k \in \{1, 5, 10\}$）
- **对比基线**：额外加入DenseLoRA进行对比

**主要结果**
- 在Llama3.1-8B上，CoMoL的pass@1达35.00%，超越所有对比方法（表4）
- 在Qwen3-8B上，CoMoL的pass@1达48.11%，显著优于FlyLoRA（20.18%）和HydraLoRA（46.89%）
- 验证了FlyLoRA因稀疏性导致的学习容量不足问题，而CoMoL在保持参数效率的同时维持稳定的学习能力

### 3. 秩的扩展性分析（Scaling with Rank）

**实验设计**
在代码生成任务上，系统评估不同LoRA秩（$r \in \{8, 16, 32, 64\}$）对性能与参数量的影响。

**关键发现**（图2）
- CoMoL在所有秩设置下均稳定优于标准LoRA
- 在$r=16$时取得最佳性能（pass@1约46-48%）
- 在固定专家数量（$N=8$）的情况下，CoMoL的参数量与LoRA保持可比性（曲线几乎重合），而CoMoL w/o CR因包含完整路由网络参数量显著更高

### 4. 专家数量的扩展性分析（Scaling with Expert Number）

**实验设计**
在数学推理任务上，评估专家数量$N \in \{8, 16, 32, 64\}$对性能与参数效率的影响，对比CoMoL与CoMoL w/o CR。

**关键发现**（表5）
- 两种配置在$N=8$时均达到最佳性能（Qwen3-8B: 84.48%; Qwen3-14B: 86.34%）
- **参数效率**：CoMoL通过核心空间路由，在专家数量从8增至64时，参数量仅从25.16M增至27.91M（Qwen3-8B），增幅极小；而CoMoL w/o CR从33.39M激增至93.78M
- **可扩展性**：CoMoL可高效扩展至64个专家，而HydraLoRA等方法在16个专家时即出现OOM（显存溢出）

### 5. 消融实验（Ablation Study）

通过**CoMoL w/o CR**（移除核心空间路由，使用标准线性路由$W_g \in \mathbb{R}^{N \times n}$）验证核心空间路由的有效性：
- 在Qwen3-8B数学推理任务中，CoMoL w/o CR需33.39M参数，而完整CoMoL仅需25.16M，证明核心空间路由可显著降低路由网络参数开销
- 在代码生成任务中，CoMoL w/o CR在Qwen3-8B上pass@1为43.23%，低于完整CoMoL的48.11%，表明低秩投影路由不仅节省参数，还有助于性能优化

## 未来工作

基于论文第9节（Limitations）及实验观察，以下方向值得进一步探索：

### 1. PEFT方法学习能力的系统性评估框架
当前研究主要关注参数效率，但缺乏对不同微调场景下**学习能力边界**的系统性理解。具体包括：
- 建立标准化基准测试，量化不同PEFT方法在任务复杂度、数据分布偏移、模型架构差异等维度上的容量边界
- 分析PEFT方法在"长思考模型"（如Qwen3）与标准指令模型间的适应性差异，解释FlyLoRA在Llama3.1-8B与Qwen3-8B上性能悬殊的根本原因（论文观察到FlyLoRA在Qwen3-8B上pass@1仅为20.18%，而在Llama3.1-8B上达32.32%）

### 2. 核心空间结构的优化与扩展
- **动态专家数量**：当前CoMoL使用固定数量的专家（$N$），未来可探索基于任务复杂度或层深度的自适应专家数量调整机制
- **核心矩阵初始化策略**：研究不同初始化方法（如正交初始化、基于预训练知识的初始化）对专家多样性和收敛速度的影响
- **异构核心空间**：探索不同层使用不同秩（$r$）的核心矩阵，进一步平衡参数效率与表达能力

### 3. 跨架构泛化机制
- 研究CoMoL在低秩投影空间（$V_A^\top x$）进行路由的普适性，验证该机制是否可迁移至其他PEFT范式（如IA3、Adapter、Prompt Tuning等）
- 探索核心空间合并在视觉-语言模型（VLM）或多模态大模型中的应用潜力

### 4. 路由机制的精细化改进
- **分层路由策略**：结合CoMoL的令牌级路由与SMEAR的实例级路由，设计混合粒度路由机制，在计算效率与细粒度适应间取得更好平衡
- **动态路由稀疏性**：在保持核心空间合并优势的同时，引入稀疏性约束（如Top-$k$核心专家选择），进一步降低计算开销

### 5. 理论分析
- 对核心矩阵 $M \in \mathbb{R}^{r \times r}$ 的谱特性进行理论分析，理解专家合并后的有效秩（effective rank）与模型表达能力的关系
- 分析共享子空间（$U_B, V_A$）与核心矩阵（$M_i$）在知识存储中的分工机制，指导更高效的参数分配策略

## 总结

该论文针对参数高效微调（PEFT）中混合专家低秩适应（MoE-LoRA）架构的局限性，提出了**Core Space Mixture of LoRA (CoMoL)**框架。以下是主要内容总结：

### 1. 研究背景与动机
- **现有问题**：标准LoRA通过低秩分解 $W + \Delta W = W + BA$ 实现高效微调，但单纯增加秩 $r$ 收益递减。MoE-LoRA通过引入多专家（$N$个）和路由机制提升容量，但存在两大缺陷：
  - **参数效率受限**：专家参数随 $N$ 线性增长（$O(N \cdot (m+n)r)$），冗余严重
  - **粗粒度适应**：现有软合并方法（如SMEAR）在**实例级**（instance-level）路由，牺牲令牌级（token-level）动态性

### 2. 方法概述
CoMoL通过**核心空间（Core Space）**操作实现高效且细粒度的专家混合，包含两个关键组件：
- **核心空间专家**：将专家知识压缩存储于紧凑的核心矩阵 $M \in \mathbb{R}^{r \times r}$
- **核心空间路由**：在低秩核心空间内进行令牌级动态路由与软合并

### 3. 核心技术细节
- **SVD重参数化**：将LoRA更新分解为
  $$
  \Delta W = U_B M V_A^\top
  $$
  其中 $U_B \in \mathbb{R}^{m \times r}$、$V_A^\top \in \mathbb{R}^{r \times n}$ 为跨专家共享的奇异子空间，$M = \Sigma_B V_B^\top U_A \Sigma_A$ 为专家特定的核心矩阵。参数复杂度降至 $O((m+n)r + N \cdot r^2)$。

- **令牌级软合并**：利用矩阵分配律在核心空间合并专家
  $$
  h = W x + U_B \left(\sum_{i=1}^N G(x)_i M_i\right) V_A^\top x
  $$
  高维投影 $U_B, V_A$ 每令牌仅计算一次，合并成本仅为 $O(r^2)$。

- **低秩投影路由**：将路由输入投影至核心空间 $\hat{x} = V_A^\top x \in \mathbb{R}^r$，路由网络参数从 $O(N \cdot n)$ 降至 $O(N \cdot r)$。

### 4. 实验验证
- **数学推理**（Qwen3-8B/14B）：在GSM8K等6个基准上，CoMoL以与标准LoRA相当的参数量（约25M/36M）超越所有基线，平均准确率分别达84.48%和86.34%，优于参数量4倍的MoLoRA。
- **代码生成**（Qwen3-8B与Llama3.1-8B）：在HumanEval上pass@1达48.11%（Qwen3-8B）和35.00%（Llama3.1-8B），展现跨架构鲁棒性。
- **扩展性分析**：
  - 随秩 $r$ 增加，性能稳定优于LoRA
  - 专家数从8增至64时，参数量增幅极小（25.16M→27.91M），而传统方法在16专家时即显存溢出

### 5. 主要贡献
- 揭示MoE-LoRA的参数冗余与粗粒度适应瓶颈
- 提出首个在**令牌级**对LoRA专家进行**核心空间软合并**的框架，实现参数、计算效率与细粒度适应的统一
- 验证CoMoL在多样任务、模型规模与架构上的优越性与可扩展性



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
