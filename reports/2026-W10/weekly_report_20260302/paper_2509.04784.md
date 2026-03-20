# Post-training Large Language Models for Diverse High-Quality Responses

**arXiv**: [2509.04784](https://arxiv.org/abs/2509.04784) · [PDF](https://arxiv.org/pdf/2509.04784)  
**领域**: RLHF  
**作者**: Chen, Chakraborty, Wolf, Paschalidis, Pacchiano  
**综合评分**: 8.36  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为DQO（Diversity Quality Optimization）的新型后训练方法，用于联合优化大语言模型（LLM）的响应质量和语义多样性。该方法基于行列式点过程（DPPs），通过采样一组响应、嵌入其语义表示，并计算基于核的相似度矩阵的行列式来量化多样性。实验覆盖了指令遵循、摘要、故事生成和推理等多个任务，结果表明该方法能在不牺牲模型质量的前提下显著提升语义多样性。作者团队来自波士顿大学（Boston University）等机构，在机器学习和自然语言处理领域具有扎实的研究背景。

---

## 详细分析

> **社区热度**: ⭐ 6 (来自 papers.cool)

## 问题定义

论文旨在解决大语言模型（LLM）在后训练阶段（如监督微调 SFT 或 RLHF）普遍出现的**输出多样性坍缩**问题。具体而言：

- 现象：经过 SFT/RLHF 后，模型倾向于收敛到少数“标准答案”，生成结果高度同质化，缺乏语义层面的差异。
- 影响：
  1. 推理与个性化场景下，用户难以获得多种解题思路或风格。
  2. 测试时“best-of-n”性能下降，分布外奖励鲁棒性变差。
  3. 训练探索受限，难以发现新颖策略。

现有方法仅能在**推理阶段**通过调节温度、top-k 等采样参数做表面扰动，或停留在**词法级多样性**（token 熵、n-gram 不重复率），无法真正恢复被训练目标压制的**语义模式**。

为此，作者提出**DQO（Diversity-Quality Optimization）**，首次在**训练阶段**用 Determinantal Point Processes（DPPs）直接对**语义嵌入空间体积**进行可微优化，实现**质量与多样性联合提升**。

## 相关工作

- **Diversity 评估**  
  - Guo et al. 2024；Shaib et al. 2024：提出标准化文本多样性指标套件。  
  - Kirk et al. 2023；Shypula et al. 2025：量化 RLHF/SFT 后模型输出多样性下降。  

- **Inference-time 多样性**  
  - Ackley et al. 1985：温度采样。  
  - Holtzman et al. 2020：top-k / nucleus 采样。  
  - Nguyen et al. 2024：min-p 重分配概率。  
  - Franceschelli & Musolesi 2025：DiffSampling 依据概率差值解码。  
  - Ahmed et al. 2025：两阶段高温关键词→低温扩展。  

- **Training-time 多样性（词法级）**  
  - Yao et al. 2025：在正确样本上加 token-entropy 正则项。  
  - Li et al. 2025：SFT 阶段控制概率转移矩阵以保留 token 变异。  
  - Lanchantin et al. 2025：DPO 中从高分组选“最多样”样本做 preferred，多样性仅基于生成概率或唯一词数。  

- **Training-time 多样性（语义级，最相关）**  
  - Chung et al. 2025：DPO 损失加权平均 pairwise 余弦距离，仅适用于 DPO 且需 k≥3 样本。  

- **DPP 在 RL/NLP**  
  - Kulesza et al. 2012：DPP 机器学习综述。  
  - Parker-Holder et al. 2020：用 DPP 训练多样化策略种群以提升探索。

## 解决方案

**DQO** 将“多样性”显式写入训练目标，用 **Determinantal Point Processes (DPPs)** 在**语义嵌入空间**度量和最大化群体多样性，同时保留奖励信号保证质量。核心步骤如下：

1. **多样性度量**  
   对同一 prompt 采样 k 个回答 y₁…yₖ，经预训练编码器得到 ϕ(yᵢ)，构造 Gram 矩阵  
   $$
   \mathbf{L}_{ij}= \langle \phi(y_i),\,\phi(y_j)\rangle,\quad
   \text{Div}(y_{1:k})= \det\!\bigl(\mathbf{L}\bigr).
   $$  
   行列式即回答向量张成的平行六面体**体积平方**，体积越大→线性独立性越高→语义多样性越强。

2. **质量-多样性联合目标**  
   在 KL 正则化 RL 框架下，将单点奖励扩展为**群体目标**  
   $$
   J_{\text{Div}}(\pi_\theta)=\mathbb{E}_{x,y_{1:k}\sim\pi_\theta}
   \Bigl[\sum\nolimits_{i=1}^k r(x,y_i) + \alpha\log\det\!\bigl(\mathbf{L}_\phi(y_{1:k})\bigr)\Bigr].
   $$  
   α 控制多样性与奖励的权衡。最优策略满足  
   $$
   \pi_{\text{div}}(y_{1:k}|x)\propto \pi_{\text{ref}}(y_{1:k}|x)\exp\!\Bigl(\tfrac{1}{\beta}\sum_i r(x,y_i)+\tfrac{\alpha}{\beta}\log\det\mathbf{L}\Bigr),
   $$  
   等价于在新嵌入  
   $$
   \psi(x,y)=\sqrt{\tfrac{\exp(r/\beta)}{\pi_{\text{ref}}(y|x)}}\;\phi(y)
   $$  
   下按 **det(L_ψ)** 采样——**方向**由语义决定，**长度**由奖励决定，实现“高质量且分散”。

3. **稳定训练技巧**  
   - **正则化**：用 $\log\det(\mathbf{L}+\mathbf{I}_k)$ 替代 $\log\det\mathbf{L}$，避免行列式接近 0 造成梯度爆炸，同时保持有界 $[0,\log(1+k)]$。  
   - **Leave-One-Out 梯度估计**：  
     $$
     \nabla_{\!\text{loo}} J_{\text{Div}}=\mathbb{E}\!\sum_{i=1}^k \nabla\log\pi_\theta(y_i|x)\Bigl[r(x,y_i)+\lambda\log\tfrac{\det(\mathbf{L}+\mathbf{I})}{\det(\mathbf{L}_{-i}+\mathbf{I}_{k-1})}\Bigr],
     $$  
     利用特征值交错定理控制方差，保证每项在 $[0,\log(1+k)]$ 内。

4. **端到端训练**  
   与标准 PPO/GRPO 兼容：每次 rollout 采样 k 个回答，计算联合奖励后按 $\nabla_{\!\text{loo}}$ 更新策略，无需修改模型架构或额外判别器。

通过上述机制，DQO 在**训练阶段**直接扩大模型在语义空间中的“有效体积”，从而生成**高质量且互为补充**的多模式回答，突破传统采样或词法正则只能做表面扰动的局限。

## 实验验证

- **任务与数据集**  
  - 推理：GSM8K  
  - 摘要：CNN/DailyMail  
  - 故事生成：CommonGen  
  - 指令遵循：Dolly  

- **基线**  
  - 仅优化奖励的 GRPO（GSM8K）或 PPO（其余任务）  

- **主要结果**  
  1. **质量**：pass@n（n=1…10）  
     DQO 在 n=1 时与基线持平或略升，n>1 时显著优于基线。  
  2. **多样性**：6 项指标  
     - Distinct-1/2/4  
     - Self-BLEU / Self-ROUGE（报告 1−分数）  
     - GPT-4o-mini 人工判分  
     DQO 在所有指标上均一致高于基线，GPT-4o-mini 评分提升 0.2→0.5（GSM8K）或 0.4→0.8（CNN/DailyMail、Dolly）。  
  3. **Pareto 前沿**  
     固定采样温度或训练步数变化，DQO 始终位于“质量-多样性”平面的右上区域，显示全程占优。  

- **消融实验**  
  在 Dolly 与 GSM8K 上系统变动 α∈{0.5,1,1.5,2,5} 与 k∈{2,4,6,8}：  
  - α↑ → 多样性↑，但 α>2 时 pass@1 轻微下降；pass@10 基本稳定。  
  - k↑ → 多样性↑，计算线性增加；k>6 时 pass@1 略降。  
  整体看，DQO 在宽范围内同时提升质量与多样性，无需精细调参。

## 未来工作

- **奖励黑客与稀疏奖励**  
  - 仅使用结果奖励时，模型可在给出正确答案后追加无关内容“刷”多样性；需研究更密集的**过程奖励**或**语义一致性正则**（如 prompt-response 相似度阈值）来抑制黑客行为。  

- **自适应多样性度量**  
  - 目前依赖固定预训练嵌入，不同任务对“多样性”的侧重（事实、风格、逻辑路径）不一；可探索**任务专用或在线学习**的嵌入空间，或引入多视角核矩阵加权。  

- **计算效率**  
  - 每 prompt 采样 k 条完整回复，k 增大时 GPU 内存与梯度方差线性上升；可尝试**低秩近似**、**子集选择**、或**分块行列式估计**以降低复杂度。  

- **与偏好学习更深融合**  
  - 本文目标为奖励+多样性，尚未直接利用人类**多样性偏好**数据；未来可把 DPP 权重与 Bradley-Terry 类型损失结合，实现**多样性感知 DPO**。  

- **理论权衡刻画**  
  - α 与 β 的最优比例缺乏闭式指导；可研究**PAC-Bayes**或**信息论界限**，量化给定质量水平下的最大可达多样性，提供超参数选择理论依据。  

- **长文本与多轮场景**  
  - 实验集中在单轮短-中等长度输出；对**长文档生成**与**多轮对话**（需考虑上下文一致性）如何定义并优化多样性仍是开放问题。

## 总结

- **问题**：SFT/RLHF 后 LLM 输出坍缩到少数“标准答案”，语义多样性急剧下降，现有方法仅限推理阶段或词法扰动，无法恢复缺失模式。  

- **思路**：在训练阶段用 **Determinantal Point Processes** 把多样性显式写入目标——把 k 个回答的嵌入向量张成体积 $\det(\mathbf{L})$ 作为多样性得分，与奖励联合最大化。  

- **方法（DQO）**  
  - 目标：$\max_\theta \mathbb{E}\!\bigl[\sum r(x,y_i)+\alpha\log\det\mathbf{L}_\phi(y_{1:k})\bigr]$  
  - 正则化：$\log\det(\mathbf{L+I})$ 防止梯度爆炸  
  - 低方差梯度：Leave-One-Out 估计器 $\log\frac{\det(\mathbf{L+I})}{\det(\mathbf{L}_{-i}+\mathbf{I})}$  
  - 兼容 PPO/GRPO，无需改模型结构。  

- **实验**  
  - 四类任务（推理、摘要、故事、指令）均显示：  
    – pass@1 不降，pass@n>1 显著提升；  
    – 6 项多样性指标全面优于纯奖励基线，GPT-4 评判语义多样性提升 2× 以上；  
    – 超参数 α、k 在宽范围内有效，Pareto 前沿全程占优。  

- **局限与展望**  
  结果奖励易被黑客利用；固定嵌入可能非最优；k 大时计算线性增长。未来方向包括过程奖励、自适应嵌入、多样性感知 DPO 及理论权衡分析。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
