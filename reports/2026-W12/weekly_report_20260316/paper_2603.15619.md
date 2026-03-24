# Mixture-of-Depths Attention

**arXiv**: [2603.15619](https://arxiv.org/abs/2603.15619) · [PDF](https://arxiv.org/pdf/2603.15619)  
**领域**: Pretraining  
**作者**: Zhu, Fang, Liao, Wang, Cheng, Huang, Chen, Wei 等 13 人  
**综合评分**: 8.86  （novelty: 9.0 · method: 9.0 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为混合深度注意力（Mixture-of-Depths Attention, MoDA）的新机制，旨在解决大型语言模型（LLMs）深度扩展时出现的信号退化问题。该方法允许注意力头同时关注当前层的序列KV对和来自前几层的深度KV对，从而更有效地保留和利用浅层形成的特征。论文在1.5B参数模型上进行了实验，结果表明MoDA在多个验证基准和下游任务上均能稳定超越基线模型，且仅带来3.7%的额外FLOPs开销。作者还开发了硬件高效的算法，实现了接近FlashAttention-2的效率。代码已开源。

---

## 详细分析

> **社区热度**: ⭐ 36 (来自 papers.cool)

## 问题定义

这篇论文试图解决大型语言模型（LLM）在**深度扩展（depth scaling）**过程中面临的**信息稀释（information dilution）**或**信号退化（signal degradation）**问题。

具体而言，核心问题体现在以下几个方面：

- **特征稀释现象**：随着模型深度增加，浅层网络中形成的有信息特征（informative features）在通过多层残差连接（residual connections）传递时，会被重复的逐层更新逐渐稀释，导致深层网络难以恢复和利用这些关键信息。

- **残差连接的局限性**：标准的残差路径（ResNet风格）虽然改善了深层网络的优化稳定性，但将深度历史压缩为单一的隐藏状态轨迹（single hidden-state trajectory），未能有效保留和传递跨层的丰富历史信息。

- **密集连接的高成本**：DenseNet风格的密集跨层连接虽然能无损地保留层间历史信息，但参数和计算复杂度随深度呈二次增长（$O(L^2D^2)$），在LLM规模下难以实用化。

- **深度维度的欠利用**：当前LLM实践主要通过数据、上下文和模型宽度进行扩展，而深度维度尽管具有较强的表征潜力，却因上述优化和信息保留难题而相对未被充分利用。

为解决这些问题，论文提出了**Mixture-of-Depths Attention (MoDA)**机制，其核心创新在于：

- **统一注意力机制**：允许每个注意力头同时关注**当前层的序列KV对**（sequence KV pairs）和来自**前面所有层的深度KV对**（depth KV pairs），通过单一的softmax操作实现数据依赖的深度信息动态检索。

- **硬件高效实现**：通过融合序列与深度注意力计算、分块感知的深度KV布局（chunk-aware depth-KV layout）和组感知索引（group-aware indexing），在64K序列长度下达到FlashAttention-2的97.3%效率，仅增加3.7%的FLOPs开销。

- **优化稳定性**：实验表明MoDA与Post-Norm结合使用时性能优于Pre-Norm，为深度扩展提供了更稳定的优化路径。

## 相关工作

基于论文内容，相关研究可分为以下几个主要方向：

### 1. 深度扩展与残差学习（Depth Scaling and Residual Learning）
- **ResNet (深度残差网络)**：He et al. [16] 提出的残差连接是训练深度网络的基础，通过"read-operate-write"范式中的恒等映射（identity read）和加法写入（add write）缓解梯度消失问题，但会导致信息在深度维度上的压缩和稀释。
- **Highway Networks**：Srivastava et al. [35] 早期探索了门控机制在深度网络中的信息流动。
- **DeepNet**：Wang et al. [40] 探索了将Transformer扩展到1,000层的方法，关注极深网络的训练稳定性。

### 2. 密集跨层连接（Dense Cross-Layer Connections）
- **DenseNet**：Huang et al. [20] 提出了密集连接卷积网络，通过连接（concatenation）而非加法保留所有历史层特征，避免了信息稀释，但带来了 $O(L^2D^2)$ 的参数和计算复杂度。
- **DenseFormer**：Pagliardini et al. [28] 将密集连接思想应用于Transformer，通过深度加权平均（depth weighted averaging）增强信息流，但同样面临高计算成本。
- **Dual Path Networks**：Chen et al. [7] 结合了残差学习和密集连接的优势。

### 3. 注意力机制与硬件优化（Attention Mechanisms and Hardware Efficiency）
- **标准Transformer注意力**：Vaswani et al. [39] 提出的自注意力机制是序列建模的基础，MoDA在此基础上扩展了深度维度的注意力。
- **Group Query Attention (GQA)**：Ainslie et al. [2] 通过共享键值头减少内存带宽，MoDA利用其分组特性（group size $G$）实现高效的深度KV计算。
- **FlashAttention/FlashAttention-2**：Dao et al. [12, 13] 提出的IO感知注意力算法是MoDA硬件实现的基础，通过分块计算（tiling）和在线softmax减少HBM访问。
- **线性注意力变体**：Yang et al. [44, 45, 46] 探索了硬件高效的线性注意力机制，与MoDA的长上下文优化目标相关。

### 4. 跨层信息传递的替代方案（Alternative Cross-Layer Approaches）
- **Virtual Width Networks**：Li et al. [22] 提出了虚拟宽度网络，通过升级残差连接增加模型容量。
- **Hyper-Connections**：Zhu et al. [49] 和 **MHC (Manifold-constrained Hyper-Connections)**：Xie et al. [42] 探索了跨层连接的新范式，通过超连接（hyper-connections）增强信息流动。

### 5. 长上下文与记忆机制（Long-Context and Memory）
- **Transformer-XL**：Dai et al. [11] 通过片段级循环机制处理超长序列。
- **Attention Sinks**：Xiao et al. [41] 发现注意力汇点现象（attention sink），MoDA通过引入深度KV改变了传统的概率分配模式，减轻了sink行为。
- **Native Sparse Attention**：Yuan et al. [47] 提出了硬件对齐的稀疏注意力，与MoDA的长序列效率优化相关。

### 6. 开放语言模型（Open Language Models）
- **OLMo2**：OLMo Team et al. [27] 是本文主要的实验基准，提供了400B token的训练配方和开源实现，MoDA在此基础上进行对比实验。

这些研究构成了MoDA的设计背景：从ResNet和DenseNet的架构演进中汲取灵感，结合现代LLM的GQA和FlashAttention硬件优化技术，针对深度扩展中的信息稀释问题提出解决方案。

## 解决方案

论文通过提出**Mixture-of-Depths Attention (MoDA)** 机制解决深度扩展中的信息稀释问题，具体解决方案包括以下四个层面：

### 1. 核心注意力机制：统一序列与深度检索

MoDA将标准序列注意力扩展为**混合深度注意力**，允许每个查询（query）同时关注两个信息源：
- **序列KV**（Sequence KV）：当前层的键值对，捕获序列上下文
- **深度KV**（Depth KV）：来自前面所有层的键值对，捕获跨层历史信息

在数学形式上，对于第 $l$ 层的第 $h$ 个注意力头，MoDA计算如下：

$$
\text{MoDA}(Q_h, K_{\text{seq}}, V_{\text{seq}}, K_{\text{depth}}, V_{\text{depth}}) = \text{softmax}\left(\frac{Q_h [K_{\text{seq}}^{\top} | K_{\text{depth}}^{\top}]}{\sqrt{d}} + M\right) \begin{bmatrix} V_{\text{seq}} \\ V_{\text{depth}} \end{bmatrix}
$$

其中 $[\cdot|\cdot]$ 表示拼接操作，$M$ 是联合掩码（包含序列因果掩码和深度匹配掩码），$d$ 为头维度。关键创新在于**单一softmax操作**统一归一化序列和深度信息，而非分步计算。

### 2. 架构设计：从"read-operate-write"视角重构深度流

论文通过"read-operate-write"框架系统比较了四种深度信息传播机制：

| 机制 | Read操作 | Write操作 | 数据依赖性 | 复杂度 |
|------|----------|-----------|------------|--------|
| **Depth Residual** | 恒等映射 $X_{l-1}$ | 加法 $X_{l-1} + F(X_{l-1})$ | ✘ | $O(LD)$ |
| **Depth Dense** | 线性投影历史 $\{X_i\}_{i=0}^{l-1}$ | 拼接 $\{X_i\}_{i=0}^{l}$ | ✘ | $O(L^2D^2)$ |
| **Depth Attention** | 注意力查询历史KV | 拼接当前KV | ✔ | $O(LD^2)$ |
| **MoDA** | 统一注意力查询序列+深度KV | 拼接当前KV至深度流 | ✔ | $O(LD^2/G)$ |

MoDA的关键改进在于：
- **读取阶段**：复用当前层的查询投影 $Q_l$，无需独立的深度查询投影，将参数复杂度从 $O(LD^2)$ 降至 $O(LD^2/G)$（$G$ 为GQA组大小）
- **写入阶段**：将当前层的Key-Value对追加到深度流 $\{K_i, V_i\}_{i=0}^{l}$，供后续层检索
- **FFN层扩展**：通过轻量级KV投影将FFN输入也映射为深度KV，使FFN层的历史信息可被后续注意力层访问

### 3. 硬件高效实现：融合核与内存布局优化

为解决深度KV导致的**非连续内存访问**问题，论文提出了三级渐进优化策略：

**Flash-Compatible深度KV布局**  
将深度缓存展平为单一维度 $T \times L$（序列长度×层数），确保同一token的 $L$ 个历史深度状态物理连续存储。这使深度检索转化为连续块读取，兼容FlashAttention的分块计算范式。

**分块感知布局（Chunk-Aware Layout）**  
将查询分块（chunk size $C$），每块仅访问对应的局部深度区域 $C \times L$ 而非全局 $T \times L$。深度利用率从 $\eta_{\text{depth}} = 1/T$ 提升至 $\eta_{\text{depth}} = 1/C$，显著减少无效内存访问。

**组感知索引（Group-Aware Indexing）**  
利用GQA的组映射关系 $T_q = G \cdot T_{kv}$，将 $G$ 个相邻查询行绑定到同一基时间索引 $\lfloor i_q/G \rfloor$。这使得每查询块仅需加载 $(C/G) \times L$ 的深度KV，深度利用率进一步提升至 $G/C$，并实现查询块与G组对齐的向量化执行。

**融合前向传播算法**  
算法1（MoDA Hardware-aware Forward Pass）实现了：
- 共享在线softmax状态（running max $m$、normalizer $\text{acc}$、accumulator $o$）贯穿序列和深度计算
- 三阶段流水线：先处理完全可见的序列块，再处理边界因果块，最后处理深度块
- 深度掩码应用：$\text{mask}(i_q, j_d) = \mathbb{1}[\lfloor i_q/G \rfloor = \lfloor j_d/L \rfloor]$ 确保仅匹配同token的深度历史

### 4. 复杂度与效率优化

MoDA在保持数据依赖性的同时实现了最优复杂度：
- **参数**：$O(LD^2/G)$，相比Depth Dense的 $O(L^2D^2)$ 大幅降低
- **计算**：预填充（Prefilling）$O(TL^2D)$，解码（Decoding）$O(L^2D)$，与Depth Attention同级但常数更小
- **缓存**：$O(TLD/G)$，与GQA线性注意力兼容

实验验证，该实现达到FlashAttention-2的**97.3%效率**（64K序列长度），仅引入**3.7%额外FLOPs**，使深度感知聚合在实际LLM训练中具备可行性。

## 实验验证

论文进行了系统的实验验证，涵盖模型性能、架构变体、扩展性分析及硬件效率等多个维度。具体实验内容如下：

### 1. 实验设置与基准
- **模型规模**：在700M和1.5B参数规模下训练解码器-only语言模型，采用Group Query Attention (GQA)
- **训练数据**：使用OLMo2数据集的400B token子集，全局批次大小1024，序列长度4096，bfloat16精度训练
- **评估基准**：
  - **下游任务**：PiQA、HellaSwag、WinoGrande、OpenBookQA、BoolQ、SciQ、ARC-Easy、ARC-Challenge、COPA、MMLU
  - **验证困惑度**：C4、ICE、m2d2-s2orc、Pile、Wiki-text及Dolma领域（Books、Common Crawl、peS2o、Reddit、Stack）

### 2. MoDA变体消融实验（Table 3）
在700M参数规模下（36层，宽度1024，GQA组大小$G=2$），系统比较了不同组件的有效性：
- **Baseline**：标准因果注意力（OLMo2）
- **Depth KV**：仅引入深度键值对（复用前层序列KV）
- **Extra FFN KV Proj.**：为FFN层添加独立的深度KV投影
- **Extra Attn KV Proj.**：为注意力层添加独立的深度KV投影

**关键发现**：Depth KV显著提升性能（C4困惑度从18.59降至18.48），添加FFN KV投影进一步改善（降至18.21），而额外的Attn KV投影收益递减且参数开销大。

### 3. 模型规模扩展实验（Table 4 & Table 5）
对比OLMo2与MoDA在700M和1.5B规模下的性能：
- **下游任务（Table 4）**：MoDA在1.5B规模下平均性能提升**2.11%**（62.28% → 64.39%），在HellaSwag、WinoGrande、ARC-Challenge等任务上均有提升
- **领域困惑度（Table 5）**：MoDA在1.5B规模下将平均验证困惑度从13.67降至13.47，在Reddit（21.21→20.85）、ICE（15.37→15.08）等所有10个领域均实现降低

### 4. 层数与归一化策略分析（Table 6）
在FineWeb-Edu数据集上测试不同深度配置（24层与48层）和归一化策略（Pre-norm vs. Post-norm）：
- **深度影响**：在48层深层模型中，MoDA（Depth KV）将验证损失从3.3800（Pre-norm）降至3.3759，从3.4062（Post-norm）降至3.3653
- **归一化交互**：Post-norm与MoDA结合效果更优，损失降低0.0409（对比Pre-norm的0.0041），表明MoDA有助于缓解深层Post-norm的优化不稳定
- **FFN扩展**：在Post-norm设置下，添加FFN KV投影进一步将48层模型损失降至3.3484

### 5. 注意力可视化分析（Figure 5）
对700M模型（400B token训练）的注意力模式进行可视化：
- **深度注意力分布**：在中间层和深层中，注意力头持续将显著概率质量分配给深度KV块（非零注意力权重），证实模型主动检索跨层历史信息
- **注意力汇点（Attention Sink）行为**：MoDA改变了传统注意力汇点现象，概率质量从固定汇点位置重新分配到包含有用信息的序列/深度槽位，呈现更分散的注意力分配模式

### 6. 硬件效率实验
#### 6.1 内核实现消融（Table 7）
在固定配置（$B=1, T=1024, G=8, L=64$）下比较不同优化策略的端到端前向+反向传播时间：
- **Naive PyTorch**：2128.9 ms
- **Flash-Compatible布局**：13.102 ms（**162.5×加速**）
- **+ Chunk-Aware**：6.286 ms（额外52%提升）
- **+ Group-Aware索引**：1.460 ms（最终**1458×加速**）

#### 6.2 与FlashAttention-2对比（Table 2）
在A100 GPU上对比MoDA-Triton与FA2-Triton的"前向+反向"运行时间：
- **序列长度扩展**（$T$从4K到64K）：MoDA额外时间占比从25.86%降至**2.73%**，随序列增长深度开销被摊销
- **GQA组大小扩展**（$G$从2到32）：深度利用率从3.12%提升至50%，额外时间占比从27.07%降至**2.84%**
- **模型深度扩展**（$L$从64到256）：运行时间从127.661 ms线性增长至167.958 ms，符合预期的线性深度缩放特性

### 7. 训练动态监控（Figure 2）
在1.5B参数设置下，对比MoDA与OLMo2在400B token训练过程中的指标：
- **验证损失**：MoDA在C4验证集上持续保持更低损失
- **下游性能**：HellaSwag、WinoGrande、ARC-Challenge等任务的准确率曲线显示MoDA全程优于基线

这些实验全面验证了MoDA在模型性能、优化稳定性、注意力机制解释性及硬件效率方面的优势，确立了其作为深度扩展有效原语的地位。

## 未来工作

基于论文内容，可进一步探索的研究方向包括：

### 1. 工业级系统优化与工程实现
- **高级CUDA内核优化**：当前硬件高效实现虽达到FlashAttention-2的97.3%效率，但在万亿参数规模的工业训练中，仍需开发更激进的内存调度策略、深度计算流水线（computation pipelining）以及与分布式通信（如张量并行、流水线并行）的更紧密重叠，以进一步消除内存停滞和内核启动开销。
- **编译器与自动优化**：探索基于Triton或CUTLASS的自动化代码生成，针对MoDA的非规则内存访问模式（non-contiguous depth-KV access）进行静态分析和动态优化。

### 2. 有界深度记忆机制（Bounded Depth Memory）
针对极深网络（如数百至上千层）中深度KV缓存线性增长的内存瓶颈：
- **固定预算的深度槽位缓存（Fixed-Size Depth KV Slot Buffer）**：将深度记忆从无界缓存（unbounded cache）转为固定大小 $S \ll L$ 的槽位预算。需研究：
  - **动态选择策略**：基于效用评分（utility scoring）机制动态保留Top-$S$个关键深度KV条目。
  - **混合滑动窗口**：部分槽位保留近期历史（recency-based），部分槽位保留全局高分记忆（high-score global memories）。
  - **联合训练策略**：如何端到端训练槽位选择策略，平衡质量、延迟与硬件效率。

### 3. 跨架构与跨模态扩展
- **与线性注意力机制结合**：将MoDA的深度检索机制扩展到Mamba、RWKV、Gated Linear Attention等线性注意力架构，探索在递归（recurrent）或状态空间模型（SSM）中维护深度记忆的方法。
- **多模态与大世界模型**：将MoDA应用于视觉Transformer（ViT）、视频理解模型及世界模型（World Models）。在多模态场景中，深度记忆可能捕获跨层级的视觉语义层次或物理动力学信息。

### 4. 理论理解与机制解释
- **注意力汇点（Attention Sink）的再审视**：实验观察到MoDA改变了传统注意力汇点行为（概率质量从固定sink位置重新分配），需深入研究：
  - 这种重新分配是否对应于对浅层语义信息的显式检索？
  - 是否存在最优的"深度-序列"注意力质量分配比例？
- **信息流动的理论建模**：从信息论角度量化深度残差连接中的信息稀释程度，建立MoDA缓解信号退化的理论边界。
- **Post-Norm协同效应**：实证发现MoDA与Post-Norm结合显著优于Pre-Norm，需从优化景观（optimization landscape）或梯度传播角度解释其内在机制。

### 5. 自适应与动态机制
- **深度自适应检索（Adaptive Depth Retrieval）**：当前MoDA默认聚合所有前层深度KV，可探索动态停止机制（early exiting in depth）或稀疏深度注意力（sparse depth attention），使模型根据输入复杂度自适应决定检索的历史深度范围。
- **分层深度记忆（Hierarchical Depth Memory）**：设计分层的深度缓存结构，如将历史层聚类为不同语义级别的"摘要"（summarization），实现更高效的深度信息检索。

### 6. 与混合专家（MoE）的协同
- **MoDA与MoE的联合设计**：探索Mixture-of-Depths Attention与Mixture-of-Experts的协同，例如在不同深度层级路由到不同专家，或让专家网络利用深度历史信息进行更细粒度的专家选择。

## 总结

该论文针对大型语言模型（LLM）在深度扩展（depth scaling）过程中遇到的**信息稀释（information dilution）**问题，提出了**Mixture-of-Depths Attention (MoDA)**机制，并开发了硬件高效的实现方案。以下是论文的主要内容概述：

### 1. 问题背景与动机
现代Transformer通过残差连接（residual connections）扩展深度时，浅层形成的有信息特征（informative features）会在逐层传递中被重复更新稀释，导致深层难以恢复关键信号。虽然DenseNet风格的密集跨层连接可以缓解此问题，但其参数和计算复杂度随深度呈二次增长（$O(L^2D^2)$），在LLM规模下不可行。因此，需要一种既能**数据依赖地动态检索历史深度信息**，又具备**硬件效率**的机制。

### 2. Mixture-of-Depths Attention (MoDA)
MoDA的核心创新在于将标准注意力机制扩展为统一处理**序列信息**（当前层）和**深度信息**（历史层）：

- **统一注意力空间**：对于第$l$层的查询 $Q_l$，MoDA允许其同时关注当前层的序列键值对（Sequence KV）以及来自前面所有层的深度键值对（Depth KV $\{K_i, V_i\}_{i=0}^{l-1}$），并在单一softmax操作下完成归一化：

$$
\text{MoDA}(Q_l) = \text{softmax}\left(\frac{Q_l [K_{\text{seq}}^{\top} | K_{\text{depth}}^{\top}]}{\sqrt{d}} + M\right) \begin{bmatrix} V_{\text{seq}} \\ V_{\text{depth}} \end{bmatrix}
$$

其中 $M$ 为联合掩码（包含序列因果掩码和深度位置匹配掩码）。

- **FFN层的深度参与**：通过轻量级投影将FFN层的输入也映射为深度KV，使FFN的历史信息可被后续层检索，突破了传统仅注意力层保留历史的限制。

- **"Read-Operate-Write"范式**：MoDA在深度流上实现了数据依赖的读取（attention-based read）和追加写入（concat write），在避免密集连接高成本的同时保留了深度历史信息。

### 3. 硬件高效实现
为解决深度KV非连续访问导致的效率问题，论文提出了三级优化策略：

1. **Flash-Compatible深度KV布局**：将历史KV展平为连续内存（$T \times L$），使深度检索转化为块读取。
2. **分块感知布局（Chunk-Aware）**：将查询分块（chunk size $C$），每块仅访问局部深度区域 $(C \times L)/G$，将深度利用率从 $1/T$ 提升至 $G/C$。
3. **组感知索引（Group-Aware Indexing）**：利用GQA的组映射关系 $T_q = G \cdot T_{kv}$，使 $G$ 个查询共享同一深度KV块，减少冗余加载。

该融合核实现共享在线softmax状态，在64K序列长度下达到**FlashAttention-2效率的97.3%**，仅增加3.7%的FLOPs开销。

### 4. 实验验证
在OLMo2数据集（400B tokens）上进行的实验表明：

- **性能提升**：1.5B参数模型在10个验证基准上平均困惑度降低0.2，在10个下游任务上平均性能提升**2.11%**（如ARC-Challenge提升4.35%）。
- **架构协同**：MoDA与**Post-Norm**结合时性能显著优于Pre-Norm，有助于缓解深层网络的优化不稳定。
- **效率验证**：内核实现相比Naive PyTorch获得**1458×加速**，在不同序列长度和GQA组大小下均保持高效（表2）。
- **注意力可视化**：MoDA改变了传统"注意力汇点"（attention sink）行为，将概率质量从固定位置重新分配到包含历史信息的深度槽位。

### 5. 主要贡献
- 提出了**MoDA机制**，以数据依赖方式动态聚合深度历史信息，解决LLM深度扩展中的信息稀释问题。
- 开发了**硬件感知的融合算法**，使深度注意力在长上下文训练中具备实用效率（线性宽度复杂度 $O(LD^2/G)$）。
- 提供了从700M到1.5B参数的完整实证研究，验证了MoDA在困惑度和下游任务上的一致性和显著收益。

论文代码已开源：https://github.com/hustvl/MoDA



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
