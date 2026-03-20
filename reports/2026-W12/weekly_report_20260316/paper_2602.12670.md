# SkillsBench: Benchmarking How Well Agent Skills Work Across Diverse Tasks

**arXiv**: [2602.12670](https://arxiv.org/abs/2602.12670) · [PDF](https://arxiv.org/pdf/2602.12670)  
**领域**: Agent  
**作者**: Li, Chen, Liu, Zheng, Chen, He, Li, You 等 41 人  
**综合评分**: 8.36  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了SkillsBench，一个用于评估Agent Skills在多样化任务中有效性的基准测试。该研究首次系统性地量化了结构化程序知识包对LLM智能体的影响，填补了该领域缺乏标准化评估工具的空白。通过涵盖11个领域的86个任务、精心设计的Skills和确定性验证器，研究揭示了Skills在不同领域的效果差异（从软件工程的+4.5pp到医疗健康的+51.9pp），并发现模型无法可靠地生成它们能从中受益的程序知识。实验规模大（7,308条轨迹），结论具有实证基础。

---

## 详细分析

> **社区热度**: ⭐ 27 (来自 papers.cool)

## 问题定义

这篇论文试图解决**Agent Skills（代理技能）缺乏系统性评估基准**的问题。具体而言，论文针对以下几个核心问题：

**1. 效果衡量缺失**
尽管Agent Skills（结构化的程序知识包，用于在推理时增强LLM代理）已被快速采用，但业界缺乏标准方法来客观衡量它们是否真正提升了代理性能，以及提升的程度如何。

**2. 作用机制不明**
现有研究未能系统回答：
- Skills在什么情况下、以何种方式改善代理性能
- 哪些内容组件（指令、代码模板、示例等）对性能提升贡献最大
- 什么设计原则能区分有效与无效的Skills

**3. 自生成Skills的可行性存疑**
论文指出，模型能否可靠地编写它们从中受益的程序性知识（即自我生成Skills）尚属未知，这需要通过对比"人工策划Skills"与"模型自生成Skills"的效果来验证。

**4. 领域与模型差异性**
不同领域（如软件工程 vs. 医疗健康）和不同模型-代理配置对Skills的响应差异巨大，但此前缺乏大规模实证数据来揭示这些差异的模式。

为应对这些挑战，论文提出了**SKILLSBENCH**——首个将Skills视为一等评估工件的基准测试，通过84个跨领域任务、7种模型-代理配置和7,308条轨迹的对比实验（无Skills、人工策划Skills、自生成Skills三种条件），系统性地量化了Skills的效用边界与设计原则。

## 相关工作

这篇论文的相关研究主要涵盖以下三个方向：

## 1. 代理基准测试（Agent Benchmarks）

现有基准测试主要评估端到端的代理能力：
- **TerminalBench** (Merrill et al., 2026)、**SWE-bench** (Jimenez et al., 2024) 及其后续工作 (Yang et al., 2024; 2025) 专注于命令行界面和软件工程任务
- **AgentBench** (Liu et al., 2023)、**WebArena** (Zhou et al., 2024b)、**VisualWebArena** (Koh et al., 2024)、**OSWorld** (Xie et al., 2024) 覆盖交互式、网页和GUI环境
- **MLE-bench** (Chan et al., 2025)、**CyBench** (Zhang et al., 2024)、$\tau$-bench (Yao et al., 2025)、**BigCodeBench** (Zhuo et al., 2025) 等强调工具介导工作流、交互执行反馈或领域专业化

**关键区别**：这些基准测量的是"固定代理如何完成任务"，而SKILLSBENCH通过配对评估（paired evaluation）测量"增强策略（Skills）对任务性能的提升效果"。

## 2. 程序性增强与工具使用（Procedural Augmentation and Tool Use）

先前工作探索了通过结构化推理或外部知识增强代理：
- **认知架构**：CoALA和Voyager (Sumers et al., 2023; Wang et al., 2023a) 提出语言代理的认知架构
- **推理方法**：Chain-of-thought (Wei et al., 2022)、ReAct (Yao et al., 2022; 2023)、Tree of Thoughts (Yao et al., 2023)、Reflexion (Shinn et al., 2023)、Self-Refine (Madaan et al., 2023)、LATS (Zhou et al., 2024a) 用于多步问题解决
- **检索与工具**：RAG (Lewis et al., 2020)、DocPrompting (Zhou et al., 2022)、Toolformer (Schick et al., 2023)、ToolLLM (Qin et al., 2024) 实现检索增强和工具使用
- **声明式框架**：DSPy (Khattab et al., 2023) 提供声明式优化框架

**关键区别**：尽管存在多种增强方法，但现有基准很少量化它们对代理性能的实际影响，而SKILLSBENCH专门评估Skills（结合程序性指导和可执行资源）的效果。

## 3. Skills生态系统与评估方法

- **规范与协议**：Anthropic的Agent Skills规范 (Anthropic, 2025a) 和MCP (Model Context Protocol) (Anthropic, 2024) 正式定义了技能包和工具连接标准
- **商业代理工具**：Claude Code (Anthropic, 2025b)、Gemini CLI (Google, 2025)、Codex CLI (OpenAI, 2025) 提供了现实世界的代理工具（harnesses）
- **评估方法**：MLPerf (Mattson et al., 2020)、Chatbot Arena (Chiang et al., 2024)、HELM (Srivastava et al., 2023) 等推动了严谨的报告和可比性实践

**关键区别**：SKILLSBENCH同时评估商业工具和基于Terminal-Bench的模型无关工具，以分离模型和工具效应，并引入标准化增益（normalized gain）指标来比较不同基线下的改进。

## 解决方案

论文通过构建**SKILLSBENCH**基准测试框架来解决上述问题，具体方法论包括以下五个层面：

### 1. 设计Skills中心的三条件对比实验
针对"效果衡量缺失"和"自生成Skills可行性"问题，论文设计了对照实验框架，每个任务均在三种条件下执行：
- **无Skills基线**：仅提供任务指令，评估模型原生能力
- **人工策划Skills**：提供结构化的程序知识包（SKILL.md + 资源文件）
- **自生成Skills**：提示模型自主生成程序知识后再执行任务

通过配对比较（paired comparison），直接量化Skills的边际效应（average +16.2pp），同时验证模型无法可靠自生成有效Skills（平均-1.3pp）。

### 2. 构建跨领域、可复现的任务集
针对"领域差异性"问题，论文构建了包含**84个任务**、覆盖**11个领域**（软件工程、医疗健康、制造业、金融、网络安全等）的基准集，并按难度分层（Core/Extended/Extreme）。每个任务采用容器化环境（Docker），配备：
- **确定性验证器**：基于pytest的程序化断言，避免LLM-as-judge的方差
- **防泄漏审计**：确保Skills提供程序性指导而非具体任务解答
- **人类时间估算**：建立任务难度与专业人类完成时间的映射关系

### 3. 大规模多配置实证评估
针对"模型差异性"问题，论文评估了**7种模型-代理配置**（Claude Code/Gemini CLI/Codex CLI × 不同规模模型），生成**7,308条有效轨迹**。通过标准化增益（Normalized Gain）指标：
$$g = \frac{\text{pass}_{\text{skill}} - \text{pass}_{\text{vanilla}}}{1 - \text{pass}_{\text{vanilla}}}$$
实现跨模型、跨基线的改进幅度可比性，揭示Skills效应的高度变异性（范围：+13.6pp至+23.3pp）。

### 4. 组件化Skills设计与消融分析
针对"作用机制不明"问题，论文通过以下实验分离Skills设计要素：
- **数量效应**：对比1/2-3/4+个Skills的影响，发现2-3个模块最优（+18.6pp），过多导致认知过载（+5.9pp）
- **复杂度效应**：将Skills分为Compact/Detailed/Standard/Comprehensive四类，证实简洁聚焦的指导优于详尽文档（Comprehensive Skills实际损害性能-2.9pp）
- **领域分解**：按领域统计增益，识别出Healthcare（+51.9pp）与Software Engineering（+4.5pp）的极端差异，揭示程序性知识在预训练覆盖不足领域的最大价值

### 5. 轨迹级失败模式分析
建立基于验证器输出的程序化失败分类体系（Timeout/Execution/Coherence/Verification），通过对比"有/无Skills"的失败分布，发现Skills主要将**Verification失败**（质量不达阈值）降低30.8%，而非解决结构性理解问题。这种细粒度分析揭示了Skills的效用边界：它们改善的是"知道如何做但做不好"的任务，而非"完全不知道如何做"的任务。

## 实验验证

论文开展了系统性实验，可分为**主评估实验**、**Skills设计消融实验**、**失败模式分析**和**成本效率分析**四个层面：

### 1. 主实验：跨模型-代理配置的Skills效用评估（Experiment 1）

**实验设计**
- **配置**：7种模型-代理组合（Claude Code × 4模型：Opus 4.5/4.6、Sonnet 4.5、Haiku 4.5；Gemini CLI × 2模型：Pro/Flash；Codex × GPT-5.2）
- **条件**：3种Skills条件（无Skills基线、人工策划Skills、自生成Skills），其中自生成条件在5个配置上测试
- **规模**：84个任务 × 5次运行 = 7,308条有效轨迹（self-generated条件为3次运行）

**核心发现**（见Table 3, Table 10）：
- **Curated Skills**平均提升**+16.2pp**（范围：+13.6pp至+23.3pp），标准化增益$g=21.5\%$
- **Self-generated Skills**平均效果**-1.3pp**，仅Opus 4.6显示微弱正收益(+1.4pp)，Codex显著下降(-5.6pp)
- **性能巅峰**：Gemini 3 Flash + Skills达到**48.7%**通过率；Claude Opus 4.5 + Skills提升幅度最大(**+23.3pp**)

**领域差异分析**（见Table 4）：
- 跨11个领域差异巨大：Healthcare (**+51.9pp**)和Manufacturing (**+41.9pp**)受益最大
- Software Engineering (**+4.5pp**)和Mathematics (**+6.0pp**)受益最小，反映预训练数据覆盖度与Skills效用的负相关关系

**任务级差异**：
- 最大受益任务：`mario-coin-counting`、`sales-pivot-analysis`（均+85.7pp）
- **16/84个任务**（19%）显示负收益，如`taxonomy-tree-merge`(-39.3pp)，表明Skills可能引入冲突性指导

### 2. Skills设计因素消融实验（Experiment 2）

**Skills数量实验**（见Table 5）：
| Skills数量 | 有Skills通过率 | 无Skills通过率 | 绝对提升 |
|-----------|--------------|--------------|---------|
| 1个       | 42.2%        | 24.4%        | +17.8pp |
| **2-3个** | **42.0%**    | **23.4%**    | **+18.6pp** |
| 4+个      | 32.7%        | 26.9%        | +5.9pp  |

发现呈现**非单调关系**：2-3个模块最优，过多Skills导致认知过载。

**Skills复杂度实验**（见Table 6）：
将Skills按文档长度和细节程度分为四类：
- **Detailed**（详细）：+18.8pp（1,165个样本）
- **Compact**（紧凑）：+17.1pp（845个样本）  
- **Standard**（标准）：+10.1pp（773个样本）
- **Comprehensive**（全面）：**-2.9pp**（140个样本）

证实"少即是多"：聚焦的程序指导优于 exhaustive 文档。

**模型规模补偿效应**：
Claude Haiku 4.5（小模型）+ Skills（27.7%）**超越**Claude Opus 4.5（大模型）无Skills（22.0%），证明Skills可部分替代模型容量。

### 3. 轨迹级失败模式分析（Appendix I）

对5,171次代理失败进行程序化分类（基于CTRF验证器输出）：

**失败分布**（见Table 15）：
- **Verification失败**（质量不达阈值）：49.8% — 最常见
- **Timeout**（超时）：17.8%
- **Incomplete Solution**（不完整）：10.2%
- **Execution失败**（实现错误）：17.7%

**Skills对失败模式的影响**（见Table 17）：
- 主要减少**Verification失败**（从1,184例降至819例，-30.8%）
- 减少**Coherence失败**（不完整解决方案-35.8%）
- 但增加Timeout相对比例（因代理尝试更复杂策略）

**未解决任务分析**：
16个任务（19%）在所有条件下均失败，分为三类：
1. 计算不可行（如引力波检测、量子模拟）
2. 复杂多步管道（如仓库分析、行人计数）
3. 严格规范任务（如LaTeX公式提取）

### 4. 成本效率与Token分析（Appendix H）

**Token使用**（见Table 12）：
- Skills平均增加输入Token **6-13%**（额外上下文开销）
- Gemini 3 Flash采用"迭代探索"策略：输入Token为Pro的2.3倍（1.08M vs 0.47M），但推理深度更低

**成本分析**（见Table 13，基于2026年2月API定价）：
- **Gemini 3 Flash**：$0.57/任务（+Skills仅+$0.03）
- **Gemini 3 Pro**：$1.06/任务（Skills实际降低成本-$0.07，因效率提升）
- **GPT-5.2 (Codex)**：$2.07/任务（+12%成本）

**Pareto前沿分析**（见Figure 4）：
Skills将所有模型推向**成本-性能前沿上方**，Gemini 3 Flash和Claude Opus主导with-Skills前沿。小模型+Skills可实现大模型无Skills的性能水平，但成本降低44%（Flash $0.55 vs Pro $0.98）。

## 未来工作

基于论文的局限性与讨论，以下研究方向值得进一步探索：

## 1. 扩展评估边界

**多模态与GUI环境**
当前基准专注于基于终端的容器化任务。未来需开发支持视觉-语言代理的多模态Skills协议，评估其在GUI环境（如操作系统、网页浏览器）中的效用，并探索Skills在**多代理协调**和**超长时程工作流**（multi-step pipelines spanning hours or days）中的表现。

**生态系统代表性评估**
当前基准采用高质量（top quartile）Skills，代表乐观场景。需评估**生态系统代表性样本**（平均质量6.2/12的Skills）和**自动检索/选择**的Skills，以反映真实世界的不完美匹配情况。

## 2. 因果机制与对照实验

**长度匹配基线**
现有实验未完全分离"程序性结构"与"更多上下文"的效应。需引入更强的对照：
- 随机/不相关文本（random text）
- 仅检索式文档（retrieval-only documentation）
- 事实性知识对照（declarative vs. procedural knowledge）

**组件分解**
系统性地隔离Skills各组件的贡献：
- 自然语言指令 vs. 代码模板 vs. 工作示例
- 前置步骤（setup instructions）vs. 验证逻辑（verification logic）
- 不同格式的Skill规范（如MCP vs. Agent Skills规格）

## 3. Skills自动生成与优化

**改进的自生成方法**
当前结果显示自生成Skills平均无效（-1.3pp）。需探索：
- **迭代精炼**：结合执行反馈的自我改进（类似Self-Refine）
- **检索增强生成**：从文档/StackOverflow检索后生成 procedural knowledge
- **少样本示例**：提供高质量的Skill编写示例作为上下文

**自动合成管道**
从演示轨迹（demonstration trajectories）或现有文档（API docs, tutorials）自动提取并结构化Skills，验证其相对于人工策划Skills的性能差距。

## 4. Skills组合与交互

**组合效应预测**
研究多个Skills的**协同与干扰**效应：
- 当任务需要2-3个Skills时，是否存在最优组合顺序？
- 能否从原子Skills效应预测组合性能（compositional performance）？
- 识别导致负收益（16/84任务）的冲突机制（如指令矛盾、上下文过度负载）

**动态Skills管理**
开发运行时Skills选择机制：
- 基于任务描述的**自适应检索**（adaptive retrieval）
- 基于执行反馈的**动态加载/卸载**（dynamic loading）
- 与RAG结合的**混合增强**（Skills + factual retrieval）

## 5. 跨模型迁移与标准化

**跨 harness 兼容性**
当前结果显示不同代理工具（Claude Code vs. Gemini CLI vs. Codex）对相同Skills的利用率差异显著。需开发**模型无关的Skills规范**和转换工具，测试Skills在跨平台迁移时的鲁棒性。

**小模型+Skills的极限**
验证"Skills补偿模型规模"的边界：
- 在哪些任务复杂度阈值上，小模型+Skills无法匹配大模型？
- 是否存在"Skills依赖"现象（过度依赖外部知识导致基础推理能力退化）？

## 6. 领域特定优化

**高增益领域深挖**
Healthcare（+51.9pp）和Manufacturing（+41.9pp）显示巨大潜力。需构建**领域专用Skills库**，并研究：
- 临床工作流（clinical workflows）的标准操作程序编码
- 制造业约束优化与设备维护的Skills设计模式

**负收益任务诊断**
针对16个Skills损害性能的任务（如taxonomy-tree-merge -39.3pp），开展**错误模式根因分析**（root cause analysis），开发**元认知Skills**（meta-Skills）帮助代理识别何时应忽略或修改提供的指导。

## 总结

该论文提出了**SKILLSBENCH**，首个系统性评估Agent Skills（增强LLM代理的程序性知识包）效用的基准测试。核心内容包括：

## 研究背景与问题
Agent Skills作为推理时增强LLM代理的模块化方案（包含指令、代码模板、工作流等）已被广泛采用，但缺乏标准方法衡量其实际效果、设计原则及失效模式。现有基准仅评估原始模型能力，无法回答"特定Skills对任务性能的提升幅度"这一关键问题。

## SKILLSBENCH框架
- **任务集**：84个跨11个领域的容器化任务（软件工程、医疗、制造、金融等），配备确定性验证器
- **三条件对比**：每个任务在"无Skills"、"人工策划Skills"、"自生成Skills"三种条件下执行
- **规模**：7种模型-代理配置（Claude Code、Gemini CLI、Codex CLI × 不同规模模型），共7,308条有效轨迹

## 核心发现

**1. Skills效用具有显著异质性**
- 人工策划Skills平均提升**+16.2个百分点**，但范围跨度大（+4.5pp到+51.9pp）
- 医疗（+51.9pp）和制造（+41.9pp）等预训练覆盖不足的领域受益最大；软件工程（+4.5pp）受益最小
- **16/84个任务**出现负收益，表明Skills可能引入冲突性指导

**2. 自生成Skills无效**
模型自主生成的程序性知识平均导致**-1.3pp**性能下降，仅有个别模型显示微弱改善。证明有效Skills需要人工策划的领域专业知识，模型无法可靠地编写其从中受益的知识。

**3. 设计原则：少即是多**
- **数量**：2-3个Skills模块最优（+18.6pp），4个以上Skills导致收益锐减（+5.9pp）
- **复杂度**：简洁聚焦的Detailed/Compact Skills（+17-19pp）显著优于全面详尽的Comprehensive Skills（-2.9pp）

**4. Skills可部分替代模型规模**
小模型（Claude Haiku 4.5）配备Skills（27.7%）可超越大模型无Skills（Claude Opus 4.5，22.0%）的表现，为资源受限场景提供可行路径。

## 结论与意义
论文确立了Skills效能评估的标准化方法（配对比较+标准化增益），证明Skills并非万能增强器，其效果高度依赖于任务领域、代理工具实现和Skills设计质量。研究为Skills的甄选、设计和部署提供了实证基础，强调**简洁的程序性指导**优于** exhaustive 文档**，并倡导将配对评估作为代理增强研究的标准实践。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
