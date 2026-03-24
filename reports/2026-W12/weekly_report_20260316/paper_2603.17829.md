# CodeScout: An Effective Recipe for Reinforcement Learning of Code Search Agents

**arXiv**: [2603.17829](https://arxiv.org/abs/2603.17829) · [PDF](https://arxiv.org/pdf/2603.17829)  
**领域**: Agent  
**作者**: Sutawika, Soni, R, Gandhi, Yassine, Vijayvargiya, Li, Zhou 等 11 人  
**综合评分**: 8.50  （novelty: 8.5 · method: 8.5 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为CodeScout的强化学习配方，用于训练代码搜索智能体。该方法的核心创新在于，仅使用标准Unix终端作为工具，通过精心设计的强化学习训练方案，即可在代码定位任务上取得优异性能，超越了更大规模的基座模型和部分闭源模型。作者团队来自卡内基梅隆大学（CMU）等知名机构，其中Graham Neubig教授是该领域知名学者。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

该研究旨在解决**代码仓库级别的代码定位（Code Localization）**问题，即在给定GitHub问题描述和代码仓库的情况下，自动识别需要修改的相关文件、类和函数的具体位置。

具体而言，该论文针对以下核心挑战：

**1. 现有代理方法的工具复杂性**
先前基于代理的代码搜索方法通常依赖复杂的专用工具，例如基于静态分析构建的仓库依赖图（如LocAgent）、Python语言服务器（如RepoNavigator的"jump"工具）或特定语言的AST解析器。这些工具不仅增加了工程开销，而且通常局限于特定编程语言（主要是Python），难以扩展到其他语言。

**2. 通用性与性能的权衡**
虽然通用编码代理通常仅配备标准Unix终端（bash工具），但此前研究表明，未经专门训练的大型语言模型（LLM）在使用简单终端进行代码定位时表现极差（接近零F1分数），导致高成本、错误修复和代码膨胀。

**3. 强化学习训练方案的设计**
该研究的核心科学问题是：**在给定适当强化学习算法的前提下，如何设计有效的训练方案，使仅配备标准Unix终端的简单代理能够达到甚至超越使用专用复杂工具的代理的定位精度？**

为解决上述问题，本文提出了CODESCOUT——一种基于Group Sequence Policy Optimization（GSPO）的强化学习方案，通过精心设计的数据筛选、奖励函数（基于多粒度F1分数）和训练策略，证明即使仅使用`ripgrep`、`sed`等基础Unix命令，也能在SWE-Bench系列基准上实现与18倍参数量的开源模型相当、甚至接近Claude Sonnet等闭源前沿模型的性能。

## 相关工作

该论文在第2节（Related Work）及相关表格中详细讨论了以下相关研究方向与具体工作：

### 1. 代码定位方法范式

**基于嵌入的检索（Embedding-based Retrieval）**
- 传统方法依赖向量数据库进行语义代码搜索，如使用BM25或向量相似度匹配（Xia et al., 2024; Wang et al., 2025b; Xie et al., 2025a）

**基于代理的代码搜索（Agentic Code Search）**
- 近期研究转向使用代理迭代导航仓库以 uncover 必要证据，而非单次检索

### 2. 专用工具与脚手架（Specialized Scaffolds）

| 方法 | 核心工具/技术 | 局限性 |
|------|-------------|--------|
| **LocAgent** (Chen et al., 2025) | 基于AST的代码图导航（TraverseGraph, RetrieveEntity） | 需预索引代码图，依赖Python特定解析器 |
| **CoSiL** (Liu et al., 2025a) | 动态构建模块调用图（module-call graphs）和函数调用图 | 依赖语言特定静态分析构建调用关系 |
| **OrcaLoca** (Yu et al., 2025) | 基于优先级的代码图探索与距离感知上下文剪枝 | 需预索引，工具数量多（5个） |
| **RepoSearcher** (Ma et al., 2025) | 专用检索工具（GetImportOfFile, SearchClassMethod等） | 依赖语言特定静态分析，使用RFT从闭源模型蒸馏 |
| **RepoNavigator** (Zhang et al., 2026) | "jump"工具（基于Python语言服务器解析符号定义） | 虽使用RL训练，但仍依赖Python语言服务器和静态分析 |

### 3. 模型训练策略

**拒绝采样微调（Rejection Sampling Fine-Tuning, RFT）**
- **LocAgent** 和 **RepoSearcher** 采用此方法：从闭源模型（如Claude-3.5-Sonnet, Claude-3.7-Sonnet）采样轨迹，筛选成功轨迹训练开源模型
- 局限：依赖昂贵的闭源模型进行数据策展

**直接强化学习（Direct RL Post-training）**
- **RepoNavigator**：直接使用RL训练，但需筛选"简单"样本（仅保留基础模型至少一次成功的实例）
- **CODESCOUT**（本文）：首个公开演示仅使用标准Unix终端、无需闭源模型蒸馏的纯RL训练方案

### 4. 通用编码代理框架

- **OpenHands** (Wang et al., 2025a)：软件代理SDK，提供基础工具（终端、浏览器等）
- **SWE-Agent** (Yang et al., 2024)：针对软件工程任务的代理框架
- **Claude Code** (Anthropic, 2025)：工业级代理编码助手

### 关键对比总结

与上述工作相比，**CODESCOUT**的核心差异在于：
- **语言无关性**：仅使用标准Unix终端（bash），无需语言特定的静态分析工具（AST解析器、依赖图等）
- **工具极简性**：仅需1个工具（终端），而LocAgent/OrcaLoca需3-5个专用工具
- **训练独立性**：直接通过RL训练，不依赖闭源LLM进行蒸馏（与RepoSearcher不同），也不需预筛选简单样本（与RepoNavigator不同）

## 解决方案

论文通过提出 **CODESCOUT** —— 一个基于强化学习的代码搜索代理训练配方——来解决上述问题。该方案包含以下四个关键组件：

---

### 1. 数据与环境策展（Data and Environment Curation）

**真实目标提取**  
从SWE-Smith等数据集中的GitHub问题（$I$）和真实PR补丁（$P$）中提取定位目标，定义三个粒度：
- **文件级**：$F^\star = \{f^\star_1, \dots, f^\star_{N_f}\}$（被修改的文件集合）
- **模块级**：$M^\star = \{m^\star_1, \dots, m^\star_{N_m}\}$（被修改的类/模块）
- **函数级**：$U^\star = \{u^\star_1, \dots, u^\star_{N_u}\}$（被编辑的函数/方法）

**环境构建**  
- 克隆PR提交前的仓库状态作为RL环境
- **关键简化**：由于定位任务无需执行代码，不安装项目依赖或使用容器化，仅需标准Linux文件系统
- 代理通过终端工具与预定义工作目录交互

---

### 2. 极简代理脚手架（OpenHands-Bash）

**工具设计**  
采用OpenHands SDK构建仅含两个工具的脚手架：
- **`Terminal`**：标准Unix终端，支持`rg`（ripgrep）、`find`、`ls`、`cat`、`sed`等命令
- **`LocalizationFinish`**：结构化输出工具，强制要求代理以JSON格式提交预测的文件、模块和函数列表

**关键创新**  
- **语言无关性**：不依赖Python AST解析器或代码图，仅通过Unix命令探索任何语言的仓库
- **避免奖励噪声**：早期实验发现字符串格式输出易因解析失败产生噪声奖励，改用结构化工具强制schema验证，提升奖励信号保真度

---

### 3. 奖励函数设计（Reward Design）

**核心奖励（多粒度F1）**  
对于轨迹$\tau$和预测输出$y = (F, M, G)$，计算与真实值$y^\star$的F1分数：
$$r(\tau, y, y^\star) = r_{\text{F1-file}} + r_{\text{F1-module}} + r_{\text{F1-func}}$$

**辅助奖励（防止训练崩溃）**  
针对CODESCOUT-14B观察到的训练崩溃（代理耗尽步数不提交预测），添加二进制奖励：
$$r_{\text{turn}}(\tau, k) = \mathbb{1}[\text{agent terminates in exactly } k \text{ turns}]$$
鼓励代理在步数限制$k$内及时终止（$k=4$对于14B模型）。

---

### 4. 强化学习训练算法（RL Training）

**后端与算法**  
- 使用**SkyRL**框架支持异步训练（并行化轨迹生成与权重优化）
- 采用**GSPO（Group Sequence Policy Optimization）**，并做以下关键修改（遵循Dr. GRPO）：
  - 移除KL正则化项
  - 从优势计算中移除标准差标准化：$\hat{A}_i = r_i - \text{mean}(r)$
  - 禁用熵损失
  - 对未完成轨迹（未调用finish工具）屏蔽损失

**异步训练机制**  
- 参数更新在收集足够轨迹后触发，允许使用最多$t=4$步陈旧的检查点生成轨迹
- 每次优化后同步权重到vLLM推理引擎

**分阶段训练策略**  
- **4B/14B模型**：直接从Qwen3基础模型使用GSPO训练
- **1.7B模型**：由于基础模型表现极差（接近零F1），先使用**拒绝采样微调（RFT）**从CODESCOUT-14B蒸馏4K成功轨迹，再在此基础上进行RL训练

---

### 关键协同效应

该配方的有效性依赖于组件间的协同：
1. **极简工具**（仅终端）使环境搭建成本极低，支持大规模并行RL训练
2. **结构化输出**确保奖励信号准确反映定位质量而非格式遵从
3. **多粒度F1奖励**直接优化文件、模块、函数三个层面的精确率和召回率
4. **异步GSPO**使长上下文（32K-50K tokens）多轮交互的训练在计算上可行

实验表明，该配方使仅配备终端的1.7B参数模型超越配备复杂工具（RepoNavigator）的7B模型，14B模型达到与Claude-Sonnet-4.5相当的性能。

## 实验验证

该论文在三个标准基准上进行了系统的对比评估，并辅以深入的消融分析与行为研究，具体包括：

---

### 1. 主实验：基准测试与对比评估

**评估基准**  
在以下三个具有代表性的GitHub issue定位数据集上进行评估：
- **SWE-Bench Verified**（500实例）
- **SWE-Bench Pro**（266个Python实例，更具挑战性）
- **SWE-Bench Lite**（300实例）

**评估指标**  
针对三个粒度计算实例级平均 **F1分数**（精确率与召回率的调和平均），并分别报告精确率（Prec.）和召回率（Rec.）：
- 文件级（File-level）
- 模块/类级（Module-level）
- 函数级（Function-level）

**对比基线**  
- **闭源大模型**：GPT-5、Claude-3.7-Sonnet、Claude-Sonnet-4.5（分别配备RepoNavigator复杂脚手架或OpenHands-Bash极简脚手架）
- **开源大模型+专用工具**：RepoNavigator（7B/14B/32B）、RepoSearcher、LocAgent、OrcaLoca、CoSiL、Agentless（基于Qwen2.5-32B）
- **基础模型**：Qwen3系列（1.7B至32B）直接使用OpenHands-Bash作为零样本基线

**关键结果**（详见Table 2-5）：
- **参数效率**：CODESCOUT-1.7B（经RFT+RL训练）性能超越8倍大的Qwen3-14B基础模型，并与RepoNavigator-7B相当；CODESCOUT-4B/14B超越18倍大的Qwen3-32B（Thinking）及32B参数量的RepoNavigator。
- **脚手架简化优势**：在相同OpenHands-Bash脚手架下，GPT-5与Claude-Sonnet-4.5的性能显著优于其配备RepoNavigator（专用符号跳转工具）的版本，表明复杂脚手架对前沿模型可能产生负面影响。
- **闭源模型差距缩小**：CODESCOUT-14B在函数级F1上超越Claude-3.7-Sonnet（配备RepoNavigator）5-8%，且整体逼近Claude-Sonnet-4.5（配备OpenHands-Bash）的性能。

---

### 2. 消融实验：强化学习算法选择（Appendix E）

为验证训练配方的鲁棒性，以Qwen3-4B-Instruct为基础，在SWE-Bench Pro上对比了四种无值网络策略梯度算法：
- **GSPO**（本文采用）：序列级重要性采样比、紧裁剪（$\epsilon=3\text{e-}4/4\text{e-}4$）、无优势标准化
- **GRPO**（DeepSeekMath）：令牌级PPO裁剪、优势标准化
- **Dr.GRPO**：令牌级裁剪、长度无偏损失归约、无标准化
- **SAPO**：软门控函数替代裁剪

**发现**：各算法在文件级F1（47-55%范围）和函数级F1（22-25%范围）表现相近，表明代码定位任务对特定RL算法选择不敏感，但GSPO配置在各粒度上综合表现最优。单因素消融显示，长度无偏归约（Dr.GRPO所用）与GSPO结合会导致性能显著下降（文件级F1从54.83%降至42.02%）。

---

### 3. 应用实验：对下游任务的影响（Section 6.1）

验证精准定位对实际软件工程任务的增益，使用OpenHands Agent SDK在SWE-Bench Verified上进行issue修复实验：
- **模型**：Qwen3-4B-Instruct 与 Qwen3-Coder-30B-A3B-Instruct
- **三阶段设置**：（1）无定位辅助（Vanilla）；（2）注入CODESCOUT-14B预测位置；（3）注入真实位置（Oracle）

**结果**（Table 6）：
- **性能提升**：4B模型修复率从13.40%提升至17.20%（+3.8%）；30B模型从45.20%提升至46.00%（+0.8%），Oracle设置下进一步提升至52.00%。
- **效率优化**：使用CODESCOUT定位后，4B模型平均步数减少2.18步，输入/输出token分别减少17.46%和6.71%，证实精准定位可显著降低下游代理的探索开销。

---

### 4. 行为分析：工具使用演变（Section 6.2）

通过解析训练过程中各检查点的轨迹日志，分析CODESCOUT-4B与14B的Unix命令使用分布（Figure 3）：
- **收敛现象**：尽管初期使用多种工具（`grep`, `find`, `wc`, `cat`等），随着RL训练进行，工具使用急剧收敛。
- **极简策略**：CODESCOUT-14B最终仅依赖 **`ripgrep (rg)`** 与 **`sed`**；CODESCOUT-4B主要依赖 **`rg`, `cat`, `sed`, `xargs`**。
- **启示**：有效代码定位仅需极小子集的Unix工具即可实现，为安全敏感场景下的进一步脚手架简化提供了依据。

## 未来工作

基于论文的局限性讨论（Appendix D）及实验发现，以下方向值得进一步探索：

---

### 1. 跨编程语言的泛化
当前训练与评估主要局限于Python仓库，原因在于：
- 缺乏大规模多语言训练数据（如Multi-SWE-Bench虽包含多语言issue，但主要用于评估）
- ground truth提取依赖语言特定的补丁处理脚本（AST解析等）

**探索路径**：开发语言无关的ground truth提取方法，或构建覆盖Java、C++、Go等语言的训练数据集，验证bash-only脚手架在多语言场景下的零样本迁移能力。

---

### 2. Ground Truth定义的完善
现有方法仅从PR补丁的**修改区域**提取定位目标（$F^\star, M^\star, U^\star$），可能遗漏：
- 需要理解但未修改的关键依赖文件
- 仅阅读即可获取上下文的辅助代码位置

**探索路径**：建立更全面的相关性标注标准（如人工标注或基于程序依赖分析），训练模型识别"需修改"与"需参考"的不同类别，提升下游任务的理解深度。

---

### 3. 扩展至广义仓库级代码搜索
当前任务聚焦于issue修复前的代码定位，未覆盖：
- 仓库级代码问答（如SWE-QA, CodeAssistBench）
- 跨文件语义搜索与代码摘要
- 架构理解与重构建议

**探索路径**：将CODESCOUT的训练范式迁移至问答和检索增强生成（RAG）场景，探索其在代码知识图谱构建、跨模块依赖分析等任务中的适用性。

---

### 4. 极致简化的工具脚手架
第6.2节的分析表明，训练后的模型仅依赖**2-5个核心命令**（`rg`, `sed`, `cat`等）即可有效工作。

**探索路径**：设计"最小可行工具集"（如仅保留`ripgrep`和文件读取），甚至探索**单工具**（仅grep或仅文件浏览器）的极限性能，为安全敏感环境（如沙箱化代理）提供部署方案。

---

### 5. 与Issue修复的端到端联合训练
当前采用两阶段分离范式（先定位，再修复），存在级联误差。

**探索路径**：构建**端到端RL训练框架**，将定位奖励与修复成功率联合优化：
- 设计复合奖励函数：$r = \alpha \cdot r_{\text{localization}} + \beta \cdot r_{\text{patch correctness}}$
- 探索定位与编辑动作的交替执行策略，而非先定位后提交

---

### 6. 精细化的奖励塑形
当前奖励基于F1分数的粗粒度匹配，未考虑：
- 代码间的依赖权重（如核心模块vs边缘工具函数）
- 定位路径的效率（搜索步数与信息增益的权衡）

**探索路径**：引入基于代码图距离的加权F1，或设计中间奖励（如发现关键线索文件时给予部分奖励），加速早期训练收敛。

---

### 7. 长上下文与计算效率优化
训练使用32K-50K tokens的长上下文，计算成本高昂。

**探索路径**：
- 探索**分层记忆机制**：先快速检索文件列表，再加载特定代码块，减少每步上下文长度
- 研究**推测性执行**：预测可能的代码位置并提前加载，减少多轮交互中的重复I/O开销

---

### 8. 安全可控的工具使用
虽然极简脚手架降低了攻击面，但`bash`工具仍存在风险（如`rm -rf`或数据外泄）。

**探索路径**：在RL训练中引入**安全约束奖励**（如禁止特定命令模式），或采用形式化验证确保生成的bash命令在给定白名单内，实现功能性与安全性的帕累托最优。

## 总结

该论文针对**仓库级代码定位**（repository-level code localization）任务，提出了一种基于强化学习的极简代理训练方案 **CODESCOUT**，证明了无需复杂语言特定工具，仅通过标准Unix终端即可实现先进的定位性能。

### 1. 研究背景与问题
代码定位是编码代理解决GitHub issue的关键前置步骤：给定问题描述和代码仓库，识别需修改的文件、类和函数。现有方法存在以下局限：
- **工具复杂性**：LocAgent、RepoNavigator等方法依赖AST解析、代码依赖图或语言服务器等语言特定工具，工程开销高且难以跨语言扩展。
- **训练依赖性**：RepoSearcher等方法需从闭源模型（Claude/GPT）蒸馏数据，成本高昂。
- **基线性能缺陷**：通用LLM（如Qwen3-14B）直接使用终端进行定位时性能接近零，需专门训练。

### 2. CODESCOUT 方法
论文提出了一套完整的强化学习训练配方，包含四个核心组件：

**数据与环境策展**
- 从SWE-Smith等数据集的GitHub issue和PR补丁中提取三粒度真实标签：文件集（$F^\star$）、模块/类集（$M^\star$）、函数/方法集（$U^\star$）。
- 构建轻量化RL环境：仅需克隆仓库，无需安装依赖或容器化，显著降低训练开销。

**极简代理脚手架（OpenHands-Bash）**
- 仅配备两个工具：**Terminal**（标准Unix命令：rg, find, cat, sed等）和 **LocalizationFinish**（结构化输出结果）。
- 语言无关设计：不依赖Python特定解析器，通过bash命令探索任何语言的仓库。
- 结构化输出：避免字符串格式导致的奖励噪声，确保奖励信号准确反映定位质量。

**奖励设计**
- 核心奖励：三粒度F1分数之和（文件、模块、函数）。
$$r(\tau, y, y^\star) = r_{\text{F1-file}} + r_{\text{F1-module}} + r_{\text{F1-func}}$$
- 辅助奖励：针对14B模型的训练崩溃，添加二进制奖励 $r_{\text{turn}}$ 鼓励在规定步数内及时终止。

**强化学习算法**
- 采用 **GSPO（Group Sequence Policy Optimization）** 并做关键修改（移除KL正则化、优势标准化和熵损失）。
- 异步训练框架（SkyRL）：并行化轨迹生成与权重优化，支持长上下文（32K-50K tokens）多轮交互。
- 分阶段训练：1.7B模型先经拒绝采样微调（RFT）从14B模型蒸馏，再进行RL；4B/14B模型直接RL训练。

### 3. 实验结果
在SWE-Bench Verified、Pro和Lite上的评估表明：

**性能超越大体量模型**
- CODESCOUT-1.7B（经RFT+RL）性能超越8倍大的Qwen3-14B基础模型，与7B参数量的RepoNavigator相当。
- CODESCOUT-4B/14B性能超越18倍大的Qwen3-32B（Thinking）及32B参数量的RepoNavigator，在函数级F1上超越Claude-3.7-Sonnet（配备专用工具）5-8%。

**极简工具的有效性**
- GPT-5和Claude-Sonnet-4.5在使用OpenHands-Bash（仅终端）时，性能反而优于配备RepoNavigator（专用符号跳转工具）的版本，表明复杂脚手架对前沿模型可能产生负面影响。

**下游任务增益**
- 将CODESCOUT-14B的定位结果注入issue修复代理（OpenHands），使Qwen3-4B-Instruct的修复率从13.40%提升至17.20%，同时减少17.46%的输入token和6.71%的输出token。

### 4. 关键发现与贡献
- **工具使用收敛**：训练过程中，模型工具使用急剧收敛至极简集合——14B模型最终仅依赖`ripgrep (rg)`和`sed`；4B模型主要使用`rg`, `cat`, `sed`, `xargs`。
- **语言无关性**：首个公开演示仅使用标准Unix终端、无需语言特定静态分析或闭源模型蒸馏的纯RL代码定位方案。
- **参数效率**：证明通过有效RL训练，小模型（1.7B-14B）可在代码定位任务上匹配或超越大得多的基础模型和专用工具方法。

论文公开发布了模型权重、代码和数据，为社区构建更强的代码代理提供了可扩展的基线。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
