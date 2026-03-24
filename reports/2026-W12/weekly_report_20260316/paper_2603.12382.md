# SPARROW: Learning Spatial Precision and Temporal Referential Consistency in Pixel-Grounded Video MLLMs

**arXiv**: [2603.12382](https://arxiv.org/abs/2603.12382) · [PDF](https://arxiv.org/pdf/2603.12382)  
**领域**: Multimodal  
**作者**: Alansari, Suryanto, Velayudhan, Javed, Werghi, Naseer  
**综合评分**: 8.64  （novelty: 9.0 · method: 8.5 · evidence: 9.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为SPARROW的像素级视频多模态大语言模型，旨在解决视频理解中的空间精度和时间一致性跟踪问题。该方法通过引入目标特定跟踪特征（TSF）和双提示设计，显著提升了现有视频MLLM在多个基准测试上的性能。实验设计严谨，在六个基准上验证了方法的有效性，并提供了大规模数据集支持。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

该论文旨在解决**像素级视频多模态大语言模型（Pixel-Grounded Video MLLMs）**在时空推理中的关键局限性，具体包括以下核心问题：

### 1. 时间指代一致性缺失（Temporal Referential Inconsistency）
现有视频 MLLMs 通常依赖静态的 $[\text{SEG}]$ token 进行逐帧（frame-wise）grounding，该 token 仅提供语义信息而缺乏时间上下文。当目标物体在视频中移动、被遮挡或重新出现时，模型难以维持对象身份的跨帧一致性，导致：
- **身份切换（Identity Switches）**：模型在不同帧中将同一物体识别为不同实例
- **空间漂移（Spatial Drift）**：掩码定位随时间推移逐渐偏离真实目标
- **指代不连贯（Referential Incoherence）**：无法稳定跟踪特定 referent 的时空轨迹

### 2. 首帧初始化不稳定（Unstable First-Frame Initialization）
由于 $[\text{SEG}]$ token 仅提供语义线索而缺乏空间先验（spatial priors），模型在序列初始帧的掩码预测经常出现错位。这种早期误差会通过时间传播不断放大，形成误差累积效应，最终降低复杂视频问答和指代视频分割（RVOS）的性能。

### 3. 几何与语义线索的分离
现有方法未能有效融合：
- **几何定位信息**（如边界框提供的粗粒度空间约束）
- **语义分割信息**（如像素级掩码提供的细粒度区域特征）

这种分离导致模型在处理复杂场景（如多实例交互、小目标、遮挡）时，难以同时保证空间精度（spatial precision）和时间稳定性（temporal stability）。

### 解决方案概述
为应对上述挑战，论文提出 **SPARROW** 框架，通过以下两个互补组件实现统一：
- **Target-Specific Tracked Features (TSF)**：在训练阶段注入时间对齐的指代特征，强制模型学习目标身份的跨帧 persistence
- **Dual-Prompt Design**：联合解码 $[\text{BOX}]$ 和 $[\text{SEG}]$ token，将几何先验（box proposals）与语义 grounding 相结合，实现 coarse-to-fine 的层级化定位

该方法无需外部检测器或跟踪器即可端到端运行，在 Referring Video Object Segmentation (RVOS)、视觉定位（Visual Grounding）和 Grounded Conversation Generation (GCG) 等任务中显著提升了时空一致性和定位精度。

## 相关工作

该论文的相关研究主要涵盖以下四个层面，按从基础到具体的逻辑展开：

### 1. 语言模型与多模态基础
**大语言模型（LLMs）**：GPT-3、PaLM、LLaMA 等系列模型展示了强大的推理与指令遵循能力，为后续多模态扩展奠定基础。

**多模态大语言模型（MLLMs）**：
- **跨模态对齐**：Flamingo（通过交叉注意力融合视觉-语言）、BLIP-2 与 InstructBLIP（采用适配器连接冻结的视觉编码器与 LLM）
- **视觉编码器**：CLIP、Swin Transformer、InternVideo2 等提供高容量视觉表征

### 2. 图像级 Grounded 理解
**区域级与像素级推理**：
- **位置建模**：Kosmos-2、Shikra、VisionLLM 等通过坐标 token 或位置编码实现显式空间定位
- **密集分割**：LISA、GLaMM 等将语言推理与像素级分割结合，实现静态图像的细粒度 grounding，但缺乏跨时间维度的对象身份保持能力

### 3. 视频 MLLMs 与 Grounded 分割
**早期视频理解**：VideoChat、Video-LLaMA、Valley 等侧重整体场景理解或时刻检索，未涉及像素级定位。

**像素级 Grounded 视频 MLLMs**（论文直接对比的基线）：
- **VideoGLaMM**：通过 $[\text{SEG}]$ token 驱动 SAM2 解码器，但缺乏显式时间线索，易出现漂移
- **UniPixel**：基于 Qwen2.5-VL 构建，采用对象记忆库（object memory bank）实现在线掩码传播，但依赖首帧掩码初始化
- **GLUS**：将全局上下文与密集查询帧结合，使用可学习记忆库进行长期建模，但仍基于逐帧语义而非序列级指代线索
- **SA2VA / VideoMoLmo**：将 SAM2 与 LLM 结合用于密集视频理解

**关键局限**：现有方法普遍依赖**静态**的 $[\text{SEG}]$ token 进行逐帧 grounding，导致时间一致性与首帧鲁棒性不足。

### 4. 启发 SPARROW 设计的具体技术
**目标特定特征跟踪**：Artemis 研究表明，跟踪对象特定特征可改善视频中的时间连贯性，直接启发了论文的 **TSF（Target-Specific Tracked Features）** 机制。

**双提示/定位先验**：Groma 提出在图像 MLLM 中使用局部化视觉 token 增强细粒度 grounding，启发了 SPARROW 的 **Dual-Prompt**（$[\text{BOX}]$ + $[\text{SEG}]$）设计，将其扩展至视频领域以融合几何先验与语义线索。

**底层视觉模块**：
- **SAM2 / Hiera**：作为类无关的分割解码器与特征提取骨干
- **GroundingDINO**：用于开放集目标检测，为 TSF 提供伪标签
- **CLDTracker**：综合语言描述跟踪器，用于生成 TSF 所需的轨迹监督
- **Deformable-DETR**：作为轻量级区域提议头（proposal head）的架构基础

这些研究表明，尽管现有视频 MLLMs 在单帧 grounding 上取得进展，但**缺乏对时间指代一致性与空间先验融合的系统建模**，这正是 SPARROW 试图填补的空白。

## 解决方案

论文通过提出 **SPARROW**（Spatial Precision and Referential Reasoning in Object-centric Video grounding）框架解决上述问题，该框架通过两个互补模块统一空间精度与时间稳定性，并采用两阶段训练策略实现端到端优化。

### 1. 框架概览
SPARROW 基于双分支视觉编码器架构：
- **空间分支**：采用 CLIP 等图像编码器提取细粒度空间特征 $f_g = F_g(V)$
- **时间分支**：采用 InternVideo2 等视频编码器提取时序动态特征 $f_h = F_h(V)$

特征通过视觉-语言适配器（V→L）投影至 LLM 嵌入空间：
$$Z_g = W_g(f_g), \quad Z_h = W_h(f_h)$$

多模态输入表示为：
$$I = [Q; Z_g; Z_h; Z_{TSF}]$$
其中 $Z_{TSF}$ 为可选的目标特定跟踪特征（见 3.2 节）。LLM 通过 LoRA 微调，其输出的 $[\text{BOX}]$ 和 $[\text{SEG}]$ 隐藏状态经语言-视觉适配器（L→V）投影，分别用于条件化区域提议与分割解码。

---

### 2. 目标特定跟踪特征（Target-Specific Features, TSF）
**核心目标**：在训练阶段注入时间对齐的指代线索，使模型学习目标身份的跨帧 persistence，而推理时无需显式跟踪。

**实现机制**：
1. **跟踪与选择**  
   给定文本查询 $Q$，使用 GroundingDINO 在关键帧检测 referent，经 CLDTracker 传播至全序列，生成候选框集合 $\{B'_i\}_{i=1}^{K'}$。通过 K-means 聚类（$K=4$）在联合视觉-空间特征空间中筛选代表性样本 $\{B_j\}_{j=1}^K$，确保每个簇中心代表目标的不同外观形态。

2. **特征编码与集成**  
   对选中的跟踪区域编码并投影：
   $$F_{TSF} = \{F_g(B_j)\}_{j=1}^K, \quad Z_{TSF} = \{W_g(f) \mid f \in F_{TSF}\}$$
   $Z_{TSF}$ 作为额外 token 附加至 LLM 输入（式 3），提供时间一致的指代监督。

3. **数据集支持**  
   构建包含 30,646 个视频序列与 45,231 个问答对的大规模指代视频数据集，整合 HC-STVG、MeViS、LaSOT 等来源，预计算检测轨迹、K-means 筛选后的特征及 SAM2 生成的密集掩码，实现离线监督与训练时高效加载。

**推理模式**：TSF 在推理时默认省略（零开销），仅当需要最大时间稳定性时可选启用（见附录 D.2）。

---

### 3. 双提示 Grounding（Dual-Prompt Grounding）
**核心目标**：通过几何先验（$[\text{BOX}]$）与语义线索（$[\text{SEG}]$）的协同，稳定首帧初始化并抑制误差传播。

#### 3.1 边界框提示（$[\text{BOX}]$）
LLM 输出的 $[\text{BOX}]$ 嵌入 $e_{\text{BOX}} \in \mathbb{R}^d$ 条件化一个轻量级回归头，基于 SAM2 的冻结 Hiera 特征构建**类无关区域提议器**：
- 采用 Deformable-DETR 解码器替代传统检测头，移除分类分支，仅保留 objectness 评分
- 生成 $K=300$ 个提议框，经 NMS 筛选后保留高置信度锚框

**语言条件细化**：
对每个提议框 $b_i$，通过 ROIAlign 提取多尺度特征 $\{F_i^\ell\}_{\ell=1}^L$ 并池化为 $G_i$。通过交叉注意力融合 $e_{\text{BOX}}$：
$$A_i = \text{softmax}\left(\frac{(W_q e_{\text{BOX}})(W_k F_i)^\top}{\sqrt{d}}\right), \quad Z_i = A_i(W_v F_i)$$

最终评分与边界框细化：
$$s_i = \sigma(W_2 \text{ReLU}(W_1[Z_i^{\text{pool}}; G_i; e_{\text{BOX}}]))$$
$$\Delta b_i = f_{\text{ref}}([Z_i^{\text{pool}}; G_i; e_{\text{BOX}}]), \quad \hat{b}_i = b_i \oplus \Delta b_i$$

#### 3.2 分割提示（$[\text{SEG}]$）
LLM 输出的 $[\text{SEG}]$ 嵌入 $e_{\text{SEG}}$ 注入 SAM2 的提示编码器。对每个精炼后的框 $\hat{b}_i \in B^*$，构建空间-语义查询 $(\hat{b}_i, e_{\text{SEG}})$，由 SAM2 解码器生成最终掩码 $\hat{M}_i$。多实例查询（如“both players”）通过独立评分自然支持。

**粗到细协同**（图 3）：
$[\text{BOX}]$ 提供粗粒度空间约束筛选相关区域，$[\text{SEG}]$ 在此基础上进行细粒度语义分割，两者联合解码实现几何与语义的互补，显著降低早期帧漂移。

---

### 4. 训练策略

#### Stage 1：目标特定信息注入
- **优化目标**：仅更新 V→L 适配器 $(W_g, W_h)$、L→V SEG 适配器 $W_s$ 及 LLM 内的 LoRA 参数，冻结视觉骨干与 SAM2 解码器
- **损失函数**：
  $$\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{CE}} + \mathcal{L}_{\text{mask}}, \quad \mathcal{L}_{\text{mask}} = \mathcal{L}_{\text{BCE}} + \mathcal{L}_{\text{DICE}}$$
  其中 $\mathcal{L}_{\text{CE}}$ 强制视觉-语言 token 的语义对齐，$\mathcal{L}_{\text{mask}}$ 监督像素级 grounding

