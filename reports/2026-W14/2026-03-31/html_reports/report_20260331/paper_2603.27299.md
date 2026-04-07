# From Inference Routing to Agent Orchestration: Declarative Policy Compilation with Cross-Layer Verification

**arXiv**: [2603.27299](https://arxiv.org/abs/2603.27299) · [PDF](https://arxiv.org/pdf/2603.27299)  
**领域**: Agent  
**作者**: Chen, Liu, He, Liu  
**综合评分**: 7.74  （novelty: 9.0 · method: 9.5 · evidence: 7.0 · clarity: 8.0）

---

## 摘要

> 本文提出了一种从推理路由扩展到智能体编排的声明式策略编译方法，通过非图灵完备的策略语言实现了跨层验证的统一策略管理。论文将原有的语义路由器DSL从单请求推理路由扩展到多步骤智能体工作流，实现了从推理网关到智能体编排再到基础设施部署的全链路策略统一。该方法在可审计性、成本效率、可验证性和可调性四个支柱上建立了理论基础，并识别了各层的验证边界。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

这篇论文试图解决**LLM应用从单请求推理路由扩展到多步骤智能体工作流时的策略一致性与可验证性问题**。

具体而言，论文识别了以下核心问题：

### 1. 策略层与编排层的割裂
现有的智能体编排框架（如LangGraph、Temporal、CrewAI、OpenClaw）擅长状态管理、故障恢复与控制流，但将**策略决策**（如选择哪个模型、是否允许工具调用、路由逻辑是否穷尽）留给临时的Python函数实现。这导致：
- 缺乏对路由逻辑穷尽性、分支冲突的静态分析
- 策略决策分散在应用代码中，难以审计与验证

### 2. 跨层策略漂移
在典型部署中，推理网关、智能体编排、协议边界（MCP/A2A）、基础设施（Kubernetes/网络策略）通常由不同团队用不同语言维护：
- 阈值变更（如越狱检测阈值从0.8改为0.75）需要在多个独立制品中手动同步
- 这种跨团队协调是**策略漂移（policy drift）**的主要来源，导致不同层面对同一内容的策略不一致

### 3. 多步骤工作流中的策略缺口
现有Semantic Router DSL已用于生产环境的**单请求、无状态**推理路由，但现代LLM应用涉及：
- 多步骤模型调用链
- MCP工具调用与A2A代理间委托
- 工具响应的间接提示注入防护
这些场景需要在每个步骤点进行内容感知的策略决策，但缺乏统一的声明式策略语言。

### 核心解决方案
论文提出通过**多目标编译（multi-target compilation）**解决上述问题：将非图灵完备的Semantic Router DSL从单一推理路由目标，扩展到四个层次：
1. **推理路由**：现有的YAML网关配置
2. **智能体编排**：LangGraph决策节点/OpenClaw策略包
3. **协议边界**：MCP/A2A内容感知门控
4. **基础设施**：Kubernetes NetworkPolicy/Sandbox CRD、YANG/NETCONF配置

通过单一`.sr`源文件编译到所有目标，确保信号定义、阈值、验证保证（穷尽性、无冲突性、引用完整性）在跨层间自动保持一致，消除策略漂移的协调负担。

## 相关工作

论文在第8节"Related Work"中系统梳理了相关研究，可分为以下五个类别：

### 1. LLM路由与成本优化
这些研究关注如何智能选择模型以平衡质量与成本，但采用**学习型**而非**声明式**方法：

- **RouteLLM** (Ong et al., 2024)：基于偏好数据训练路由器，使用 learned routers
- **FrugalGPT** (Chen et al., 2023)：级联从廉价到昂贵的模型
- **HybridLLM** (Ding et al., 2024)：学习质量-成本边界进行查询路由
- **Dekoninck et al. (2025)**：统一路由与级联方法

**与本文关系**：这些方法是互补的——学习型路由器可作为DSL中的Signal，而DSL提供围绕它们的验证层（验证穷尽性、无冲突性）。

### 2. 策略语言与授权框架
- **NetKAT** (Anderson et al., 2014) 与 **ProbNetKAT** (Foster et al., 2016)：提供网络策略的代数基础，通过限制表达力实现可判定分析。DSL借鉴其设计理念，但针对LLM路由信号而非数据包头部谓词。

- **OPA** (Open Policy Agent)、**Cedar** (AWS, 2023)、**XACML** (Turkmen et al., 2017)：评估基于元数据的精确谓词（crisp predicates）。DSL扩展此模型至**概率谓词**（信号返回$[0,1]$分数），引入ProbPol的Voronoi归一化解决共触发问题。

### 3. 防火墙决策图与冲突检测
- **Firewall Decision Diagrams (FDD)** (Gouda and Liu, 2007)：检测优先级规则集中的冲突
- **Al-Shaer and Hamed (2004)**：防火墙策略建模与管理

DSL的Decision_Tree编译为FDD，而ProbPol在此基础上扩展了三种**概率冲突类型**（传统防火墙文献未涉及）。

### 4. 配置验证
- **Xu et al. (2016)**：通过跨引用系统制品检测配置错误  
DSL编译器执行类似的跨制品检查：Network端点与Route信号对齐、工具配置文件与声明技能兼容、生成的Kubernetes资源与路由策略一致。

### 5. 声明式代理流水线
- **PayPal's Declarative Agent Pipeline Language** (PayPal AI Research, 2025)：声明式定义多步骤代理工作流，关注步骤组合与工具编排。  
**区别**：该语言关注步骤编排（orchestration），而本文DSL关注步骤内的**策略决策**（policy decisions within each step）。两者可结合：流水线语言定义步骤顺序，DSL在每个步骤边界提供验证门控。

### 6. 代理安全与运行时隔离
- **OWASP LLM Top 10** (2025)：识别过度授权（LLM08）和提示注入（LLM01）为主要威胁
- **Kubernetes agent-sandbox** (Kubernetes SIG Apps, 2026)：提供运行时隔离  
DSL与这些互补：沙箱提供运行时隔离，DSL添加内容感知门控和结构化审计追踪，共同应对OWASP推荐的基础设施和语义层安全。

### 7. 作者前期工作（基础）
- **Transparent Discrete Networks** (Chen et al., 2026a)：DSL信号-投影-决策流程与离散前馈网络同构，支持符号程序优化
- **ProbPol** (Liu et al., 2026)：概率ML谓词的冲突检测形式化，提出三级可判定性层次与Voronoi归一化
- **Workload-Router-Pool (WRP)** (Chen et al., 2026b)：将DSL定位为WRP架构中Router支柱的策略语言

## 解决方案

论文通过**多目标编译（multi-target compilation）**架构解决策略一致性与可验证性问题。核心机制是将非图灵完备的Semantic Router DSL从单一推理路由层扩展到四个目标层，通过编译时验证确保跨层一致性。

## 1. 核心架构：单一源文件，多目标发射

解决方案的基础是**声明式策略编译（declarative policy compilation）**：

- **单一源文件（`.sr`）**：管理员在DSL中定义信号（Signals）、决策树（Decision_Tree）、阈值和路由逻辑。DSL刻意限制为非图灵完备（无可变状态、无副作用、无循环），以支持静态分析。
- **多目标编译器**：同一源文件编译为四类制品（图2）：
  1. **推理路由**：YAML网关配置（已有）
  2. **智能体编排**：LangGraph决策节点 / OpenClaw策略包
  3. **协议边界**：MCP/A2A内容感知门控函数
  4. **基础设施**：Kubernetes NetworkPolicy/ConfigMap、YANG/NETCONF配置

- **跨层一致性机制**：所有 emitted 制品携带相同的`source-hash`标签（如`94b69c9d`），确保在推理网关、LangGraph节点、K8s ConfigMap和NETCONF配置中的策略逻辑同源。

## 2. 分层编译策略

### 2.1 编排层：策略节点嵌入（§3.3）

**LangGraph（图编排框架）**：
编译器提供三种策略映射（表2）：
- **策略A（条件边）**：DSL生成信号评估节点（计算所有Signal分数并写入图状态）和条件边函数（读取分数并返回目标节点名）
- **策略B（命令返回节点）**：单节点原子操作，同时完成信号评估、决策树遍历、审计追踪写入和路由跳转
- **策略C（完整工作流中的策略门）**：在开发者编写的工作流骨架中，编译器填充策略检查点

**OpenClaw（网关平台）**：
将DSL编译为五类配置制品（JSON + TypeScript钩子）：
1. 代理定义（`agents.list`）：从`Agent`和`Deploy`块提取模型、沙箱模式
2. 路由绑定：从`Decision_Tree`生成分层绑定表
3. 工具策略：从`Network`端点生成`tools.allow/deny`列表
4. 会话发送策略：将安全信号阈值（越狱、PII）编译为消息过滤规则
5. 插件钩子代码：生成`before_tool_call`函数，内嵌与LangGraph相同的信号评估逻辑

### 2.2 协议边界层：传输-策略分离（§3.4）

在MCP（Model Context Protocol）和A2A（Agent2Agent）协议边界，编译器生成**门控函数（gate function）**：
- 从协议消息中提取文本（MCP的`tools/call`参数、A2A的文本部分）
- 评估与其他层相同的Signal定义（越狱、PII、意图嵌入）
- 返回`allow/deny`及结构化审计追踪

关键特性：协议处理传输（transport），DSL添加策略评估点。新增协议仅需消息提取适配器，复用现有策略评估逻辑。

### 2.3 基础设施层：跨制品引用完整性（§3.5）

**Kubernetes**：
从`Network`和`Deploy`块生成三类制品，并执行交叉引用检查：
- **NetworkPolicy**：根据声明的端点生成出口规则
- **ConfigMap**：包含结构化路由策略（信号阈值、模型引用）
- **Sandbox CRD**：带`PolicySpec`注解的运行时沙箱配置

编译时验证：Agent块中引用的技能必须匹配Network端点；信号评估模型必须有网络访问权限；后端地址必须可达。

**YANG/NETCONF**：
- 生成RFC 7950 YANG模块（信号类型作为identity，阈值为decimal64约束）
- 打包为NETCONF `` payload
- 三阶段验证：（i）结构模式验证（YANG）→（ii）DSL语义冲突检查 →（iii）NETCONF payload良构性

## 3. 验证保证：编译时静态分析（§4.3）

编译器在发射前执行五项跨目标检查，这些检查在ad-hoc Python代码中难以维持：

1. **穷尽性（Exhaustiveness）**：`Decision_Tree`必须包含`Else`分支，确保所有输入都有决策路径
2. **死分支检测**：检查非Else分支的条件是否被高优先级分支严格包含（阴影检测）
3. **信号共触发预防**：对`Signal_Group`应用Voronoi归一化（softmax + 温度缩放），保证至多一个信号触发
4. **引用完整性**：所有信号、后端、技能引用必须解析成功
5. **跨编译一致性**：同一`Decision_Tree`在所有目标中产生相同逻辑，通过结构哈希验证

**可判定性分层**（基于ProbPol）：
- **精确谓词**（授权、关键词）：完全可判定
- **几何谓词**（嵌入相似度）：在嵌入空间假设下可判定
- **分类器谓词**（越狱、PII）：仅支持经验验证（测试块）

## 4. 解决策略漂移的具体机制（§5.4）

**问题场景**：传统方式下，网关团队维护YAML、应用团队维护LangGraph代码、基础设施团队维护K8s清单，阈值变更需要跨团队协调。

**DSL解决方案**：
- **单一参数变更**：在`.sr`文件中修改阈值（如`threshold: 0.75`→`0.70`）
- **一键重编译**：触发完整重新验证（<1秒）
- **原子传播**：同一阈值自动注入到：
  - 推理网关路由配置
  - LangGraph条件边函数
  - OpenClaw的`before_tool_call`钩子和会话策略
  - MCP/A2A协议门控
  - Kubernetes ConfigMap和NetworkPolicy注解
  - YANG/NETCONF payload

**漂移检测**：所有制品嵌入的`source-hash`支持自动化漂移检测——若运行时发现某层制品的哈希与源不一致，可立即告警。

## 5. 与编排框架的互补关系（§2.2, §5）

关键设计原则：**DSL不替代编排框架，而是添加策略层**。

- **编排框架负责**：步骤排序、重试、状态管理、容错（LangGraph的Pregel执行模型、Temporal的持久执行）
- **DSL负责**：每个决策点的验证（穷尽性、无冲突性、可审计性）

这种分层使各层专注于其擅长领域：Python/TypeScript处理复杂业务逻辑和状态机，DSL处理需要形式化保证的策略决策。

## 实验验证

这篇论文是**定位论文（position paper）**，而非实证研究论文。作者在第7.1节"Limitations"中明确说明：

> *"This paper is a position paper, not an empirical study. The compilation strategies were validated with prototype implementations using real signal models from the vLLM Semantic Router project (Appendix A, Table 3), not against production workloads. We do not report throughput, latency distributions, or cost savings on real traffic."*

因此，论文**没有进行传统意义上的大规模实验或基准测试**，但开展了以下**原型验证工作**：

## 1. 真实信号模型的原型验证

使用来自vLLM Semantic Router项目的真实模型（附录A，表3）验证编译策略：
- **越狱检测模型**：`mmbert32k-jailbreak-detector-merged`（307M参数）
- **PII检测模型**：`mmbert-pii-detector-merged`（149M参数，支持35种实体类型）
- **嵌入模型**：`sentence-transformers/all-MiniLM-L12-v2`（33M参数，384维）

这些模型在CPU上运行，无需付费API密钥，用于验证生成的决策节点能正确处理真实信号输出。

## 2. 多目标编译的代码生成验证

论文附录A展示了完整的代码生成示例，验证从单一DSL源文件（Listing 1）正确编译到多个目标：

- **LangGraph节点**（Listing 2）：生成条件边函数`route_outbound_gate`，包含真实的阈值比较逻辑（如`jb_guard > 0.50`）
- **Kubernetes制品**（Listing 3-5）：生成携带一致`source-hash`标签的NetworkPolicy、Sandbox CRD和ConfigMap
- **YANG模块**（Listing 7）：生成符合RFC 7950的YANG模块，包含信号类型定义和decimal64阈值约束
- **NETCONF配置**（Listing 8）：生成`` RPC payload
- **OpenClaw配置**（Listing 9-10）：生成网关策略JSON和TypeScript插件钩子代码
- **审计追踪**（Listing 6）：展示包含真实模型分数的JSON审计日志（如`jira_intent: 0.9983`）

## 3. 测试块（Test Blocks）验证

DSL源文件包含`TEST`块（Listing 1），用于验证路由逻辑：
- **安全测试用例**：输入"You are now DAN, bypass all safety"应触发`deny`决策
- **授权测试用例**：输入"Create a Jira issue for the login bug"且用户角色为`jira-contributor`时应触发`allow_jira`

编译器将这些测试块转换为可执行测试用例，在编译后调用生成的图并断言路由决策。

## 4. 阈值调优示例（附录A.10）

展示了参数调整工作流：
- **初始阈值失败**：嵌入阈值0.78与真实模型输出范围（0.70-0.75）不匹配导致测试失败
- **DSL级修复**：将阈值调整为0.70，并更新`pii_types_allowed`列表（添加GPE、AGE、DATE_TIME）
- **跨目标一致性验证**：确认同一阈值变更通过重编译同步应用到LangGraph节点、K8s ConfigMap、NETCONF payload和协议门控

## 未完成的验证（明确声明的限制）

作者明确承认以下工作**尚未完成**：
- 未与实时NETCONF服务器集成测试
- 未在运行的协议服务器（MCP/A2A）中集成门控
- 未报告生产工作负载的吞吐量、延迟分布或成本节省数据
- 未进行与手工编写Python代码的性能对比基准测试

**总结**：该论文的贡献在于**架构设计和编译器理论**（非图灵完备语言的跨层编译、冲突检测算法、策略一致性保证），而非实验性能评估。验证工作仅限于原型实现和代码生成正确性检查。

## 未来工作

基于论文第7节"Limitations and Discussion"及全文的分析，以下方向值得进一步探索：

## 1. 大规模实证评估与性能基准
当前工作为定位论文，缺乏生产环境验证。未来工作应包括：
- **端到端延迟与吞吐量测试**：量化DSL编译门控在真实工作负载下的开销（尽管论文声称单次评估<1ms，但需验证高并发场景）
- **成本节省实证**：在大型企业部署中测量"正确路由"带来的实际成本降低（如小模型路由比例、工具调用阻断率）
- **策略漂移检测的有效性**：量化跨团队维护与DSL统一编译在策略一致性上的差异

## 2. 编译时模型依赖的离线化解决方案
当前嵌入信号的冲突检测需在编译时访问嵌入模型计算向量距离（§7.1），这与典型CI/CD流程冲突。可探索：
- **离线校准通道（offline calibration pass）**：预计算嵌入空间的几何约束并序列化为可验证的"签名"文件，供编译器在无模型环境下使用
- **向量数据库集成**：利用预先构建的嵌入索引进行边界检查，避免实时模型调用

## 3. 框架无关的中间表示（Framework-Agnostic IR）
当前针对LangGraph和OpenClaw的编译策略各异（§7.1）。可设计一个**策略中间表示层**，将DSL AST降维为与具体编排框架无关的决策图（decision graph），再由各框架后端（LangGraph、Temporal、CrewAI、AutoGen）分别发射为原生代码。这类似于机器学习中的MLIR或ONNX思路。

## 4. 运行时校准与信号可信度监控
论文明确区分了"决策可审计性"与"信号准确性"（§4.1, §7.2）。未来可集成：
- **在线校准监控**：运行时跟踪分类器（越狱、PII）的置信度分布漂移，触发阈值自动调整
- **因果追踪（causal tracing）扩展**：Chen et al. (2026a) 提出的因果追踪可进一步与DSL编译器集成，实现"哪个信号→哪个阈值→哪个误路由"的自动诊断

## 5. 加密审计链与合规增强
附录提到的加密哈希链（§7.2）仅处于提案阶段：
- **默克尔树（Merkle tree）结构**：为审计日志生成不可篡改的证据链，满足GDPR Article 22和EU AI Act Article 86的强合规要求
- **跨层日志关联**：将推理网关、编排节点、协议门控的审计条目通过共享哈希链接成完整证据链

## 6. 复杂DSL构造的子图生成
表2指出以下构造尚未映射到编排目标，需要**多节点子图生成**技术：
- `Algorithm confidence`：置信度驱动的模型级联（可能需要循环结构）
- `Algorithm rl_driven`：强化学习驱动的动态路由（需与RL策略网络集成）
- `Plugin semantic_cache`：语义缓存层（需添加检索-验证节点对）
- `Plugin rag`：检索增强生成（需嵌入向量存储访问节点）

## 7. 信号组平局消解策略的语义形式化
Signal_Group在等距输入时的softmax平局问题（§7.5）目前有三种临时解决方案。可形式化为：
- **确定性平局消解算子**：定义声明顺序（declaration order）的代数语义
- **概率性平局处理**：引入微小噪声的随机舍入策略，并验证其对穷尽性的影响

## 8. 与学习型路由器的深度集成
虽然论文提到RouteLLM等学习型路由器可作为DSL中的Signal（§8），但具体实现需解决：
- **概率谓词与神经网络的接口**：将RouteLLM的输出概率映射到DSL的布尔阈值逻辑，同时保持可验证性
- **在线学习安全**：当路由器参数更新时，如何在不重新编译整个策略的情况下验证新参数不会引入冲突

## 9. 渐进式图灵完备扩展
DSL的非图灵完备性是保证可验证性的基础，但某些复杂工作流需要状态依赖循环（§7.8）。可探索：
- **安全嵌入模式（safe embedding mode）**：允许在DSL中调用外部Python函数，但通过类型系统限制其副作用（纯函数约束），保持核心决策树的非图灵完备性
- **分层验证**：外层编排（允许循环）与内层策略（DSL验证）的严格边界形式化

## 10. 上游标准集成
- **Kubernetes PolicySpec CRD**：推动agent-sandbox项目（Kubernetes SIG Apps, 2026）采纳论文提出的CRD扩展，实现NetworkPolicy的自动生成与沙箱控制器的原生集成
- **协议标准化**：将MCP/A2A的DSL门控模式提案为协议扩展（如`mcp-policy-gate`扩展头），使内容感知策略成为多代理协议的一等公民

## 总结

这篇论文将**语义路由器DSL（Semantic Router DSL）**从单一推理网关扩展到完整的智能体工作流栈，通过声明式策略编译解决跨层策略一致性与可验证性问题。

## 1. 核心问题

现代LLM应用已从单请求推理演进为多步骤智能体工作流（LangGraph、MCP工具调用、A2A代理间委托），但面临三重割裂：
- **策略与编排割裂**：编排框架（LangGraph、Temporal）管理控制流，但内容感知决策（选模型、工具授权）依赖临时Python代码，缺乏静态验证
- **跨层策略漂移**：推理网关、智能体逻辑、协议边界、基础设施通常由不同团队维护，阈值变更（如越狱检测从$0.8$调至$0.75$）需手动同步，导致不一致
- **验证边界模糊**：现有方案无法保证路由穷尽性、分支冲突检测和结构化审计

## 2. 解决方案：多目标编译架构

提出**声明式策略编译（declarative policy compilation）**：单一`.sr`源文件通过编译器生成四类制品（图1）：

| 目标层 | 生成制品 | 策略注入点 |
|--------|---------|-----------|
| **推理路由** | YAML网关配置 | 每请求模型选择、隐私路由 |
| **智能体编排** | LangGraph决策节点 / OpenClaw策略包 | 工作流内步骤级路由、工具调用前授权 |
| **协议边界** | MCP/A2A门控函数 | 工具调用参数检查、代理间消息过滤 |
| **基础设施** | NetworkPolicy、ConfigMap、YANG/NETCONF | 网络 egress 规则、运行时沙箱策略 |

所有制品携带相同`source-hash`（如`94b69c9d`），确保阈值变更在单次编译中原子传播到全栈，消除策略漂移。

## 3. 关键机制

### 3.1 非图灵完备策略语言
DSL刻意限制为**无状态、无副作用、无循环**，仅含五类块：Signal（概率分类器，输出$[0,1]$）、Route（优先级规则）、Decision_Tree（编译为防火墙决策图FDD）、Signal_Group（softmax归一化防共触发）、Backend（模型/动作目标）。这种受限表达力支持可判定验证。

### 3.2 三层验证边界
基于ProbPol理论构建可判定性层次：
- **精确谓词**（授权、关键词）：编译时完全可判定
- **几何谓词**（嵌入相似度）：在向量空间假设下可判定  
- **分类器谓词**（越狱、PII）：仅支持经验验证（Test块）

### 3.3 跨制品引用完整性
编译器执行五项跨目标检查：
1. **穷尽性**：Decision_Tree必须含Else分支，确保$\forall x \in \text{Input}, \exists! \text{ leaf}$
2. **死分支检测**：识别被高优先级分支阴影覆盖的不可达条件
3. **信号共触发预防**：Signal_Group通过Voronoi归一化（温度缩放softmax）保证至多一个信号触发
4. **引用完整性**：信号、后端、技能引用必须解析成功
5. **跨编译一致性**：结构哈希验证各目标逻辑等价

## 4. 四支柱分析

- **可审计性**：审计追踪与决策AST同源生成，支持"信号分数→阈值穿越→分支选择"的完整因果链，满足GDPR Article 22和EU AI Act要求
- **成本效率**：通过内容感知路由将简单请求导向小模型（如4B vs 70B），并在工具调用前拦截违规请求，避免无效API开销
- **可验证性**：编译时静态分析替代运行时调试，显式区分"编译器保证"与"需经验验证"的边界
- **可调优性**：阈值、优先级作为一等声明，变更触发即时重验证（<1秒），形成"调整-编译-验证-部署"的闭环MDP

## 5. 与编排框架的互补关系

DSL**不替代**Python/TypeScript编排逻辑，而是**嵌入验证层**：
- **LangGraph**：编译为条件边函数或Command节点，与图状态通道集成，审计追踪使用append-only reducer防篡改
- **OpenClaw**：生成五类配置（代理定义、路由绑定、工具策略、会话策略、插件钩子），实现网关级策略与内循环代理的隔离

## 6. 局限与适用条件

作为定位论文，当前工作**未包含**生产工作负载的吞吐量/成本基准测试，且YANG/NETCONF集成、协议门控仍需上游标准采纳。

该方案在以下场景价值最大：
- 多代理、多环境部署，具备合规要求（审计、数据隐私）
- 策略复杂度高（信号数量$\times$制品数量$\times$团队规模）的长期演进项目

对于单一模型、无合规要求的短期项目，DSL的额外抽象层可能过度设计。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
