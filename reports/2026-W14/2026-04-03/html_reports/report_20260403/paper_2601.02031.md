# Output Embedding Centering for Stable LLM Pretraining

**arXiv**: [2601.02031](https://arxiv.org/abs/2601.02031) · [PDF](https://arxiv.org/pdf/2601.02031)  
**领域**: Pretraining  
**作者**: Stollenwerk, Lokrantz, Hertzberg  
**综合评分**: 8.45  （novelty: 9.0 · method: 9.0 · evidence: 8.0 · clarity: 8.5）

---

## 摘要

> 本文提出了一种名为输出嵌入中心化（Output Embedding Centering, OEC）的新方法，用于解决大语言模型预训练后期常见的输出logit发散不稳定问题。论文从输出嵌入的几何特性角度分析了问题的根源（各向异性嵌入），并提出了两种具体实现方式（μ-centering和μ-loss）。实验表明，该方法在训练稳定性方面优于广泛使用的z-loss，且对超参数调优的敏感性更低。作者团队信息未在摘要中明确提及，故省略背景介绍。

---

## 详细分析

> **社区热度**: ⭐ 8 (来自 papers.cool)

## 问题定义

该论文聚焦于大语言模型（LLM）预训练阶段出现的一种训练不稳定性——**输出 logit 发散（output logit divergence）**。具体而言，当使用较大学习率时，语言模型头部的输出 logit 会趋向于无限增长，导致训练崩溃。已有主流缓解手段 z-loss 仅抑制症状而非根因。作者从**输出嵌入（output embedding）的几何分布**出发，发现其各向异性（anisotropy）——即全体输出嵌入整体偏离原点——是引发 logit 发散的根本原因。为此，论文提出**输出嵌入中心化（Output Embedding Centering, OEC）**策略，包括确定性操作 µ-centering 与正则项 µ-loss，在理论上抑制 logit 发散，并在实验中验证其相比 z-loss 具有更高的训练稳定性、学习率鲁棒性及超参数鲁棒性。

## 相关工作

相关研究可归纳为三条主线，均与“输出 logit 发散”或“嵌入各向异性”直接关联：

1. 输出 logit 发散的观测与缓解  
   - Chowdhery et al. (2022) 首次在 PaLM 训练中观察到 logit 爆炸，提出 z-loss 正则项 $L_z=10^{-4}\log^2\!\bigl(\sum_j \exp l_j\bigr)$。  
   - Wortsman et al. (2023) 在小型代理实验中复现该现象，确认 z-loss 可抑制发散，但指出其在大学习率下仍可能失效。  
   - Baichuan 2 (Yang et al. 2025) 提出 max-z-loss，对最大 logit 平方惩罚；Gemma 2 (Gemma Team et al. 2024) 采用 logit soft-capping 直接裁剪数值；NormSoftMax (Jiang et al. 2023) 动态缩放 softmax 温度。上述方法均针对“症状”而非“根因”。

2. 嵌入各向异性的成因与治理  
   - Gao et al. (2019) 发现 Transformer 词嵌入呈“窄锥”分布，提出 cosine 正则化。  
   - Zhang et al. (2020) 引入 Laplace 正则化；Wang et al. (2020) 通过谱控制改善分布。  
   - Biś et al. (2021) 指出嵌入实为“近各向同性+共同偏移”，首次将偏移量与原点距离关联。  
   - Stollenwerk & Stollenwerk (2025) 证明 Adam 的二阶矩是共同偏移的源头，提出 Coupled Adam 优化器修正；Machina & Mercer (2024) 进一步验证该现象主要源于输出嵌入而非输入嵌入。

3. 训练稳定性与优化器视角  
   - Dehghani et al. (2023) 在 22B ViT 训练中报告注意力 logit 爆炸，提出梯度裁剪与温度缩放。  
   - Takase et al. (2025) 系统分析预训练“尖峰”现象，强调学习率与批次大小耦合对稳定性的影响。  
   - Porian et al. (2024) 给出不同规模 LLM 的 compute-optimal 缩放曲线，为本文实验规模选择提供基准。

本文工作首次将“嵌入整体偏移”与“输出 logit 发散”建立因果联系，并据此提出针对“根因”的确定性或正则化解决方案，区别于上述仅缓解症状的研究。

## 解决方案

论文从“根因”出发，将输出 logit 发散归咎于**输出嵌入整体偏离原点**（即各向异性中的共同偏移）。解决方案分三步：

1. 理论剖析  
   - 证明输出嵌入的均值 μ 与 logit 均值 $l$ 满足 $l = \boldsymbol{μ}^\top \boldsymbol{h}$，且单个 logit 满足 $|l_j| \le \max_i\|\boldsymbol{e}_i\|\cdot\|\boldsymbol{h}\|$。  
   - 由此得出：只要控制 $\|\boldsymbol{μ}\|$ 与 $\max_i\|\boldsymbol{e}_i\|$，就能收紧 logit 的全局上下界，阻断其无限增长。

2. 提出 Output Embedding Centering (OEC)  
   - **μ-centering**（确定性版本）：每步优化后执行  
     $$\boldsymbol{e}_i^\star = \boldsymbol{e}_i - \boldsymbol{μ}, \quad \boldsymbol{μ}=\frac{1}{V}\sum_{k=1}^V \boldsymbol{e}_k$$  
     使新嵌入均值为 0，logit 均值归零，且在不改变 softmax 分布的前提下严格减小 $\max_i|\boldsymbol{e}_i^\star\cdot\hat{\boldsymbol{μ}}|$，从而压缩 logit 动态范围。  
   - **μ-loss**（正则化版本）：在训练目标中增加  
     $$L_μ = λ\,\boldsymbol{μ}^\top\boldsymbol{μ}, \quad λ=10^{-4}$$  
     通过梯度持续把 μ 拉向原点，近似达到中心化效果；理论证明当 $L_μ\to\infty$ 时 $\max_j |l_j|\to\infty$ 被抑制。

3. 实验验证  
   - 在 16 M–221 M 参数的 decoder-only Transformer 上，用 7 个学习率 $\eta\in[3\times10^{-4},3\times10^{-1}]$ 对比 baseline、z-loss、μ-loss、μ-centering。  
   - 结果：  
     – μ-centering 与 μ-loss 在所有规模、所有大学习率下均未发散，学习率敏感度 LRS 比 z-loss 低 30 %–80 %。  
     – μ-loss 对正则系数 λ 不敏感（$10^{-4}\le λ\le 10^{2}$ 均稳定），而 z-loss 在 λ 过大或配合大学习率时仍崩溃，且其“最优”λ 需提高到 $10^{-1}$ 才能媲美 μ-loss。  
     – 计算开销 <1 %，几乎零成本。

综上，论文通过“把输出嵌入均值钉在原点”这一简单操作，从根本上压缩了 logit 的允许取值范围，从而在大学习率场景下稳定了 LLM 预训练。

## 实验验证

实验设计围绕“输出 logit 发散”这一不稳定现象展开，核心目标是验证 **μ-centering** 与 **μ-loss** 能否在**大学习率区间**内持续抑制发散，并对比主流 **z-loss**。具体实验内容如下：

---

### 1. 训练稳定性主实验  
- **模型**：5 个 decoder-only Transformer，参数量 16 M–221 M。  
- **数据**：FineWeb 13.1 B token，GPT-2 tokenizer（V = 50 304）。  
- **学习率**：7 个 η ∈ {3e-4, 1e-3, 3e-3, 1e-2, 3e-2, 1e-1, 3e-1}，覆盖“小”到“极端”区间。  
- **变量**：每种 (模型, η) 组合分别训练  
  – baseline（无正则）  
  – z-loss（λ = 1e-4）  
  – μ-loss（λ = 1e-4）  
  – μ-centering（确定性中心化）  
  共 7 × 5 × 4 = 140 次完整预训练。  

- **观测指标**  
  - 最终测试 loss 随 η 的变化曲线  
  - 学习率敏感度 LRS = E_η[min(L(η), L_0) − min_η L]  
  - 额外训练时间 overhead  

---

### 2. 内部机制分析实验  
对主实验产生的 140 个 checkpoint，额外抽取 5×10^5 条验证集隐藏状态，离线计算：  
- logits 均值 l  
- logits 标准差 σ_l  
- 最大绝对 logit max_j |l_j|  
- 输出嵌入均值范数 ‖μ‖  

用于验证理论预测：  
  – μ-centering 使 l ≈ 0，且 σ_l 与 baseline 相同；  
  – μ-loss/z-loss 对 σ_l 有轻微修正；  
  – μ-centering/μ-loss 将 ‖μ‖ 锁定在 0 或小值，从而压制 max_j |l_j|。

---

### 3. 正则化超参数敏感性实验  
固定 221 M 模型，单独扫描  
λ ∈ {1e-7, 1e-4, 1e-1, 1e2}  
对 μ-loss 与 z-loss 分别重复完整 7-η 网格训练，共 2 × 4 × 7 = 56 次运行。  
评估最优 loss 与 LRS 随 λ 的变化，得出：  
  – μ-loss 在 λ ≥ 1e-4 即稳定，几乎无需调参；  
  – z-loss 需 λ ≈ 1e-1 才能获得同等稳定性，且 λ 过大（1e2）反而引发新发散。

---

### 4. 辅助验证  
- **Bratio 统计**：对全部 35 个 baseline 模型计算  
  Bratio = max(B_−, B_+) / max(B_−−‖μ‖², B_++‖μ‖²)  
  验证定理 6 的前提 Bratio ≤ 1 始终成立，且随 η 增大而减小，解释为何大学习率下中心化收益更高。  

- **训练时间测量**：在 4×A100 数据并行环境记录 wall-clock，确认 μ-centering/μ-loss 额外开销 <1 %。

---

综上，论文通过**大规模网格训练 + 离线诊断 + 超参数扫描**三重实验，系统验证了所提方法在**稳定性、鲁棒性与效率**上均优于现有 z-loss。

## 未来工作

以下方向可视为对现有工作的自然延伸或深层追问，均围绕“输出嵌入几何-训练稳定性”这一新视角展开：

---

### 1. 规模外推与模型家族
- **参数量级**：继续实验 1 B→10 B→100 B 模型，观察 Bratio≤1 是否依然成立；当 V≫H 时 μ-centering 是否仍足够。  
- **架构差异**：  
  – Encoder-only (BERT)、Encoder-Decoder (T5)、MoE、Hybrid-Mamba 是否同样存在 μ-漂移？  
  – 多模态融合模型（Chameleon、Flamingo）中图像/文本输出嵌入是否共享同一偏移机制？  
- **位置编码**：RoPE、ALiBi、LongContext 变体对 μ 的累积速度有无不同？

---

### 2. 优化器与精度视角
- **Adam 变体**：AdamW、Adafactor、SOAP、Shampoo 的二阶矩更新规则是否会放大或缩小 μ-漂移？  
- **低精度训练**：FP16/FP8 下 μ-centering 能否减少梯度溢出事件？与动态损失缩放如何协同？  
- **学习率调度**：cosine↔linear↔constant↔CoolDown 对 Bratio 的时序演化有何影响？能否设计“μ-感知”调度器？

---

### 3. 理论深挖
- **收敛保证**：在凸因子分解视角下，证明 μ-centering 每步均降低 logits 动态范围的期望上界。  
- **与层归一化交互**：Pre-LN、Post-LN、RMSNorm 对 μ-漂移的放大/抑制系数可否定量刻画？  
- **温度与 softmax 谱**：研究 μ-漂移与 softmax 温度 dithering 的耦合，导出“免温度” softmax 变体。

---

### 4. 正则化杂交与自适应
- **混合损失**：z-loss + μ-loss 的 Pareto 前沿；能否通过元学习让 λ_z, λ_μ 随 ‖μ‖ 实时调整？  
- **权重衰减协同**：当 weight-decay ≠0 时，μ-loss 的最优 λ 如何重新标度？  
- **Drop-in 替换**：将 μ-centering 嵌入到 FSDP/Deepspeed 的 all-reduce 阶段，实现零额外通信开销。

---

### 5. 下游影响与评测
- **表示质量**：μ-中心化后的嵌入在语义相似性、探针任务、Few-shot 推理上是否保持各向同性优势？  
- **生成多样性**：logit 界被压缩后，重复率、熵、长尾词召回是否下降？如何与 EntMax、α-entmax 结合？  
- **量化/蒸馏**：嵌入均值归零是否简化 INT8 量化系数搜索？对 logits-based 蒸馏 (MiniLLM) 有无稳定性增益？

---

### 6. 数据与任务维度
- **多语言**：不同语料（中文、阿拉伯语、代码）是否呈现不同的 μ-漂移速度？  
- **领域漂移**：持续预训练时，新领域数据会不会重新触发 μ-漂移？能否用 μ-centering 作为“领域切换”信号。  
- **长文本**：序列长度 >8 k 时，μ 是否会随位置索引累积？与 RoPE 的远程衰减如何相互作用？

---

### 7. 实用决策框架
- **自动阈值**：基于 Bratio 或 ‖μ‖ 的实时监测，给出“何时启用 μ-centering”“何时提升 λ”的硬规则。  
- **预算最优**：在固定 GPU hour 内，对比“放大 η + μ-centering”与“缩小 η + z-loss”的性价比曲线，给出规模化训练的操作手册。

---

这些探索可进一步巩固“输出嵌入几何”视角在 LLM 预训练稳定性研究中的核心地位，并衍生出新的优化器、正则化与系统设计范式。

## 总结

**Output Embedding Centering for Stable LLM Pretraining** 提出一种针对大语言模型预训练末期“输出 logit 发散”不稳定现象的新解法，核心观点与成果如下：

1. **问题根源**  
   输出 logit 发散并非单纯数值溢出，而是**输出嵌入整体偏离原点**（μ≠0）导致的各向异性；该偏移通过 $l_j=\boldsymbol{e}_j\cdot\boldsymbol{h}$ 直接放大 logit 动态范围，最终在大学习率下爆炸。

2. **理论工具**  
   - 证明 logit 均值 $l=\boldsymbol{μ}\cdot\boldsymbol{h}$，且 $|l_j|\le \max_i\|\boldsymbol{e}_i\|\cdot\|\boldsymbol{h}\|$。  
   - 引入比值 Bratio≤1 作为“中心化后 logit 界必然收紧”的充分必要条件。

3. **方法：Output Embedding Centering (OEC)**  
   - **μ-centering**（无超参数）：每步优化后执行 $\boldsymbol{e}_i\leftarrow \boldsymbol{e}_i-\boldsymbol{μ}$，使嵌入均值归零，logit 界被确定性压缩。  
   - **μ-loss**（正则化）：在损失中增加 $L_μ=λ\|\boldsymbol{μ}\|^2$，梯度持续把 μ 拉回原点；λ=1e-4 即稳定，且对 λ 不敏感。

4. **实验验证**  
   - 16 M–221 M 模型 × 7 个大学习率（3e-4–3e-1）× 4 种方法，共 140 次完整预训练。  
   - **训练稳定性**：baseline 与 z-loss 在大 η 下仍发散；OEC 两种变体全程无发散。  
   - **学习率敏感度**：μ-centering 与 μ-loss 的 LRS 比 z-loss 低 30 %–80 %。  
   - **超参数鲁棒性**：μ-loss 在 λ≥1e-4 即稳定；z-loss 需提高到 1e-1 才能媲美，且 λ 过大反而崩溃。  
   - **开销**：额外训练时间 <1 %。

5. **结论**  
   首次将“嵌入整体偏移”确立为输出 logit 发散的根因，并给出简单、低成本、易部署的中心化策略，全面优于现有 z-loss，为大学习率、大参数训练提供新的稳定基线。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