#### Stage 2：边界框提示学习
- **提议生成器预训练**：在 COCO、Objects365、OpenImages、V3Det 上预训练类无关的 Deformable-DETR 头，损失为：
  $$\mathcal{L}_{\text{prop}} = \mathcal{L}_{\text{obj}} + \lambda_1 \mathcal{L}_{\ell_1} + \lambda_2 \mathcal{L}_{\text{GIoU}}$$
- **任务适配**：冻结提议生成器与 Hiera 编码器，仅微调过滤头（Filtration Head）$F_{\text{filter}}$ 与 L→V BOX 适配器 $W_b$，使用带标注的 grounding 数据集进行框级监督：
  $$\mathcal{L}_{\text{filter}} = \lambda_{\text{cls}} \mathcal{L}_{\text{BCE}} + \lambda_{\text{box}} (\mathcal{L}_{\ell_1} + \mathcal{L}_{\text{GIoU}})$$

---

### 5. 推理流程
默认配置下，SPARROW 以**端到端**方式运行：
- 输入视频与文本查询直接送入双分支编码器与 LLM
- LLM 并行输出 $[\text{BOX}]$ 与 $[\text{SEG}]$ token
- 类无关提议器生成候选框，经语言条件过滤后，与 $[\text{SEG}]$ 共同引导 SAM2 解码器输出掩码

