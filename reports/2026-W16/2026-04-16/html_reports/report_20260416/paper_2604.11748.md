# LangFlow: Continuous Diffusion Rivals Discrete in Language Modeling

**arXiv**: [2604.11748](https://arxiv.org/abs/2604.11748) · [PDF](https://arxiv.org/pdf/2604.11748)  
**领域**: Pretraining  
**作者**: Chen, Liang, Sui, Guo, Cheng, You, Liu  
**综合评分**: 8.00  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为LangFlow的连续扩散语言模型，首次在语言建模任务中使连续扩散方法达到与离散扩散方法相媲美的性能。论文通过将嵌入空间的扩散语言模型与基于Bregman散度的流匹配技术相结合，并引入了三个关键创新：基于ODE的负对数似然边界、基于信息均匀原则的可学习噪声调度器以及改进的训练协议。在LM1B和OpenWebText数据集上，LangFlow分别达到了30.0和24.6的困惑度，并在7个基准测试中的4个上超过了自回归基线的零样本迁移性能。论文提供了代码开源，实验设计严谨，为连续扩散在语言建模领域的应用提供了有力证据。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

这篇论文致力于解决**连续扩散语言模型（Continuous Diffusion Language Models, DLMs）在语言建模任务中长期落后于离散扩散模型（Discrete Diffusion）和自回归模型（Autoregressive Models）的核心问题**。

具体而言，论文识别并解决了以下关键挑战：

### 1. **理论基础薄弱与训练目标不明确**
现有的嵌入空间（embedding-space）连续扩散模型缺乏严格的理论支撑，其训练目标往往是启发式的（如直接回归嵌入向量）或实现繁琐（如需动态切片批次来优化不同损失项）。这导致模型优化缺乏原则性指导。

**解决方案**：论文通过**Bregman散度**建立连续Flow Matching与语言模型交叉熵损失之间的理论联系，证明最小化交叉熵等价于在词元（token）空间进行后验匹配，从而为嵌入空间扩散提供了严格的训练目标。

### 2. **缺乏可靠的似然评估机制**
语言建模的核心评估指标是困惑度（Perplexity, PPL），其依赖于对数似然（NLL）的精确估计。先前工作仅提供基于随机微分方程（SDE）的变分下界（ELBO），而连续扩散的优势在于常微分方程（ODE）采样，但缺乏对应的ODE-based NLL评估方法。

**解决方案**：论文推导出**基于ODE的NLL上界**（Theorem 3.1），通过积分概率流ODE的散度项，首次实现了对嵌入空间DLM的精确似然评估，且该界限优于先前的SDE-based方法。

### 3. **次优的噪声调度（Noise Schedule）**
图像扩散的最优噪声调度（如均匀分布或重要性采样）直接迁移到语言建模时效果不佳。论文发现语言数据的稀疏性和离散性导致其信息增益曲线与图像截然不同：在较高噪声水平（低信噪比）区域，模型已能完美预测词元，继续在该区域分配训练资源造成浪费。

**解决方案**：提出**信息均匀原则（Information-Uniform Principle）**，即噪声密度应与单位噪声水平的信息增益率（后验熵的导数）成正比。基于此，论文设计了一个可学习的Gumbel分布噪声调度器，将训练和采样步骤集中到信息增益最显著的噪声区域。

### 4. **自条件（Self-Conditioning）机制的误用**
自条件是提升扩散模型样本质量的关键技术，但论文发现其在连续扩散与离散扩散中的作用机制存在本质差异：在离散扩散中，自条件会改善生成困惑度（Gen. PPL）但损害似然困惑度（PPL）；而在连续扩散中，自条件能同时提升两者。

**解决方案**：修正了连续DLM的自条件训练协议，证明在嵌入空间扩散中启用自条件对PPL和Gen. PPL均有显著增益，从而弥合了连续与离散模型之间的性能差距。

### 总结
通过上述理论创新（Bregman Flow Matching、ODE-based NLL界）与技术改进（Gumbel噪声调度、自条件协议），论文提出的**LangFlow**首次实现了连续扩散语言模型在标准基准（LM1B、OpenWebText）上匹敌甚至超越顶尖离散扩散模型（如MDLM）和自回归基线，证明了连续扩散是语言建模中一个可行且前景广阔的范式。

## 相关工作

与LangFlow相关的研究可从以下几个维度进行梳理：

### 1. 离散扩散语言模型（Discrete Diffusion Language Models）
直接在离散状态空间（词元级别）上构建扩散过程，基于连续时间马尔可夫链（CTMC）理论：
- **D3PM** (Austin et al., 2021)：在离散状态空间上定义前向加噪过程，支持吸收态（absorbing-state）和均匀态（uniform-state）转移。
- **SEDD** (Lou et al., 2023)：通过估计数据分布比率（ratio estimation）实现离散扩散建模。
- **MDLM** (Sahoo et al., 2024)：简化掩码扩散语言模型，在吸收态设置下实现可扩展的语言建模。
- **UDLM** (Schiff et al., 2024)：针对均匀态转移的离散扩散改进。
- **Duo** (Sahoo et al., 2025a)：通过结合离散扩散与自回归技术提升性能。
- **其他**：DiffusionBert (He et al., 2023)、基于块扩散的半自回归方法 (Arriola et al., 2025)。

### 2. 连续扩散语言模型（Continuous Diffusion Language Models）
将连续扩散（源于图像生成）扩展到语言数据，分为两个子类：

**单纯形扩散（Simplex Diffusion）**：
在词元概率单纯形上定义扩散过程，但受困于高维稀疏性：
- **Categorical Flow Matching** (Cheng et al., 2024)
- **Shortlisting Model** (Song et al., 2025)
- **α-Flow** (Cheng et al., 2025a)

**嵌入空间扩散（Embedding-space Diffusion）**：
在词嵌入空间中进行高斯扩散，是LangFlow所属的类别：
- **Diffusion-LM** (Li et al., 2022)：最早将连续扩散应用于语言，通过嵌入空间扩散实现可控文本生成。
- **Plaid** (Gulrajani & Hashimoto, 2023)：提供基于SDE的似然评估，但训练目标复杂且存在嵌入坍缩问题。
- **FLM** (Lee et al., 2026)：同期工作，专注于单步/少步生成，但缺乏ODE-based PPL评估。

### 3. 生成建模基础框架
**Flow Matching** (Lipman et al., 2023)：LangFlow的理论基础，通过回归速度场学习ODE流。
**变分流匹配（Variational Flow Matching）**：
- **VFM** (Eijkelboom et al., 2024)：通过辅助后验引入变分视角。
- **EF-VFM** (Guzmán-Cordero et al., 2025)：基于Bregman散度的指数族变分流匹配。

### 4. 训练技术与设计选择
**自条件（Self-Conditioning）**：
- **Analog Bits** (Chen et al., 2022)：在扩散模型中引入自条件技术，LangFlow发现其在连续扩散中的作用机制与离散扩散有本质差异。

**噪声调度（Noise Scheduling）**：
- **重要性采样** (Nichol & Dhariwal, 2021)：根据损失分布分配训练预算，被Plaid等嵌入空间模型采用。
- **图像扩散启发式**：EDM (Karras et al., 2022)、Stable Diffusion 3 (Esser et al., 2024)的均匀调度策略，LangFlow证明其不适用于语言。

### 5. 多模态与统一生成架构
- **Transfusion** (Zhou et al., 2024)：统一文本生成与图像扩散的多模态架构。
- **Diffuse Everything** (Rojas et al., 2025)：探索任意状态空间上的多模态扩散。

### 6. 加速与蒸馏技术（潜在关联）
- **一致性模型（Consistency Models）** (Song et al., 2023)：用于ODE路径的少步采样加速，LangFlow的ODE公式为应用此类技术保留可能性。

这些研究共同构成了从离散到连续、从理论到应用的扩散语言模型发展谱系，LangFlow通过连接Flow Matching与Bregman散度，填补了嵌入空间扩散在理论严谨性和评估可靠性方面的空白。

## 解决方案

论文通过**LangFlow**框架系统性地解决了连续扩散语言模型的核心挑战，具体从理论奠基、评估方法和训练技术三个层面展开：

---

### 1. 理论奠基：通过Bregman散度连接Flow Matching与交叉熵

**问题**：现有嵌入空间扩散模型的训练目标缺乏严格理论支撑，多为启发式回归（如直接回归嵌入向量）或实现复杂。

**解决方案**：
论文建立了**Bregman散度流匹配**（Bregman Divergence Flow Matching）框架，证明在词元（token）空间最小化交叉熵等价于在嵌入空间进行后验匹配。

- **核心推导**：对于凸函数 $f(p) = p \cdot \log p$，Bregman散度为
  $$D_f(p, q) = p \cdot \log p - p \cdot \log q$$
  当 $p = 1_{x^{(i)}}$（真实词元的one-hot向量）时，散度退化为负对数似然：
  $$D_f(1_{x^{(i)}}, q) = -\log q(x^{(i)})$$

- **训练目标**：定义交叉熵损失（公式18）：
  $$\mathcal{L}_{\text{CE}}(\theta) = \mathbb{E}_{\gamma \sim \pi} \left[ -\frac{1}{L} \sum_{i=1}^L \log \hat{x}^{(i,x^{(i)})}_\theta(z_\gamma, \gamma) \right]$$

  其中模型输入为噪声嵌入 $z_\gamma = \alpha_\gamma z + \sigma_\gamma \epsilon$，输出为词元概率 $\hat{x}_\theta$。通过嵌入矩阵 $E$ 将概率映射回嵌入空间得到去噪器：
  $$\hat{z}^{(i)}_\theta = E^\top \hat{x}^{(i)}_\theta$$

- **优势**：该目标使模型在词元空间优化离散似然，同时通过 $\hat{z}_\theta$ 保持ODE轨迹的连续性，避免了单纯形扩散的高维稀疏问题。

---

### 2. 评估方法：ODE-based负对数似然上界

**问题**：缺乏针对嵌入空间扩散的可靠困惑度（PPL）评估方法，先前工作仅提供SDE-based变分下界（ELBO），且无法配合ODE采样使用。

**解决方案**：
论文推导出**基于概率流ODE的NLL上界**（Theorem 3.1），实现了对连续DLM的精确似然估计。

- **关键公式**（公式20）：
  $$\log p(x) \geq \mathbb{E}_{z} \left[ \frac{LD}{2} - \frac{\|z_b\|^2}{2\sigma_b^2} + \sum_{i=1}^L \log \hat{x}^{(i,x^{(i)})}_\theta(z_a, a) - \int_a^b \frac{\alpha_\gamma}{2} \nabla \cdot \hat{z}_\theta(z_\gamma, \gamma) \, d\gamma \right]$$

- **实现细节**：
  - 使用Heun-2求解器计算积分轨迹
  - 采用Hutchinson迹估计器（trace estimator）高效计算散度项 $\nabla \cdot \hat{z}_\theta$
  - 相比SDE方法，该界限更紧且与确定性ODE采样兼容

---

### 3. 训练技术一：信息均匀噪声调度（Gumbel分布）

**问题**：图像扩散的均匀噪声调度在语言建模中效率低下，因语言数据在较高噪声水平（低信噪比）时已可完美预测，导致训练资源浪费。

**解决方案**：
提出**信息均匀原则**（Information-Uniform Principle），即噪声密度应与单位噪声水平的信息增益率成正比。

- **$\gamma$-路径重参数化**：使用对数噪声-信号比（logNSR）$\gamma = \log(\sigma^2/\alpha^2)$代替时间 $t$，使噪声水平指数变化时时间条件线性变化。

- **Gumbel分布调度**：
  通过分析发现信息增益率 $H'_\gamma = dH_\gamma/d\gamma$（后验熵的导数）呈正偏分布，最佳拟合为**Gumbel分布**：
  $$H_\gamma = H_{+\infty} \cdot \exp\left(-\exp\left(-\frac{\gamma - P_\mu}{P_\beta}\right)\right)$$

  其中 $P_\mu$（位置）和 $P_\beta$（尺度）为可学习参数，通过辅助损失 $\mathcal{L}_{\text{Scheduler}} = \mathbb{E}[(\ell_{\text{CE}}(\gamma) - H_\gamma)^2]$ 优化。

- **效果**：将训练和采样步骤集中在信息增益最大的噪声区域（图2），显著降低生成困惑度（从约1000降至154.2）。

---

### 4. 训练技术二：自条件（Self-Conditioning）协议修正

**问题**：自条件在离散扩散中会改善生成质量但损害似然（PPL），此前研究因此禁用自条件评估。论文发现该结论不适用于连续扩散。

**解决方案**：
- **机制差异**：在LangFlow中，自条件同时改善**似然困惑度（PPL）**和**生成困惑度（Gen. PPL）**（表1），这与离散扩散（如MDLM）的权衡机制截然不同。

- **实现方式**：
  - **训练时**：以概率 $p_{\text{SC}} = 0.25$ 将前一步预测 $\hat{z}_\theta$ 作为辅助输入注入模型（算法1第7-13行）
  - **采样时**：始终启用自条件，第一步初始化为零，后续步骤使用前一步预测（算法2第8、11行）
  - **架构适配**：通过零初始化的权重矩阵 $W_{\text{SC}}$ 融合自条件输入：$z_\gamma \leftarrow z_\gamma + W_{\text{in}}z_\gamma + W_{\text{SC}}\hat{z}_{\text{SC}}$

---

### 5. 整体训练与采样流程

**训练流程**（算法1）：
1. 嵌入词元 $z = (e_{x^{(1)}}, \ldots, e_{x^{(L)}})$
2. 从Gumbel分布采样 $\gamma$，构造噪声 $z_\gamma \sim \mathcal{N}(\alpha_\gamma z, \sigma_\gamma^2 I)$
3. 以0.25概率应用自条件，计算交叉熵损失 $\mathcal{L}_{\text{CE}}$ 和调度损失 $\mathcal{L}_{\text{Scheduler}}$
4. 联合优化

**采样流程**（算法2）：
1. 从Gumbel分布的尾部采样 $\gamma_0$，初始化 $z_0 \sim \mathcal{N}(0, \sigma_0^2 I)$
2. 使用Euler求解器沿ODE迭代：利用当前预测 $\hat{x}_\theta$ 计算 $\hat{z}$，更新下一步状态
3. 最终通过 $\arg\max$ 解码词元

通过这些创新，LangFlow在LM1B上达到PPL 30.0，在OpenWebText上达到24.6，首次证明连续扩散可匹敌离散扩散和自回归模型。

## 实验验证

论文在**标准语言建模基准**上进行了系统评估，涵盖似然估计、生成质量、零样本迁移及消融分析。以下是详细实验内容：

---

### 1. 实验设置

**数据集**
- **LM1B** (Chelba et al., 2013)：上下文长度128，使用bert-base-uncased分词器
- **OpenWebText (OWT)**：上下文长度1024，使用gpt2-large分词器，采用序列打包（sequence packing）

**模型架构**
- 基于DiT（Diffusion Transformer）的130M参数模型：12层、768隐藏维度、12注意力头
- 时间条件基于$\gamma$（对数NSR）而非$t$，使用可学习的Gumbel噪声调度器
- 嵌入归一化到单位球面并缩放$\sqrt{D}$（遵循潜在扩散实践）

**训练协议**
- 训练步数：1M步（LM1B和OWT）
- 批次大小：512
- 优化器：AdamW，学习率$3 \times 10^{-4}$，预热2,500步
- 自条件概率：0.25
- 预条件跳跃连接：前5K步warmup

**基线模型**
- **自回归**：Transformer（重新训练）
- **离散扩散**：D3PM、DiffusionBert、SEDD（Absorb/Uniform）、MDLM、UDLM、Duo
- **连续扩散**：Diffusion-LM、Plaid、FLM（同期工作）

---

### 2. 主要实验结果

#### 2.1 语言建模性能（表2）
在LM1B和OWT验证集上评估**困惑度（PPL）**和**生成困惑度（Gen. PPL）**：

| 模型 | LM1B (Gen. PPL↓ / PPL↓) | OWT (Gen. PPL↓ / PPL↓) |
|------|------------------------|------------------------|
| **LangFlow** | **92.2** / **30.0** | **36.5** / **24.6** |
| Autoregressive | 66.7 / 22.8 | 35.9 / 17.5 |
| MDLM (离散) | 103.9 / 31.0 | 104.9 / 23.2 |
| Duo (离散) | 97.6 / 33.6 | 77.6 / 25.2 |
| Plaid (连续) | 77.3 / 32.4 | - / - |
| FLM (连续) | 96.9 / - | 62.2 / - |

**关键发现**：
- LangFlow在LM1B上达到**PPL 30.0**，超越所有均匀态离散扩散，与最先进掩码扩散（MDLM）相当
- 在OWT上达到**PPL 24.6**，排名第三，但**Gen. PPL 36.5**排名第一，显著优于MDLM（104.9）和Duo（77.6）

#### 2.2 零样本迁移（表3）
在OWT上训练后，评估7个下游语料库的零样本困惑度：

| 模型 | PTB | Wikitext | LM1B | Lambada | AG News | PubMed | Arxiv |
|------|-----|----------|------|---------|---------|--------|-------|
| **LangFlow** | **81.20** | **32.28** | 68.21 | **46.93** | 69.41 | 46.74 | 38.47 |
| Autoregressive | 82.05 | 25.75 | 51.25 | 51.28 | 52.09 | 49.01 | 41.73 |
| MDLM | 95.26 | 32.83 | 67.01 | 47.52 | 61.15 | **41.89** | **37.37** |
| Duo | 89.35 | 33.57 | 73.86 | 49.78 | 67.81 | 44.48 | 40.39 |

**关键发现**：
- LangFlow在**PTB、Wikitext、Lambada**上取得最佳（金色）或次佳（银色）表现
- 在7个基准中的**4个上超越自回归模型**，在**3个上超越MDLM**

---

### 3. 消融实验

#### 3.1 自条件机制（表1）
对比自条件（SC）在离散（MDLM）与连续（LangFlow）扩散中的不同效应：

| 模型 | Gen. PPL↓ | $\Delta$ | PPL↓ | $\Delta$ |
|------|-----------|----------|------|----------|
| MDLM | 103.9 | - | 31.0 | - |
| MDLM + SC | 94.9 | -9.0 | 32.7 | **+1.7** |
| LangFlow | 154.2 | - | 49.0 | - |
| LangFlow + SC | 81.5 | **-72.7** | 30.0 | **-19.0** |

**结论**：自条件在LangFlow中**同时改善似然和生成质量**，与离散扩散中的权衡机制（改善Gen. PPL但损害PPL）截然不同。

#### 3.2 噪声调度分析（图2）
通过损失几何分析验证Gumbel调度的必要性：
- **左图**：基于时间$t$的均匀调度导致$t \in [0.2, 1.0]$区间损失几乎为零（资源浪费）
- **中图**：基于$\gamma$（logNSR）的调度显示跨训练阶段的稳定损失结构
- **右图**：损失导数$\partial \mathcal{L}/\partial \gamma$呈现正偏分布，与**Gumbel分布**高度吻合

**效果**：采用Gumbel调度后，生成困惑度从**~1000降至154.2**（LM1B）。

---

### 4. 采样效率分析（NFE）

评估不同采样步数（NFE）下的生成质量（表5、表6）：

**LM1B（LangFlow）**：
| NFE | Gen. PPL | 熵 |
|-----|----------|-----|
| 128 | 92.24 | 4.31 |
| 64 | 104.83 | 4.32 |
| 32 | 127.32 | 4.33 |
| 16 | 179.60 | 4.35 |

**OWT对比**（与离散模型对比）：
| NFE | LangFlow (Gen. PPL) | Duo | MDLM | SEDD Absorb |
|-----|---------------------|-----|------|-------------|
| 1024 | 36.53 | 77.69 | 104.85 | 105.03 |
| 128 | 60.09 | 80.02 | 120.77 | 117.28 |
| 64 | 80.34 | 85.62 | 143.88 | 138.42 |

**观察**：LangFlow在少步采样（如64步）下仍保持相对较低的Gen. PPL，且样本熵（5.25-5.51）接近数据熵（5.44），表明生成质量稳定。

---

### 5. 定性分析（附录D.3）

提供LM1B（128词元）和OWT（1024词元）的生成样本，对比：
- **LangFlow**：语法连贯，事实一致性较好（如科学、体育新闻）
- **AR**：流畅但偶有事实幻觉
- **MDLM/Duo**：存在更多语义不连贯或重复模式
- **Plaid**：观察到嵌入坍缩导致的模式重复（高频内容词重复）

**熵分析**：LangFlow的样本熵略低于离散基线（表6），但这主要源于高频内容词的全局分布偏置，而非局部退化（如重复短语）。通过最大内容词频率统计（表7）验证：LangFlow的重复词通常分布在不同句子中，不影响语义连贯性。

---

### 6. 理论验证实验

**嵌入坍缩检测**（图3，附录C.3）：
通过最近邻距离（NND）分布验证Plaid（MSE损失）存在严重嵌入坍缩（平均NND=0.058），而LangFlow（CE损失）与AR、MDLM分布相似（平均NND≈1.0），证明交叉熵目标有效保持嵌入空间结构。

这些实验共同证明，通过理论基础的修正（Bregman Flow Matching、ODE-based评估）和技术创新（Gumbel调度、自条件），LangFlow首次实现了连续扩散在语言建模中的竞争力。

## 未来工作

基于论文的局限性与讨论，以下方向值得进一步探索：

### 1. 样本熵与生成多样性的深度分析
论文观察到LangFlow的样本熵低于某些离散扩散基线（表6），尽管定性评估未发现明显的重复或退化模式。这一现象可能源于：
- **全局频率偏置**：连续扩散对高频内容词的过度表示（附录D.3表7）
- **熵指标的局限性**：现有熵计算基于词元频率，可能无法区分"有益的连贯性"与"有害的重复"

未来可探索：设计更细粒度的多样性指标（如语义层面而非词元层面），或在大规模（>7B参数）场景下验证低熵是否导致模式崩溃。

### 2. 自条件机制的内在机理
论文发现自条件（Self-Conditioning）在连续与离散扩散中的作用机制存在**本质差异**（表1）：
- 离散扩散：改善Gen. PPL但损害PPL（似然与质量的权衡）
- 连续扩散：同时改善两者

潜在探索方向：
- 分析自条件对ODE轨迹平滑性的影响
- 研究自条件概率 $p_{\text{SC}}$ 的动态调度策略（当前固定为0.25）
- 探索迭代精炼（iterative refinement）与自条件的协同效应

### 3. 计算效率优化
**散度估计的加速**：当前ODE-based NLL评估（定理3.1）依赖Hutchinson迹估计器计算 $\nabla \cdot \hat{z}_\theta$，计算成本较高。可探索：
- 基于代理模型（surrogate model）的快速散度近似
- 随机投影方法的方差缩减技术

**少步生成**：LangFlow目前使用128-1024步采样。结合论文提到的**一致性模型（Consistency Models）**（Song et al., 2023）进行蒸馏，实现单步或4步生成是自然的下一步。

### 4. 噪声调度的动态适应
当前Gumbel调度器通过可学习参数 $P_\mu, P_\beta$ 拟合静态信息增益曲线。可扩展至：
- **数据依赖的动态调度**：根据输入序列的复杂度自适应调整 $\gamma$ 范围
- **课程学习（Curriculum Learning）**：在训练初期关注高噪声区域，后期逐渐转向精细去噪阶段

### 5. 规模扩展与涌现能力
论文在130M参数规模验证有效性。需验证：
- **大语言模型（LLM）扩展性**：在1B+参数规模下是否保持对离散扩散的竞争力
- **长上下文建模**：当前最大1024词元，扩展到8K-32K上下文时，连续扩散的轨迹编辑优势是否更显著

### 6. 连续扩散的固有优势挖掘
论文强调连续扩散具备**轨迹可编辑性**（trajectory editing）和**确定性双射**（bijective mapping），但实验中未充分探索：
- **可控生成**：利用嵌入空间的插值实现属性控制（如情感、风格迁移）
- **填充与编辑（Infilling/Editing）**：利用ODE的可逆性实现文本的部分修改而非从头生成
- **多模态统一**：与图像/视频扩散共享同一连续空间（参考Transfusion方向）

### 7. 理论边界 tightened
定理3.1提供的NLL上界可通过以下方式改进：
- **更高阶ODE求解器**：当前使用Euler/Heun-2，采用Runge-Kutta方法可能降低截断误差
- **方差缩减技术**：在期望估计中引入控制变量（control variates）

### 8. 与离散方法的混合架构
探索**离散-连续混合**范式：
- 在粗粒度阶段使用离散扩散进行语义规划，细粒度阶段使用连续扩散优化流畅性
- 利用Gumbel-Softmax或Concrete分布桥接两种表示空间

这些方向既包括即时的技术改进（效率、规模），也涵盖对连续扩散本质优势的深层挖掘（可控性、多模态统一）。

## 总结

本文提出了**LangFlow**，首个在语言建模任务中匹敌离散扩散模型和自回归基线的连续扩散语言模型（Continuous Diffusion Language Model）。以下是论文的主要内容总结：

### 1. 研究背景与核心问题
连续扩散模型在图像、视频等连续模态生成中取得了显著成功，但在语言建模中长期落后于离散扩散（Discrete Diffusion）和自回归模型。现有嵌入空间（Embedding-space）连续扩散模型面临三大瓶颈：
- **理论基础薄弱**：训练目标多为启发式回归，缺乏严格数学基础
- **评估机制缺失**：缺乏可靠的ODE-based负对数似然（NLL）评估方法，无法准确计算困惑度（PPL）
- **设计空间未探索**：噪声调度和自条件（Self-Conditioning）等关键超参数直接迁移自图像扩散，未考虑语言数据的稀疏性和离散性

### 2. LangFlow的核心技术创新

#### (1) Bregman散度流匹配（Bregman Divergence Flow Matching）
通过Bregman散度建立连续Flow Matching与交叉熵损失的理论联系：
- 证明最小化交叉熵损失等价于在词元（Token）空间进行后验匹配
- 训练目标为：
  $$\mathcal{L}_{\text{CE}}(\theta) = \mathbb{E}_{\gamma \sim \pi} \left[ -\frac{1}{L} \sum_{i=1}^L \log \hat{x}^{(i,x^{(i)})}_\theta(z_\gamma, \gamma) \right]$$
- 通过嵌入矩阵 $E$ 将离散概率映射回连续嵌入空间：$\hat{z}_\theta = E^\top \hat{x}_\theta$，实现离散似然优化与连续ODE轨迹的统一

#### (2) 基于ODE的NLL上界（Theorem 3.1）
推导出首个适用于嵌入空间扩散的ODE-based似然评估公式：
$$\log p(x) \geq \mathbb{E}_{z} \left[ \frac{LD}{2} - \frac{\|z_b\|^2}{2\sigma_b^2} + \sum_{i=1}^L \log \hat{x}^{(i,x^{(i)})}_\theta(z_a, a) - \int_a^b \frac{\alpha_\gamma}{2} \nabla \cdot \hat{z}_\theta(z_\gamma, \gamma) \, d\gamma \right]$$
该界限克服了先前SDE-based方法的局限性，为连续DLM提供了可靠的PPL评估手段。

#### (3) 信息均匀噪声调度（Information-Uniform Principle）
提出根据信息增益率分配噪声密度的原则：
- 发现语言数据的信息增益曲线呈正偏分布，最佳拟合为**Gumbel分布**：
  $$H_\gamma = H_{+\infty} \cdot \exp\left(-\exp\left(-\frac{\gamma - P_\mu}{P_\beta}\right)\right)$$
- 通过可学习参数 $P_\mu, P_\beta$ 动态调整训练采样和推理步长分布，将计算资源集中于信息增益最大的噪声区域

#### (4) 自条件协议修正
揭示自条件在连续与离散扩散中的机制差异：
- 在离散扩散中，自条件改善生成质量但损害似然（PPL）
- 在连续扩散中，自条件**同时改善PPL和生成困惑度（Gen. PPL）**
- 据此修正训练协议，以0.25概率在训练时启用自条件，采样时始终启用

### 3. 实验结果与验证

**主要性能指标**：
- **LM1B**：PPL达到**30.0**，Gen. PPL为92.2
- **OpenWebText**：PPL达到**24.6**，Gen. PPL为36.5

**对比基线**：
- 超越所有均匀态离散扩散（如D3PM Uniform、SEDD Uniform）
- 与最先进掩码扩散模型MDLM（PPL 31.0 → 30.0）和Duo（PPL 33.6 → 30.0）相当或更优
- 零样本迁移：在PTB、Wikitext、Lambada等7个基准中的4个上超越自回归Transformer

**消融实验**：
- Gumbel噪声调度将Gen. PPL从约1000降至154.2（LM1B）
- 自条件使LangFlow的PPL从49.0降至30.0，Gen. PPL从154.2降至81.5

### 4. 结论与意义
LangFlow通过连接Bregman散度与Flow Matching，建立了连续扩散语言建模的严格理论框架，并解决了噪声调度和自条件等关键工程问题。该工作提供了**首个明确证据**，证明连续扩散在语言建模中可与离散方法竞争，同时保留了连续扩散固有的优势：可编辑的生成轨迹、稳定的ODE采样，以及未来通过一致性模型（Consistency Models）实现少步加速的潜力。这为统一多模态生成架构（文本、图像、视频共享同一连续扩散框架）奠定了基础。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
