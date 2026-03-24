# M$^2$RNN: Non-Linear RNNs with Matrix-Valued States for Scalable Language Modeling

**arXiv**: [2603.14360](https://arxiv.org/abs/2603.14360) · [PDF](https://arxiv.org/pdf/2603.14360)  
**领域**: Pretraining  
**作者**: Mishra, Tan, Stoica, Gonzalez, Dao  
**综合评分**: 8.13  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文由加州大学伯克利分校（UC Berkeley）的研究团队提出了一种新型非线性循环神经网络架构M²RNN，该架构采用矩阵值隐藏状态和表达性非线性状态转移，旨在解决Transformer在计算表达能力上的局限性。论文在语言建模任务上展示了M²RNN在状态跟踪泛化、大规模建模效率和长上下文泛化方面的显著优势，特别是在混合架构中替换单个循环层即可带来可观的性能提升。

---

## 详细分析

> **社区热度**: ⭐ 5 (来自 papers.cool)

## 问题定义

这篇论文旨在解决现有序列建模架构在表达能力、状态容量和硬件效率方面的关键局限性，具体包括：

## 1. Transformer 的表达能力限制
Transformer 虽具备高度并行性，但受限于 $\text{TC}^0$ 复杂度类，无法解决需要更强计算表达能力的任务（如实体跟踪、代码执行、排列组合等 $\text{NC}^1$-完全问题）。

## 2. 线性 RNN 的局限性
- **有限的状态跟踪能力**：线性 RNN（如 Mamba、Gated DeltaNet）在硬状态跟踪任务（如 $S_5$ 排列群问题）上表达能力不足，被证明弱于非线性 RNN。
- **较差的上下文检索性能**：由于通过固定秩外积更新循环状态，当键值关联数量超过状态容量时，先前信息会被覆盖，导致在"大海捞针"等长上下文检索任务中表现不佳。

## 3. 传统非线性 RNN 的局限性
- **状态容量不足**：传统非线性 RNN（如 LSTM、GRU）使用向量值隐藏状态 $h_t \in \mathbb{R}^d$，其状态大小远小于线性注意力模型的矩阵值状态 $H_t \in \mathbb{R}^{K \times V}$，导致语言建模性能显著落后。
- **上下文检索缺陷**：受限的状态容量难以压缩并检索长序列中的特定键值关联。
- **训练效率低下**：由于元素级非线性破坏了并行扫描算法所需的结合律，非线性 RNN 无法在序列长度上并行化；同时向量化实现导致批次维度填充浪费 FLOPs，硬件利用率低下。

## 提出的解决方案
论文提出 **Matrix-to-Matrix RNN (M2RNN)**，通过以下机制解决上述问题：
- **矩阵值状态与非线性转移**：采用矩阵值隐藏状态 $H_t \in \mathbb{R}^{K \times V}$ 和富有表现力的非线性状态转移，既保持非线性 RNN 的状态跟踪能力（可表示正则语言），又通过外积状态扩展机制 $k_t v_t^\top$ 显著增加有效状态大小。
- **硬件高效的计算**：外积形式使 GEMM 计算维度与批次大小解耦，避免 FlashRNN 中的填充开销，实现张量核心的高效利用。
- **混合架构策略**：通过将少量 M2RNN 层（甚至仅一层）与线性 RNN 或注意力机制混合，在不显著影响训练吞吐量的前提下，提升长上下文泛化能力和语言建模性能。

## 相关工作

根据论文内容，相关研究可分为以下几个类别：

## 1. 基础序列建模架构
- **Transformer架构**：Vaswani et al. (2017) 提出的注意力机制；Brown et al. (2020) 的GPT-3；Chowdhery et al. (2023) 的PaLM；Grattafiori et al. (2024) 的Llama系列
- **位置编码**：Su et al. (2024) 的RoPE；Kazemnejad et al. (2023) 关于无位置编码(NoPE)的研究

## 2. 线性RNN与状态空间模型(SSMs)
- **Mamba系列**：Gu & Dao (2024) 的Mamba-1；Dao & Gu (2024) 的Mamba-2（结构化状态空间对偶性）
- **线性注意力变体**：Katharopoulos et al. (2020) 的核特征线性注意力；Yang et al. (2023, 2024b) 的Gated Linear Attention与DeltaNet；Yang et al. (2024a) 的Gated DeltaNet
- **其他SSMs**：Fu et al. (2022) 的H3；Peng et al. (2023) 的RWKV；Sun et al. (2023) 的RetNet

## 3. 表达能力与状态跟踪理论
- **复杂度类分析**：Merrill et al. (2022) 证明Transformer属于$\text{TC}^0$；Merrill et al. (2024) 分析线性RNN与非线性RNN的表达能力差距
- **状态跟踪改进**：Grazzi et al. (2024) 关于负特征值解锁状态跟踪的研究；Siems et al. (2025) 基于Householder矩阵的DeltaProduct；Terzić et al. (2025) 使用结构化稀疏转移矩阵的方法

## 4. 传统非线性RNN
- **经典架构**：Elman (1990) 的基础RNN；Hochreiter & Schmidhuber (1997) 的LSTM；Cho et al. (2014a,b) 的GRU
- **现代优化**：Beck et al. (2024) 的xLSTM；Pöppel et al. (2025) 的FlashRNN（块级优化）

## 5. 高效训练与并行化算法
- **并行扫描**：Hillis & Steele (1986)；Blelloch (1990) 的并行前缀和算法
- **分块并行**：Hua et al. (2022) 的块级并行公式
- **非线性并行化尝试**：Danieli et al. (2025) 基于Newton法的Pararnn

## 6. 系统优化与硬件效率
- **注意力优化**：Dao et al. (2022) 的FlashAttention；Dao (2023) 的FlashAttention-2；Shah et al. (2024) 的FlashAttention-3；Zadouri et al. (2026) 的FlashAttention-4
- **编译与内核**：Tillet et al. (2019) 的Triton DSL；NVIDIA的CUTLASS与WMMA/WGMMA指令集
- **分布式训练**：Shoeybi et al. (2019) 的张量并行(TP)；Zhang et al. (2025) 的Desync-Residual策略

## 7. 评估基准与数据集
- **长上下文评估**：Hsieh et al. (2024) 的RULER（含S-NIAH、MQ/MK/MV-NIAH）；Bai et al. (2024) 的LongBench；Nelson et al. (2024) 的Needle-in-Haystack
- **下游任务**：Gao et al. (2024) 的LM Eval Harness；包括LAMBADA、HellaSwag、PIQA、ARC、Winogrande等
- **状态跟踪合成任务**：$S_3$/$S_5$排列群任务（Grazzi et al., 2024）

## 8. 混合架构与生产系统
- **混合模型**：Lieber et al. (2024) 的Jamba；Ren et al. (2024) 的Samba；Blakeman et al. (2025) 的Nemotron系列；IBM Granite Team (2025) 的Granite 4.0

这些研究构成了M2RNN的理论基础（状态跟踪的复杂度理论）、动机（线性RNN的局限性）和技术手段（外积状态扩展、硬件优化策略）。

## 解决方案

论文通过提出 **Matrix-to-Matrix RNN (M2RNN)** 架构，从架构设计、状态表示、计算效率和系统集成四个维度系统性地解决了上述问题。具体解决方案如下：

## 1. 矩阵值状态与非线性转移机制

### 核心创新：外积状态扩展
不同于传统非线性 RNN 的向量值状态 $h_t \in \mathbb{R}^d$，M2RNN 采用矩阵值隐藏状态 $H_t \in \mathbb{R}^{K \times V}$，并通过外积形式进行状态更新：

$$
Z_t = \tanh(H_{t-1}W + k_t v_t^\top)
$$

$$
H_t = f_t H_{t-1} + (1 - f_t) Z_t
$$

其中 $k_t \in \mathbb{R}^{K \times 1}$、$v_t \in \mathbb{R}^{V \times 1}$ 为键和值向量，$W \in \mathbb{R}^{V \times V}$ 为转移矩阵，$f_t \in [0,1]$ 为遗忘门。该设计的关键优势在于：

- **参数效率**：状态大小 $K \times V$（例如 $64 \times 16 = 1024$）远大于向量维度 $d$（例如 $1360$），但参数仅随 $K+V$ 线性增长，而非二次增长
- **表达能力**：保留非线性激活 $\tanh$，使其能够表示所有正则语言（Regular Languages），解决 $S_5$ 等 $\text{NC}^1$-完全状态跟踪问题（定理 1）

### 独立遗忘门设计
遗忘门 $f_t$ 仅依赖于输入 $x_t$ 而与历史状态 $H_{t-1}$ 无关：

$$
f_t = \frac{1}{(1 + e^{x_t + \beta_n})^{\alpha_n}}
$$

这使得遗忘门计算可在序列维度上完全并行，同时通过多头初始化不同 $\alpha_n, \beta_n$ 实现差异化的记忆衰减特性。

## 2. 解决状态跟踪的表达能力缺陷

针对线性 RNN 无法解决硬状态跟踪问题（如 $S_5$ 排列群组合）的限制，M2RNN 通过非线性状态转移实现了：

- **理论保证**：可模拟任意确定性有限状态自动机（DFA），严格强于 $\text{TC}^0$ 类模型
- **长度泛化**：在 $S_3$ 任务上，M2RNN 在训练长度（128）到测试长度（512）上实现 $>99.5\%$ 准确率，而理论上可表达该任务的 Gated DeltaProduct 在长度外推时出现显著退化（图 3）

## 3. 提升语言建模与上下文检索性能

### 大状态容量的关键作用
论文通过实验验证（表 7），状态大小是递归模型性能的关键驱动因素：

| 模型 | 状态大小 | Wiki PPL | Lambada PPL |
|------|----------|----------|-------------|
| RNN-406M | 1,360 | 33.74 | 317.38 |
| GRU-474M | 1,360 | 25.80 | 63.83 |
| M2RNN-410M | 86,016 | 22.92 | 33.63 |

M2RNN 通过外积机制将有效状态扩展至 86K，显著优于同等参数规模的向量值 RNN。

### 上下文检索增强
矩阵状态 $H_t$ 通过外积 $k_t v_t^\top$ 存储键值关联，通过查询读取 $H_t^\top q_t$ 实现检索：
- 在 RULER 基准的 MQ/MK/MV-NIAH 任务中，Hybrid Gated DeltaNet + M2RNN-1 显著优于纯 Gated DeltaNet（图 6、8）
- 在 LongBench 长上下文任务中，混合 M2RNN 架构比纯线性 RNN 混合架构提升高达 8 个百分点（表 5、6）

## 4. 硬件效率优化

### 避免填充开销
与传统向量化非线性 RNN（如 FlashRNN）不同，M2RNN 的 GEMM 计算维度为 $M, N, K = K, V, V$（例如 $64 \times 16 \times 16$），与批次大小 $B$ 无关：

- **张量核心利用**：只要 $K, V$ 为 16 的倍数，即可使用 WGMMA 指令，无需批次填充
- **消除 FLOPs 浪费**：FlashRNN 需将批次填充至 16 的倍数（如 $B=4$ 时浪费 75% FLOPs），而 M2RNN 在任意批次大小下均满效率运行（第 3.4 节）

### 稀疏化使用策略
由于非线性递归计算成本较高，论文提出**混合架构**（Hybrid Architecture）策略：
- 将 M2RNN 层与线性 RNN（Mamba-2、Gated DeltaNet）或注意力层交错使用
- **关键发现**：即使仅将**单层**线性 RNN 替换为 M2RNN（如 Hybrid Gated DeltaNet + M2RNN-1），也能获得与全 M2RNN 混合模型相当的质量提升，而训练吞吐量仅下降 6%（图 10）

## 5. 分布式训练支持

针对大规模训练需求，论文设计了两种张量并行（TP）策略：

1. **拓扑感知策略**：采用分组值形式（Grouped-Value），查询/键头数等于 TP 世界大小，无额外通信开销，但参数数依赖于 TP 配置
2. **拓扑无关策略**：保持多头值形式（Multi-Value），通过 RMSNormTP 模块引入少量 AllReduce 操作，保持参数数恒定，支持灵活部署（第 4.3 节）

## 6. 完整计算流程

M2RNN 层的完整前向计算流程为：

$$
q_t = \text{SiLU}(\text{conv1d}(W_q x_t)) \in \mathbb{R}^{K \times 1}
$$

$$
k_t = \text{SiLU}(\text{conv1d}(W_k x_t)) \in \mathbb{R}^{K \times 1}
$$

$$
v_t = \text{SiLU}(\text{conv1d}(W_v x_t)) \in \mathbb{R}^{V \times 1}
$$

$$
f_t = \psi(W_f x_t) \in [0,1]
$$

$$
g_t = \text{SiLU}(W_g x_t) \in \mathbb{R}^{V \times 1}
$$

$$
Z_t = \tanh(H_{t-1}W + k_t v_t^\top)
$$

$$
H_t = f_t H_{t-1} + (1 - f_t) Z_t
$$

$$
y_t = H_t^\top q_t + w_r \odot v_t
$$

$$
o_t = W_o \cdot \text{RMSNorm}(y_t \odot g_t)
$$

其中所有输入投影（$q_t, k_t, v_t, f_t, g_t$）均可在序列维度上并行计算，仅递归更新（$Z_t, H_t$）需顺序执行。

通过上述设计，M2RNN 在保持非线性 RNN 表达能力的同时，通过矩阵值状态扩展达到了线性 RNN 的状态容量，并通过硬件感知优化和混合架构策略实现了实用的训练效率。

## 实验验证

论文在 **410M 参数密集模型** 和 **7B 总参数（1.1B 激活）MoE 模型** 两个规模上开展了系统性的实验评估，所有模型均在相同数据（100B tokens from Nemotron-CC-v2）和相同训练配置下训练以确保公平比较。

## 1. 架构对比实验

### 1.1 同质架构（纯递归模型）
对比了纯 M2RNN 与以下基线：
- **线性 RNN**：Mamba-2、Gated DeltaNet
- **传统非线性 RNN**：RNN、GRU

**主要结果**（表 1、表 2）：
- M2RNN 在 WikiText 和 LAMBADA 困惑度上与 Mamba-2 相当（差距 $\leq 0.01$），显著优于 Gated DeltaNet（在 7B MoE 上低 0.09 困惑度）
- 传统 RNN 和 GRU 显著落后（WikiText 困惑度分别高 10+ 和 3+）
- 下游任务平均准确率：M2RNN 与 Mamba-2 差距 $\leq 0.06$ 点，均优于 Gated DeltaNet

### 1.2 混合架构（Hybrid）
**2-way 混合**（每 8 层含 1 层注意力）：
- Hybrid M2RNN vs Hybrid Mamba-2 vs Hybrid Gated DeltaNet vs Transformer++
- **结果**：Hybrid M2RNN 在 WikiText 上比 Hybrid Gated DeltaNet 低 **0.4–0.5 困惑度**（410M 和 7B 均如此），下游任务准确率最高

**3-way 混合**（在 Hybrid Gated DeltaNet/Mamba-2 中替换部分层为 M2RNN）：
- Hybrid Gated DeltaNet + M2RNN-1（替换 1 层）、+ M2RNN-3/5（替换 3/5 层）
- **关键发现**：仅替换 **1 层** M2RNN 即可达到与完整 Hybrid M2RNN 相当的质量提升，WikiText 困惑度降低 0.44–0.50（表 1、表 2）

## 2. 状态跟踪能力验证

**S3 排列群任务**（图 3）：
- 训练序列长度 128，测试长度至 512
- M2RNN 和 GRU 达到 **>99.5% 准确率** 且完美长度泛化
- Gated DeltaNet 完全无法学习该任务
- Gated DeltaProduct（理论可表达 S3）在训练长度内表现良好，但在长度外推时显著退化

**结论**：M2RNN 兼具非线性 RNN 的表达能力（解决 $\text{NC}^1$-完全问题）和优异的长度泛化能力。

## 3. 上下文检索实验

### 3.1 合成任务（RULER 基准）
评估了单针检索（S-NIAH-1/2/3）和多针检索（MQ/MK/MV-NIAH），上下文长度从 4K（训练长度）到 16K（图 6–9）：

- **Hybrid Gated DeltaNet + M2RNN**：在 MQ/MK/MV-NIAH 上显著优于纯 Hybrid Gated DeltaNet，在训练长度（$\leq$ 4K）和零样本外推长度（>4K）上均有提升
- **Hybrid Mamba-2 + M2RNN**：单 M2RNN 层改进有限，但替换 3/5 层后 consistent 提升（归因于 Mamba-2 较弱的状态转移需要更多 M2RNN 层补偿）

### 3.2 真实世界数据
在 6 个真实检索数据集（SQuAD、NQ、DROP、TriviaQA、FDA、SWDE）上评估（表 3、表 4）：

- **纯递归模型**：Gated DeltaNet 因状态大小优势优于 M2RNN
- **混合模型**：Hybrid M2RNN 平均准确率比 Hybrid Gated DeltaNet 高 **3.8 点**（410M）和 **4.2 点**（7B MoE）
- **3-way 混合**：Hybrid Gated DeltaNet + M2RNN-1 比基线提升 **2.4 点**（410M）和 **3.0 点**（7B）

## 4. 长上下文性能（LongBench）

在 8 个长上下文任务（涵盖摘要、问答、代码检索）上评估（表 5、表 6）：

- **2-way 混合**：Hybrid M2RNN 在 7B MoE 规模上比 Hybrid Gated DeltaNet 高 **1.5 点** 平均准确率
- **3-way 混合**：Hybrid Gated DeltaNet + M2RNN-1 比基线提升 **4.1 点**（410M）和 **7.3 点**（7B MoE）；+ M2RNN-3/5 分别提升 **7.6 点** 和 **8.1 点**
- 在 TriviaQA、RepoBench-P 等关键任务上提升最为显著

## 5. 消融实验（表 7）

**状态大小的影响**：
- RNN（状态大小 1,360）：WikiText 33.74 PPL
- GRU（状态大小 1,360，474M 参数）：25.80 PPL  
- **M2RNN（状态大小 86,016，410M 参数）：22.92 PPL**

**结论**：状态大小是递归模型性能的主要驱动因素，非线性本身不是瓶颈；M2RNN 的外积扩展机制在固定参数预算下实现了最大状态容量。

## 6. 系统性能实验

**训练吞吐量**（图 10，8×H100）：
- 在 7B MoE 配置下，Mamba-2 吞吐量最高，Transformer++ 在长上下文下降显著
- **Hybrid Gated DeltaNet + M2RNN-1** 与纯 Hybrid Gated DeltaNet 吞吐量差距 **<6%**（在 4K 和 16K 上下文长度下）
- 纯 M2RNN 因计算成本较高吞吐量较低，但混合策略使其具备实用性

**硬件效率验证**：
- 验证了 M2RNN 的 GEMM 计算（$K \times V \times V$）无需批次填充即可利用张量核心，避免了 FlashRNN 在批次大小为 4 时 75% 的 FLOPs 浪费

## 7. 分布式训练验证

验证了两种张量并行（TP）策略的有效性（第 4.3 节）：
- **拓扑感知策略**：无额外通信，但参数数依赖于 TP 世界大小
- **拓扑无关策略**：通过 RMSNormTP 模块引入少量 AllReduce，保持参数数恒定，支持灵活部署

这些实验共同证明了 M2RNN 在表达能力（状态跟踪）、模型质量（语言建模、检索、长上下文）和系统效率（训练吞吐量、硬件利用率）三个维度上的综合优势。

## 未来工作

基于论文结论与讨论，以下是可以进一步探索的研究方向：

## 1. 计算效率与内核优化
- **更高效的非线性递归实现**：当前 M2RNN 的前向与反向传播基于 Triton DSL 实现，反向传播受限于 HBM 带宽。开发 CUTLASS 等底层优化的内核实现可显著降低计算开销（论文第 4.2 节提及正在开发中）。
- **近似算法探索**：非线性递归相比线性替代方案引入了额外计算开销。研究在保持表达能力的前提下，通过近似（如低秩近似、稀疏化）或更高效的数值方法降低计算成本的策略（论文第 6 节）。
- **避免序列长度顺序计算**：探索突破非线性 RNN 必须在序列长度上顺序计算的根本限制，例如改进 Danieli et al. (2025) 基于 Newton 法的并行化方案，或开发新的并行算法。

## 2. 规模与训练设置扩展
- **更大规模验证**：当前实验限于 410M 密集模型和 7B MoE 模型。在数十亿至千亿参数规模以及更长训练上下文（如 128K+ tokens）下验证 M2RNN 的有效性和扩展规律（论文第 6 节）。
- **超长上下文训练**：当前模型训练于 4K 上下文长度，仅在推理时测试至 16K。探索在更长序列（如 100K+）上训练时的内存效率与性能表现，结合线性 RNN 的线性内存优势与 M2RNN 的表达能力。

## 3. 架构设计与混合策略优化
- **最优层放置策略**：论文发现替换单层 M2RNN 即可带来显著提升，但尚未系统性研究在深层网络中**哪些层**（如早期层 vs 深层）替换为 M2RNN 效果最佳，以及不同任务（代码、推理、检索）是否对应不同的最优配置。
- **状态扩展机制的改进**：当前采用固定的外积状态扩展（$K \times V$）。探索动态状态大小调整、自适应维度分配，或结合低秩压缩技术以在参数预算内进一步增大有效状态容量（参考 Qin et al., 2024）。
- **遗忘门机制深化**：当前使用标量遗忘门（per-head），论文提及向量值遗忘门效果有限但参数量大。研究更精细的门控机制（如元素级或结构化门控）以平衡梯度稳定性与参数效率。

## 4. 表达能力与理论分析
- **超越正则语言**：论文证明 M2RNN 可表示所有正则语言（DFA）。探索其是否能在有限精度下模拟更复杂的计算模型（如 pushdown automata），或在特定结构约束下的表达能力边界。
- **与线性 RNN 的深度融合**：研究如何在不破坏并行训练能力的前提下，将 M2RNN 的非线性转移与 Mamba、Gated DeltaNet 的硬件高效实现更深度结合，例如通过结构化稀疏性或分层混合设计。

## 5. 下游任务与应用场景
- **硬状态跟踪任务的实际应用**：在真实代码执行、实体跟踪、长程依赖推理等任务上验证 M2RNN 相比 Transformer 和线性 RNN 的优势，而不仅是合成任务（如 $S_5$ 排列群）。
- **多模态扩展**：论文提及基础模型需处理文本、代码、图像、视频、语音等多模态数据。探索 M2RNN 在处理时序连续数据（如视频、音频）或结构化数据（如代码 AST）时的归纳偏置优势。

## 6. 分布式训练与系统优化
- **张量并行策略的进一步优化**：当前两种 TP 策略（拓扑感知与拓扑无关）在通信与参数效率间权衡。研究更精细的并行策略，如序列并行（Sequence Parallelism）与 M2RNN 的结合，或自适应 TP 拓扑选择。
- **与 MoE 的协同优化**：当前 7B MoE 实验仅验证基础可行性。探索 M2RNN 层与专家路由（Expert Routing）的交互，以及在大规模 MoE 中的负载均衡与通信优化。

## 总结

这篇论文提出了 **M2RNN (Matrix-to-Matrix RNN)**，一种具有矩阵值隐藏状态的非线性循环神经网络架构，旨在解决现有序列模型在表达能力、状态容量和硬件效率方面的关键局限。

## 核心问题
- **Transformer**：受限于 $\text{TC}^0$ 复杂度，无法解决实体跟踪、代码执行等需要 $\text{NC}^1$ 计算能力的任务。
- **线性 RNN**（如 Mamba、Gated DeltaNet）：在硬状态跟踪任务（如 $S_5$ 排列群）上表达能力不足，且因固定秩状态更新导致上下文检索性能差。
- **传统非线性 RNN**（如 LSTM、GRU）：使用向量值状态，容量太小导致语言建模性能显著落后，且训练无法并行化、硬件利用率低。

## 主要贡献
**1. 矩阵值状态与非线性转移**
- 采用矩阵隐藏状态 $H_t \in \mathbb{R}^{K \times V}$，通过外积 $k_t v_t^\top$ 扩展状态大小（例如 86,016 vs 1,360），参数仅随 $K+V$ 线性增长。
- 保留 $\tanh$ 非线性，可表示所有正则语言，解决 $\text{NC}^1$-完全的状态跟踪问题（如 $S_5$），并在 $S_3$ 任务上实现完美的长度泛化（训练长度 128 → 测试长度 512 保持 $>99.5\%$ 准确率）。

**2. 硬件高效的实现**
- 外积形式使 GEMM 计算维度 $(K \times V \times V)$ 与批次大小无关，避免 FlashRNN 因批次填充导致的 FLOPs 浪费（如批次为 4 时浪费 75%）。
- 遗忘门 $f_t$ 仅依赖输入 $x_t$，可并行计算，防止梯度消失。

**3. 稀疏混合策略**
- 即使仅在混合架构中**替换单层**为 M2RNN（如 Hybrid Gated DeltaNet + M2RNN-1），也能获得与完整 Hybrid M2RNN 相当的质量提升，而训练吞吐量仅下降 6%。

## 关键实验结果
- **语言建模**：Hybrid M2RNN 在 7B MoE 模型上比 Hybrid Gated DeltaNet 低 **0.5 困惑度**。
- **长上下文**：在 LongBench 上，Hybrid Gated DeltaNet + M2RNN 比基线提升高达 **8 个点**。
- **上下文检索**：在真实检索任务（RULER、SQuAD 等）上，混合 M2RNN 模型显著优于纯线性 RNN 混合模型。
- **消融验证**：状态大小是递归模型性能的关键驱动因素，而非非线性本身；M2RNN 以更少参数实现了比 GRU 大 63 倍的状态容量。

## 系统优化
- 提出两种张量并行（TP）策略：拓扑感知（无额外通信）和拓扑无关（参数数恒定，少量 AllReduce）。
- 基于 Triton 实现自定义前向/反向内核，通过重计算避免缓存大状态矩阵。

## 结论
M2RNN 通过**矩阵值状态扩展**突破了传统非线性 RNN 的状态容量瓶颈，在保持非线性表达能力（解决硬状态跟踪任务）的同时，达到了与线性 RNN 相当的语言建模性能，并通过混合架构策略实现了实用的训练效率，为高效且可扩展的语言模型提供了新的构建模块。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