此过程**无需**外部检测器（GroundingDINO）、跟踪器（CLDTracker）或 K-means 聚类，保持与基线模型相似的推理开销（附录 F.2 显示 FPS 下降小于 3%）。可选地，在复杂场景（严重遮挡、快速运动）中可启用 TSF 注入以进一步提升稳定性，额外开销约 20 ms/目标/帧。

## 实验验证

论文在 **4. Experiments** 章节及附录中进行了全面的实验验证，涵盖**三大任务**、**六个基准数据集**、**三类骨干网络**（UniPixel、GLUS、VideoGLaMM），并辅以详细的消融研究与效率分析。

### 1. 主要任务与基准

| 任务 | 数据集 | 评估指标 | 测试重点 |
|------|--------|----------|----------|
| **Referring Video Object Segmentation (RVOS)** | MeViS (val/valu)<br>Ref-YouTube-VOS<br>Ref-DAVIS17 | $J$ (Region IoU), $F$ (Contour), $J\&F$ | 运动表达理解、跨帧身份保持、边界精度 |
| **Video Visual Grounding (VG)** | VidSTG | mIoU | 语言-像素对齐、空间定位精度 |
| **Video Grounded Conversation Generation (GCG)** | VideoGCG | mIoU, Recall<br>METEOR, CIDEr, CLAIR | 密集描述生成与掩码交错质量 |

