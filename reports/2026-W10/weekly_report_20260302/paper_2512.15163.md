# MCP-SafetyBench: A Benchmark for Safety Evaluation of Large Language Models with Real-World MCP Servers

**arXiv**: [2512.15163](https://arxiv.org/abs/2512.15163) · [PDF](https://arxiv.org/pdf/2512.15163)  
**领域**: Agent  
**作者**: Zong, Shen, Wang, Lan, Yang  
**综合评分**: 8.43  （novelty: 9.0 · method: 9.0 · evidence: 8.5 · clarity: 7.5）

---

## 摘要

> 本文提出了一种针对大型语言模型（LLM）在真实世界工具调用场景下的安全评估基准MCP-SafetyBench。该工作针对新兴的模型上下文协议（MCP）生态，首次构建了一个基于真实MCP服务器的综合性安全基准，覆盖了浏览器自动化、金融分析、位置导航、仓库管理和网络搜索五个关键领域，并系统性地定义了20种攻击类型。论文通过该基准评估了主流开源和闭源LLM，揭示了模型在MCP环境下的普遍脆弱性及安全与效用的权衡，为诊断和缓解真实部署中的安全风险奠定了基础。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

论文旨在填补现有评估体系在“真实世界 Model Context Protocol（MCP）场景下大模型智能体安全性”方面的空白。具体而言，它聚焦以下核心问题：

- **MCP 生态的开放性与多服务器工作流引入了新的攻击面**，如工具投毒、上下文链污染、恶意服务器提权等，而既有基准或仅覆盖单点攻击，或脱离真实 MCP 服务器，无法反映多轮、跨服务器、工具链耦合的实际风险。
- **缺乏统一、细粒度且可执行的威胁分类**，导致研究社区对 MCP 漏洞的系统性理解不足。
- **现有评估多为一次性提示或仿真环境**，无法衡量模型在多步推理、跨服务器协调、不确定条件下的持续鲁棒性。

为此，作者提出 **MCP-SafetyBench**：基于真实 MCP 服务器构建的 245 例多轮任务基准，覆盖 20 种统一归类的攻击类型（服务器/主机/用户侧），并配套自动化双标签评估（任务成功 + 攻击成功）。通过系统评测 13 个主流开闭源模型，论文揭示：

1. 所有模型在 MCP 环境中均存在显著漏洞（ASR 29.8%–48.2%）。
2. 任务成功率与防御成功率呈显著负相关（r = −0.57），出现“能力–安全”权衡。
3. 服务器侧攻击占比 74.7%，其中主机侧攻击成功率最高（81.9%），身份注入达 100% 通杀。
4. 简单安全提示仅对显式恶意攻击有效，对语义操控类攻击反而有害，提示级防御不足以应对 MCP 威胁。

综上，论文首次在真实 MCP 部署层面量化了大模型智能体的系统性安全风险，为后续多层防御研究提供诊断基准与方向。

## 相关工作

与 MCP-SafetyBench 直接相关的研究可划分为三类：  
1. Model Context Protocol（MCP）本身的设计与实现；  
2. 针对 MCP 场景提出的攻击向量与漏洞披露；  
3. 评估 MCP 安全风险的现有基准或测试床。  

以下按类别列出代表性文献，并给出与本文的差异点。

---

### 1. MCP 协议与系统框架
| 文献 | 核心贡献 | 与本文关系 |
|---|---|---|
| Anthropic, 2024 – “Introducing the Model Context Protocol” | 首次提出 MCP 三侧架构（Host-Client-Server）与 JSON-RPC 2.0 标准 | 构成本文威胁模型的基础边界 |
| L. Edwin, 2025 – “Model Context Protocol: Solution to AI Integration Bottlenecks” | 综述 MCP 生态（STDIO/SSE 传输、动态工具发现） | 为“真实服务器”选材提供技术背景 |

---

### 2. MCP 攻击向量研究
| 文献 | 攻击类型 | 与本文差异 |
|---|---|---|
| Beurer-Kellner & Fischer, 2025 – Tool Poisoning Attacks | 提出 Tool Poisoning、Shadowing、Rug Pull | 本文将其归入 Server 侧并扩展出 6 种子类 |
| Hou et al., 2025 – “MCP: Landscape, Security Threats, and Future Directions” | 生命周期视角归纳 4 类威胁（配置/交互/终止+沙箱逃逸） | 缺乏实验量化；本文用 245 例真实任务验证 |
| Radosevich & Halloran, 2025 – MCP Safety Audit | 披露恶意代码执行、凭据窃取、远程控制、检索欺骗 | 对应本文 User 侧 4 个攻击类型，但未提供基准 |
| Wang et al., 2025b – Preference Manipulation Attack | 通过工具描述偏向性影响模型选择 | 本文将其单列为 Preference Manipulation 并评测 13 模型 |

---

### 3. MCP 安全基准
| 基准 | 是否基于真实 MCP 服务器 | 多轮/跨服务器 | 覆盖攻击侧 | 攻击类型数 | 与本文差距 |
|---|---|---|---|---|---|
| SafeMCP (Fang et al., 2025) | × | ✓ | Server | 2 | 无真实服务器，仅 10 领域 |
| MCPTox (Wang et al., 2025a) | ✓ | × | Server | 1 | 单轮工具投毒，无 Host/User 侧 |
| MCIP-bench (Jing et al., 2025) | × | × | Server+Host | 10 | 静态函数语料，无真实执行 |
| MCP-AttackBench (Xing et al., 2025) | × | × | Server+User | 10 | 70k 样本但脱离真实服务器 |
| MCPSecBench (Yang et al., 2025b) | × | × | 全三侧 | 17 | 模块化测试床，无多轮任务 |
| **MCP-SafetyBench (本文)** | **✓** | **✓** | **Server+Host+User** | **20** | **真实服务器、245 多轮任务、双标签评估** |

---

### 4.  broader 安全框架
| 文献 | 相关点 |
|---|---|
| OTM (Verma et al., 2025) | 提供 LLM 部署阶段威胁模型；本文将 MCP 攻击映射到其 CIAP 四维度（机密性/完整性/可用性/隐私） |
| Feffer et al., 2024 – Red-teaming for Generative AI | 强调动态工具调用场景的红队方法；本文在真实 MCP 环境落地 |

---

### 小结
现有工作要么聚焦单一攻击类型、要么脱离真实服务器与多轮交互，未能系统量化 MCP 生态的整体风险。MCP-SafetyBench 首次把 20 种攻击统一置于真实服务器、多轮、跨域任务流下进行执行级评估，填补了上述空白。

## 解决方案

论文通过“构建基准 → 系统化威胁建模 → 大规模实证评估 → 暴露缺陷并验证防御极限”四步闭环，解决“真实 MCP 场景下大模型智能体安全性无法被系统度量与改进”的核心问题。具体做法如下：

---

### 1. 构建真实多轮基准 MCP-SafetyBench
- **数据源**：直接复用并改写 MCP-Universe 的 245 个真实服务器任务（金融、导航、仓库、浏览器、搜索），保留原始工具链与运行环境。  
- **攻击注入流水线**（图 3）  
  1. 任务选择：提取 Goal-Context-Tools 三元组与可机检输出模式。  
  2. 攻击实例化：对每条任务精确植入 1 个来自统一分类法的攻击 A，生成对抗版任务 τ = (G,C,Tavailable,A)。  
  3. 形式化打包：输出含攻击元数据、双标签评估脚本、可复现执行环境的完整用例。  
- **规模与分布**  
  - 245 例覆盖 20 种攻击，Server:Host:User ≈ 75%:12%:13%，Stealth vs Disruption ≈ 53%:47%。  
  - 所有任务均支持 ReAct 多轮调用，平均 3–8 轮，跨 2–4 个 MCP 服务器。

---

### 2. 提出三侧 20 类统一威胁分类法
- **Server 侧**（12 类）：Tool Poisoning 6 子类 + Function Overlapping + Preference Manipulation + Tool Shadowing + Function Return Injection + Rug Pull。  
- **Host 侧**（4 类）：Intent Injection, Data Tampering, Identity Spoofing, Replay Injection。  
- **User 侧**（5 类）：Malicious Code Execution, Credential Theft, Remote Access Control, Retrieval-Agent Deception, Excessive Privileges Misuse。  
该分类法将先前零散披露的攻击首次归一化，并指出 7 类此前基准未覆盖的新攻击（表 2 标✓但前人空白）。

---

### 3. 设计双标签、执行式评估协议
- **Task Evaluator**（Etask）：沿用 MCP-Universe 的格式/静态/动态检查，给出 Pass/Fail。  
- **Attack Evaluator**（Eattack**：针对 20 类攻击编写签名检测器（如参数篡改、命令注入、重放、身份伪造等），自动在运行轨迹上输出 Success/Failure。  
- **指标**：  
  - TSR = 任务通过数 / 总数  
  - ASR = 攻击成功数 / 总数  
  - DSR = 1 − ASR  

该协议保证结果可复现、无人工打分偏差，并直接暴露“任务完成”与“被攻击”之间的张力。

---

### 4. 大规模实证评估与发现
- **模型池**：13 个 SOTA（GPT-5 ~ GPT-4o、Claude-4/3.7、Gemini-2.5、Grok-4、GLM-4.5、Kimi-K2、Qwen3-235B、DeepSeek-V3.1）。  
- **环境统一**：temperature=1，max_tokens=2048，单轮超时 60 s，ReAct 最大 20 轮，每任务重复 3 次。  

**关键发现**  
1. 无模型免疫：ASR 29.8%–48.2%，最高 o4-mini 48.2%。  
2. 显著安全–效用权衡：TSR 与 DSR 皮尔逊 r = −0.57 (p = 0.041)。  
3. 域差异：金融分析 ASR 46.6% 最高，Web Search 30.3% 最低。  
4. 攻击侧差异：Host 侧 81.9% > User 侧 39.4% > Server 侧 33.5%；Identity/Intent Injection 达 100%。  
5. 模型无系统优劣：开源 vs 闭源、推理 vs 非推理模型均无显著差异（p > 0.4）。  
6. 防御极限：通用安全提示仅整体 ASR ↓1.22%（不显著），对语义操控类攻击反而↑7–9%，证明纯提示无法解决 MCP  toolchain 耦合威胁。

---

### 5. 给出后续防御方向
- 动态工具行为审查 + 上下文最小权限原则  
- 实时风险分级与自适应降级  
- 将 MCP-SafetyBench 扩展到更多域与长程任务  

通过“真实服务器 + 统一分类 + 双标签执行评估”，论文首次系统量化了 MCP 生态的安全水位，并明确提示级防御的不足，为后续多层防御研究奠定基准与诊断依据。

## 实验验证

论文围绕 **MCP-SafetyBench** 共设计并执行了 **6 组实验**，覆盖模型横向对比、域差异、攻击侧差异、防御有效性、系统复杂度与策略消融，形成从“宏观水位”到“微观机理”的完整证据链。结果均以 **TSR（Task Success Rate）** 与 **ASR（Attack Success Rate）** 双指标报告。

---

### 1. 主实验：13 款 SOTA 模型全基准测评
- **目的**：量化当前 LLM 在真实 MCP 多轮任务中的安全水位。  
- **设置**：245 例任务 × 3 次重复 × 统一 ReAct 配置（temperature=1，max_tokens=2048，20 轮上限，60 s 超时）。  
- **结果**（表 4）：  
  - 无模型免疫：ASR 29.8 %–48.2 %；TSR 8.9 %–21.2 %。  
  - 显著负相关：TSR vs DSR 皮尔逊 r = −0.57（p = 0.041）。  

---

### 2. 域差异分析
- **目的**：验证攻击成功率是否随任务领域变化。  
- **方法**：单因素 ANOVA 与事后 pairwise t-test。  
- **结果**（图 5 + 表 9）：  
  - 金融分析 ASR 46.6 % 显著高于其余域平均 +8.8 %（p < 0.001，d = 1.87）。  
  - Web Search ASR 30.3 % 显著低于平均 −11.5 %（p = 0.0026，d = −0.95）。  

---

### 3. 攻击侧与攻击类型细粒度拆解
- **目的**：找出最危险攻击向量。  
- **方法**：20 类攻击 ASR 单因素 ANOVA + Mann–Whitney U。  
- **结果**（表 10 + 附录 B.3）：  
  - Host 侧平均 ASR 81.9 % > User 侧 39.4 % > Server 侧 33.5 %。  
  - Identity/Intent Injection 达 100 %；Tool Redirection 70.6 %；Network Request Poisoning 仅 7.7 %。  
  - 76.9 % 模型呈现“尖刺型”防御——对某些攻击 0 %，对另一些 100 %。  

---

### 4. 安全提示防御有效性实验
- **目的**：检验通用安全提示能否降低 MCP 攻击成功率。  
- **设置**：将统一 130-token 安全提示 prepend 到用户 query，重复主实验流程。  
- **结果**（附录 C）：  
  - 整体加权 ASR 39.88 % → 38.65 %（−1.22 %，p = 0.29，不显著）。  
  - 对显式恶意攻击有效：Malicious Code Execution −21.5 %，Credential Theft −21.4 %，Remote Access Control −10.8 %（均 p < 0.01）。  
  - 对语义操控有害：Function Overlapping +9.4 %，Preference Manipulation +7.3 %（p < 0.05）。  
  - 闭源模型 7/9 受益，开源 4/4 无效或更差。  

---

### 5. 系统复杂度消融
#### 5.1 服务器数量缩放
- **设置**：金融域任务，分别接入 2/3/4 个 MCP 服务器，GPT-4.1 评估。  
- **结果**（表 11）：  
  - TSR 24.5 % → 30.2 %，ASR 43.4 % → 47.2 %；更多服务器提升任务完成但同步放大风险。  

#### 5.2 误报评估（Decoy Manifest）
- **设置**：仓库域全部 benign 工具描述加入“DANGEROUS”警告，观察模型是否过度防御。  
- **结果**（附录 D.2）：  
  - 零误报：所有模型仍调用工具。  
  - 行为差异：GPT-5/Grok-4 主动设私有，GPT-4.1/Qwen3-235B 无视警告直接默认参数调用。  

---

### 6. 攻击者预算与代理策略消融
#### 6.1 编辑字符长度
- **设置**：金融域 Tool-Poisoning 攻击，注入恶意描述长度 200–700 字符。  
- **结果**（表 12）：  
  - TSR 稳定 40 %；ASR 在 500 字符处峰值 60 %，过长反而降至 50 %（噪声效应）。  

#### 6.2 最大迭代轮数
- **设置**：ReAct 迭代上限 10–30 轮。  
- **结果**（表 13）：  
  - TSR 15–20 轮最佳；ASR 20–30 轮最低；过短规划不足，过长冗余思考均降性能。  

#### 6.3 代理策略对比
- **设置**：Plan-and-Execute vs ReAct，金融域 245 例。  
- **结果**（表 14）：  
  - TSR 25.9 % vs 32.8 %（p = 0.21），ASR 49.8 % vs 45.5 %（p = 0.40），差异不显著；当前安全性能与代理范式无关。  

---

### 7. 补充分析
- **低 TSR 解释实验**（附录 E）：给出 clean 无攻击基线，证明 SOTA 模型在干净环境 TSR 可达 31–46 %，低分主因“安全拒答 + 被攻击失败”，非模型能力缺陷。  

---

综上，论文通过 **13 模型×5 领域×20 攻击×多变量消融** 的立体实验，首次系统量化了 MCP 场景下大模型智能体的安全水位与防御极限，为后续研究提供可复现的实证基准。

## 未来工作

以下方向可视为 MCP-SafetyBench 的“直接延伸”或“深层扩展”，既补足当前基准的固有缺口，也回应实验暴露出的关键痛点。为方便后续研究立项，按“数据-模型-系统-生态”四层归纳，并给出可量化的切入点与预期指标。

---

### 1. 数据与任务层
| 方向 | 切入点 | 可验证指标 |
|---|---|---|
| **长程任务** | 将单会话 20 轮上限扩展到 50–100 轮，引入跨天/跨周持续规划（如投资组合再平衡 + 报表定时推送）。 | 随着轮次增加，ASR 的累积曲线与指数拟合斜率；首次成功潜伏轮数（Stealth Latency）。 |
| **多模态 MCP** | 引入屏幕像素、音频流、摄像头等模态的 MCP 服务器（Android-UI、VLM-visual-retrieval）。 | 跨模态 Tool Redirection / Adversarial Patch 攻击 ASR；模态间权限隔离失败率。 |
| **动态工具更新** | 在任务生命周期中热更新工具描述或代码（Rug Pull++），模拟供应链劫持。 | 更新后 N 轮内 ASR 提升幅度；版本签名/哈希校验对 ASR 的抑制比。 |
| **协作式任务** | 多用户多 Agent 并发调用同一服务器，引入“交叉会话污染”与“竞态条件”攻击。 | 并发度 k 与 ASR 的相关系数；会话间数据泄露率（Cross-session Data Leak Rate）。 |

---

### 2. 模型与算法层
| 方向 | 切入点 | 可验证指标 |
|---|---|---|
| **动态工具审计** | 在 Agent 推理链中插入“工具行为异常检测”小模型（轻量化 BERT/异常检测 GNN），实时阻断可疑调用。 | 相同任务下 ASR ↓Δ%；误拦截率（False Block Rate）；额外延迟 ms。 |
| **上下文最小权限** | 形式化“Contextual Least-Privilege”策略：工具调用前自动裁剪上下文中的敏感键值、降级高权工具。 | Credential Theft ASR ↓Δ%；任务失败率增幅（TSR 损失 ≤3 % 为可接受）。 |
| **对抗训练** | 用 MCP-SafetyBench 的 245 例对抗轨迹做强化学习微调（RLHF-secure），奖励“高 TSR + 低 ASR”组合。 | 微调后模型在保留测试集上的 ASR ↓Δ%；clean TSR 变化；迁移到未见过攻击的泛化 ASR。 |
| **可验证推理** | 将工具规约写成形式化合约（Pre/Post-condition），Agent 生成调用前先由 SMT 求解器验证。 | Command Injection/FileSystem Poisoning ASR → 0 的比例；合约编写人工成本（分钟/工具）。 |

---

### 3. 系统与协议层
| 方向 | 切入点 | 可验证指标 |
|---|---|---|
| **MCP 安全扩展** | 在 JSON-RPC 层增加“权限证明”字段（capability token），服务器需出示零知识证明才能注册高权工具。 | 无证明时高权工具 ASR ↓Δ%；协议往返延迟 ↑ms；Token 分发开销（QPS 损耗）。 |
| **可信执行环境** | 把 MCP Server 放入 SGX/TEE，工具代码与返回数据经远程证明验证。 | Rug Pull + Function Return Injection ASR ↓Δ%；TEE 内存限制导致的任务失败率。 |
| **链式溯源日志** | 为每次工具调用生成可验证日志（Merkle tree + append-only DB），事后审计攻击路径。 | 从攻击成功到溯源定位的平均轮数；日志存储开销（GB/千任务）。 |

---

### 4. 生态与评测层
| 方向 | 切入点 | 可验证指标 |
|---|---|---|
| **在线灰度沙箱** | 建立公共 MCP-SafetyArena：任何人可提交未知服务器，实时对 13 款 LLM 做黑盒攻击测试。 | 新攻击类型发现速率（种/月）；零日漏洞从提交到公开披露的平均天数。 |
| **自动化红队生成** | 用 LLM-self-play 不断改写工具描述与返回 payload，目标是在有限 token 内最大化 ASR（类似 AutoRedteam）。 | 自动生成样本的 ASR 相比人工模板提升 %；生成→通过可行性过滤→成功攻击的转化率。 |
| **安全-效用 Pareto 基准** | 引入多目标评价：以 (TSR, DSR, Latency, Cost) 四维向量构成 Pareto 前沿，推动“可接受任务损失”下的最大防御。 | 新防御算法在前沿上的支配点数；与基线相比的 Hyper-Volume 提升。 |

---

### 5. 伦理与治理
- **隐私保护度量**：量化防御机制本身引入的新隐私风险（如日志泄露用户查询），采用 Differential Privacy 指标 ε。  
- **标准化威胁模型**：推动将本文三侧 20 类攻击纳入 ISO/IEC 27090 系列或 NIST AI RMF 的 MCP 补充章节。  

---

### 可执行的小规模“快速验证”示例
1. 选取金融域 53 例任务，仅增加“动态工具审计”模块（小模型 110 M 参数）。  
2. 目标：Credential Theft ASR 从 55.6 % → 15 % 以内，clean TSR 下降 < 5 %。  
3. 四周周期：两周训练/微调，一周集成，一周在 MCP-SafetyBench 官方脚本跑分。  

---

综上，从“长程-多模态-动态更新”到“形式化验证-可信硬件-生态沙箱”，每层均有明确可量化的指标与实验路径，可直接在 MCP-SafetyBench 的代码框架上迭代，形成持续更新的社区级安全防线。

## 总结

- **问题**：LLM 通过 Model Context Protocol（MCP）调用真实工具时，现有基准缺乏对“多轮、跨服务器、 toolchain 耦合”攻击的系统评估。  
- **方法**：提出 MCP-SafetyBench——基于 245 个真实 MCP 服务器任务、覆盖 20 类统一三侧（Server/Host/User）攻击、支持 ReAct 多轮执行与双标签（任务成功+攻击成功）自动化评测。  
- **实验**：对 13 款 SOTA（GPT-5 到 DeepSeek-V3.1）横向测评，发现  
  – 无模型免疫：ASR 29.8 %–48.2 %；  
  – 显著安全-效用权衡（r = −0.57）；  
  – Host 侧攻击最致命（Identity/Intent Injection 100 %）；  
  – 通用安全提示仅整体 −1.22 %，对语义操控甚至有害。  
- **结论**：给出真实 MCP 部署的量化风险基线，指出动态工具审计、上下文最小权限、形式化合约等多层防御必要性，并开源 benchmark 供社区持续迭代。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
