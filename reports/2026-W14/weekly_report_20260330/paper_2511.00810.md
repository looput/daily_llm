# GUI-AIMA: Aligning Intrinsic Multimodal Attention with a Context Anchor for GUI Grounding

**arXiv**: [2511.00810](https://arxiv.org/abs/2511.00810) · [PDF](https://arxiv.org/pdf/2511.00810)  
**领域**: Multimodal  
**作者**: Zhou, Lai, Tan, Kil, Zhu, Chen, Zhang  
**综合评分**: 7.88  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 7.5）

---

## 摘要

> 本文提出了一种名为GUI-AIMA的基于注意力机制的无坐标监督微调框架，用于图形用户界面（GUI）的视觉定位任务。该方法通过将多模态大语言模型（MLLM）固有的注意力与分块定位信号对齐，并引入可插拔的放大阶段，实现了高效的数据利用和卓越的性能。在多个基准数据集（ScreenSpot-Pro, ScreenSpot-v2, OSWorld-G, MMBench-GUI-LUI-Vision）上，其3B参数模型取得了当前最优结果。论文实验设计严谨，代码已开源，验证了方法的有效性。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

论文旨在解决**GUI grounding**（图形用户界面定位）任务中的两个核心难题：

1. **坐标直接生成困难**  
   现有多模态大模型（MLLM）普遍将 grounding 建模为“文本→坐标”的文本生成任务，但在高分辨率、复杂布局的屏幕上直接回归精确像素坐标既困难又计算昂贵。

2. **视觉-文本对齐效率低**  
   传统方法要么依赖 HTML/Accessibility Tree 等结构化表示（信息冗余、可移植性差），要么引入额外定位模块（如 GUI-Actor），导致训练阶段复杂、数据需求大。

为此，作者提出**GUI-AIMA**：  
- **坐标无关**——不直接预测坐标，而是利用 MLLM 固有的多头自注意力（MHSA）矩阵，把 grounding 转化为“选 patch”任务。  
- **注意力即监督**——通过可学习的 `` token 聚合查询-视觉注意力，再用“视觉汇聚查询 token”（visual-sink Qs）动态加权各注意力头，实现轻量级、数据高效的微调（仅 85k 张截图）。  
- **即插即用 zoom-in**——patch-wise 预测天然支持两步推理：先粗定位再裁剪放大，无需重新训练即可修正偏移误差。  

综上，GUI-AIMA 试图证明：**在不增加额外定位模块、仅利用 MLLM 内在注意力并配合简单监督信号的情况下，即可实现与大规模坐标生成方法相当甚至更好的 GUI 定位精度，同时显著降低训练数据与计算开销。**

## 相关工作

相关研究按“坐标式”与“无坐标”两条主线梳理如下：

### 坐标式 GUI Grounding
- **结构化辅助**  
  - UGround（Gou et al., 2024）– 额外输入 HTML。  
  - OmniParser / AriaUI（Wan et al., 2024; Yang et al., 2024）– 先视觉解析出元素列表或 caption，再让 MLLM 选坐标。  

- **端到端直接回归坐标**  
  - SeeClick（Cheng et al., 2024）、OS-Atlas（Wu et al., 2024）、AGUVIS（Xu et al., 2024b）– 仅用截图，让模型输出文本化坐标或 bbox。  
  - UI-TARS（Qin et al., 2025）、JEDI（Xie et al., 2025b）– 进一步扩大数据与模型规模，提升跨平台泛化。  

- **强化学习优化坐标**  
  - UI-R1（Lu et al., 2025）、InfiGUI-R1（Liu et al., 2025）、GUI-G1/G2（Zhou et al., 2025; Tang et al., 2025）– 用 RL 把“点中与否”作为奖励，微调定位策略。  

### 无坐标 / 注意力式 GUI Grounding
- **TAG**（Xu et al., 2024a）– 首次验证 MLLM 原始 attention 可零样本定位 GUI，但手工选 token/head，泛化受限。  
- **GUI-Actor**（Wu et al., 2025）– 引入额外嵌入层，用 `` token 与 patch 嵌入做相似度匹配；需两阶段训练。  
- **SE-GUI**（Yuan et al., 2025）– 仍输出坐标，但在训练阶段用自注意力过滤噪声样本。  

### 其他相关
- **视觉-语言定位通用方法**  
  - 基于 bbox 输出的 MDETR、GLIP 系列，以及 patch 选择的 Patch-TR 等，为“patch 选区”提供技术参考。  

- **注意力头功能分析**  
  - Voita et al., 2019；Clark et al., 2019；Elhelo & Geva, 2024 – 指出仅少数 head 真正承担“语义-视觉”对齐，为 GUI-AIMA 的 head 加权策略提供理论依据。

## 解决方案

论文通过“**注意力即监督**”的坐标无关框架 GUI-AIMA 将 GUI grounding 转化为**轻量级 patch 选择任务**，核心步骤如下：

1. **patch-wise 标签化**  
   将坐标框 $[x_1,y_1,x__2,y_2]$ 转成与视觉 patch 同维度的软标签  
   $$p_{v_i}= \mathrm{IoU}(v_i,\mathrm{gt}_\mathrm{bbox})\cdot\mathcal{N}\!\bigl(\mu_{v_i};\mu_\mathrm{gt},\Sigma_\mathrm{gt}\bigr)$$  
   既考虑重叠面积，又以高斯权重鼓励点击中心区域，解决“坐标↔patch”标注鸿沟。

2. **简化查询聚合——`` token**  
   在输入序列后追加可学习的 ``，令其在每层每头生成 patch-attention 向量 $\mathbf{A}_{l,h}^{a,V}\in\mathbb{R}^{|V|}$，天然地把所有查询 token 的注意力压缩到单一向量，避免逐 token 加权带来的训练不稳定。

3. **视觉汇聚查询 token（visual-sink Qs）选取**  
   不依赖全部查询 token，也不依赖尚未收敛的 ``，而是：  
   a) 用隐藏状态全局计算查询-视觉相似度  
   $$c_{q_i}= \textstyle\sum_{v_j}\mathrm{sim}(\mathbf{H}_{q_i},\mathbf{H}_{v_j})$$  
   b) 取 top-K 作为 Qs，表征“对视觉最敏感”的语义 token。