---

### 2. 核心定量结果

#### (1) Referring Video Object Segmentation
**MeViS**（表 1）：测试对**已见/未见运动-表达组合**的泛化能力。
- VideoGLaMM + SPARROW 在 unseen 验证集（valu）上取得 **$J\&F$ +8.9** 的显著提升（48.5→57.4）。
- UniPixel 与 GLUS 在双验证集上均有 **+0.3 至 +3.4** 的稳定增益。

**Ref-YTVOS & Ref-DAVIS17**（表 2）：
- **Ref-YTVOS**：平衡提升区域重叠与边界质量，VideoGLaMM 的 $J\&F$ 提升 **+2.1**。
- **Ref-DAVIS17**：边界精度（$F$）提升尤为显著，VideoGLaMM 的 $F$ 分数提升 **+14.5**，$J\&F$ 提升 **+7.3**；UniPixel 与 GLUS 的 $F$ 分数均突破 **80**。

#### (2) Video Visual Grounding
**VidSTG**（图 5）：在疑问句（interrogative）设定下，SPARROW 为三个骨干网络均带来约 **+5 mIoU** 的绝对提升（相对增益 13–18%），表明双提示设计显著增强语言驱动的空间定位能力。

#### (3) Video Grounded Conversation Generation
**VideoGCG**（表 3）：
- **掩码质量**：mIoU 提升 **+2.0 至 +3.25**，Recall 小幅提升。
- **描述质量**：对于生成文本的模型（UniPixel、VideoGLaMM），CLAIR 指标提升显著（VideoGLaMM **+5.4**），表明生成的描述与视觉区域对齐更精准。

