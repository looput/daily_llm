# AVA-VLA: Improving Vision-Language-Action models with Active Visual Attention

**arXiv**: [2511.18960](https://arxiv.org/abs/2511.18960) · [PDF](https://arxiv.org/pdf/2511.18960)  
**领域**: Multimodal  
**作者**: Xiao, Li, Gao, Ye, Jin, Qian, Zhang, Wu 等 9 人  
**综合评分**: 8.07  （novelty: 8.5 · method: 8.5 · evidence: 9.0 · clarity: 7.5）

---

## 摘要

> 本文提出了一种改进视觉-语言-动作（VLA）模型的新框架AVA-VLA，通过引入部分可观测马尔可夫决策过程（POMDP）视角和主动视觉注意力机制，显著提升了模型在机器人顺序决策任务中的性能。论文在标准机器人基准测试（LIBERO和CALVIN）上取得了最先进的性能，并有效迁移到真实世界的双臂操作任务中。作者团队未明确标注来自知名机构，因此省略背景介绍。

---

## 详细分析

> **社区热度**: ⭐ 4 (来自 papers.cool)

## 问题定义

论文针对现有 Vision-Language-Action（VLA）模型在动态序列决策中“逐帧独立处理视觉输入”的 MDP 隐式假设，指出其两大缺陷：

1. 历史盲区：每帧仅依赖当前观测，丢弃了历史观测与动作中包含的不可见动态、遮挡信息等关键上下文。  
2. 被动视觉：注意力权重仅由静态语言指令驱动，无法在时间轴上主动预判“接下来该看什么”，导致冗余信息反复重估，任务相关区域难以持续聚焦。

为此，作者将问题重新形式化为 Partially Observable Markov Decision Process（POMDP），提出 AVA-VLA 框架，用可学习的循环状态 $r_{t-1}$ 近似 belief-state，并设计 Active Visual Attention（AVA）模块，使模型在每一时刻依据历史信念动态调节视觉 token 的权重，实现“主动视觉”下的动作生成。

## 相关工作

与 AVA-VLA 直接相关的研究可归纳为三条主线，每条均给出代表性文献及与本文的差异/继承关系：

- **Vision-Language-Action 模型**  
  - RT-1/RT-2、OpenVLA、π₀、CogACT、UniVLA 等  
  - 共同点：借助 VLM 先验，把控制转化为序列建模（tokenization 或 diffusion）。  
  - 差异：它们均按 MDP 逐帧独立推理，无显式历史状态；AVA-VLA 首次引入 POMDP 式的循环 belief 近似，实现主动视觉注意力。

- **序列/时序视觉理解 VLM**  
  - VLM-3R、Streaming-LLM、Continuous-3D-Perception 等  
  - 共同点：利用 KV-cache、持久状态或 3D token 聚合视频帧。  
  - 差异：目标为被动视频问答或 3D 重建；AVA-VLA 把历史状态用于在线决策，强调“感知-动作”闭环。

- **视觉 Token 压缩与加速**  
  - DynamicViT、SP-VLA、SpecPrune-VLA、VLA-Cache 等  
  - 共同点：依据注意力得分或帧间差异剪枝/缓存 token，降低计算。  
  - 差异：它们侧重效率，剪枝信号多来自静态置信度或帧差异；AVA 的权重由循环 belief 动态生成，兼顾任务相关性与效率，且主目标是提升策略性能而非加速。

## 解决方案

论文将“逐帧独立、历史无关”的 MDP 建模缺陷转化为 **POMDP 视角下的 belief-state 缺失问题**，通过两步机制解决：

1. 用**循环状态**近似 belief  
   以上一时刻动作相关隐状态 $h_{t-1}^M$ 经 MLP 压缩得到  
   $$r_{t-1}=B(h_{t-1}^M)\in\mathbb{R}^{L_A\times d}$$  
   作为“神经信念”，同时用它初始化动作占位符，实现历史信息在时序上的递归传递。

2. 用**主动视觉注意力（AVA）**动态调节视觉 token  
   - 将当前视觉 token 作为 Query，$r_{t-1}$ 作为 Key/Value，执行 Cross-Attention 得到混合表征；  
   - 经自注意力+FFN+线性层输出二维 logits，Softmax 后得增强/削弱权重 $\rho_t\in\mathbb{R}^{L_I\times 2}$；  
   - 与可学习向量 $\gamma=[\gamma_0,\gamma_1]$ 相乘得到软权重 $\omega_t$，按公式  
     $$A_{t,m}^{i,j}=\frac{\exp(C_{t,m}^{i,j})U_t^{i,j}}{\sum_l\exp(C_{t,m}^{i,l})U_t^{i,l}},\quad U_t^{i,j}= \begin{cases}1 & j\notin\Lambda_I\\ \omega_t^j & j\in\Lambda_I\end{cases}$$  
     直接修正 LLM 每一层的注意力矩阵，实现“历史-条件化”的主动聚焦。

训练阶段采用**截断 BPTP**（T=4 步）兼顾显存与长期依赖，并以 MAE 动作损失 + 权重均值正则联合优化。推理时完全循环，逐帧更新 $r_t$，无需未来信息。

## 实验验证

实验从**仿真基准**、**真实机器人**与**系统分析**三个层面展开，覆盖 100+ 任务、多种扰动与消融配置，结果均以 Success Rate 或 Average Completed Length 量化。

| 实验类别 | 数据集/平台 | 关键设置 | 主要结论 |
|---|---|---|---|
| **1. 仿真基准** | LIBERO（100 任务，4 suite） | 单策略/每策略独立训练 | AVA-VLA 在 One-policy-for-all 与 One-policy-per-suite 均取得 SOTA，平均 SR 98.0% vs. 97.1%（OpenVLA-OFT） |
|  | CALVIN ABC→D（34 任务） | 零样本跨环境泛化 | 5 任务连做成功率 84.1%，平均长度 4.65，显著高于先前最佳 4.53 |
|  | LIBERO+（7 类扰动） | 无再训练直接评估 | 7 维扰动平均 SR 70.1%（多任务）/74.7%（单任务），领先次佳 67.9%/69.6% |
| **2. 真实机器人** | Mobile ALOHA 双臂平台 | 4 类长时序任务，30–450 条演示微调 | 平均成功率 87.5%，较 UniVLA↑18.3%，较 OpenVLA-OFT↑11.9%；sim-to-real 零额外域适应 |
| **3. 消融与剖析** | 骨干网络 | OpenVLA-7B / LLaMA2-7B / Qwen2.5-0.5B | 三种骨干均获 1.4–2.6% 绝对提升，验证框架通用性 |
|  | 组件消融 | 仅 State-init / 仅 AVA / 全模块 | 组合后平均 SR 98.0%，高于任一单独组件 97.5% |
|  | 视觉 Token 剪枝 | 0–90% 剪枝率 | 70% 剪枝仍保持 97.3% SR，证明权重真正捕捉任务关键信息 |
|  | 可视化 | 实时软权重热图 | 注意力始终锁定机械手接触区与目标物体，跨视角一致，解释性能提升来源 |

## 未来工作

以下方向可在此基础上继续深入，分为“理论-算法”与“系统-应用”两大层面：

- **理论-算法层面**  
  1. **可解释 belief 表征**  
     将循环状态 $r_t$ 显式分解为任务阶段、物体位姿或语义掩码等可解释隐变量，引入变分推断或对比学习，使 belief 具备人类可读性。  
  2. **层次化 POMDP 与选项框架**  
     在高层选项空间维护 belief，低层原子动作共享同一 AVA 模块，实现“子任务级”与“原始动作级”双重主动视觉，支持更长程、更抽象的任务。  
  3. **跨模态 belief 更新**  
     引入触觉、音频或本体感受流，设计多模态 cross-attention 融合，使 belief 更新不再仅依赖视觉-语言，提升严重遮挡或光照变化下的鲁棒性。  
  4. **自适应剪枝与计算图动态化**  
     将 $\omega_t$ 作为强化学习策略的 action，奖励为“任务成功率-延迟”，在线学习最优剪枝比例，实现“精度-延迟”自动平衡。  
  5. **持续学习与 belief 防遗忘**  
     采用 EWC 或记忆回放约束 belief 网络参数，避免在新任务上微调时历史 belief 被覆盖，实现终身机器人学习。

- **系统-应用层面**  
  1. **高速双臂协同与人形机器人**  
     将 AVA 扩展为双 agent 系统，各自维护私有 belief 并通过共享注意力图实现协同抓取、交接等任务，验证在高自由度平台下的实时性。  
  2. **开放世界物体操作**  
     在未知物体类别、无 CAD 模型条件下，联合开放词汇检测器（OWL-ViT）在线扩充词汇表，使 AVA 权重可聚焦新类别，实现真正“零样本”抓取。  
  3. **边缘计算与模型压缩**  
     将 AVA 模块蒸馏至 <2B 的小模型，结合 INT4/INT8 量化与 token 剪枝，在 NVIDIA Jetson Orin 上达到 ≥30 Hz 闭环频率，满足家用机器人成本要求。  
  4. **人机共融与安全**  
     在 belief 中显式建模人类意图不确定性，引入安全约束 CBF 或人类反馈 RLHF，使机器人在共享工作空间主动“看向”人类手部，提前规避碰撞。  
  5. **仿真-真实自动迭代**  
     利用 AVA 权重热图自动生成“困难帧”集合，通过 NeRF-GS 重建+域随机化生成新仿真场景，实现 data-centric 的自动 sim-to-real 闭环。

## 总结

论文核心内容可概括为“一个视角转换、一个模块设计、一套实验验证”：

1. 视角转换：把传统 VLA 的**逐帧 MDP** 建模改为 **POMDP**，指出历史 belief 缺失导致视觉注意力被动、冗余信息无法滤除。

2. 模块设计：  
   - **循环状态** $r_{t-1}=B(h_{t-1}^M)$ 作为可学习 belief 近似，同时初始化动作占位符，实现时序递归。  
   - **Active Visual Attention（AVA）** 以 $r_{t-1}$ 为 Key/Value、当前视觉 token 为 Query，生成软权重 $\omega_t$ 直接修正 LLM 各层注意力矩阵，使模型“依据历史”主动聚焦任务关键区域。

3. 实验验证：  
   - 在 LIBERO、CALVIN 两大仿真基准及 Mobile ALOHA 真实双臂平台共 100+ 任务上取得新 SOTA；70% 视觉 token 剪枝后性能几乎不降，验证权重有效性；跨骨干、跨扰动、跨 sim-to-real 均稳定提升。

综上，AVA-VLA 首次将 POMDP 式的主动视觉注意力引入 VLA 框架，用轻量级循环 belief 实现历史上下文驱动的动态视觉筛选，在仿真与真实机器人上均表现出更强的泛化与鲁棒性。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
