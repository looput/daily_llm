# Future Policy Approximation for Offline Reinforcement Learning Improves Mathematical Reasoning

**arXiv**: [2509.19893](https://arxiv.org/abs/2509.19893) · [PDF](https://arxiv.org/pdf/2509.19893)  
**领域**: RLHF  
**作者**: Oh, Choi, Choi, Jo  
**综合评分**: 8.13  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为未来策略近似（FPA）的新方法，用于解决离线强化学习在数学推理任务中的梯度纠缠问题。该方法通过基于未来策略估计的梯度重加权机制，有效提升了长轨迹推理的训练稳定性与性能。在三个模型和七个数学基准测试上的实验表明，FPA显著优于DPO、RPO、KTO等强基线，并能以极低的计算成本达到与在线RLVR方法相当的精度。作者团队信息未在提供内容中明确显示，故省略背景介绍。

---

## 详细分析

> **社区热度**: ⭐ 5 (来自 papers.cool)

## 问题定义

论文针对数学推理场景下大语言模型（LLM）偏好学习（preference learning）的“梯度纠缠（gradient entanglement）”失效问题，提出并验证了一种通用、低开销的解决方案——**Future Policy Aware（FPA）偏好学习**。

- **核心痛点**  
  在数学推理任务中，偏好对（preferred/dispreferred trajectories）通常共享大量 token（公式、中间步骤、符号）。现有方法（DPO、RPO、SimPER 等）在抑制 dispreferred 序列概率时，会连带降低这些共享 token 的概率，造成“过度惩罚”，导致模型性能崩溃。

- **关键观察**  
  现有算法依赖“当前策略概率”作为正则化系数 $C(\pi_\theta)$，只有当概率已经降至很低时才触发保护，此时共享的有用 token 已被过度惩罚，属于**事后（reactive）**正则。

- **解决思路**  
  FPA 用**事前（proactive）**方式估计“未来策略”$\hat\pi_\theta$，把正则化系数改为 $C(\hat\pi_\theta)$。未来策略通过轻量级 logit 空间外推得到：  
  $$\hat\pi_\theta = \mathrm{softmax}\!\bigl((1+\lambda)\,h_\theta - \lambda\,h_{\mathrm{ref}}\bigr)$$  
  无需额外前向计算，即可提前感知策略即将跌落的位置，从而提前抑制危险梯度。

- **效果**  
  在 MATH 与 GSM8K 上，将 FPA 嵌入 DPO、RPO、SimPER 后均取得一致提升，**最大单点增益 5.75%**；延长训练时有效防止性能坍塌，且计算开销可忽略。

## 相关工作

论文在第 5 节“Related Works”中系统梳理了两条主线：① 提升数学推理能力的研究，② 偏好学习（Preference Learning）及其在推理任务中的改进。以下按这两条线索归纳相关研究，并补充 FPA 与它们的本质区别。

1. 提升数学推理的代表性研究  
- **RLVR（Reinforcement Learning from Verifiable Rewards）**  
  - Shao et al. 2024（DeepSeekMath）  
  - Guo et al. 2025（DeepSeek-R1）  
  直接利用“答案可验证”特性，把最终答案对错作为奖励信号做在线 RL，但在线采样开销大、训练不稳定。  

- **离线自举/自训练**  
  - Zelikman et al. 2022 STaR：迭代地让模型生成 CoT，只保留正确答案做 SFT。  
  - Gulcehre et al. 2023 ReST-EM：用验证器收集正确样本后离线微调。  
  仅对“正例”做最大似然，缺乏负例梯度信号，无法告诉模型“什么不该做”。  

2. 偏好学习及其在推理上的改进  
- **DPO 家族**  
  - Rafailov et al. 2023 DPO：将 RLHF 简化为单阶段 Bradley-Terry 偏好损失。  
  - Ethayarajh et al. 2024 KTO：无需成对偏好，仅按“好/坏”样本优化。  
  - Xiao et al. 2024 Cal-DPO：引入校准项，同时抬高 preferred、压低 dispreferred。  
  共同点：正则化系数均依赖**当前策略概率**，属事后保护。  

- **针对梯度纠缠的补丁**  
  - Pang et al. 2024 RPO：在 DPO 基础上给 preferred 样本再加 NLL 损失，强化正梯度。  
  - Pal et al. 2024 DPOP：显式约束 preferred 概率不得下降。  
  - Mao et al. 2025 BiPO：双向负反馈损失，试图缓解不对称崩溃。  
  这些方法仍用**当前**$\pi_\theta$计算正则，无法提前阻止共享 token 被过度惩罚。  

3. FPA 与上述研究的本质区别  
- **视角**：前人“看到危险再刹车”，FPA“预测危险提前刹车”。  
- **机制**：用轻量级 logit 外推估计**未来策略**$\hat\pi_\theta$，把正则化系数 $C(\cdot)$ 从“事后”$\pi_\theta$换成“事前”$\hat\pi_\theta$，实现** proactive regularization**。  
- **通用性**：不改动原损失结构，仅在外层替换系数，适用于任何已存在或未来的偏好学习算法。

## 解决方案

论文把“梯度纠缠”导致的过度惩罚问题转化为**正则化时机**问题，并提出**Future Policy Aware（FPA）**框架，用三步实现“提前刹车”：

1. 问题建模：把任何偏好损失统一写成  
   $$ \nabla_\theta \mathcal{L}= -\mathbb{E}_{(x,y_w,y_l)}\Big[C_w(\pi_\theta)\nabla_\theta\log\pi_\theta(y_w|x) -C_l(\pi_\theta)\nabla_\theta\log\pi_\theta(y_l|x)\Big]$$  
   其中系数 $C(\pi_\theta)$ 依赖**当前**策略概率，触发过晚。

2. 未来策略估计（零额外前向）  
   利用已算出的当前 logits $h_\theta$ 与参考 logits $h_{\mathrm{ref}}$，做线性外推  
   $$ \hat\pi_\theta = \mathrm{softmax}\!\bigl((1+\lambda)\,h_\theta -\lambda\,h_{\mathrm{ref}}\bigr)$$  
   只需一次向量运算，无需再跑模型。

3.  proactive 正则化  
   把原损失里的系数替换为**未来**策略概率，并用 stop-gradient 保证主梯度仍沿 $\pi_\theta$ 回传：  
   $$ \mathcal{L}_{\text{FPA}}= -\mathbb{E}\Big[\underbrace{\mathrm{sg}[C_w(\hat\pi_\theta)]}_{\text{提前系数}}\log\pi_\theta(y_w|x) -\mathrm{sg}[C_l(\hat\pi_\theta)]\log\pi_\theta(y_l|x)\Big]$$  
   - 当 $\hat\pi_\theta(y_l|x)$ 预测将骤降时，$C_l$ 提前减小，抑制 dispreferred 梯度爆炸。  
   - 共享 token 因系数提前“松手”而得以保留，避免性能坍塌。

实验上，FPA 作为**插件**直接嵌入 DPO、RPO、SimPER，在 MATH/GSM8K 上平均提升 1.2–2.6%，最长训练 40 k 步无退化，计算开销忽略不计。

## 实验验证

论文围绕“FPA 能否稳定且高效地缓解数学推理中的梯度纠缠”展开系统实验，覆盖**算法、模型、数据、训练动态、消融、超参、学习率对比**七个维度。主要结果如下：

1. 主实验：三模型 × 两基准  
   模型：Qwen2.5-Math-7B、DeepSeekMath-7B、Llama-3.2-3B-Instruct  
   数据：MATH500 + GSM8K 官方测试集  
   指标：Pass@1（8× 采样，温度 0.7）  
   结果：  
   - DPO+FPA 平均 +1.22 pp  
   - RPO+FPA 平均 +1.92 pp  
   - SimPER+FPA 平均 +2.58 pp，**单点最高 +5.75 pp（Qwen2.5-Math-7B MATH）**

2. 训练动态可视化  
   - Log-prob：FPA 使 preferred/dispreferred 轨迹的 log πθ 全程高于基线，且保持必要间隔。  
   - 梯度系数：Cl 提前下降，起“早期刹车”作用；Cw 几乎不变，实现**靶向抑制**。

3. 延长训练 & 性能坍塌  
   训练至 40 k 步：  
   - 基线 SimPER 25 k 步后准确率骤降 20 pp；  
   - FPA 全程无坍塌，Cl 系数更早、更频繁地逼近 0，主动屏蔽危险更新。

4. 学习率对照  
   手工把基线学习率最终衰减到 90 %（DPO/RPO）或 80 %（SimPER），结果**无提升**；证实 FPA 收益并非“慢速训练”副作用。

5. 外推强度 λ 消融  
   λ∈{0.5,1,2} 稳定领先；λ=10 过度正则，性能回落。  
   问题级细拆：λ=0.5 时**Total Loss 显著减小**，验证“减少遗忘”是净收益主因。

6. 靶向消融  
   仅对 Cl 应用 FPA 已可获得完整收益；仅对 Cw 应用反而崩溃，**确认关键在抑制 dispreferred 梯度**。

7. 计算开销  
   - 对已有参考模型的算法（DPO/RPO）**零额外前向**；  
   - 对无参考模型的 SimPER，提前缓存 logits，仅增加**内存占用 <2 %**，训练时间几乎不变。

综上，实验从**性能、稳定性、收敛行为、消融、效率**多角度证明：FPA 以可忽略成本，持续、显著地缓解数学推理偏好学习的梯度纠缠与模型退化。

## 未来工作

以下方向可在大规模、多任务或理论层面进一步挖掘 FPA 的潜力与边界：

- **外推策略升级**  
  - 将线性外推改为二次或贝叶斯预测，用滑动窗口估计“下一步”logit 分布，提升未来策略准确度。  
  - 引入可学习的 λ(x,y) 让外推强度随样本难度自适应，而非全局固定。

- **跨任务与多模态迁移**  
  - 在代码生成、逻辑推理、科学问答等“答案可验证”任务上验证 FPA 是否仍保持通用增益。  
  - 把 FPA 与图像-文本配对任务（MathVista、GeoQA）结合，考察梯度纠缠是否同样存在。

- **在线 RL 场景**  
  - 将 FPA 系数嵌入 PPO/GRPO 的 importance sampling 权重，研究能否减少 KL 散度突增与回滚（roll-back）频率。  
  - 与 RLVR 结合，用验证器奖励替代人工偏好，检验 proactive 正则能否降低 reward hacking。

- **理论分析**  
  - 在 deterministic preference 设定下给出 FPA 的收敛界，比较其系数提前衰减对 KL 散度与累积 regret 的影响。  
  - 研究 λ 与 Lipschitz 常数、梯度噪声的定量关系，为自动调参提供理论最优区间。

- **系数结构搜索**  
  - 对 Cw、Cl 采用神经网络超网络（hyper-network）预测，让正则化系数成为输入 x 的函数，实现更细粒度控制。  
  - 引入熵正则项，使未来策略在压低 dispreferred 概率的同时维持足够探索。

- **长链推理与稀疏奖励**  
  - 在 ≥1000 token 的长证明或几何题中，验证 FPA 是否仍能提前识别“局部错误”导致的梯度爆炸。  
  - 与过程奖励模型（PRM）配合，把 FPA 系数从整序列级细化到 step 级，实现更细粒度的 proactive 正则。

- **计算效率极限**  
  - 用 4-bit 量化参考模型缓存 logits，把内存开销压至 <1 %，验证是否能在百亿级模型上零成本部署。  
  - 探索 CPU-offload 策略，使外推计算与 GPU 前向并行，进一步隐藏延迟。

- **自动调参与监控**  
  - 实时监测训练集梯度夹角与 ∥Cl∥ 突增信号，用控制器自动调整 λ，实现“闭环” FPA。  
  - 开发早停替代指标：当未来策略熵骤降或 Cl 连续 100 步低于阈值时触发保存点，避免事后验证。

这些方向既能把 FPA 从“数学推理”推广到更广泛的验证性任务，也能在理论与系统层面深化对 proactive 正则的理解。

## 总结

- **问题**：数学推理任务中，preferred/dispreferred 轨迹共享大量 token，现有偏好学习（DPO/RPO/SimPER）用“当前策略概率”正则化，触发过晚，导致共享 token 被过度惩罚，模型性能崩溃。

- **方法**：提出 **Future Policy Aware（FPA）**——用零成本 logit 外推  
  $$\hat\pi_\theta=\mathrm{softmax}\!\bigl((1+\lambda)h_\theta-\lambda h_{\mathrm{ref}}\bigr)$$  
  提前估计“下一步”策略，将正则化系数 $C(\pi_\theta)$ 替换为 $C(\hat\pi_\theta)$，实现**事前**梯度抑制。

- **结果**：插件式嵌入 DPO、RPO、SimPER，在 MATH 与 GSM8K 上**一致提升**，最高 +5.75 pp；延长训练 40 k 步无退化，计算开销忽略不计。

- **结论**：FPA 以极低代价解决梯度纠缠，可作为通用 proactive 正则器嵌入任意偏好学习算法。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