---

### 3. 消融研究（Ablation Study）

在 **Ref-DAVIS17** 上以 VideoGLaMM 为基线进行系统消融：

#### TSF 与 [BOX] 的有效性（表 4）
| TSF 使用方式 | [BOX] 关闭 | [BOX] 开启 | 结论 |
|--------------|------------|------------|------|
| 无 TSF | 69.5 (基线) | 72.5 (+3.0) | 单独使用 [BOX] 即可提升几何稳定性 |
| 仅训练使用（默认） | 72.4 (+2.9) | **76.8 (+7.3)** | **默认配置**，无需推理开销即可获得主要增益 |
| 训练+推理 | 75.3 (+5.8) | **77.7 (+8.2)** | 最大精度模式，需额外跟踪开销 |

#### 监督信号与提示组合（表 5）
- **监督 token**：使用 $[\text{BOX}]$ 特征监督过滤头（72.5）优于使用 $[\text{SEG}]$ 特征（70.6），证明**显式空间线索**对提议排序至关重要。
- **选择机制**：专用过滤头（72.5）优于 MLLM 直接选择（70.4）。
- **推理提示**：双提示 $[\text{BOX}]+[\text{SEG}]$（72.5）显著优于单提示（$[\text{SEG}]$ 仅 69.5，$[\text{BOX}]$ 仅 68.2），验证**几何-语义协同**的必要性。

#### 其他消融（附录）
- **TSF 注入路径**（附录 D.3）：验证通过**空间投影器**（$W_g$）注入 TSF 效果最佳（76.8），优于时序投影器或双路径混合。
- **对噪声监督的鲁棒性**（附录 D.4）：在训练轨迹中加入 ±20% 的框抖动或 20% 的 ID 切换，性能仅下降 1.6–2.5 点，仍显著优于无 TSF 基线，证明方法对伪标签噪声具有**鲁棒性**。
- **提议头变体**（附录 E）：对比直接回归、MLP、Anchor-free 卷积头与 DETR 系列，**Deformable-DETR** 在召回率（Recall@0.5: 93%）与精度（$J\&F$: 72.5）上取得最佳平衡。

---

### 4. 效率与开销分析（附录 F）

| 组件 | 成本 | 说明 |
|------|------|------|
| **离线 TSF 生成** | ~2.16 GPU 天（一次性） | 包含 GroundingDINO + CLDTracker 跟踪、K-means 聚类、SAM2 掩码生成 |
| **训练开销** | +1.34 GPU 天 | Stage 1 (TSF 注入) + Stage 2 (双提示学习) 总计 4.67 GPU 天，较基线微调增加 32 GPU 小时 |
| **推理开销** | 参数 +0.017B<br>FPS 下降 <3% | 默认配置无外部检测器/跟踪器，仅增加轻量级提议头；VideoGLaMM 的 FPS 维持在 2.40，UniPixel 从 15.38 降至 15.04 |

---

### 5. 定性可视化
- **RVOS**（图 4、附录 C.1）：在复杂场景（多兔/多马交互、密集鸟群、快速运动的斑马）中，SPARROW 成功维持**实例分离**与**身份一致性**，而基线出现漂移、合并或身份切换。
- **VG**（附录 C.2）：在遮挡、人群交互场景（持杯、穿粉衣儿童、最左侧人物）中，SPARROW 保持掩码稳定与精准定位。
- **GCG**（图 6、附录 C.3）：生成更详细、语义一致的描述（如区分“杯子、瓶子、拖把”），并与像素级掩码紧密对齐。