4. **注意力头自适应加权**  
   以 Qs 在每一头对视觉 patch 的累积注意力作为头权重  
   $$\tilde{w}_{l,h}= \textstyle\sum_{q\in\mathcal{Q}_s}\sum_{v\in V}A_{l,h}^{q,v},\quad w_{l,h}= \exp(\tilde{w}_{l,h})\big/\sum_{l',h'}\exp(\tilde{w}_{l',h'})$$  
   强化与“语义-视觉”模式一致的少数头，抑制无关头，实现**无额外模块**的 head 级微调。

5. **patch 预测与损失**  
   加权聚合 `` 向量  
   $$\hat{\mathbf{a}}= \frac{1}{L H}\sum_{l,h}w_{l,h}\mathbf{A}_{l,h}^{a,V}$$  
   用 KL 散度对齐软标签：$\mathcal{L}_\mathrm{Attn}= D_\mathrm{KL}(p\parallel\mathrm{normalize}(\hat{\mathbf{a}}))$。

6. **即插即用 zoom-in 推理**  
   先整图得粗 patch 分布→按中心裁剪→放大再跑一次，无需重新训练即可修正高分辨率下的像素偏移。

通过以上设计，GUI-AIMA 仅用 85 k 截图、单阶段微调、**不引入任何额外定位模块**，便把 MLLM 固有的多模态注意力对齐到 patch 级 grounding 信号，在 3 B 规模取得 SOTA 精度并支持推理时“自我修正”。

## 实验验证

论文围绕“定位精度、数据效率、模块必要性、推理策略”四个维度展开系统实验，全部在公开 GUI 基准上完成。主要结果如下（均按官方中心点是否在 GT 框内计算 Accuracy）。

| 实验维度 | 数据集 | 对比对象 | 关键结论 |
|---|---|---|---|
| **主实验** | ScreenSpot-Pro（高分辨率专业软件） | 3B 级：JEDI-3B、GUI-Actor-3B、SE-GUI-3B、UI-R1-E-3B 等<br>7B/72B 级：UI-TARS-7B、UGround-7B、UI-TARS-1.5-7B | GUI-AIMA-3B 平均 45.2%，**超过所有同量级模型**；+zoom-in 后 58.6%，**逼近甚至反超 7B SOTA** |
|  | ScreenSpot-v2（移动/桌面/网页） | 同上 | GUI-AIMA-3B 90.8%，与 JEDI-7B、UI-TARS-7B 打平，**高于 GUI-Actor-3B 0.4%** |
|  | OSWorld-G（开放任务） | 同上 | GUI-AIMA-3B 56.9%，**领先 GUI-Actor-3B 2.3%**；+zoom-in 达 62.2%，**仅次于 UI-TARS-1.5-7B** |
| **数据效率** | 45k 子集 → ScreenSpot-Pro | GUI-Actor、Vanilla-Attention | GUI-AIMA 43.4% vs GUI-Actor 36.0%，**样本少 40% 仍领先 7.4%**；收敛速度最快（≈1/3 步数） |
| **消融实验** | 同上 | ① 无 Qs（uniform/全部 Q/仅 ANCHOR）<br>② 不同 Qs 选取（layer-wise vs global，top-1 vs top-3）<br>③ 软匹配 Eq.(11) | global top-1 Qs 最优，**+1.9%**；软匹配再 **+1.9%**（高分辨率受益大）；加权 patch 标签 **+1.3%** |
| **zoom-in 策略分析** | ScreenSpot-Pro 错误样本 | 1-step vs 2-step（1.5×/2×/3×/4×） | 2× zoom-in 最佳：Relax@1 错误 **↓31%**，整体 Acc **↑3.4%**；回收 215 个偏移点，仅丢失 33 个原本正确点 |

