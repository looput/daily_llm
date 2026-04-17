# CORA: Conformal Risk-Controlled Agents for Safeguarded Mobile GUI Automation

**arXiv**: [2604.09155](https://arxiv.org/abs/2604.09155) · [PDF](https://arxiv.org/pdf/2604.09155)  
**领域**: Agent  
**作者**: Feng, Du, Wang, Ma, Niu, Matsuo, Feng, Yu  
**综合评分**: 7.85  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为CORA（COnformal Risk-controlled GUI Agent）的GUI自动化安全保障框架，通过整合保形风险控制理论，为基于视觉语言模型的GUI智能体提供了具有统计保证的安全防护机制。该方法创新性地将安全问题转化为选择性动作执行问题，并引入了Guardian模型、Diagnostician模型和Goal-Lock机制，在移动GUI自动化场景中实现了用户可调的风险控制。论文还发布了Phone-Harm基准数据集用于严格评估。实验表明，CORA在安全-有用性-中断权衡的帕累托前沿上取得了改进，为自主GUI执行提供了一个实用且具有统计基础的安全范式。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

该论文试图解决**移动GUI代理（Graphical User Interface Agents）在自主执行操作时的安全保障问题**，具体而言是如何在保持任务完成能力（helpfulness）的同时，对可能造成严重、不可逆后果（如财务损失、隐私泄露）的 harmful actions 进行统计上可验证的控制。

核心问题可分解为以下几个方面：

### 1. 高风险自主执行的安全隐患
视觉语言模型（VLM）驱动的GUI代理正从被动辅助转向自主操作，可直接执行转账、权限授予、发送消息等低级别GUI动作。这种开放的动作空间使用户面临：
- **财务风险**：意外转账或未经授权的购买
- **隐私风险**：意外披露私人消息、照片或联系人
- **社交风险**：错误发送信息或授权
- **不可逆操作**：一旦执行难以撤销的高风险动作

### 2. 现有防护机制的固有缺陷
现有安全策略主要分为三类，但均存在根本性不足：
- **提示工程（Prompt Engineering）**：易被视觉注入攻击（visual prompt injection）绕过
- **启发式规则（Heuristic Rules）**：在开放、动态的移动GUI环境中过于脆弱，难以泛化
- **VLM-as-Critic**：计算成本高、校准性差（uncalibrated），且易受目标劫持（goal hijacking）影响

**根本缺陷**：上述方法均缺乏**形式化验证**和**用户可调节的统计保证**（user-tunable statistical guarantees），无法在部署时提供可证明的 harmful action 率上限。

### 3. 移动环境的特有挑战
相比桌面环境，移动场景带来额外的安全挑战：
- **界面限制**：小屏幕和密集触摸目标增加 grounding 错误和误触风险
- **身份耦合**：权限、支付和社交渠道与用户身份和资产紧密关联
- **实时注入面**：系统通知、权限弹窗、覆盖层（overlays）提供持续的视觉提示注入攻击面
- **分布漂移**：高度个性化和快速变化的界面导致跨用户、跨应用、跨版本的分布漂移（distribution shift）

### 4. 安全-自主性-干预的权衡困境
现有方法迫使开发者在以下极端间选择：
- **过度干预（Over-intervention）**：频繁暂停或拒绝执行，导致任务失败率高、用户体验差
- **静默有害执行（Silent harmful execution）**：为保持流畅性而承担未量化的安全风险

该论文提出将GUI安全重新表述为**选择性动作执行（selective action execution）**问题，旨在通过统计校准（Conformal Risk Control）提供一个**可部署的安全旋钮（deployable knob）**，使用户能够明确设定可接受的风险预算（如 $\alpha = 0.01$ 保证自动执行的动作中不超过1%是有害的），同时通过生成式诊断模型（Diagnostician）最小化不必要的用户干预。

## 相关工作

论文第2节（Related Work）从三个维度梳理了相关研究，具体如下：

### 2.1 基于视觉语言模型的GUI代理（Vision Language Models-Based GUI Agents）

该领域经历了从元数据依赖到视觉感知的范式转变：

- **早期工作**：Mind2Web 等通过文本转换接口（如 DOM/HTML 或 accessibility tree）简化感知，但在动态渲染和浏览器外环境中受限。
- **视觉定位进展**：近期系统转向基于截图的交互，减少对结构化 API 的依赖，代表性工作包括：
  - AppAgent、Ferret-UI、OmniParser、ShowUI（提升空间定位能力）
- **移动设备专门化研究**：
  - AutoGLM（实现规划与定位解耦）
  - GUIOdyssey（跨应用长程导航）
  - GUI-Xplore（通过探索与推理增强泛化能力）
  - Mobile-Agent-v3（可扩展的原生代理训练）
  - UI-TARS-2（基于多轮强化学习的策略扩展）
- **模块化推理**：Feng et al. (2026)、Xiao et al. (2025) 等探索了更具可解释性的自适应多模态工作流。

**关键局限**：上述工作主要优化任务完成能力（capability），而非**校准的执行时安全**（calibrated execution-time safety）。

### 2.2 智能体系统中的安全与对齐（Safety and Alignment in Agentic Systems）

随着代理获得自主 GUI 控制能力，评估重点从静态无害性转向轨迹级操作风险：

- **安全基准测试**：
  - OS-Harm、MobileSafetyBench、OS-Sentinel/MobileRisk、AndroidWorld（暴露误用、提示注入和不安全行为等失效模式）
  - AgentHarm、R-Judge（证明有害任务合规性和安全风险意识对强 LLM 代理和监控器仍具挑战性）
- **视觉提示注入**：Cao et al. (2025) 等研究表明，恶意屏幕内容可劫持代理行为，导致未授权操作或信息泄露。
- **形式化验证**：VeriSafe Agent 等逻辑验证系统展示了对用户意图与动作一致性进行形式化检查的前景。

**研究缺口**：现有防御策略仍在脆弱规则、昂贵判断器（VLM-as-critic）或高摩擦确认机制之间权衡，缺乏**用户可调节、统计上可靠的开放环境执行时控制**。

### 2.3 共形风险控制与选择性执行（Conformal Risk Control and Selective Execution）

CORA 的方法论根基源于以下理论进展：

- **选择性预测（Selective Prediction）**：Geifman and El-Yaniv (2017, 2019) 提出通过弃权（abstention）显式暴露风险-覆盖率（risk-coverage）权衡。
- **共形预测框架**：Barber et al. (2023) 提供有限样本不确定性控制；Angelopoulos et al. (2024) 将其扩展为**共形风险控制（CRC）**，可在用户指定预算 $\alpha$ 下约束期望风险：
  $$E[L(Z_{n+1}; \hat{\tau}(\alpha))] \leq \alpha$$
- **生成与决策场景适配**：
  - 共形语言建模（Quach et al., 2024）
  - LLM 输出的共形有效性方法（Cherian et al., 2024）
  - 共形决策理论（Lekeufack et al., 2024）：用于安全自主决策
  - 共形风险训练（Yeh et al., 2025）：端到端优化共形风险目标
- **序列依赖性**：Xu and Xie (2023) 强调时间依赖和分布漂移带来的额外挑战。

**本文定位**：相较于上述工作，CORA 在开放移动 GUI 环境中实例化 CRC，其控制目标不仅是预测有效性，而是**用户指定自主性预算下执行低级别动作的有害动作率**（harmful-action rate of executed low-level actions）。

## 解决方案

论文提出 **CORA（COnformal Risk-controlled GUI Agent）** 框架，通过将移动 GUI 安全重新表述为**选择性动作执行（selective action execution）**问题，结合统计学习理论与深度多模态模型，实现了具有形式化保证的安全控制。具体解决方案包含以下核心组件与机制：

### 1. 问题重构：选择性动作执行范式
将传统"执行所有动作"的范式转换为"预测 with 弃权（prediction with abstention）"：
- 对于基础策略 $\pi$ 提出的候选动作 $\hat{a}_t$，系统决定**执行（EXECUTE）**或**弃权（ABSTAIN）**
- 暴露显式的**风险-自主性权衡（risk-autonomy trade-off）**：执行更多动作提升任务成功率但增加风险，反之亦然
- 采用**风险-覆盖率（risk-coverage）**与**安全性-有用性-干预（safety-helpfulness-interruption）**Pareto 前沿作为评估协议，替代单一标量指标

### 2. 动作条件 Guardian 模型（System 1）
作为轻量级门控函数，对每个候选动作进行概率风险估计：
$$s_t = R_\psi(g, o_t, h_t, \hat{a}_t) \in [0,1]$$

- **输入**：冻结的用户目标 $g$、当前观察 $o_t$（截图+UI树）、历史 $h_t$、**候选动作 $\hat{a}_t$**（动作条件性至关重要，可区分同一界面下"取消"与"确认转账"的不同风险）
- **架构**：基于 VLM 主干网络提取上下文隐藏状态 $H_t$，取最后有效 token 表示 $z_t$，经可训练风险头投影为标量 logit，通过 sigmoid 得到风险分数
- **训练目标**：加权二元交叉熵损失处理类别不平衡（有害动作稀疏）：
$$\min_\psi \mathbb{E}\left[ \omega \cdot \ell_t \log(s_t) + (1-\ell_t)\log(1-s_t) \right]$$
其中 $\omega > 1$ 为固定权重，优先召回有害事件

### 3. 共形风险控制（Conformal Risk Control, CRC）模块
将原始风险分数转换为具有统计保证的执行/弃权决策边界：
- **执行风险定义**：$L(Z_t; \tau) = \ell_t \cdot \mathbb{I}\{s_t \leq \tau\}$，其中 $\ell_t \in \{0,1\}$ 为真实伤害指示器
- **用户风险预算**：满足用户指定的风险水平 $\alpha \in (0,1)$：
$$\mathbb{E}[L(Z_{n+1}; \hat{\tau})] \leq \alpha$$
- **校准规则**：基于保留的校准集 $\{(Z_i, \ell_i)\}_{i=1}^n$，计算最大可行阈值：
$$\hat{\tau} = \sup\left\{\tau \in [0,1] : \frac{1}{n+1}\left(\sum_{i=1}^n L(Z_i; \tau) + 1\right) \leq \alpha \right\}$$
该选择保证在可交换性假设下，测试数据上自主执行的有害动作期望率不超过 $\alpha$，提供**用户可调的"部署旋钮"**（如 $\alpha=0.01$ 确保平均不超过 1% 的自主动作为有害）

### 4. 生成式语义 Diagnostician 模型（System 2）
对 Guardian 拒绝的高风险动作（$s_t > \hat{\tau}$）进行深度多模态推理，推荐最小干预：
- **输入**：完整上下文 $(g, o_t, h_t, \hat{a}_t, s_t)$，显式包含风险分数以校准推理严重性
- **输出结构化报告** $Y_t = (r_t, k_t, I_t)$：
  - $r_t$：自由文本推理链（如"用户要求聊天，但代理正点击'转账'按钮"）
  - $k_t$：危害类型分类（MISUSE / INJECTION / MISBEHAVIOR）
  - $I_t \in \{\text{ABORT}, \text{CONFIRM}, \text{REFLECT}\}$：最优干预决策（恶意注入需 CONFIRM，良性幻觉需 REFLECT）
- **训练**：因果语言建模（CLM）损失，仅对生成的诊断 token 计算损失，对齐统计严格性与语义可解释性

### 5. Goal-Lock 机制：抵御间接注入
防止不可信屏幕内容覆盖用户意图：
- **契约执行**：初始用户目标 $g$ 被冻结为规范目标，UI 树或截图中的文本严格视为**不可信证据**而非指令
- **条件独立性**：Guardian 明确以 $g$ 为条件评估风险，防止"指令漂移"（instruction drift）
- **冲突解决**：任何 $g$ 与屏幕内容的冲突均有利于 $g$，有效防御通过通知、弹窗、覆盖层实施的间接提示注入攻击

### 6. 分布鲁棒性与序列扩展
针对真实部署中的分布漂移与时间依赖性：
- **加权校准**：通过重要性加权 $w(c) \propto p_{\text{target}}(c)/p_{\text{cal}}(c)$ 处理协变量漂移（如应用分布、OS 版本、设备类型、区域语言），其中 $c$ 为部署描述符。具体实现为对前景应用频率进行裁剪归一化：
$$w(a) = \text{clip}\left(\frac{\hat{p}_{\text{target}}(a)}{\hat{p}_{\text{cal}}(a)}, w_{\min}, w_{\max}\right)$$
- **块级分割**：针对轨迹内步骤相关性，采用块级（episode/block 级）而非随机步骤级分割进行校准，确保验证集包含独立轨迹段，缓解时间依赖导致的泄漏

通过上述组件耦合，CORA 实现了**后策略（post-policy）、前动作（pre-action）**的安全控制：不修改基础策略 $\pi$ 的参数，仅通过选择性执行与统计校准，在用户指定的风险预算 $\alpha$ 下最大化任务完成率，同时通过最小干预机制控制用户负担。

## 实验验证

论文在第5节及附录中开展了系统性的实验验证，涵盖安全-有用性权衡、分布外泛化、能力保持及组件消融四个维度。具体实验内容如下：

### 1. 实验设置

**评测基准（Benchmarks）**
- **Phone-Harm**（自研）：包含 Harm-150（150个人工撰写的有害任务，带步骤级危害标签 $\ell_t$）和 Normal-150（150个纯良性任务，$\ell_t=0$），支持对抗性安全评估与混合流量（mixed-traffic）部署分析
- **MobileRisk**（公共基准）：评测移动设备代理的安全性与有用性，包含间接提示注入场景
- **AndroidWorld**（公共基准）：评测长程移动导航与一般能力，使用 CORE20 安全导航任务

**基线方法（Baselines）**
- **基础策略（ unconstrained ）**：GPT-5、Gemini-3、UI-TARS-1.5、AutoGLM-VLM
- **提示工程**：AutoGLM + Prompt（结构化安全反思提示）
- **启发式门控**：AutoGLM + Heuristic gate（基于关键词规则拦截）
- **VLM-as-Critic**：AutoGLM + VLM-as-critic（额外 VLM 评审动作安全性）

**评价指标（Metrics）**
- **HR（Harm Rate）**：有害且被执行的步骤占比，越低越好
- **mHR（Macro Harm Rate）**：被执行步骤中的条件危害率，越低越好  
- **GAR（Goal Achievement Rate）**：动作执行率（覆盖率），越高越好
- **IF1（Intervention F1）**：有害步骤上的干预决策宏 F1，越高越好
- **OIR（Over-Intervention Rate）**：诊断器被调用率，越低越好
- **MobileRisk 指标**：Accuracy、Precision、Recall、F1、FPR
- **AndroidWorld 指标**：Success Rate（SR）

### 2. 主要结果：安全-有用性-干预权衡

**Phone-Harm Harm-150 子集（表1、图2a）**
- CORA 实现 **GAR = 79.80%**、**IF1 = 85.29%**，相比最强零样本基线 VLM-as-critic（IF1 = 44.21%）提升超过 40 个绝对百分点
- 启发式门控因无法泛化到动态视觉布局，HR 恶化至 24.37%
- GPT-5 虽 HR 低至 0.40%，但 GAR 仅 37.50%，表现为极端保守的拒绝模式
- CORA 的 OIR = 19.23% 是共形风险预算的可调结果，优先保证灾难性危害的召回而非静默失败

**MobileRisk 基准（表1）**
- 在类别极度不平衡场景下，VLM-as-critic 虽 Accuracy 达 86.92%，但 F1 仅 19.05%（大量漏检）
- CORA 取得 **Recall = 76.47%**、**F1 = 66.38%**，为所有方法中最高，验证 Goal-Lock 机制对语义目标漂移与视觉注入攻击的鲁棒性

**混合流量统一评估（表2、图2b）**
- 合并 Harm-150 与 Normal-150（300 任务），CORA 保持最高 GAR（89.69%）与 IF1（85.29%），较最强基线 UI-TARS-1.5（GAR 77.28%）提升 +12.41 个百分点
- 合并 HR 降至 2.19%，低于 UI-TARS-1.5（2.87%）
- CORA 的合并 OIR 为 9.83%，在有害子集上为 19.23%，在良性子集上仅 0.42%，证明干预预算集中于真实风险步骤而非 blanket 中断

### 3. 能力保持实验（AndroidWorld CORE20）

**对齐税检验（表3）**
- 在纯良性导航任务上，AutoGLM 基础策略成功率为 30.0%
- 包裹 CORA 后成功率提升至 **40.0%**，恢复 GPT-5 上限（55.0%）的 72.7%，超越 UI-TARS-1.5（20.0%）20 个百分点
- 生成式 Diagnostician 通过 REFLECT 或 CONFIRM 干预，使代理从视觉歧义与误点击中恢复，将安全控制器转化为**轻度错误恢复层**而非对齐税来源

### 4. 消融实验（附录 E）

在保留校准集（n=1075，目标风险 $\delta=0.05$）上，对四个核心组件进行阈值扫描（$\tau \in \{0.5, 0.6, 0.7, 0.8, 0.9\}$）与 CRC 校准对比：

**共形风险控制（CRC）的必要性（图5）**
- 固定开发调优阈值无法恢复 CRC 的运行点；CRC 自适应选择 $\hat{\tau}=0.952$，在覆盖率（89.95%）与调用率（10.05%）上最优，同时约束有害执行率于 2.42%（$\leq 5\%$ 预算）

**动作条件化（Action-Conditioning）的影响（图6）**
- 去除动作条件化后，Guardian 退化为全局状态评估器，无法区分同一 UI 中"取消"与"确认转账"的风险
- 在固定阈值下，动作条件化 ON 较 OFF 覆盖率提升 5-8 个百分点，调用率降低 4-5 个百分点
- CRC 校准后，动作条件化以更低阈值（0.952 vs 0.985）实现更低有害率（2.42% vs 2.70%）

**生成式 Diagnostician vs 规则启发式（图7）**
- 替换为规则启发式后，所有固定阈值下有害执行率升至 8.37%-8.56%（生成式为 4.37%-5.21%）
- CRC 阈值下，启发式有害率 8.84% 显著违反 $\delta=0.05$ 预算，而生成式为 5.67%，证明上下文感知语义干预对维持安全边界的必要性

**Goal-Lock 机制分析（图8）**
- 禁用 Goal-Lock 后，固定阈值下有害率升至 4.84%-6.51%（启用时 0.56%-1.86%）
- CRC 被迫选择极度保守阈值（$\hat{\tau}=0.378$）以满足预算，导致覆盖率下降
- 启用 Goal-Lock 允许 CRC 选择更高阈值（0.952），同时实现更低有害率（2.42%）与更高覆盖率（89.95%），扩展安全-效用 Pareto 前沿

### 5. 运行时效分析（附录 H.4）

- 基础策略平均单步延迟：20.52 秒
- CORA 整体平均单步延迟：**21.37 秒**（System 1 Guardian 轻量级设计）
- 触发干预时单步延迟：34.13 秒（System 2 Diagnostician 深度推理）
- 平均完整 episode 延迟：110.22 秒，证实选择性执行范式在实际部署中的计算可行性

## 未来工作

基于论文第6节（Conclusion）及方法论局限，未来研究可沿以下方向深入：

### 1. 长程轨迹的序列共形推断
现有框架假设步骤级交换性（exchangeability），并通过**块级分割（blockwise splitting）**缓解时间依赖性。然而，移动 GUI 任务常涉及多步风险累积（如先搜索联系人再发送敏感信息）。可探索**自适应序列共形预测（adaptive sequential conformal prediction）**或**在线共形控制（online conformal control）**，在保持有限样本保证的同时，显式建模轨迹内的马尔可夫依赖结构，避免过度保守的阈值选择。

### 2. 异构分布漂移下的动态加权校准
当前加权校准主要处理应用级（app-level）漂移：
$$w(a) = \text{clip}\left(\frac{\hat{p}_{\text{target}}(a)}{\hat{p}_{\text{cal}}(a)}, w_{\min}, w_{\max}\right)$$
未来可扩展至**细粒度协变量漂移**，包括 OS 版本迭代、设备形态（折叠屏/平板）、UI 主题（暗黑模式/字体缩放）及区域语言设置。关键挑战在于开发**轻量级密度比估计器**，在保护隐私的前提下利用未标记的部署窗口数据实时更新权重，而非依赖静态频率估计。

### 3. 基准测试的广度与深度扩展
Phone-Harm 当前涵盖 29 个应用与 300 个任务。需扩展至：
- **跨应用长程工作流**（如"在地图中查找餐厅→在浏览器中预订→在日历中标记"），测试风险在应用边界间的传递
- **多语言与多文化场景**，检验 Goal-Lock 在非英语视觉注入攻击（如通过 OCR 识别的多语言恶意指令）下的鲁棒性
- **动态对抗攻击**，不仅限于静态提示注入，还包括时序协调的对抗 UI 变化（如弹窗时机与代理动作同步）

### 4. 细粒度风险分层与多目标控制
当前框架使用二元危害指标 $\ell_t \in \{0,1\}$ 与单一风险预算 $\alpha$。未来可发展**多类别风险控制器**，区分财务损失（不可逆）、隐私泄露（部分可逆）与社交尴尬（可解释），通过**多目标共形预测（multi-objective conformal prediction）**控制：
$$E[L_{\text{financial}}] \leq \alpha_{\text{fin}}, \quad E[L_{\text{privacy}}] \leq \alpha_{\text{priv}}$$
允许用户根据场景（如企业设备 vs 个人设备）自定义风险权重向量。

### 5. 在线适应与持续学习
现有 Guardian $R_\psi$ 与阈值 $\hat{\tau}$ 基于静态训练/校准集。部署中可探索**在线共形校准（online conformal calibration）**，利用轻量级反馈循环（如用户纠正标记）动态调整 $\hat{\tau}$ 而不破坏覆盖保证。同时，Diagnostician 可通过**连续学习（continual learning）**适应新出现的注入模式，克服灾难性遗忘。

### 6. 人类反馈与强化学习的集成
当前 Diagnostician 使用静态 CLM 损失训练。可引入**基于人类反馈的强化学习（RLHF）**或**共形风险训练（Conformal Risk Training, Yeh et al., 2025）**，直接优化干预策略的长期效用：
$$\max_{\phi} \mathbb{E}[\text{TaskSuccess}] \quad \text{s.t.} \quad \text{Risk}_{\text{conformal}} \leq \alpha$$
将 CORA 的统计约束与端到端策略优化结合，最小化用户中断负担。

### 7. 计算效率与系统优化
System 2 Diagnostician 在干预时引入约 34.13 秒延迟（附录 H.4）。未来可通过**模型蒸馏**或**早期退出机制（early exiting）**加速，或设计**分层架构**：轻量级 Guardian 快速过滤明显安全动作，仅将边缘案例路由至完整 VLM 诊断。此外，探索**结构化潜在表示（structured latent representations）**替代像素级 VLM 推理，进一步降低延迟（Feng et al., 2025）。

### 8. 对抗鲁棒性与非交换性理论保证
在**自适应攻击者**（知晓 CORA 机制并针对性构造 UI）或**非平稳环境**（应用频繁 A/B 测试 UI 布局）下，标准共形预测假设失效。需发展**对抗性共形预测（adversarial conformal prediction）**或**分布鲁棒优化（DRO）**变体，确保在最坏情况分布偏移下仍满足：
$$\sup_{P \in \mathcal{P}} E_P[L(Z; \hat{\tau})] \leq \alpha$$
其中 $\mathcal{P}$ 为与校准数据距离有限的分布族。

### 9. 多智能体协作的安全控制
当多个 GUI 代理（如跨设备个人助理与企业代理）协作完成复杂任务时，需扩展 CORA 至**多智能体共形控制**，协调各代理的风险预算，防止"风险分散"（单个代理风险低于 $\alpha$ 但系统级风险超标）或**级联失效**。

## 总结

该论文针对**视觉语言模型（VLM）驱动的移动 GUI 代理在自主执行操作时缺乏统计上可验证的安全保障**这一核心问题，提出了 **CORA（COnformal Risk-controlled GUI Agent）** 框架，实现了用户可调的、具有形式化风险保证的安全控制。

### 1. 问题背景与动机
移动 GUI 代理（如自动执行转账、发送消息、授权权限的智能体）面临严重的**不可逆风险**（财务损失、隐私泄露）。现有防护手段存在根本性缺陷：
- **提示工程**易被视觉注入攻击绕过；
- **启发式规则**在动态界面中脆弱且难以泛化；
- **VLM-as-critic** 计算昂贵、校准性差，且缺乏**形式化验证**与**用户可调的统计保证**。

此外，移动环境的小屏幕、密集交互、实时通知注入面及分布漂移等特性，加剧了安全控制的复杂性。

### 2. 方法论：选择性动作执行范式
CORA 将 GUI 安全重构为**后策略（post-policy）、前动作（pre-action）的选择性执行**问题，包含四个耦合组件：

**（1）动作条件 Guardian 模型（System 1）**
基于轻量级 VLM 主干网络，训练标量风险估计函数：
$$s_t = R_\psi(g, o_t, h_t, \hat{a}_t) \in [0,1]$$
其中 $g$ 为用户目标，$o_t$ 为当前观察（截图+UI树），$h_t$ 为历史，$\hat{a}_t$ 为候选动作。通过**动作条件化**区分同一界面下不同 affordance 的风险（如"取消" vs "确认转账"），采用加权二元交叉熵损失优化。

**（2）共形风险控制（Conformal Risk Control, CRC）**
将用户指定的风险预算 $\alpha \in (0,1)$（如 $\alpha=0.01$ 表示允许不超过 1% 的自主动作为有害）映射为执行阈值 $\hat{\tau}$。基于保留的校准集，计算：
$$\hat{\tau} = \sup\left\{\tau \in [0,1] : \frac{1}{n+1}\left(\sum_{i=1}^n L(Z_i; \tau) + 1\right) \leq \alpha \right\}$$
其中 $L(Z_i; \tau) = \ell_i \cdot \mathbb{I}\{s_i \leq \tau\}$ 为执行风险损失。该选择保证在可交换性假设下：
$$\mathbb{E}[L(Z_{n+1}; \hat{\tau})] \leq \alpha$$
提供**有限样本统计保证**，无需对风险分数的分布做参数假设。

**（3）生成式语义 Diagnostician（System 2）**
对 Guardian 拒绝的高风险动作（$s_t > \hat{\tau}$），基于可训练 VLM 生成结构化诊断报告 $Y_t = (r_t, k_t, I_t)$：
- $r_t$：自由文本推理链（解释风险原因）
- $k_t$：危害类型（MISUSE / INJECTION / MISBEHAVIOR）
- $I_t \in \{\text{ABORT}, \text{CONFIRM}, \text{REFLECT}\}$：最小干预决策

通过因果语言建模（CLM）损失训练，实现上下文感知的语义干预，避免启发式规则的刚性。

**（4）Goal-Lock 机制**
冻结初始用户目标 $g$ 作为可信规范，将屏幕文本严格视为**不可信证据**。防止间接提示注入攻击导致的"指令漂移"，确保风险评估以 $g$ 为锚点。

**（5）分布鲁棒性扩展**
通过**加权校准**（处理应用、OS、设备等协变量漂移）与**块级分割**（缓解轨迹内时间依赖性），增强真实部署中的可靠性。

### 3. 实验验证
**基准测试**：
- **Phone-Harm**（自研）：包含 Harm-150（150个带步骤级危害标签的有害任务）与 Normal-150（150个良性任务），支持对抗性与混合流量评估；
- **MobileRisk** 与 **AndroidWorld**（公共基准）。

**主要发现**：
- **安全-有用性权衡**：在 Phone-Harm 上，CORA 实现 **GAR（目标达成率）= 79.80%**、**IF1（干预 F1）= 85.29%**，较最强基线 VLM-as-critic（IF1=44.21%）提升超过 40 个百分点；在 MobileRisk 上取得 **Recall=76.47%**、**F1=66.38%** 的最优表现。
- **混合流量鲁棒性**：合并 Harm-150 与 Normal-150 后，CORA 保持 **GAR=89.69%**（较 UI-TARS-1.5 提升 +12.41%），且干预率（OIR）在良性子集上仅 0.42%，证明其将干预预算集中于真实风险。
- **能力保持**：在 AndroidWorld 良性任务上，CORA 将基础策略成功率从 30.0% 提升至 **40.0%**（恢复 GPT-5 上限的 72.7%），通过 REFLECT/CONFIRM 干预实现错误恢复，避免"对齐税"。
- **消融验证**：去除 CRC、动作条件化、生成式 Diagnostician 或 Goal-Lock 任一组件，均导致安全保证失效或性能显著下降。

### 4. 核心贡献
1. **范式创新**：提出将移动 GUI 安全建模为**选择性动作执行**（预测 with 弃权），以风险-覆盖率与安全-有用性-干预 Pareto 前沿作为评估标准。
2. **统计保证框架**：首个在开放移动 GUI 环境中实例化**共形风险控制**的框架，提供用户可调、有限样本保证的有害动作率上限。
3. **基准建设**：发布 **Phone-Harm** 基准，包含步骤级危害标签与严格的数据隔离协议，填补移动安全评估的空白。
4. **实用架构**：通过 Guardian-Diagnostician 分层架构与 Goal-Lock 机制，在统计严格性、计算效率与对抗鲁棒性间实现平衡。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
