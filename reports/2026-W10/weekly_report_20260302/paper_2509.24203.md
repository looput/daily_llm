# Group-Relative REINFORCE Is Secretly an Off-Policy Algorithm: Demystifying Some Myths About GRPO and Its Friends

**arXiv**: [2509.24203](https://arxiv.org/abs/2509.24203) · [PDF](https://arxiv.org/pdf/2509.24203)  
**领域**: RLHF  
**作者**: Yao, Chen, Sun, Chen, Zhang, Pan, Li, Ding  
**综合评分**: 8.36  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 7.5）

---

## 摘要

> 本文对强化学习中的经典REINFORCE算法及其现代变体（如GRPO）进行了深入的理论分析，揭示了其潜在的离策略性质，并提出了将REINFORCE适配到真正离策略场景的两条通用原则。研究通过理论推导统一并重新解释了近期的一些算法，并通过大量实证研究验证了其见解。论文代码已开源。

---

## 详细分析

> **社区热度**: ⭐ 8 (来自 papers.cool)

## 问题定义

论文核心解决的问题是：**在大型语言模型（LLM）强化学习（RL）中，如何在不依赖“严格 on-policy”假设的前提下，仍然使用类似 REINFORCE/GRPO 这类原本被视作 on-policy 的算法，实现稳定、高效的 off-policy 训练。**

具体而言，论文针对以下痛点：

1. 现实部署约束使得纯 on-policy 训练不可行  
   -  rollout 生成与模型更新速度不匹配；  
   -  数据来自历史策略或外部源，存在分布偏移；  
   -  奖励反馈延迟或环境昂贵，无法实时采样。

2. 现有“off-policy”算法往往需专门设计，与主流 RLHF 基础设施（围绕 PPO/GRPO 搭建）兼容性差。

3. 传统理解认为 REINFORCE 及其变体（如 GRPO）必须依赖“当前策略采样”才能保证无偏梯度，一旦数据偏离该分布就需要重要性采样（IS）或 clipping 做偏差-方差权衡，且理论保证随序列长度指数恶化。

为此，论文提出并论证了一个**反直觉的核心观点**：

> **Group-Relative REINFORCE（GRPO 的底层形式）在数学本质上就是 off-policy 算法**——它并不依赖“数据必须来自当前策略”这一假设即可导出。作者通过三步构造（KL 正则化代理目标 → 有限样本一致性损失 → 单步梯度下降）重新推导了该算法，发现其更新规则与 on-policy 推导完全一致，但全程**未对数据分布作任何限定**。这意味着：

- 重要性采样权重并非 GRPO 生效的必要条件；  
- clipping 的真正角色是“正则化”而非“修正 IS 方差”；  
- 可以把 GRPO 直接当成 off-policy 算法使用，只需在更新量和数据分布上施加两组通用原则：  
  1. 正则化更新步长（clipping、KL 惩罚、镜像下降等）；  
  2. 主动塑造数据分布（重加权、丢弃负样本、引入专家数据等）。

基于这一视角，论文进一步：

- 统一解释了近期提出的 OPMD、AsymRE 等算法，它们均可看作在 REINFORCE 损失上附加不同正则项；  
- 为“丢弃低奖励样本”“放大高奖励样本”等启发式策略提供了理论正当性；  
- 通过大规模实验验证了“去掉 IS、仅保留宽松 clipping”或“数据重加权”都能在严重 off-policy 场景下取得比标准 GRPO 更快更稳的提升。

总结：论文**拆除了“REINFORCE 系算法 = on-policy”这一长期认知壁垒**，为 LLM-RL 在真实 off-policy 环境中的**原理化、基础设施友好型算法设计**提供了新的理论支点与实证路径。

## 相关工作

以下工作与本论文的**off-policy LLM-RL**主题密切相关，按**研究脉络**与**具体贡献**两条主线归类，并指出与本文的**交集/差异**。

---

### 1. 经典 off-policy 理论向 LLM 的迁移
| 文献 | 核心思想 | 与本文关系 |
|---|---|---|
| Kakade & Langford 2002 | 保守策略迭代 + 重要性采样（IS）误差界 | 传统理论要求 πθ≈πb，本文**无需 IS**即可 off-policy |
| Schulman et al. 2015, 2017 (TRPO/PPO) | 用 IS+clipping 控制偏差/方差 | 本文指出 clipping 本质是**正则化**，IS 非必需 |
| Achiam et al. 2017 (CPO) | 带约束的策略优化 | 本文正则化视角可视为**无约束**的轻量替代 |
| Nachum et al. 2017 | KL 正则化策略更新 ⇒ 闭式解 | 本文**单步梯度**近似该闭式解，**不求解**解析最优 |

---

### 2. 近期 LLM-RL 的 off-policy 实践
| 文献 | 算法/系统 | 与本文交集 |
|---|---|---|
| **AsymRE** (Arnal et al. 2025) | 把 baseline 下移 τ，强调正样本 | 本文将其重写为**REINFORCE+KL 正则**，给出**新解释** |
| **OPMD** (Kimi-Team 2025b) | 用镜像下降一致性损失 | 本文证明其等价于**带平方惩罚项**的 REINFORCE |
| **CISPO** (Chen et al. 2025) | 对序列级 IS 比率做 clipping | 仍依赖 IS，本文**直接去掉 IS** |
| **GSPO/GMPO** (Zheng et al. 2025, Zhao et al. 2025) | 几何均值/序列级比率 | 侧重**比率归一化**，本文侧重**正则化+数据塑形** |
| **decoupled PPO** (Fu et al. 2025a) | 异步 rollout-train 流水线 | 同**pipeline off-policy**设定，本文提供**理论依据** |
| **DAPO** (Yu et al. 2025) | token-平均损失 + 大 batch | 实验上**兼容**本文方法，可叠加 RED 重加权 |
| **Contrastive PG** (Flet-Berliac et al. 2024) | 同一 batch 内 pairwise 损失 | 要求**同时采样正负对**，本文**无此限制**，更 infra-friendly |

---

### 3. 数据重用与分布塑形
| 文献 | 技术点 | 与本文关联 |
|---|---|---|
| Schaul et al. 2016 (Prioritized ER) | 经验回放+权重 | 本文 RED-Weight 可视为**优先级**的 REINFORCE 实现 |
| Hong et al. 2023a,b | 离线 RL 轨迹重加权 | 本文给出**LLM 场景**下的**简单闭式权重** |
| Yan et al. 2025; Zhang et al. 2025c | 引入专家数据 | 本文原则 2 允许**任意数据源**，无需额外 IS 修正 |
| Tajwar et al. 2024 | 利用次优 on-policy 数据 | 本文**理论兼容**负梯度样本，只需调整权重 |

---

### 4. 基础设施与系统框架
| 文献 | 系统 | 与本文关系 |
|---|---|---|
| **Trinity-RFT** (Pan et al. 2025) | 统一 rollout-train 调度 | 本文实验**全部基于**该框架，验证**sync-interval/offset**极端设定 |
| **OpenRLHF** (Hu et al. 2024) | 传统 PPO 栈 | 可直接**替换**为 REC/RED 系列，**零代码侵入** |
| **AReaL** (Fu et al. 2025a) | 异步 RL 系统 | 与本文**异步 off-policy**设定一致，可集成 RED 重加权 |

---

### 5. 理论延伸与局限
| 文献 | 贡献 | 与本文差异 |
|---|---|---|
| Rafailov et al. 2023 (DPO) | KL 正则⇒闭式最优策略 | 专注**偏好学习**，本文专注**奖励最大化** |
| Richemond et al. 2024 | 离线正则化 RL 理论 | 需**行为策略覆盖**，本文**无覆盖假设**仍可工作 |
| Ren & Sutherland 2025 | SFT-RL 动力学 | 本文提供**REINFORCE 视角**下的**数据权重**解释 |

---

### 一句话总结
> 本文在**不引入重要性采样**的前提下，为**REINFORCE 系算法**提供了**原生 off-policy 解释**，并**统一**了 AsymRE、OPMD、数据重加权等看似分散的最新研究，**填补了“理论 off-policy 保证”与“工程落地”之间的空白**。

## 解决方案

论文并未“提出一种全新算法”，而是**从第一性原理重新推导**了已经广泛使用的 **group-relative REINFORCE（GRPO 的底层形式）**，证明它**天生就是 off-policy 的**，并据此给出两条**通用增广原则**。具体解决路径分三步：

---

### 1. 重新建模：把“策略更新”写成**KL-正则化一致性损失**的单步梯度

| 步骤 | 公式/操作 | 关键洞察 |
|---|---|---|
| ① 构造代理目标 | $$ \max_\theta \mathbb{E}_{x\sim D}\!\left[\mathbb{E}_{y\sim \pi_\theta}r(x,y)-\tau D_{\text{KL}}(\pi_\theta\|\pi_{\theta_t})\right] $$ | 不假设采样分布，仅要求**下一步策略**与**当前策略**接近 |
| ② 推导一致性条件 | 对任意两样本 $(y_i,y_j)$ 要求<br>$$ r_i-\tau\!\left[\log\pi_\theta(y_i|x)-\log\pi_{\theta_t}(y_i|x)\right]=\text{const} $$ | 把**奖励**与**对数概率差**绑定，**与数据分布无关** |
| ③ 有限样本损失 | $$ \widehat{\mathcal{L}}(\theta)=\frac{1}{K^2}\sum_{i<j}(a_i-a_j)^2,\; a_i:=r_i-\tau\!\left[\log\pi_\theta(y_i|x)-\log\pi_{\theta_t}(y_i|x)\right] $$ | 仅依赖**成对差值**，**无重要性权重** |
| ④ 单步梯度 | $$ \nabla_\theta\widehat{\mathcal{L}}|_{\theta=\theta_t} \propto -\frac{1}{K}\sum_i (r_i-\bar{r})\nabla_\theta\log\pi_\theta(y_i|x) $$ | **恰好等于** group-relative REINFORCE 更新，**未对采样分布做任何限定** |

⇒ **数学结论**：GRPO 的梯度更新**天然兼容任意行为策略**，**无需 IS**。

---

### 2. 指出 vanilla 版本的**两大陷阱**

| 陷阱 | 现象 | 论文应对原则 |
|---|---|---|
| 梯度方向与最优方向**夹角可能为负** | 3-arm bandit 例子显示会收敛到次优动作 | **原则 1：正则化更新步长**（clipping、KL 惩罚、镜像下降） |
| 有限样本无法覆盖巨大动作空间 | 高方差+分布偏移→熵塌、训练崩 | **原则 2：主动塑形数据分布**（重加权、丢弃负样本、引入专家数据） |

---

### 3. 给出**即插即用**的增广实例

#### (1) 正则化更新步长——**去掉 IS，只保留宽松 clipping**
- **REC-OneSide-NoIS**：梯度掩码  
  $$ M_t^i=\mathbb{I}\!\left[A_i>0,\frac{\pi_\theta}{\pi_{\text{old}}}\le 1+\varepsilon_{\text{high}}\right]+\mathbb{I}\!\left[A_i<0,\frac{\pi_\theta}{\pi_{\text{old}}}\ge 1-\varepsilon_{\text{low}}\right] $$
- 实验验证：$\varepsilon_{\text{high}}$ 从 0.2 放大到 **2.0** 仍能稳定训练，**速度↑且不掉点**（图 3-4）。

#### (2) 数据塑形——**重加权而非简单丢弃**
- **RED-Drop**：平衡正负样本后重新计算 baseline，**无 IS 修正**。
- **RED-Weight**：  
  $$ w_i=\exp(A_i/\tau),\; g=\sum_i w_i(r_i-\bar{r}_w)\nabla_\theta\log\pi_\theta(y_i|x) $$  
  等价于**在 REINFORCE 损失上附加 KL 正则项**，**优先模仿高奖励响应**。

#### (3) 统一解释现有算法
| 原算法 | 本文重写形式 | 说明 |
|---|---|---|
| **OPMD** | $$ -\frac{1}{K}\sum_i(r_i-\bar{r})\log\pi_\theta(y_i|x)+\frac{\tau}{2K}\sum_i\!\left[\log\pi_\theta(y_i|x)-\log\pi_{\text{old}}(y_i|x)\right]^2 $$ | **平方惩罚即 KL 正则**的有限样本近似 |
| **AsymRE** | $$ -\frac{1}{K}\sum_i(r_i-\bar{r})\log\pi_\theta(y_i|x)-\frac{\tau}{K}\sum_i\log\pi_\theta(y_i|x) $$ | 第二项**反向 KL** 约束，**防止偏离旧策略** |

---

### 4. 系统级验证：在**极端 off-policy**设定仍有效
- **sync-interval=20**：rollout 用 **20 步前**的旧模型；
- **sync-offset=10**：训练时数据**滞后 10 个 batch**；
- **offline 应力测试**：仅允许用**初始策略**生成的数据。

实验覆盖 GSM8k、MATH、Guru-Math、ToolACE，模型 1.5 B→8 B，结果：

> **REC-OneSide-NoIS(ε=0.6,2.0)** 与 **RED-Weight** 在所有设定下**一致优于标准 GRPO**，且 KL 发散、熵、响应长度更稳定（图 5-11）。

---

### 解决总结
1. **理论上**：拆除“REINFORCE 必须 on-policy”枷锁，**无需 IS 即可 off-policy**。
2. **方法上**：给出两条**通用增广原则**→ 现有 clipping、KL 惩罚、数据重加权全是其**特例**。
3. **工程上**：所有改动**仅改损失/权重**，**零侵入**现有 RLHF 框架，**直接替换 GRPO/PPO 损失函数**即可落地。

## 实验验证

论文围绕“Group-Relative REINFORCE 其实是 off-policy 算法”这一核心观点，设计了**三类实验**来验证理论推导与两条增广原则的有效性。所有实验均基于 Trinity-RFT 框架，覆盖**数学推理**与**工具调用**两大任务，模型规模 1.5 B–8 B，参数同步方式覆盖从“轻度异步”到“纯离线”的极端 off-policy 场景。

---

### 1. 验证“无需重要性采样，仅靠 clipping 正则即可 off-policy”
#### 1.1 REC 系列消融
| 算法 | 是否保留 IS | clipping 范围 | 目的 |
|---|---|---|---|
| GRPO | ✔ | ε=0.2 | 基准 |
| REC-OneSide-IS | ✔ | ε=0.2 | 对照：IS 有无差异 |
| REC-OneSide-NoIS | ✘ | ε=0.2 / 0.6,2.0 | 核心：去掉 IS 后能否稳定 |
| REC-TwoSide-NoIS | ✘ | 对称 band | 检验 clipping 几何影响 |
| REC-Ring-NoIS | ✘ | 内 band+外安全区 | 进一步放宽正则强度 |

**数据集/模型**  
- GSM8k ↔ Qwen2.5-1.5B-Instruct  
- ToolACE ↔ Llama-3.2-3B-Instruct  

**off-policy 设定**  
- sync-interval=20（rollout 用 20 步前的旧模型）  
- sync-offset=10（训练数据滞后 10 个 batch）  
- offline（仅允许使用初始策略生成的固定缓冲区）  

**主要结果**  
- 图 3-4：REC-OneSide-NoIS(ε=0.6,2.0) 在三种设定下**收敛速度↑30–50 %**，最终精度**持平或更高**，KL 发散与熵更稳定。  
- 图 9：REC-Ring 在“快速上升+不崩溃”之间取得最佳折中，验证“**更宽松的 clipping 本质是更强的正则化**”而非“放大学习率”。

#### 1.2 学习率对照
- 固定 ε=0.2，将 lr 从 1e-6 提到 5e-6 → 性能**仍显著低于** REC-OneSide-NoIS(ε=0.6,2.0)  
- 图 10 确认：**放宽 clipping 不是“等效大 lr”**，而是**真正的正则化效应**。

---

### 2. 验证“数据分布塑形”原则
#### 2.1 RED 系列
| 算法 | 塑形方式 | 公式/操作 |
|---|---|---|
| RED-Drop | 样本丢弃 | 每 prompt 内正负样本 1:1 重采样，重新计算 baseline |
| RED-Weight | 优势加权 | wi=exp(Ai/τ), τ=1；梯度加权但不再 IS |

**数据集/模型**  
- GSM8k ↔ Qwen2.5-1.5B  
- Guru-Math ↔ Qwen2.5-7B  
- MATH ↔ Llama-3.1-8B  

**结果**  
- 图 5：RED-Drop & RED-Weight 在 on-policy / sync-interval=20 下**均优于 GRPO**，训练曲线更平滑。  
- 图 6：Guru-Math 7B 规模，RED-Weight **奖励↑20 %**，KL 距离与 GRPO 持平。  
- 图 7：MATH 8B 上 RED-Weight **奖励最高、KL 最小、熵与响应长度更稳定**，MATH500 测试集精度↑3.4 pp。

---

### 3. 统一视角：把现有算法“重写”成 REINFORCE+正则
| 原算法 | 本文重写形式 | 实验目的 |
|---|---|---|
| OPMD | REINFORCE + 平方惩罚 | 检验“镜像下降”是否只是**正则化 REINFORCE** |
| AsymRE | REINFORCE + 反向 KL 项 | 检验“baseline 下移”是否等价**模仿旧策略** |

**数据集/模型**  
- GSM8k ↔ Qwen2.5-1.5B  

**off-policy 设定**  
同 1.1 的三种极端模式。

**结果**  
- 图 11：OPMD 与 AsymRE 在 sync-interval=20 / offset=10 / offline 下**曲线几乎重合**于 REC-OneSide-NoIS(ε=0.6,2.0)，验证**它们都是“正则化 REINFORCE”的特例**，而非全新算法。

---

### 4. 辅助分析实验
- **Clipping 比例趋势**：图 4、9 显示，随着训练进行，REC-OneSide-NoIS(ε=0.6,2.0) 的 clipping 比例**始终低于 5 %**，说明**大范围 clipping 并不导致梯度爆炸**，反而**稳定约束**策略更新。  
- **熵与响应长度**：图 7 记录 RED-Weight 在 8B 模型上**熵下降更慢、长度更稳定**，解释其**更高泛化精度**。

---

### 实验覆盖矩阵
| 任务 | 模型 | 规模 | off-policy 模式 | 关键算法 | 图号 |
|---|---|---|---|---|---|
| GSM8k | Qwen2.5 | 1.5B | sync-int=20 / offset=10 / offline | REC 系列、RED、OPMD、AsymRE | 3,5,9-11 |
| ToolACE | Llama-3.2 | 3B | 同上 | REC 系列 | 4 |
| Guru-Math | Qwen2.5 | 7B | sync-int=20 | RED-Weight vs GRPO | 6 |
| MATH | Llama-3.1 | 8B | sync-int=20 | RED-Weight vs GRPO vs REC | 7 |

---

### 一句话总结
论文通过**“去掉 IS 的 clipping -only 实验”“数据重加权实验”“把现有算法重写成正则化 REINFORCE 的对比实验”**三大板块，**从 1.5 B 到 8 B、从轻度异步到纯离线**，系统验证了**理论推导的两条增广原则**在真实 LLM-RL 场景下的**普遍适用性与工程易用性**。

## 未来工作

以下方向可视为“把本文 off-policy 视角继续往前推”的**天然延伸**，按**理论深度→场景广度→系统规模**三层递进，并给出**可落地切入点**。

---

### 1. 理论深化：从“解释性”到“可证性”
| 问题 | 现状 | 下一步探索 |
|---|---|---|
| **1.1 收敛/改进保证** | 本文仅展示单步梯度等价，无全局收敛界 | 在**有限动作/线性策略**或**神经正切核(NTK)**设定下，证明正则化系数 τ、 clipping 范围 ε 与**策略改进下界**的定量关系；可借鉴**KL-正则ized MDP**的 Bellman 误差界 |
| **1.2 数据覆盖度量** | 目前凭经验丢弃或加权 | 引入**单轮/多轮**的**动作-状态占用度量** dπb，推导**覆盖系数 C⋆** 与**所需样本量** K 的显式公式，指导“何时停止采样” |
| **1.3 非平稳环境** | 实验仅行为策略滞后 | 把行为策略视为**非平稳过程** πb(t)，用**漂移预算** Δ=∑t‖πb(t)−πb(t+1)‖1 给出**最大允许 sync-interval** 的理论上界 |

---

### 2. 场景拓宽：从“响应级”到“细粒度”与“新数据源”
| 问题 | 现状 | 下一步探索 |
|---|---|---|
| **2.1 步级/ token 级奖励** | 本文仅整句奖励 | 将一致性条件**逐 token 展开**：<br>rti−τ[logπθ(yti|⋯)−logπθt(yti|⋯)]=const<br>推导**步级 RED-Weight**，用于**长 CoT 自我修正**场景 |
| **2.2 多模态动作空间** | 仅文本 token | 动作空间 = 文本 + 工具调用 + 图像生成；把**不同模态的 log-prob 统一为相同量纲的得分**，再套用 pairwise 一致性损失 |
| **2.3 专家数据混合** | 实验仅用旧策略数据 | 把**专家演示**视为**高权重样本**，在 RED-Weight 中固定 wi≫1，推导**专家-在线比例**与**收敛速度**的权衡曲线 |
| **2.4 合成数据/反向翻译** | 未涉及 | 用**奖励模型+规则**合成高奖励轨迹，作为**虚拟正样本**加入 RED-Drop；研究**合成-真实比例**对**分布外泛化**的影响 |

---

### 3. 系统与规模：从“算法替换”到“端到端协同设计”
| 问题 | 现状 | 下一步探索 |
|---|---|---|
| **3.1 异步流水线最优调度** | 手工设置 sync-interval/offset | 把**训练吞吐量**与**策略漂移误差**联合建模为**凸优化问题**，自动求解最优 (m,n) 与**动态调整**策略 |
| **3.2 大剪辑范围的量化/通信代价** | ε=2.0 时梯度范数大 | 研究**梯度裁剪+量化**与**宽松 clipping**的**复合误差界**，在**零冗余优化器(ZeRO)**风格系统里实现**通信-精度-收敛**三目标最优 |
| **3.3 多模型协同更新** | 单模型训练 | 把**actor、critic、reward、reference**四模型纳入**统一一致性损失**，推导**多模型 pairwise 条件**，实现**完全去 IS**的**多模型 off-policy**训练 |
| **3.4 在线/离线切换机制** | 实验要么纯在线要么纯离线 | 设计**切换阈值** ρ=‖πθ−πb‖TV，当 ρ>ρmax 时自动**冻结在线采样**、转为**离线回放**，并给出**切换不会导致性能下降**的理论保证 |

---

### 4. 评估与可复现性
| 问题 | 下一步 |
|---|---|
| **4.1 标准 off-policy 基准缺失** | 建立**sync-interval=1→100 的连续刻度** + **专家数据混入比例**作为**两大维度**，形成**LLM-Off-Policy-Bench**，方便未来算法公平比较 |
| **4.2 奖励黑客与分布外检测** | 在 RED-Weight 高权重样本上**加入不确定性估计**（如 EPO 或 R-L2E），监控**OOD 奖励黑客**，并自动下调权重 |

---

### 可快速落地的“小课题”清单
1. **步级 RED-Weight**：把 MATH 数据集的**逐步正确性标签**当 token 级奖励，跑 7B 模型，对比整句奖励版本。  
2. **专家数据比例扫描**：用 GSM8k 官方训练集当“专家”，按 {0 %,5 %,20 %,100 %} 比例混入旧策略数据，画出**样本效率-最终精度**曲线。  
3. **动态 sync-interval**：实现一个**简单 PID 控制器**：以最近 10 步验证集精度变化为误差信号，自动调整 sync-interval∈[1,50]，观察**吞吐量↑是否伴随精度不掉**。

---

### 一句话总结
> 把“**无需 IS 的 off-policy REINFORCE 视角**”继续推向**可证收敛、细粒度奖励、多模态动作、异步系统、自动调度**五大纵深，就能**同时占领理论高地与工程落地**的下一个制高点。

## 总结

**一句话概括**  
本文用第一性原理证明“Group-Relative REINFORCE（GRPO 核心）天生就是 off-policy 算法”，无需重要性采样；据此提出“正则化更新+主动塑形数据”两条通用原则，统一解释并改进了现有 clipping、OPMD、AsymRE、数据重加权等方法，在 1.5 B–8 B 模型、极端异步/离线设定下取得更快、更稳的提升。

---

**主要内容速览**

| 维度 | 核心要点 |
|---|---|
| **问题** | PPO/GRPO 被视为 on-policy，现实部署常遇数据滞后、分布偏移，需 off-policy 训练，但现有理论要求重要性采样（IS），序列长度一长大就爆炸。 |
| **发现** | 不假设任何采样分布，重新推导 KL-正则化一致性损失 → 单步梯度**恰好等于** GRPO 更新，**IS 从未出现**；因此 GRPO **原生兼容任意行为策略**。 |
| **两条原则** | ① 正则化更新步（clipping、KL、镜像下降均可）；② 主动塑形数据（重加权、丢弃负样本、混入专家）。 |
| **统一解释** | OPMD ≡ REINFORCE + 平方惩罚；AsymRE ≡ REINFORCE + 反向 KL；RED-Drop/Weight ≡ 数据加权版 REINFORCE。 |
| **实验** | 在 GSM8k、MATH、Guru-Math、ToolACE 上 1.5 B–8 B 模型，**去掉 IS、仅放大 clipping 范围**或**数据重加权**，均比原始 GRPO **训练速度↑30–50 %、最终精度持平或更高**，且 KL、熵、响应长度更稳定。 |
| **意义** | 拆除“REINFORCE 系必须 on-policy”认知壁垒，为 LLM-RL 提供**理论简洁、工程零侵入**的 off-policy 算法设计范式。 |



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