此外，作者提供了：
- 注意力可视化（图 6-8）验证 `` 高亮区域与人眼焦点一致；
- 视觉汇聚查询 token 统计（图 5）证明用 hidden-state 选 Qs 比直接累加 attention 更聚焦语义 token。

综上，实验充分验证了“注意力即监督”方案在精度、数据量、训练速度、推理灵活性上均优于现有坐标式与嵌入-无坐标方法。

## 未来工作

后续可在以下方向继续深入：

1. **多区域同时定位**  
   当前 `` 仅支持单区域；扩展为 `…` 并设计区域间竞争/协作损失，可实现一次前向完成“批量点击”或“拖拽起止点”等复合操作。

2. **动态分辨率与任意长宽比**  
   探索在 ViT 内部插入 `Pos-Interpolation` 或 `NaViT` 式 2-D 绝对编码，使同一模型无需 zoom-in 即可处理 480×800 到 4K 任意比例屏幕，减少两阶段推理延迟。

3. **连续动作空间扩展**  
   将 patch 概率图转为连续坐标期望 $\hat{\mu}=\sum_i p_i \cdot \mathrm{center}_i$，再叠加低成本偏移头，实现“粗-细”双输出，兼顾坐标-free 的鲁棒性与坐标模型的像素精度。

4. **跨平台域适应**  
   引入视觉-文本风格混合增强（图标随机化、深色/浅色主题、字体替换）与 adversarial head，使 visual-sink Qs 的选取与 head 权重对域变化不敏感，提升零样本迁移到车载、游戏机等新平台的能力。

5. **自监督预挖掘注意力**  
   利用大规模无标注 GUI 截图，设计自监督任务：遮盖 30% patch 让模型重建被遮区域，同时要求 `` 关注遮罩边缘；预训练后再进入下游有监督阶段，有望进一步降低标注需求。

6. **强化学习微调**  
   以 GUI-AIMA 的 patch 概率图作为策略 $\pi(a|s)$，用任务完成度或 UI-Automation Reward 进行轻量级 RL 微调，实现“点中即得分”到“任务成功才得分”的跃迁，缓解中心点偏置问题。

7. **可解释性与安全**  
   系统分析哪些 head 负责文本按钮、哪些负责图标，结合输入扰动测试，检测并抑制“注意力劫持”风险；同时提供失败案例的注意力热图，帮助开发者快速定位 UI 设计缺陷。

8. **统一视频-GUI  grounding**  
   将帧间差异作为额外视觉 token，让 `` 在时序上“跟踪”同一元素，实现动态界面（动画、下拉刷新、滚动加载）下的稳定定位，为移动端自动测试提供支撑。

## 总结

**GUI-AIMA：把 GUI 定位做成“注意力选 patch”**

- **问题**  
  现有多模态大模型用“文本生成坐标”方式做 GUI grounding，高分辨率下误差大、训练数据多、需额外模块。

- **思路**  
  利用 MLLM 固有的多头自注意力，把任务转化为“选中最相关视觉 patch”，完全抛弃坐标输出。

- **方法要点**  
  1. 坐标-free 标签：把 GT 框转成重叠+高斯中心加权的 patch 软标签。  
  2. `` token：一个可学习 token 聚合全部查询 token 对 patch 的注意力，简化监督。  
  3. visual-sink Qs：用隐藏状态选出“对视觉最敏感”的查询 token，再以这些 token 在每一头的注意力总和为权重，突出语义头、抑制噪声头。  
  4. 两步推理：先整图粗定位→裁剪放大再跑一次，无需再训练即可修正像素偏移。

- **结果**  
  仅用 85k 截图、单阶段微调、无额外模块，3B 模型在 ScreenSpot-Pro 达 58.6%（+zoom-in），超过所有同量级方法并与 7B SOTA 持平；在 ScreenSpot-v2、OSWorld-G 亦取得 90.8%、62.2%，收敛速度最快。

- **意义**  
  证明“注意力即监督”即可激发 MLLM 的固有定位能力，为轻量级、数据高效、可扩展的 GUI agent 提供了新范式。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