## 未来工作

基于论文 **Discussion and Limitation** 部分及方法论特性，以下方向具有进一步探索价值：

### 1. 高召回率提议生成机制
当前框架的性能受限于类无关提议器的召回能力：**小目标、严重遮挡对象或未见过类别的对象**一旦在提议阶段被遗漏，即无法通过后续的 $[\text{BOX}]$ 筛选或 $[\text{SEG}]$ 细化恢复。未来工作可探索：
- 多尺度特征融合与注意力机制，提升极小目标的检测灵敏度
- 针对遮挡场景的显式遮挡感知（occlusion-aware）提议生成
- 结合认知视觉（cognitive vision）的主动感知策略，动态调整提议密度

### 2. 长序列误差修正与自恢复机制
尽管双提示设计缓解了早期漂移，但在**长视频序列**中，初始帧的 $[\text{BOX}]$ 误差仍可能随时间累积。可探索：
- 显式的漂移检测模块，通过时序一致性检查触发重新初始化
- 基于不确定性量化的自适应关键帧选择，在置信度下降时自动注入新的空间先验
- 双向或滑动窗口式的时序推理，替代单向因果传播，以利用未来上下文修正当前估计

### 3. 超越伪跟踪的自监督时序学习
当前 TSF 依赖 GroundingDINO 与 CLDTracker 生成的**伪轨迹**， severe tracking noise 或 ID switches 仍可能引入监督偏差。未来可研究：
- 无外部跟踪器的自监督目标持久性学习，如通过对比学习构建跨帧的实例嵌入空间
- 端到端可微分的时序对应学习，将目标关联（data association）与分割联合优化
- 利用视频内在的几何一致性（如光流约束）作为免费监督信号，增强对伪标签噪声的鲁棒性

### 4. 多轮交互中的指代消解与会话历史建模
论文主要关注单轮查询的 grounding，但在**多轮对话**场景（如 "the man on the left... now the one next to him"）中，跨轮次的指代消解与视觉状态维护仍待探索：
- 将 SPARROW 的 TSF 机制扩展至对话历史编码，维护跨轮次的对象记忆库
- 研究指代链（referring chains）中的时序一致性，处理代词消解与对象重新识别

### 5. 跨模态空间先验融合
当前双提示仅融合了几何框与语义掩码，可进一步整合：
- **深度信息**：为遮挡处理与三维空间定位提供几何先验
- **音频线索**：在声源定位（audio-visual segmentation）任务中，将声纹特征作为额外的目标特定特征注入 TSF
- **运动线索**：显式光流或轨迹预测，增强对高速运动对象的跟踪稳定性

### 6. 边缘计算与实时性优化
尽管 SPARROW 在 A100 上的开销较小，但在**资源受限设备**上的部署仍需优化：
- 蒸馏小型化的提议头与分割解码器，保持精度的同时降低 GFLOPs
- 开发稀疏 TSF 更新策略，仅在检测到显著外观变化时触发特征重新编码，而非每帧处理

### 7. 开放集泛化与领域自适应
当前类无关设计在**极端开放词汇**或**领域漂移**（如医疗视频、无人机视角）下的泛化能力可进一步验证：
- 研究 TSF 在零样本（zero-shot）跨数据集迁移中的有效性
- 结合持续学习（continual learning），使模型能够增量学习新对象类别而不遗忘时序先验

这些方向均直接回应了论文指出的关键局限——**提议召回瓶颈**、**长程误差累积**与**伪监督依赖**——并构成像素级视频 grounding 向更鲁棒、更通用系统演进的自然路径。

## 总结

本文提出 **SPARROW**（Spatial Precision and Referential Reasoning in Object-centric Video grounding），一种面向像素级视频多模态大语言模型（Video MLLMs）的统一框架，旨在解决现有方法在时间指代一致性与空间定位精度上的关键局限。

