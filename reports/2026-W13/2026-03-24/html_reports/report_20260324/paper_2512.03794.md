# AdaptVision: Efficient Vision-Language Models via Adaptive Visual Acquisition

**arXiv**: [2512.03794](https://arxiv.org/abs/2512.03794) · [PDF](https://arxiv.org/pdf/2512.03794)  
**领域**: Multimodal  
**作者**: Lin, Liu, Yang, Tao, Ye  
**综合评分**: 8.28  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为AdaptVision的高效视觉语言模型范式，通过自适应视觉标记获取机制，在保持任务准确性的同时显著降低计算开销。该方法受人类主动视觉机制启发，采用从粗到细的策略，通过强化学习框架训练模型自主决定每个样本所需的最小视觉标记数量。论文在多个VQA基准测试上进行了全面实验，证明了方法的有效性。

---

## 详细分析

> **社区热度**: ⭐ 8 (来自 papers.cool)

## 问题定义

论文旨在解决**视觉-语言模型（VLM）在视觉问答任务中因视觉 token 数量庞大而导致的计算开销过高**的问题。具体而言：

- **核心痛点**：现有高效 VLM 方法采用**固定压缩比例**减少视觉 token，缺乏对样本差异的适应能力，无法根据任务复杂度动态调整所需视觉信息量。  
- **研究目标**：让 VLM **自主决定每个样本所需的最少视觉 token 数量**，在保持高准确率的同时显著降低计算成本。  
- **生物启发**：借鉴人类“主动视觉”机制——先粗看全局，再按需聚焦关键区域——提出**由粗到细的自适应视觉 token 获取范式**。  

总结：论文提出 AdaptVision 框架，通过强化学习训练 VLM 动态调用“裁剪工具”获取高分辨率局部信息，实现**精度与效率的双重优化**。

## 相关工作

论文在第 2 节“Related work”中将与自身相关的研究划分为两条主线，并指出其差异。可归纳如下：

1. 视觉推理型 VLM（Vision Language Model with Reasoning）  
   - 代表工作  
     - OpenAI o1、DeepSeek-R1：用 RL 提升 LLM 推理能力。  
     - DeepEyes、Mini-o3：支持 zoom/crop 等细粒度视觉操作，提升细节任务准确率。  
   - 与 AdaptVision 的区别  
     上述方法聚焦“更高准确率”，未将工具调用用于**减少视觉 token 消耗**；AdaptVision 首次把“thinking-with-images”范式用于**效率优化**。

2. 视觉 token 压缩型高效 VLM（Efficient VLM with Vision Token Compression）  
   - 静态压缩（固定比例）  
     - FastV：第二层后按注意力得分剪枝 50 % token。  
     - SparseVLM、VisionZip：按跨模态相关性保留固定比例 token。  
     - PyramidDrop：渐进式压缩。  
   - 动态但粗粒度  
     - VisionThink：用 RL 决策“整图”用低分或原分辨率，仅两种选择。  
   - 与 AdaptVision 的区别  
     现有方法均为**被动、固定或仅 coarse 级别**压缩；AdaptVision 提出**coarse-to-fine、样本级自适应**的最小 token 获取机制，并通过新 RL 算法 DTPO 解决双目标（精度+效率）训练难题。

## 解决方案

论文将“为每个样本动态决定最少视觉 token”建模为**带工具调用的两回合决策问题**，并通过**强化学习**端到端训练。具体方案分为三部分：

1. 自适应粗-细视觉获取框架  
   - 先以 1/4 分辨率图像 $I_{\text{low}}$ 获得 25 % token；  
   - VLM 自主决定：  
     – 直接输出答案（单回合）；  
     – 或调用 `[x_1,y_1,x_2,y_2]` 裁剪高分辨率局部区域 $I_{\text{crop}}$ 后再回答（两回合）。  
   - 总视觉 token 数：  
     $$n_{\text{img}} = n_{\text{low}} + \mathbb{1}_{\text{tool}}\,n_{\text{crop}}$$  
     目标是在保证答案正确的前提下最小化 $n_{\text{img}}$。

2. 双分量奖励函数  
   - 结果奖励 $R_{\text{oc}}$：  
     – 准确率奖励 $R_{\text{acc}}$（LLM-as-judge 0/1）；  
     – 格式奖励 $R_{\text{form}}$（标签合规 0.5）；  
     – 平衡奖励 $R_{\text{bal}}$（抑制过度或不足的工具调用）。  
   - 工具奖励 $R_{\text{tool}}$：  
     $$R_{\text{tool}} = R_{\text{crop}} - \alpha R_{\text{area}}$$  
     $R_{\text{crop}}$ 由 GPT-4o 判断裁剪区是否包含答题关键信息；$R_{\text{area}}$ 为相对面积惩罚，鼓励“最小有效框”。

3. 解耦回合策略优化（DTPO）  
   标准 GRPO 对序列级奖励统一归一化，导致：  
   - 信用分配模糊——工具决策 token 与答案 token 共享同一优势；  
   - 优化失衡——两回合序列的工具 token 被 $\frac{1}{N_i}$ 抑制。  

   DTPO 做出两项关键改进：  
   - 按回合拆分策略损失并**分别归一化**  
     $$J_{\text{DTPO}}(\theta)=\mathbb{E}_{x,o_i}\!\left[\underbrace{\frac{1}{\sum_i T_i}\sum_{i=1}^G \sum_{t=1}^{T_i} L_{i,t}(\theta)}_{\text{Tool Tokens}} + \underbrace{\frac{1}{\sum_i (N_i{-}T_i)}\sum_{i=1}^G \sum_{t=T_i+1}^{N_i} L_{i,t}(\theta)}_{\text{Answer Tokens}}\right]$$  
   - 为两类 token 计算**独立优势**  
     $$A_{i,t}=A^{(i)}_{\text{oc}}+\lambda A^{(i)}_{\text{tool}}\cdot\mathbb{1}_{1\le t\le T_i}$$  
     其中 $A^{(i)}_{\text{oc}}, A^{(i)}_{\text{tool}}$ 分别用 $R_{\text{oc}}$ 与 $R_{\text{tool}}$ 组内标准化得到。

通过上述设计，AdaptVision 在多个 VQA 基准上**平均仅使用 33 % 原始 token**即达到与 100 % token 模型相当甚至更优的精度，实现**精度-效率双优化**。

## 实验验证

论文在第 5 节“Experiment”与附录中系统评估了 AdaptVision 的**精度、效率、训练稳定性与可解释性**，可归纳为 4 组实验：

1. 主实验：9 基准综合评测  
   数据集  
   ChartQA / OCRBench / DocVQA / MME / MMVet / RealWorldQA / POPE / MathVista / MathVerse  
   对比方法  
   - 100 % token：Qwen2.5-VL-7B-Instruct（Vanilla）  
   - 25 % token：Down-Sample（固定 1/4 分辨率）  
   - 50 % token：FastV / SparseVLM / VisionZip（静态压缩）  
   - 动态：VisionThink / VisionThink†（官方权重）  
   - 消融：AdaptVision w/o DTPO（用原始 GRPO 训练）  
   指标  
   - 任务指标：各数据集官方 accuracy / F1 / 得分  
   - 效率指标：相对 token 消耗比 (#Token↓) 与实测推理时间  
   结果（表 1 & 图 4）  
   - AdaptVision 平均性能 97.9 %（相对 Vanilla），仅消耗 33 % token；  
   - 相比 Down-Sample，精度↑ 5.8 %，token 只↑ 7 %；  
   - 端到端推理速度 1.67× 于 Vanilla，显著快于 VisionThink†。

2. 奖励与训练动态消融  
   - 奖励消融（图 5a）  
     去掉 $R_{\text{bal}}$ → 100 % 工具调用；去掉 $R_{\text{tool}}$ → 0 % 工具调用；二者共同作用才获得稳定自适应策略。  
   - GRPO vs DTPO（图 5b & 表 1）  
     GRPO 训练出现“先拒绝工具→后滥用工具”的震荡，最终 token 利用率 57 % 且精度更低；DTPO 工具调用比例平稳收敛至 30 % 左右，精度与效率双赢。  
   - 按样本难度分析（图 6a）  
     DTPO 在“需高分图”样本上工具调用率↑，在“低分即够”样本上调用率↓，显示真正的样本级自适应；GRPO 则几乎全调用。

3. 工具调用可视化与案例研究  
   - 跨基准工具比例（图 6b）  
     细节密集型任务（ChartQA、MathVerse）调用率 40–50 %；通用理解任务（POPE）< 10 %。  
   - 定性案例（图 7 & 附录图 8–9）  
     低分辨率即可回答的场景，AdaptVision 与 Down-Sample 行为一致；  
     低分辨率导致 OCR 错误（“15”→“75”）时，AdaptVision 主动裁剪关键区域，仅用额外 4–66 个 token 即纠正答案，而 Vanilla 需 4× token 才获得同样正确结果。

4. 扩展对比实验（附录表 2）  
   将静态方法进一步调至 70 % token 比例，其精度虽提升，仍低于 AdaptVision，且 token 消耗翻倍以上，再次验证**自适应粗-细策略**优于**固定比例压缩**。

综上，实验从**精度-效率曲线、训练稳定性、奖励设计必要性到可解释案例**多维度证明：AdaptVision 在显著减少视觉 token 的同时，保持了 SOTA 级别的问答性能。

## 未来工作

论文第 6 节已指出两条未来方向，结合实验结果与框架特性，可进一步拓展为以下 6 个探索点：

1. 多工具与动态分辨率  
   当前仅支持单尺度裁剪且初始固定 1/4 分辨率。可扩展：  
   - 多尺度缩放工具（zoom-in / zoom-out / multi-crop）；  
   - 自适应选择初始分辨率，实现“任意比例”token 预算。

2. 更深层的多轮视觉推理  
   现限制最多 2 轮（工具调用 + 回答）。对需要“迭代定位-比较-计数”的复杂任务（如多步几何证明、物体追踪），可引入：  
   - 轮次终止决策器；  
   - 历史裁剪区域记忆机制，避免重复采集。

3. 连续空间工具参数化  
   目前 bounding box 为离散整数坐标，可改用：  
   - 可微的软注意力掩码；  
   - 梯度近似或强化学习连续控制，直接回归高斯均值/方差，实现子像素级精细采样。

4. 多模态工具与跨帧推理  
   将工具概念从“静态图像裁剪”扩展到：  
   - 视频时序裁剪（关键帧/管状区域）；  
   - 外部知识检索（图表→对应 Excel 单元格；OCR→字典查询）；  
   - 3D 点云或深度图局部获取。

5. 数据效率与自监督探索  
   当前依赖人工标注 VQA 对错。可研究：  
   - 自监督信号（预测区域与文本注意力一致性、CLIP 相似度）作为稠密奖励，减少 GPT-4o 标注成本；  
   - 课程强化学习，由易到难自动安排样本顺序，提高样本效率。

6. 推理阶段优化与部署  
   - 结合推测解码（speculative decoding）或 KV-cache 复用，降低多轮工具调用的生成延迟；  
   - 将策略蒸馏成小型“路由网络”，在边缘端实现零样本自适应 token 预算，进一步提速降耗。

这些方向可分别对应**工具空间、轮次深度、参数连续性、模态广度、数据效率与系统部署**六个维度，为构建更通用、更高效的“主动视觉”VLM 提供持续研究路径。

## 总结

- **问题**：视觉-语言模型（VLM）依赖大量视觉 token，导致高计算开销；现有压缩方法采用固定比例，无法按样本复杂度自适应调整。  

- **思路**：借鉴人类“主动视觉”机制，提出 coarse-to-fine 自适应 token 获取——先用低分辨率（25 % token），必要时调用裁剪工具获取高分辨率局部信息。  

- **方法**：  
  1. 框架 AdaptVision：把“是否裁剪”建模为两回合 RL 决策，最小化总 token 数 $n_{\text{img}}=n_{\text{low}}+\mathbb{1}_{\text{tool}}\,n_{\text{crop}}$。  
  2. 奖励：结果奖励 $R_{\text{oc}}$（准确率+格式+平衡）+ 工具奖励 $R_{\text{tool}}$（裁剪正确性 − 面积惩罚）。  
  3. 算法 DTPO：将策略损失与优势估计按“工具 token / 答案 token”解耦归一化，解决 GRPO 的信用分配模糊与优化失衡。  

- **实验**：在 9 个 VQA 基准上，AdaptVision 仅用 33 % 原始 token 即达到 97.9 % 相对性能，推理速度提升 1.67×，显著优于静态压缩与先前动态方法；消融与案例验证其自适应性与训练稳定性。  

- **贡献**：首次将“thinking-with-images”范式用于效率优化，提出可学习的最小 token 决策机制，为高效 VLM 提供新的生物启发范式与训练算法。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
