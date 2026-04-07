# StelLA: Subspace Learning in Low-rank Adaptation using Stiefel Manifold

**arXiv**: [2510.01938](https://arxiv.org/abs/2510.01938) · [PDF](https://arxiv.org/pdf/2510.01938)  
**领域**: SFT  
**作者**: Li, Sajadmanesh, Li, Lyu  
**综合评分**: 8.52  （novelty: 9.0 · method: 9.0 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文由索尼研究院（Sony Research）团队提出了一种基于Stiefel流形的低秩自适应方法StelLA，通过三因子分解和流形优化技术改进LoRA，在常识推理、数学与代码生成、图像分类与生成等多个下游任务上取得了优于现有LoRA变体的性能。

---

## 详细分析

> **社区热度**: ⭐ 7 (来自 papers.cool)

## 问题定义

该论文旨在弥合低秩适配（LoRA）与全参数微调之间的性能差距。核心问题是：  
LoRA 仅通过两个可训练矩阵 $A\in\mathbb{R}^{n\times r}$、$B\in\mathbb{R}^{m\times r}$ 对预训练权重 $W$ 做低秩修正 $W+BA^\top$，却未在训练过程中显式约束或优化其“输入-输出子空间”的几何结构，导致低秩流形中的有效信息未被充分挖掘。

为此，作者提出 **Stiefel Low-rank Adaptation（StelLA）**，将适配矩阵重新参数化为三因子形式  
$$ \tilde W = W + \frac{\alpha}{\sqrt r}\,U S V^\top, $$  
其中 $U\in\mathrm{St}(r,m)$、$V\in\mathrm{St}(r,n)$ 被强制驻留在 Stiefel 流形（列正交矩阵流形），从而在整个微调过程中保持子空间的正交性与几何结构；$S\in\mathbb{R}^{r\times r}$ 学习子空间间的缩放/耦合。通过配套的黎曼优化算法，StelLA 直接在 Stiefel 流形上更新 $U,V$，实现“子空间学习”而非仅学习低秩系数。实验表明，该方法在常识推理、数学与代码生成、图像分类及文本到图像生成等任务上均显著优于现有 LoRA 变体，缩小了与全参数微调的性能差距。

## 相关工作

与 StelLA 直接相关或构成对比的研究可按以下主题归类：

- **基于 SVD 的 LoRA 初始化**  
  - PiSSA、LaMDA：用预训练权重前 $r$ 个主奇异向量初始化适配器。  
  - MiLoRA：采用末尾 $r$ 个奇异向量。  
  - EVA：对激活值做 SVD 并动态调整秩。  
  - LoRA-GA：对梯度做 SVD 以对齐任务相关方向。  
  - OLoRA：用 QR 分解得到正交初始化。  
  上述方法仅影响**初始点**，训练阶段不再约束子空间几何；StelLA 则在**整个训练过程中**显式优化 Stiefel 子空间。

- **对 LoRA 施加正交/谱约束**  
  - OFT：用 Cayley 参数化学习正交旋转。  
  - Spectral Adapter：在谱子空间内微调，并对主奇异向量做正交旋转。  
  - GeoLoRA、AdaLoRA：三因子分解 $USV^\top$，通过正则化或梯度流保持 $U,V$ 正交，但侧重**秩自适应**而非流形优化。  
  StelLA 与之差异在于：① 采用**Stiefel 流形约束**；② 提供**通用黎曼优化框架**，可无缝嫁接任意欧氏优化器；③ 不局限于秩自适应，而是持续学习最优子空间。

- **三因子分解的 LoRA 变体**  
  - TriLoRA、MoSLoRA：三因子形式，但 $U,V$ 无正交约束。  
  - LoRA-XS：冻结 $U,V$ 仅训 $S$，追求极致参数效率。  
  StelLA 保持 $U,V$ 正交且**可训练**，兼顾表达能力与参数效率。

- **几何/黎曼优化用于微调**  
  - Zhang & Pilanci：用黎曼预条件加速 LoRA 优化，但未对子空间做流形约束。  
  - Riemannian SGD/Adam：通用流形优化器，需侵入优化器内部状态；StelLA 通过“投影-回缩”模块将**任意欧氏优化器**升级为黎曼版本，无需修改其动量或自适应机制。

- **方向-幅度解耦的 LoRA**  
  - DoRA、DeLoRA：将权重更新分解为幅度与方向。  
  StelLA 的三因子 $U,S,V$ 天然对应“方向-幅度-方向”，但方向部分被严格约束在 Stiefel 流形，提供几何可解释性。

- **参数高效微调其他路线**  
  前缀微调、提示微调、适配器层、ReLoRA、QLoRA、rsLoRA、LoRA+ 等，与 StelLA 的**子空间学习**视角正交，可潜在组合。

## 解决方案

论文把“LoRA 只能在欧氏空间自由更新 A、B，导致子空间几何结构未被利用”这一瓶颈，转化为**在 Stiefel 流形上持续学习最优输入-输出子空间**的黎曼优化问题。具体做法分为三步：

1. 三因子重参数化  
   将原 LoRA 的加法项 $BA^\top$ 改写成  
   $$ \Delta W = \frac{\alpha}{\sqrt r}\,U S V^\top, $$  
   其中  
   - $U\in\mathrm{St}(r,m)$ 是输出子空间的**正交基**  
   - $V\in\mathrm{St}(r,n)$ 是输入子空间的**正交基**  
   - $S\in\mathbb R^{r\times r}$ 负责子空间间的缩放/耦合  
   该形式与截断 SVD 同构，但所有因子**全程可训**。

2. 黎曼梯度+模块化优化流程  
   对 $U,V$ 的欧氏梯度 $\nabla_U,\nabla_V$ 做**显式投影**得到黎曼梯度  
   $$ \mathrm{grad}_U = \nabla_U - U\,\mathrm{sym}(U^\top\nabla_U), $$  
   再用**极回缩**  
   $$ \rho_U(\Delta) = \mathrm{uf}(U+\Delta) $$  
   把更新后的矩阵拉回 Stiefel 流形。  
   关键设计：把任意欧氏优化器（SGD/AdamW 等）的“一步更新”当作**切空间方向的扰动**，随后用投影-回缩两步纠正，**无需改动优化器内部动量或自适应状态**。算法 1 以 pre-/post-hook 形式实现，可一键接入 HuggingFace PEFT。

3. 梯度幅值均衡  
   由于 $U,V$ 列向量长度固定为 1，而 Adam 类优化器对坐标梯度做单位方差归一化，导致不同维度学习速度失衡。论文在投影前对梯度乘校正系数 $\sqrt{d/m}$、$\sqrt{d/n}$，使 $U,V$ 更新步调一致，加速收敛。

通过上述几何约束与优化策略，StelLA 在训练全程**显式搜索最优正交子空间**，而非仅在初始化时利用 SVD 启发式。实验表明，这一子空间学习机制在常识推理、数学/代码生成、图像分类及文本到图像生成任务上均显著优于现有 LoRA 变体，平均提升 1–2 个百分点或 FID 下降 7 点，逼近全参数微调性能。

## 实验验证

论文在 4 类任务、共 30 余个数据集上系统对比 StelLA 与 10 余种 LoRA 变体，并辅以消融实验与效率分析。具体实验矩阵如下：

| 任务领域 | 数据集/基准 | 主干模型 | 对标方法 | 核心指标 |
|---|---|---|---|---|
| 常识推理 | 8 个子任务（BoolQ、PIQA、SIQA、HellaSwg、WinoGrande、ARC-e/c、OBQA） | LLaMA2-7B、LLaMA3-8B | LoRA、DoRA、PiSSA、OLoRA、TriLoRA、MoSLoRA、ScaledAdamW | 平均准确率 ↑ |
| 数学&代码生成 | GSM8K、MATH、HumanEval、MBPP | LLaMA2-7B | LoRA、DoRA、PiSSA | Pass@1 准确率 ↑ |
| 图像分类 | CIFAR-10/100、Food101、Flowers102 等 8 个数据集 | ViT-Base、ViT-Large（ImageNet-21K 预训练） | LoRA、DoRA、PiSSA | Top-1 准确率 ↑ |
| 文本到图像生成 | Barbie、Cyberpunk、ElementFire、Expedition、Hornify（CivitAI） | Stable Diffusion v1.5 & v2.0 | LoRA、DoRA、PiSSA | FID ↓、CLIP Score ↑ |

此外，论文还完成以下深度分析：

1. 消融实验（LLaMA3-8B + Commonsense）  
   - 几何结构：对比欧氏三因子、商空间 St/(O(r)×O(r))、StelLA 的 St×St×ℝ^{r×r}。  
   - 初始化：零初始化、伪零初始化、SVD-major、SVD-minor vs. 论文默认非零随机正交。  
   - 梯度缩放 & 平行移动：验证梯度幅值均衡带来 +0.3 平均点提升；平行移动无额外收益。  
   - 回缩算子：极回缩 vs. 指数映射，性能几乎相同，但前者耗时更低。

2. 效率与规模分析  
   - 参数增量：StelLA 仅比 LoRA 每层多 r² 可训参数（r≪m,n），占比 <0.01 %。  
   - 训练耗时：单卡 H100 上 LLaMA3-8B 3-epoch 训练，StelLA 仅比 LoRA 慢 ≈15 %；批量化 SVD 使回缩步骤加速 15–20×，消除瓶颈。  
   - 尺度稳定性：推导前向/反向二阶矩，说明 α/√r 缩放可维持信号方差，与 rsLoRA 结论一致。

3. 定性结果  
   在 SD-1.5 上给出 5 组提示词的可视化，显示 StelLA 在风格一致性、细节保真度上优于 LoRA/DoRA/PiSSA。

综上，实验覆盖 NLP、CV、多模态生成三大领域，既有大规模语言模型（7B–8B），也有视觉 Transformer 和扩散模型，结果一致表明 StelLA 在**相同或更少可训参数**条件下，显著优于现有最强 LoRA 变体，且计算开销可控。

## 未来工作

- **更大规模验证**  
  将 StelLA 扩展至 70 B+ 模型（LLaMA-70B、Mistral-8×22B）或长上下文场景（>32 k），观察几何约束是否仍能保持收敛速度与下游增益。

- **与其他 PEFT 模块组合**  
  把 StelLA 的 Stiefel 子空间学习与 AdaLoRA 的**秩调度**、ReLoRA 的**权重合并**、QLoRA 的**量化**叠加，验证是否可取得“正交叠加”效果。

- **动态秩 / 块稀疏 Stiefel 流形**  
  在训练过程中根据奇异值阈值实时增减 Stiefel 列数，或仅对子集通道施加正交约束，进一步压缩参数与计算。

- **非标准几何结构**  
  探索 Grassmann 商流形、Flag 流形或乘积球面 $S^{m-1}\times\cdots\times S^{n-1}$ 作为子空间约束，比较收敛速度与泛化界。

- **理论分析**  
  给出 StelLA 的 Riemannian 梯度流收敛率、泛化误差界，并刻画 Stiefel 约束对低秩适应**表达能力**与**鲁棒性**的影响。

- **多模态与子空间对齐**  
  在 LLaVA、BLIP-2 等多模态模型中，为视觉-文本各自低秩分支强制联合 Stiefel 约束，研究能否提升跨模态对齐与幻觉抑制。

- **对抗与 OOD 鲁棒性**  
  利用 Stiefel 的正交特性，分析其是否天然抑制权重扰动敏感度，并与随机平滑、正交正则化等方法结合，系统评估对抗鲁棒性与分布外性能。

- **硬件级加速**  
  将极分解的 batched SVD 算子（gesvda）进一步移植到 TPU/NPU，或用近似 QR-迭代回缩，实现**毫秒级**流形投影，真正达到推理零开销。

- **下游任务特定先验**  
  引入**结构化 Stiefel**（如卷积核正交、频域正交）或任务相关的初始子空间（指令微调、医学影像），考察先验与数据驱动子空间的融合效果。

## 总结

**StelLA：基于 Stiefel 流形的低秩适配子空间学习**

1. 问题  
   低秩适配（LoRA）仅把更新拆成两个自由矩阵 $BA^\top$，训练期间不约束子空间几何，导致与全参数微调仍有性能差距。

2. 思路  
   将更新项改为三因子 $USV^\top$，并把输入-输出子空间矩阵 $U,V$ 强制驻留在 **Stiefel 流形**（列正交），从而在训练全程**显式学习最优正交子空间**；$S$ 负责幅度与耦合。

3. 方法  
   - 前向：$\tilde W = W + \frac{\alpha}{\sqrt r}USV^\top,\; U^\top U=V^\top V=I$  
   - 反向：欧氏梯度→黎曼梯度→投影回切空间→极回缩拉回流形；模块化设计，可**无缝嫁接任意欧氏优化器**（SGD/AdamW 等）。  
   - 梯度幅值均衡：按 $\sqrt{d/m},\sqrt{d/n}$ 缩放 $U,V$ 梯度，避免 Adam 归一化造成的更新失衡。

4. 实验  
   - 覆盖 NLP（8 项常识推理、GSM8K/MATH、HumanEval/MBPP）、CV（8 数据集 ViT 分类）、生成（Stable Diffusion 1.5/2.0 五风格）共 30+ 数据集。  
   - 对比 LoRA、DoRA、PiSSA、OLoRA、TriLoRA、MoSLoRA、ScaledAdamW 等；StelLA 在**相同或更少可训参数**下平均提升 **+1.3 准确率**、**−7.11 FID**，全面领先。

5. 消融  
   欧氏三因子 < 商空间 < StelLA 几何；随机正交初始化即可，SVD 初始化无额外收益；极回缩与指数映射精度一致但更快；梯度缩放带来额外 +0.3 点。

6. 结论  
   StelLA 用轻量级黎曼优化把“子空间几何”持续注入低秩微调，**即插即用**、**开销微小**（≈+15 % 训练时间、+r² 参数），在多种模型与任务上取得一致且显著的性能增益，为参数高效微调提供了新的几何视角与实用工具。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