### 1. 核心问题
现有视频 MLLMs 普遍依赖静态的 $[\text{SEG}]$ token 进行逐帧（frame-wise）grounding，导致：
- **时间漂移与身份切换**：缺乏跨帧对象身份建模，难以处理遮挡、重现及相似实例交互
- **首帧初始化脆弱**：纯语义线索无空间先验，早期误差随时间传播放大
- **几何-语义分离**：边界定位与像素分割缺乏协同机制

### 2. 方法概述
SPARROW 通过两个互补模块实现**端到端**的时空一致 grounding：

#### (i) 目标特定跟踪特征（Target-Specific Tracked Features, TSF）
- **机制**：在训练阶段利用 GroundingDINO 与 CLDTracker 生成伪轨迹，经 K-means 聚类（$K=4$）筛选代表性外观，投影为 $Z_{TSF}$ 注入 LLM
- **功能**：强制模型学习目标身份的跨帧 persistence，无需推理时外部跟踪器即可提升时间稳定性
- **数据支持**：构建包含 30,646 视频与 45,231 QA 对的指代视频数据集，涵盖 HC-STVG、MeViS、LaSOT 等来源

#### (ii) 双提示 Grounding（Dual-Prompt Grounding）
- **$[\text{BOX}]$ token**：基于 SAM2/Hiera 特征与 Deformable-DETR 构建类无关区域提议器，提供几何先验
- **$[\text{SEG}]$ token**：语言条件化 SAM2 解码器，实现像素级语义分割
- **协同策略**：粗到细（coarse-to-fine）流程，$[\text{BOX}]$ 筛选候选区域，$[\text{SEG}]$ 精炼掩码，抑制早期漂移

### 3. 训练策略
采用两阶段训练隔离不同学习目标：
- **Stage 1（TSF 注入）**：冻结视觉骨干，仅优化 V→L 适配器、LoRA 参数及 L→V SEG 适配器，损失为 $\mathcal{L}_{\text{total}} = \mathcal{L}_{\text{CE}} + \mathcal{L}_{\text{BCE}} + \mathcal{L}_{\text{DICE}}$
- **Stage 2（双提示学习）**：独立预训练类无关提议器（$\mathcal{L}_{\text{prop}} = \mathcal{L}_{\text{obj}} + \lambda_1\mathcal{L}_{\ell_1} + \lambda_2\mathcal{L}_{\text{GIoU}}$），随后仅微调过滤头与 L→V BOX 适配器，保持其余模块冻结防灾难性遗忘

### 4. 实验验证
在 **6 个基准**、**3 个骨干网络**（UniPixel、GLUS、VideoGLaMM）上验证：

| 任务 | 数据集 | 关键提升 |
|------|--------|----------|
| **RVOS** | MeViS (valu) | $J\&F$ **+8.9**（VideoGLaMM） |
| **RVOS** | Ref-DAVIS17 | $F$ **+14.5**，$J\&F$ **+7.3** |
| **Visual Grounding** | VidSTG | mIoU **+5**（约 13–18% 相对增益） |
| **GCG** | VideoGCG | CLAIR **+5.4**，掩码 mIoU **+3.25** |

**消融研究**证实：
- TSF 训练（推理省略）即可带来 **+2.9 $J\&F$**，结合 $[\text{BOX}]$ 达 **+7.3**
- 双提示 $[\text{BOX}]+[\text{SEG}]$（72.5）显著优于单提示（$[\text{SEG}]$ 仅 69.5）
- 对噪声监督（20% ID 切换）具有鲁棒性，性能下降可控

### 5. 效率特性
- **默认推理**：零外部检测/跟踪开销，参数量增加 **+0.017B**，FPS 下降 **<3%**
- **可选 TSF 推理**：在遮挡/快速运动场景可启用，额外开销约 **20 ms/目标/帧**

### 6. 局限与展望
当前性能受限于提议器召回率（小目标、严重遮挡易遗漏），且长序列仍存在误差累积风险。未来方向包括高召回提议机制、自监督时序学习、多轮对话中的指代链建模，以及跨模态（深度、音频）线索融合。

**项目资源**：代码、数据集与预训练模型已开源（https://github.com/RISys-Lab/SPARROW）。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
