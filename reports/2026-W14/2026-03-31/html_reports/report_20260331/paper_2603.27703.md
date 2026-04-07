# KAT-Coder-V2 Technical Report

**arXiv**: [2603.27703](https://arxiv.org/abs/2603.27703) · [PDF](https://arxiv.org/pdf/2603.27703)  
**领域**: Agent  
**作者**: Li, Zhang, Huang, Wang, Hao, Yuan, Li, Zhang 等 46 人  
**综合评分**: 8.24  （novelty: 8.5 · method: 8.5 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文由快手KAT团队（KwaiKAT team at Kuaishou）提出了一种新的智能体编程模型KAT-Coder-V2，采用“先专业化后统一”的范式，将智能体编程分解为五个专家领域，通过独立监督微调、强化学习和策略蒸馏进行整合。在多个基准测试中取得了优异性能，特别是在SWE-bench Verified、PinchBench和前端美学场景中表现突出。模型已公开可用。

---

## 详细分析

> **社区热度**: ⭐ 5 (来自 papers.cool)

## 问题定义

这篇论文针对**Agentic Coding**（代理式编程）领域中的三个根本性挑战，提出了系统性的解决方案：

### 1. 能力碎片化（Capability Fragmentation）
Agentic coding 涉及多种异构任务领域（如软件工程修复、前端界面生成、终端命令推理等），各领域的训练信号存在本质差异甚至冲突：
- **SWE 任务**要求基于测试验证的长链代码编辑与仓库级理解
- **WebCoding 任务**需在稀疏口语化输入下进行审美判断与视觉生成
- **Terminal 任务**依赖持久的环境状态跟踪与命令行推理

单一的单体训练管道难以在所有领域同时达到最优，导致传统方法在跨领域泛化时性能受损。

### 2. 基础设施耦合（Infrastructure Coupling）
现有的 Agentic RL 训练系统存在严重的紧耦合问题：
- **数据集异构性**：不同基准测试（如 SWE-bench、Terminal-Bench）对沙箱镜像和评估逻辑有不同要求
- **脚手架（Scaffold）激增**：Claude Code、OpenClaw、OpenCode 等代理框架的集成协议差异显著，缺乏统一抽象导致每次接入新框架都需昂贵的工程投入
- **高吞吐量需求**：RL 训练阶段需要并发执行海量 rollout，对沙箱调度和轨迹收集提出严苛性能要求

### 3. Agentic RL 的规模化瓶颈（Scaling Agentic RL）
有效训练编码代理需同时沿**任务复杂度**、**意图对齐**和**脚手架泛化**三个维度扩展，但面临以下技术障碍：
- **MoE 训练不稳定性**：混合专家模型在 RL 训练中因策略不匹配和 log-probability 估计方差过大导致梯度方向不稳定
- **树结构轨迹的计算冗余**：现代代理脚手架产生的轨迹呈树状结构（含并行子代理、多轮上下文管理），朴素线性化处理导致共享前缀被重复计算，造成严重的训练效率瓶颈

### 解决方案框架
为应对上述挑战，论文提出 **KAT-Coder-V2**，采用 **"Specialize-then-Unify"（先专精后统一）** 范式：
- 将能力解耦为五个正交专家领域（SWE、WebCoding、Terminal、WebSearch、General）独立训练
- 开发模块化基础设施 **KwaiEnv**，实现数据集、沙箱、脚手架与验证器的完全解耦
- 提出 **MCLA（Monte-Carlo Log-probability Averaging）** 稳定 MoE RL 训练，以及 **Tree Training** 消除树结构轨迹的冗余计算（最高实现 $6.2\times$ 加速）
- 通过 **On-Policy Distillation（OPD）** 将多领域专家能力无损融合为单一部署模型

## 相关工作

这篇论文的相关研究可分为以下几个主要类别：

### 1. 前沿 Agentic Coding 模型
近期前沿模型在 Agentic Coding 领域取得显著进展，本文与其进行直接对比：
- **Claude Opus 4.6** [1] 和 **Claude Code**：Anthropic 的旗舰编码代理，在 SWE-bench 等基准上表现领先
- **Gemini 3 Pro** [2]：Google DeepMind 的多模态代理模型
- **GLM-5** [3]：智谱 AI 的"氛围编程"到"代理工程"模型
- **Kimi K2.5** [4]：月之暗面的视觉代理智能模型
- **Step 3.5 Flash** [5]：阶跃星辰的开源前沿模型
- **DeepSeek-V3.2** [6]：深度求索的开源大模型

### 2. 软件工程基准测试（SWE Benchmarks）
- **SWE-bench** [7]：评估语言模型解决真实 GitHub issue 能力的标准基准，包含 Verified 和 Multilingual 变体
- **SWE-bench Pro** [11]：针对长周期软件工程任务的扩展基准
- **Swe-rebench-V2** [21]：大规模语言无关的 SWE 任务集合
- **SWE-smith** [20]：用于软件工程代理的数据扩展方法
- **LiveCodeBench** [12]：全面的代码生成评估基准

### 3. 终端与命令行代理基准
- **Terminal-Bench** [8]：评估 CLI 环境中硬实例任务的基准
- **CLI-Gym** [15]：通过代理环境反转生成可扩展 CLI 任务
- **TermiGen** [16]：高保真终端环境与鲁棒轨迹合成

### 4. 对话与工具使用基准
- **𝜏2-Bench** [9]：评估双控制环境下对话代理的基准（本文在 Telecom 子集上评估）
- **𝜏-Bench** [22]：真实领域中的工具-代理-用户交互基准
- **WebArena** [13]：用于构建自主代理的真实 Web 环境
- **PinchBench** 和 **Claw-Eval**：OpenClaw 框架下的真实世界代理执行基准（本文评估所用）

### 5. 前端生成与美学评估
- **Design2Code** [14]：多模态代码生成基准，专注于自动化前端工程
- **IFBench** [24]：可验证指令遵循的泛化基准

### 6. 长上下文与推理基准
- **AA-LCR** [23]：Artificial Analysis 长上下文推理基准
- **WebCoding 美学评估**：本文自建的首个系统性参考无关的前端美学评估基准（涵盖 Landing Page、Slides、Data Visualization）

### 7. 强化学习算法与训练方法
- **GRPO (Group Relative Policy Optimization)** [17]：DeepSeekMath 提出的组相对策略优化，无需价值模型
- **GSPO (Group Sequence-level Policy Optimization)** [18]：通过序列级概率聚合提高稳定性
- **Tree Training** [19]：通过共享前缀重用加速代理 LLM 训练的方法（本文在此基础上实现 $6.2\times$ 加速）

### 8. 先前工作
- **KAT-Coder-V1** [10]：本文的前身模型，KAT-Coder-V2 在此基础上进行持续后训练

### 9. 相关基础设施与脚手架
- **SWE-agent** [6]：用于软件工程任务的代理框架
- **Claude Code**、**OpenClaw**、**OpenCode**、**Kilo Code**、**Cline** 等：各类 Coding Agent 脚手架（本文通过 KwaiEnv 实现黑盒集成）

## 解决方案

论文通过 **Specialize-then-Unify 范式**、**模块化基础设施 KwaiEnv** 以及 **系统化的 Agentic RL 训练框架** 三个层面，分别对应解决能力碎片化、基础设施耦合和规模化训练三大挑战。

---

### 1. 应对能力碎片化：Specialize-then-Unify 范式

将 Agentic Coding 的完整能力谱系解耦为五个正交专家领域，每个领域独立构建数据、训练专家模型，最终通过 On-Policy Distillation 实现无损融合。

#### 1.1 五域专家分解与独立训练
| 专家域 | 核心能力 | 关键数据构建方法 |
|--------|----------|------------------|
| **SWE** | 软件工程修复与开发 | Issue-PR 配对（200万+样本）、AutoBuilder 合成（3万可验证任务）、代码理解轨迹合成 |
| **WebCoding** | 前端生成与审美 | 三视角标签系统（L1-L7 层级）、提示重写（设计师/专业用户/普通用户变体）、设计师面板的审美评估 |
| **Terminal** | 命令行推理 | 专家标注、多代理合成、SWE→Terminal 跨格式转换（10万+任务）、开源数据集成 |
| **WebSearch** | 代理式搜索 | 基于搜索轨迹的知识图谱构建、Pass@8 过滤、拒绝采样微调 |
| **General** | 通用代码-数学推理 | 组合约束训练、长对话样本、在线判题系统验证 |

每个专家域均通过 **KwaiEnv** 独立进行监督微调（SFT）与环境反馈强化学习（RL），确保在各自领域达到最优。

#### 1.2 On-Policy Distillation (OPD) 统一融合
直接权重平均会导致灾难性遗忘，而标准 RL 反馈过于稀疏。OPD 通过以下机制实现单模型部署：
- **主动探索**：统一的学生模型（Student）在混合域提示上主动生成完整轨迹
- **双重优化**：联合优化标准 RL 损失（环境提供稀疏奖励）与 OPD 损失（动态选择最佳专家作为教师，提供步骤级 log-probability 监督）
- **避免暴露偏差**：结合 RL 的主动探索与蒸馏的密集监督，避免离线模仿的暴露偏差

---

### 2. 应对基础设施耦合：KwaiEnv 模块化架构

构建高度解耦的代理训练基础设施，通过标准化接口实现组件独立迭代。

#### 2.1 五大核心模块
- **Dataset**：统一抽象接口封装异构基准（SWE-bench、LiveCodeBench 等），屏蔽任务格式、镜像依赖与评分逻辑差异
- **Verifier**：支持确定性评分（测试用例执行）、LLM-as-Judge（开放任务评分）与 SWE 专用评估（沙箱内测试套件执行）
- **Scaffold**：网络层代理实现"黑盒"集成，支持 Claude Code、OpenClaw、OpenCode 等 10+ 框架，无需代码修改仅需配置 API 端点
- **Sandbox**：基于容器的大规模沙箱集群，支持数万并发实例，管理全生命周期（创建、任务分配、监控、回收）
- **Trajectory Manager**：拦截 LLM 请求记录完整元数据（I/O、工具调用、token 使用、时间戳），并格式化为 RL 算法所需输入

#### 2.2 关键设计优势
- **数据可扩展性**：新增数据集仅需实现统一接口，不影响沙箱与脚手架
- **脚手架可扩展性**：新 Agent 通过配置容器命令与 API 端点即可接入
- **评估敏捷性**：评估与训练共享基础设施，确保高一致性与短迭代周期

---

### 3. 应对 Agentic RL 规模化：算法与系统工程创新

#### 3.1 Agentic Scaling 数据合成范式
沿三个维度系统性扩展 RL 训练数据（总计 10万+ 样本）：
- **任务复杂度**：利用 AutoBuilder 池，仅保留前沿教师模型需多次反思才能解决的困难实例
- **意图对齐**：通过语义增强将标准化任务描述改写为多样化变体（详细专家指令到口语化低信息输入），弥合 Sim-to-Real 差距
- **脚手架泛化**：对同一任务使用多种脚手架（Claude Code、OpenCode 等黑盒与 SWE-agent 白盒）生成轨迹，培养脚手架无关的泛化行为

统一表示为五元组：
$$\mathcal{D}_{\text{RL}} = \left\{ \langle \mathcal{E}, \mathcal{T}_{\text{tools}}, \mathcal{S}_{\text{agent}}, \mathcal{I}_{\text{task}}, \mathcal{V}_{\text{verifier}} \rangle \right\}$$

#### 3.2 训练稳定性：MCLA (Monte-Carlo Log-probability Averaging)
针对 MoE 模型在 RL 训练中因 log-probability 估计方差过大导致的梯度不稳定：
- **问题识别**：策略梯度依赖重要性权重 $w(a) = \exp(\log \pi_{\text{rollout}}(a) - \log \pi_{\text{train}}(a))$，而 MoE 的随机路由导致 $\log \pi(a) = \log \pi^*(a) + \epsilon$ 噪声放大权重方差
- **解决方案**：训练前对每条轨迹进行 $K=8$ 次前向传播预填充，平均 log-probability：
$$\bar{\log} \pi(a) = \frac{1}{K} \sum_{k=1}^{K} \log \pi^{(k)}(a)$$
- **协同机制**：与 IcePop（抑制训练-推理分布不一致）结合，前者降低估计方差，后者缓解系统级不匹配

#### 3.3 计算效率：Tree Training
消除树结构轨迹（含并行子代理、多轮上下文分支）的冗余计算：
- **核心观察**：代理轨迹呈树状结构，共享前缀在不同分支中被重复计算
- **技术实现**：将轨迹树序列化为单次 DFS 展平序列，配合：
  - 树结构注意力掩码（基于 FlashAttention V3，限制每个 token 仅关注其根到叶路径）
  - 每 token 位置 ID 恢复原始序列位置
  - 每 token 损失权重（梯度线性性保证与独立训练所有根到叶路径等价）
- **加速效果**：在真实代理轨迹上实现最高 $6.2\times$ 端到端训练加速

#### 3.4 信用分配：Modified Turn-level Policy Optimization
改进 GSPO (Group Sequence-level Policy Optimization) 以平衡稳定性与精细信用分配：
- 将完整序列 $y$ 划分为 $N$ 个交互轮次，每轮 $n$ 包含 token 子集 $\mathcal{T}_n$
- 每轮计算独立重要性比率：
$$r_{\text{turn}}^{(n)}(\theta) = \prod_{i \in \mathcal{T}_n} \frac{\pi_\theta(y_i|x, y_{<i})}{\pi_{\theta_{\text{old}}}(y_i|x, y_{<i})}$$
- 裁剪代理目标：
$$\mathcal{L}_{\text{Turn}}(\theta) = \mathbb{E}_{\tau \sim \pi_{\theta_{\text{old}}}} \left[ \frac{1}{N} \sum_{n=1}^{N} \min\left( r_{\text{turn}}^{(n)}(\theta) A_n, \text{clip}\left( r_{\text{turn}}^{(n)}(\theta), 1-\epsilon, 1+\epsilon \right) A_n \right) \right]$$

#### 3.5 高并发训练框架 (KRL)
- **架构**：Cache-Aware 智能调度最大化 KV Cache 命中率，Dynamic Streaming 实现生成（Rollout）与权重更新（Training）阶段的细粒度流水线交织
- **资源切换**：支持 GPU 资源从 SGLang 推理服务到 Megatron 训练服务的实时上下文切换
- **成本降低**：单位样本成本降低 $2.8\times$

## 实验验证

论文在 **KwaiEnv** 评估平台上开展了四个维度的系统性实验，涵盖多脚手架兼容性、代理任务执行、前端美学生成及通用任务处理能力。

---

### 1. 多脚手架编码能力评估（Multi-Scaffold Coding）

**实验设置**：验证模型在 **SWE-bench Verified**、**SWE-bench Multilingual** 和 **SWE-rebench-V2**（子集）上的跨脚手架泛化能力，使用 **Claude Code**、**OpenCode**、**OpenClaw** 等主流脚手架的原生交互协议和系统提示配置。

**对比基线**：Claude Opus 4.6（当前最先进的编码模型之一）

**主要结果**（Table 2）：

| 基准 | 脚手架 | KAT-Coder-V2 | Claude Opus 4.6 |
|------|--------|--------------|-----------------|
| **SWE-bench Verified** | Claude Code | **79.6%** | 80.8% |
| | OpenCode | **74.8%** | 75.0% |
| | OpenClaw | 72.8% | **75.7%** |
| **SWE-bench Multilingual** | Claude Code | 75.4% | **77.8%** |
| | OpenCode | **71.2%** | 70.2% |
| **SWE-rebench-V2** (子集) | Claude Code | 43.3% | **43.7%** |
| | OpenCode | **38.7%** | 37.3% |

**结论**：模型在不同脚手架环境下保持性能稳定，与 Claude Opus 4.6 相当，证明其具备优秀的框架泛化能力。

---

### 2. 代理任务执行能力评估（Agent Task Execution）

**实验设置**：基于 **OpenClaw** 框架，在 **PinchBench** 和 **Claw-Eval** 基准上测试模型在复杂代理场景（定时触发、高并发请求处理、长链任务执行）下的表现。

**对比基线**：GLM-5、MiniMax M2.7、Claude Opus 4.6、GPT-5.4、Gemini 3.1 Pro

**主要结果**（Table 3）：

| 基准 | KAT-Coder-V2 | GLM-5 | MiniMax M2.7 | Claude Opus 4.6 | GPT-5.4 | Gemini 3.1 Pro |
|------|--------------|-------|--------------|-----------------|---------|----------------|
| **PinchBench** (Best Score) | **88.7** | 86.4 | 87.1 | 87.4 | **90.5** | 86.7 |
| PinchBench (Avg Score) | **81.9** | 80.3 | 81.8 | 82.3 | 81.6 | 75.9 |
| **Claw-Eval** (Pass@3) | 55.6 | **57.7** | 51.9 | 66.3 | **66.3** | 50.0 |
| Claw-Eval (Avg Score) | 73.4 | 73.0 | 70.7 | **79.3** | **80.6** | 74.2 |

**结论**：KAT-Coder-V2 在 PinchBench 上取得最高分（88.7），超越 GLM-5（86.4）和 MiniMax M2.7（87.1），但在 Claw-Eval 上略低于 Claude Opus 4.6 和 GPT-5.4。

---

### 3. 前端美学生成能力评估（Frontend Aesthetics Generation）

**实验设置**：在自建的**参考无关（reference-free）**美学评估基准上测试，覆盖 **Landing Pages**（10 个评估维度）、**Slides**（5 维度）和 **Data Visualization**（5 维度）。所有测试提示均为普通用户的口语化表达（如"make it cool and street-style"），由职业 UI/UX 设计师在标准化条件下（Chrome、1920×1080 视口）进行盲评。

**对比基线**：GLM-5、Kimi K2.5

**主要结果**（Table 4 & Figure 7）：

| 场景 | KAT-Coder-V2 | GLM-5 | Kimi K2.5 |
|------|--------------|-------|-----------|
| **Landing Page** | **59.8** | 57.6 | 54.6 |
| **Slides** | **57.6** | 42.8 | 34.8 |
| **Data Visualization** | **67.6** | 42.4 | 46.0 |

**结论**：KAT-Coder-V2 在所有三个前端场景均取得领先分数，尤其在 Data Visualization 上优势显著（67.6 vs 46.0/42.4）。

---

### 4. 通用任务处理能力评估（General Task Processing）

**实验设置**：评估模型在端到端复杂任务、多轮交互推理、长上下文理解和高精度指令遵循方面的能力，覆盖：
- **Terminal-Bench Hard**：命令行界面硬实例任务
- **𝜏2-Bench Telecom**：双控制环境下的对话代理任务
- **AA-LCR (Artificial Analysis Long Context Reasoning)**：长上下文推理
- **IFBench**：可验证指令遵循

**对比基线**：GLM-5、MiniMax M2.7、Claude Opus 4.6、GPT-5.4、Gemini 3.1 Pro

**主要结果**（Table 5）：

| 基准 | KAT-Coder-V2 | GLM-5 | MiniMax M2.7 | Claude Opus 4.6 | GPT-5.4 | Gemini 3.1 Pro |
|------|--------------|-------|--------------|-----------------|---------|----------------|
| **Terminal-Bench Hard** | **46.8** | 43.2 | 39.4 | 46.2 | **57.6** | 53.8 |
| **𝜏2-Bench Telecom** | 93.9 | **98.2** | 84.8 | 92.1 | 91.5 | **95.6** |
| **AA-LCR** | 68.0 | 63.3 | 68.7 | **70.7** | **74.0** | 72.7 |
| **IFBench** | 67.0 | 72.3 | **75.7** | 53.1 | **73.9** | **77.1** |

**结论**：KAT-Coder-V2 在 Terminal-Bench Hard 上表现最强（46.8），在 𝜏2-Bench 和 IFBench 上处于中等水平，展现了对复杂编程环境的良好适应性。

---

### 5. 训练效率验证（Algorithmic Efficiency）

除上述功能评估外，论文还验证了关键训练技术的效率提升：
- **Tree Training**：在真实代理轨迹上实现最高 **$6.2\times$** 的训练加速（通过消除树结构轨迹的冗余前缀计算）
- **MCLA + IcePop**：结合使用显著稳定 MoE RL 训练，加快收敛并提升最终性能
- **KRL 框架**：通过 Cache-Aware 调度和 Dynamic Streaming，实现单位样本成本降低 **$2.8\times$**

## 未来工作

基于论文的局限性与技术展望，以下方向值得进一步探索：

### 1. 代理执行能力的持续增强
尽管 KAT-Coder-V2 在 **Claw-Eval** 等高压代理执行基准上与 Claude Opus 4.6 和 GPT-5.4 仍存在差距（Pass@3 55.6 vs 66.3）。未来可通过以下方式缩小差距：
- **更大规模的 Agentic RL 训练**：进一步扩展环境交互数据的规模与多样性
- ** richer 环境反馈机制**：引入更细粒度的中间状态奖励，而非仅依赖最终任务成败的稀疏奖励
- **多模态感知集成**：将视觉感知（如 UI 截图、终端界面状态）纳入观察空间，提升在复杂 GUI/CLI 环境中的决策能力

### 2. Specialize-then-Unify 范式的跨领域迁移
当前范式主要针对编码领域（SWE、WebCoding、Terminal 等）。将其扩展至**更广泛的代理领域**具有重要价值：
- **科学研究代理**（如实验设计、数据分析流程自动化）
- **办公自动化**（复杂文档处理、跨应用工作流编排）
- **创意内容生成**（视频剪辑、多模态内容创作）
需解决跨领域知识冲突与专家融合时的容量分配问题。

### 3. 高效专家融合策略的深化
On-Policy Distillation 虽实现了无损融合，但仍存在轻微性能下降。可探索：
- **动态路由机制**：在统一模型中引入可学习的领域路由，动态激活特定专家子网络，而非静态权重共享
- **分层蒸馏架构**：先进行领域内多教师蒸馏，再进行跨领域融合，减少优化目标冲突
- **容量自适应分配**：根据领域复杂度动态调整模型容量分配，而非均分参数预算

### 4. 自动化审美评估体系
当前 WebCoding 评估依赖**专业设计师人工评分**，成本高昂且难以规模化。未来可研究：
- **训练专用美学评判模型**（Aesthetic Reward Model），学习设计师的评分标准
- **多模态对比学习**：利用设计稿与生成网页的对比，建立无参考（reference-free）的自动美学度量
- **用户偏好对齐**：通过 RLHF 捕捉终端用户的个性化审美偏好，而非仅追求专业设计标准

### 5. 长周期任务与记忆机制
现有工作主要关注单会话（single-session）任务。对于**跨会话的长期软件工程任务**（如持续数周的功能迭代）：
- 开发**持久化记忆架构**，有效管理跨会话的代码库状态、决策历史与上下文依赖
- 研究**时间信用分配**（temporal credit assignment）在极长 horizon（数千步）场景下的稳定性
- 探索**渐进式任务分解**，将长期目标自动拆解为可独立执行的子任务序列

### 6. 基础设施的通用化与开源
KwaiEnv 当前主要服务于编码任务。可扩展至：
- **通用代理执行环境**，支持科学计算、物联网设备控制等异构执行环境
- **标准化脚手架协议**：推动业界建立统一的 Agent-Environment 接口标准（类似 OpenAPI for Agents），进一步降低新框架接入成本

### 7. 安全对齐与鲁棒性
Agentic Coding 的高自主性带来安全风险：
- **代码安全验证**：在 RL 奖励函数中集成安全约束（如禁止注入漏洞、权限越界），训练**对齐且安全**的编码代理
- **对抗鲁棒性**：针对恶意构造的仓库环境（如毒化依赖、混淆代码）提升模型的鲁棒识别能力
- **可解释性**：增强多步决策过程的可解释性，提供可追溯的决策链条以满足关键软件工程场景的审计需求

## 总结

这篇论文介绍了 **KAT-Coder-V2**，一个由快手 KwaiKAT 团队开发的综合性 Agentic Coding 模型，通过 **Specialize-then-Unify** 范式、模块化基础设施与系统化的大规模强化学习训练，解决了当前代理式编程面临的三大核心挑战。

### 1. 核心挑战与解决思路
针对 Agentic Coding 领域的三个根本性难题：
- **能力碎片化**：SWE、前端生成、终端推理等领域的训练信号异构甚至冲突
- **基础设施耦合**：数据集、沙箱、脚手架（Scaffold）与验证器紧密耦合，难以扩展
- **规模化瓶颈**：MoE 训练不稳定、树结构轨迹计算冗余、长周期任务信用分配困难

论文提出分层次解决方案：
- **训练范式**：Specialize-then-Unify（先专精后统一）
- **基础设施**：KwaiEnv（完全解耦的模块化架构）
- **训练方法**：Agentic Scaling（沿任务复杂度、意图对齐、脚手架泛化三维度扩展）与算法创新（MCLA、Tree Training）

### 2. Specialize-then-Unify 训练范式
将能力解耦为五个正交专家领域独立训练，再通过 On-Policy Distillation (OPD) 无损融合：

| 专家域 | 核心能力 | 关键创新 |
|--------|----------|----------|
| **SWE** | 软件工程修复 | Issue-PR 配对（200万+样本）、AutoBuilder 合成（3万可验证任务）、F2P+P2P 双重验证 |
| **WebCoding** | 前端美学生成 | 三视角标签系统（$L: V_{\text{user}} \to V_{\text{design}} \to V_{\text{impl}}$）、提示重写（设计师→普通用户）、参考无关美学评估 |
| **Terminal** | 命令行推理 | 跨格式 SWE→Terminal 转换、多代理合成、Docker 验证 |
| **WebSearch** | 代理式搜索 | 基于搜索轨迹的知识图谱构建、Pass@8 过滤（$\hat{r} = \frac{1}{K}\sum_{j=1}^K \mathbb{1}[a_j = a^*]$）、拒绝采样微调 |
| **General** | 通用代码-数学 | 组合约束训练、长对话样本、在线判题验证 |

**统一阶段**：通过 OPD 将专家能力蒸馏至单一模型，联合优化环境反馈（稀疏奖励）与专家监督（步骤级 log-probability），避免灾难性遗忘与暴露偏差。

### 3. 基础设施与系统工程
**KwaiEnv** 采用五模块解耦架构（Dataset、Verifier、Scaffold、Sandbox、Trajectory Manager），支持：
- 黑盒集成 10+ 主流脚手架（Claude Code、OpenClaw、OpenCode 等）
- 数万并发沙箱实例，满足 RL 高吞吐需求
- 评估与训练pipeline共享，确保一致性

**训练框架 KRL** 集成：
- **Tree Training**：将树结构轨迹序列化为 DFS 展平序列，配合树注意力掩码与梯度权重，消除共享前缀冗余计算，实现最高 **$6.2\times$** 训练加速
- **高并发流水线**：Cache-Aware 调度与 Dynamic Streaming，单位样本成本降低 **$2.8\times$**

### 4. 强化学习算法创新
- **Turn-level Policy Optimization**：改进 GSPO，将序列划分为交互轮次，每轮计算独立重要性比率：
  $$r_{\text{turn}}^{(n)}(\theta) = \prod_{i \in \mathcal{T}_n} \frac{\pi_\theta(y_i|x, y_{<i})}{\pi_{\theta_{\text{old}}}(y_i|x, y_{<i})}$$
  平衡序列级稳定性与细粒度信用分配

- **MCLA (Monte-Carlo Log-probability Averaging)**：针对 MoE 训练不稳定，通过 $K=8$ 次前向传播平均 log-probability：
  $$\bar{\log} \pi(a) = \frac{1}{K} \sum_{k=1}^{K} \log \pi^{(k)}(a)$$
  显著降低重要性权重方差，结合 IcePop 实现稳定训练

### 5. 实验评估结果
在 KwaiEnv 平台上进行四维评估：

**多脚手架编码**（Table 2）：
- SWE-bench Verified：Claude Code 脚手架下 **79.6%**（vs Claude Opus 4.6 的 80.8%），OpenCode 下 **74.8%**（vs 75.0%）
- 展现跨脚手架稳定性能

**代理任务执行**（Table 3）：
- PinchBench：Best Score **88.7**，超越 GLM-5（86.4）与 MiniMax M2.7（87.1）
- Claw-Eval：Pass@3 为 55.6%（略低于 Claude Opus 4.6 的 66.3%）

**前端美学**（Table 4）：
- Landing Page **59.8**、Slides **57.6**、Data Visualization **67.6**，均领先于 GLM-5 与 Kimi K2.5

**通用任务**（Table 5）：
- Terminal-Bench Hard **46.8**（最优）、$\tau^2$-Bench Telecom **93.9**、AA-LCR 68.0、IFBench 67.0

### 6. 结论与局限
KAT-Coder-V2 验证了**领域专精训练 + 大规模 Agentic RL + 统一蒸馏**的技术路径有效性，在多数基准上接近或超越 Claude Opus 4.6。当前局限包括 Claw-Eval 等高压代理执行场景仍存在差距，未来工作将聚焦于进一步扩展 Agentic RL 规模、自动化美学评估体系，以及将 Specialize-then-Unify 范式拓展至更广泛的代理领域。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
