# GIFT: Reconciling Post-Training Objectives via Finite-Temperature Gibbs Initialization

**arXiv**: [2601.09233](https://arxiv.org/abs/2601.09233) · [PDF](https://arxiv.org/pdf/2601.09233)  
**领域**: SFT  
**作者**: Zhao, Ma, Jiang, Ma, Meng, Shen, Tang, Sun 等 10 人  
**综合评分**: 8.12  （novelty: 9.0 · method: 9.0 · evidence: 8.0 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为GIFT（Gibbs Initialization with Finite Temperature）的新方法，旨在解决大型推理模型（LRMs）后训练中监督微调（SFT）与强化学习（RL）之间的优化不匹配问题。该方法通过将SFT重新表述为有限温度下的吉布斯初始化，建立了一个分布桥，以促进整个后训练流程中的目标一致性。实验表明，GIFT在作为RL初始化时，显著优于标准SFT和其他基线方法，为保持探索性和对齐两个后训练阶段提供了数学原理清晰的路径。代码已开源。

---

## 详细分析

> **社区热度**: ⭐ 4 (来自 papers.cool)

## 问题定义

论文针对大推理模型（LRM）“监督微调（SFT）→ 强化学习（RL）”两阶段后训练范式中的**优化失配**问题：  
- SFT 阶段以零温度极限最小化交叉熵，导致策略分布坍缩到专家数据，抹杀基模型先验；  
- RL 阶段需要高熵分布以探索高奖励轨迹，却因探索空间被提前耗尽而陷入瓶颈。  

作者将 SFT 重新形式化为统一后训练全局目标的最优初始化子问题，提出 **GIFT（Gibbs Initialization with Finite Temperature）**，用有限温度 Gibbs 分布  

$$ \pi_{\text{sft}}^*(y|x)=\frac{1}{Z(x)}\pi_{\text{base}}(y|x)\,e^{\beta R(x,y)} $$  

替代标准 SFT 的狄拉克坍缩，从而在**数学上保证两阶段目标一致**，在**实践中保留探索能力**，最终解锁后训练的全局最优性。

## 相关工作

相关研究可归纳为三条主线，均围绕“如何缓解 SFT 带来的分布坍缩、提升后续 RL 探索”展开：

1. **改进 SFT 目标函数**  
   - 动态加权：DFT（Wu et al. 2025）按预测概率重标交叉熵；ASFT（Zhu et al. 2025a）引入 KL 锚定项防止漂移。  
   - 关键 token 选择：CFT（Ruan et al. 2025）用反事实扰动只回传“关键 token”损失；高熵 token 优先（Wang et al. 2025, Jiang et al. 2025）。  
   - 裁剪/截断：PSFT（Zhu et al. 2025b）借鉴 PPO-clip 抑制概率比过大的 token。

2. **统一或交错 SFT-RL 范式**  
   - 迭代式：ReLIFT（Ma et al. 2025）在线交错 SFT 与 RL；LUFFY（Yan et al. 2025）在 off-policy 数据上联合优化。  
   - 目标统一：UFT（Liu et al. 2025a）、Chen et al. 2025b 将两阶段目标写成单一损失，但实现复杂，尚未取代标准流水线。

3. **理论视角下的 KL-正则化 RL**  
   - 早期工作：Peters et al. 2010 推导出 KL-约束下的最优策略为 Gibbs 形式；Rafailov et al. 2024 将其用于 DPO，证明最优策略即 $\pi^*\propto\pi_{\text{base}}e^{\eta R}$。  
   - 近期扩展：Wu et al. 2025 从 RL 视角解释 SFT 需“奖励修正”；Kang et al. 2025 诊断 SFT 过拟合导致 RL 探索失效，提出用验证损失早停。

GIFT 与上述方法的区别在于：  
- 不改动 RL 算法本身，也不把两阶段硬合并；  
- 首次**从全局最优性出发**，将 SFT 显式推导为有限温度 Gibbs 初始化，使两阶段目标在信息论意义上严格一致，从而直接解决“零温度坍缩”这一根本失配。

## 解决方案

论文把“SFT→RL”两阶段后训练视为**单一全局优化问题**的近似求解，通过三步将标准 SFT 的“零温度坍缩”替换为**有限温度 Gibbs 初始化（GIFT）**，从而一次性解决优化失配：

1. 建立全局目标  
   定义后训练最优策略为  
   $$ \pi^*_{\text{global}}(y|x)=\frac{1}{Z_{\text{base}}(x)}\pi_{\text{base}}(y|x)\,e^{\eta R(x,y)} $$  
   该分布在信息论意义上同时最大化奖励并保留基模型先验。

2. 推导最优初始化  
   令 RL 阶段收敛点  
   $$ \pi^*_{\text{stage2}}(y|x)=\frac{1}{Z_{\text{sft}}(x)}\pi_{\text{sft}}(y|x)\,e^{\lambda R(x,y)} $$  
   与全局最优重合，解得 SFT 应输出的初始策略  
   $$ \pi^*_{\text{sft}}(y|x)=\frac{1}{Z(x)}\pi_{\text{base}}(y|x)\,e^{\beta R(x,y)},\quad \beta=\eta-\lambda $$  
   标准 SFT 对应 $\beta\to\infty$ 的狄拉克极限；GIFT 保持有限 $\beta$，避免坍缩。

3. 实现 token-级软标签训练  
   在稀疏奖励假设下，将序列级 Gibbs 目标分解为  
   $$ \pi^*_{\text{sft}}(y_t|y_{<t},x)\propto \pi_{\text{base}}(y_t|y_{<t},x)\,e^{\beta\cdot\mathbb{I}(y_t=y^*_t)} $$  
   实际算法只需对专家 token 的 logit 加上常数 $\beta$，再用常规交叉熵拟合该软分布（Algorithm 1）。  
   这样既放大高质量轨迹，又保留基模型对非专家 token 的概率质量，为后续 RL 留下可探索空间。

通过上述“理论推导→闭式解→软标签实现”，GIFT 把 SFT 从**孤立模仿任务**转变为**全局最优的有限温度初始化**，在数学与实现层面同时解决“分布坍缩-探索不足”的核心矛盾。

## 实验验证

实验围绕“GIFT 作为 RL 初始化能否带来一致且显著的性能提升”展开，分四大板块：

1. 主实验：数学推理基准  
   - 数据集：DeepMath-103k 抽样 10 k 做 SFT，10 k 做 RL，1 k 做验证。  
   - 评测：GSM8K、MATH500、OlympiadBench、AIME24/25 共 5 个数学 benchmark，报告 pass@1。  
   - 骨干：Qwen2.5-7B 与 Llama-3.1-8B。  
   - 对照：  
     – Direct 类：仅 SFT 或仅 RL；  
     – Unified 类：LUFFY、ReLIFT；  
     – SFT-then-RL 类：Standard SFT、SFT+Entropy、DFT、ASFT、PSFT。  
   - 结果：GIFT 在两组 backbone 上均取得最高平均准确率（52.43 % vs. 最佳 baseline 50.76 %；35.60 % vs. 30.21 %），AIME 提升近 10 %。

2. 泛化 & OOD 评测  
   - 基准：GPQA、MMLU-Pro、MMLU-Redux、ARC-Challenge。  
   - 结果：GIFT 在 Qwen-7B 上平均 64.10 %，超越 Standard SFT 的 59.78 %；在 Llama-8B 上 55.24 %，同样领先所有基线，验证有限温度初始化抑制过拟合、保留先验。

3. 探索潜力（pass@k 曲线）  
   - 在 RL 开始前，用 T=0.6 采样 k∈{1,2,4,8} 评估初始化策略。  
   - GIFT 的 pass@k 随 k 增大斜率更高；Qwen-7B 的 pass@8 比 Standard SFT 高 3.8 %，表明分布多样性更好，为 RL 提供更宽搜索空间。

4. 深入分析  
   - 逆温度 β 消融：β∈[0,25] 扫描，性能呈单峰曲线，验证“零温度（SFT）（β→∞）与无约束（β→0）均非最优”。  
   - 几何一致性：计算最后一层 cos-sim 与 L2 距离，GIFT 在 Base→SFT→RL 两阶段均保持更高 cos、更低 L2，参数更新更平滑。  
   - 分布一致性：KL 散度与 Top-K token 重叠显示，GIFT 显著减缓分布漂移，RL 阶段与基模型保持更高重叠，提升采样效率。  
   - 训练动态：RL 过程中 GIFT 初始化样本效率更高，Llama-8B 全程领先，Qwen-7B 在 25 % 步数即反超并持续拉开差距。

## 未来工作

以下方向可被视为 GIFT 框架的直接延伸或深层扩展，均围绕“如何让有限温度初始化更通用、更自适应、更理论严谨”展开：

- **自适应温度 β**  
  当前 β 为全局常数。可探索：  
  – 样本级：根据题号难度、奖励稀疏度或模型置信度动态调节 β(x,y)；  
  – 训练级：随 RL 阶段剩余 KL 预算或策略熵实时更新 β，实现“温度退火”。

- **端到端可学习温度**  
  将 β 视为可训练参数，通过元学习或梯度下降直接优化下游 RL 回报，避免网格搜索。

- **非稀疏奖励扩展**  
  GIFT 推导依赖“仅专家轨迹得高 reward”的稀疏假设。对于 dense reward 环境，需显式建模 Soft Q-function 或引入蒙特卡洛估计，使 token-级目标不再简化为 one-hot 加权。

- **多轮对话 / 长程推理**  
  数学任务 reward 仅依赖最终答案。对话、代码等多轮场景 reward 结构复杂，需研究如何分解 advantage 至每轮 token，同时保持 Gibbs 形式。

- **理论收敛速度与样本复杂度**  
  给出 β 与 RL 阶段所需采样次数、KL 正则系数 η 之间的定量关系，证明“最优 β* 最小化总样本复杂度”。

- **与其它 RL 算法耦合**  
  目前仅在 GRPO 上验证。可测试 GIFT 初始化对 PPO、RLOO、DPO 等算法的增益，并分析不同基线散度惩罚项对 β 有效区间的敏感性。

- **跨模态迁移**  
  将 GIFT 从文本数学扩展到代码生成、多模态科学推理，验证有限温度是否同样缓解代码语法坍缩或视觉-语言对齐崩溃。

- **与 Unified 范式正交结合**  
  把 GIFT 的软标签损失插入在线交错训练（ReLIFT）或动态加权框架（LUFFY），考察能否在“统一目标”场景下继续提升探索效率。

- **计算开销优化**  
  每次前向需同时运行 base 模型。探索：  
  – 低秩近似 base  logits；  
  – 缓存 base 分布并周期性更新；  
  – 蒸馏得到“轻量 base”供 GIFT 使用。

- **理论风险与收敛界**  
  研究有限 β 对策略优化误差的影响，给出 |π*_global – π*_stage2| 的界，明确 β 过大或过小导致的次优差距。

这些方向既可直接提升 GIFT 的实用性与鲁棒性，也能深化对“监督-探索”权衡的定量理解。

## 总结

论文核心可概括为“一个矛盾、一个框架、一个算法、一套实验”：

| 要点 | 内容 |
|---|---|
| **问题** | 标准 SFT 用零温度交叉熵强行拟合专家轨迹，导致分布坍缩，后续 RL 探索空间被提前耗尽，形成“优化失配”瓶颈。 |
| **框架** | 把 SFT→RL 视为统一全局优化：寻找既最大化奖励又贴近基模型的策略，其信息论最优解为 Gibbs 分布<br>$$\pi^*_{\text{global}}\propto \pi_{\text{base}}\,e^{\eta R}.$$ |
| **算法** | 推导出 SFT 应输出的最优初始化同样是有限温度 Gibbs<br>$$\pi^*_{\text{sft}}\propto \pi_{\text{base}}\,e^{\beta R},\ \beta=\eta-\lambda;$$<br>实现上只对专家 token 的 logit 加常数 β，用软标签交叉熵训练——即 GIFT。 |
| **实验** | 在 Qwen-7B 与 Llama-8B 上，GIFT 相对最强基线平均提升 1.7 %–5.4 %，AIME 提升约 10 %；OOD 任务同样领先，且 pass@k 曲线更陡，验证保留探索能力。几何与分布一致性指标全面优于标准 SFT。 |

综上，GIFT 用“有限温度”替代“零温度”，在理论与实践中同时解决 SFT 坍缩-RL 探索不足的矛盾，为两阶段后训练提供了一条通向全局最优的初始化路径。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
