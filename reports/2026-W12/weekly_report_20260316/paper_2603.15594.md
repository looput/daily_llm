# OpenSeeker: Democratizing Frontier Search Agents by Fully Open-Sourcing Training Data

**arXiv**: [2603.15594](https://arxiv.org/abs/2603.15594) · [PDF](https://arxiv.org/pdf/2603.15594)  
**领域**: Agent  
**作者**: Du, Ye, Tang, Zhu, Lu, Cai, Chen  
**综合评分**: 8.36  （novelty: 9.0 · method: 8.5 · evidence: 9.0 · clarity: 7.5）

---

## 摘要

> 本文提出了一种名为OpenSeeker的完全开源搜索智能体，旨在解决前沿大语言模型智能体开发中高质量训练数据稀缺和封闭的问题。论文通过两项核心技术——基于事实的可扩展可控QA合成和去噪轨迹合成——仅用1.17万个合成样本进行监督微调，便在多个基准测试中达到了最先进的性能，甚至超越了部分工业界竞品。作者团队未明确标注其所属机构，但论文内容显示其工作具有显著的工程和研究价值，旨在推动搜索智能体研究的民主化和透明化。

---

## 详细分析

> **社区热度**: ⭐ 16 (来自 papers.cool)

## 问题定义

这篇论文旨在解决**开源社区在开发高性能搜索代理（Search Agents）时面临的数据稀缺与封闭性问题**，具体表现为以下几个方面：

## 1. 核心问题：训练数据的垄断与封闭

深度搜索能力已成为前沿大语言模型（LLM）代理的关键能力，但该领域长期被工业巨头垄断。这些机构（如OpenAI、Google、Kimi、Minimax等）即使开源了模型权重，也**保留训练数据作为"数据护城河"（data moat）**，导致学术社区无法复现或改进这些高性能代理。

## 2. 具体技术障碍

论文指出了阻碍学术社区发展的两个关键数据瓶颈：

- **缺乏复杂、多跳推理的QA数据**：现有开源数据集往往只包含简单的检索任务，模型可通过表面模式匹配解决，无法迫使代理进行"推理→工具调用→工具响应"的多轮深度交互。

- **缺乏高质量、去噪的轨迹数据**：原始网络内容充满噪声，现有方法难以合成稳定、可复现的长周期工具使用轨迹（trajectories），导致训练信号质量低下。

## 3. 现有开源工作的局限性

论文分析了当前研究社区的困境：
- 部分工作仅开源模型而不开源数据（如WebLeaper）
- 部分工作仅提供极小部分数据（如WebSailor）
- 部分工作因数据保真度不足而无法达到竞争性能（如DeepDive、MiroThinker）

## 4. 解决方案概述

为打破这一僵局，论文提出了**OpenSeeker**——首个完全开源（模型与数据）的搜索代理，通过以下两项技术创新实现前沿级性能：

- **基于事实的可扩展可控QA合成（Fact-grounded scalable controllable QA synthesis）**：通过逆向工程网页图拓扑结构，生成具有可控复杂度、基于真实网络拓扑的多跳推理任务。

- **去噪轨迹合成（Denoised trajectory synthesis）**：采用回顾式摘要机制对轨迹进行去噪，使教师模型生成高质量动作，同时训练学生在原始噪声上下文中学习去噪能力。

通过开源完整的11.7k合成样本数据集及模型权重，该工作旨在**使前沿搜索代理研究民主化（democratize）**，打破工业界对高质量搜索数据的垄断。

## 相关工作

根据论文第2节（Related Work）及附录A的内容，该领域的相关研究可分为以下几类：

## 1. 闭源商业代理（Closed-Source Proprietary Agents）
这类工作代表了当前工业界的前沿水平，但完全封闭训练细节与数据：
- **OpenAI Deep Research** (OpenAI, 2025a)：完全闭源的先驱性工作
- **Kimi-Researcher** (Kimi, 2025)
- **Gemini's Deep Research** (DeepMind, 2025)
- **Perplexity's Deep Research** (Perplexity, 2025)

## 2. 开源权重模型（Open-Weights Models）
近期工业界发布了多款仅开源模型权重、但保留训练数据的搜索代理，形成"数据护城河"：
- **Kimi K2/2.5 series** (Team et al., 2025b, 2026a)
- **Zhipu GLM 4.5-5** (Zeng et al., 2025, 2026)
- **MiniMax M2-2.5** (MiniMax, 2025, 2026)
- **Alibaba's Tongyi DeepResearch** (Team et al., 2025d)：采用持续预训练(CPT)、SFT和RL的复杂流程

## 3. 学术研究社区的框架
学术界近期提出了多个搜索代理框架，但在数据透明度或性能上存在局限：

| 工作 | 主要局限 |
|------|----------|
| **WebDancer** (Wu et al., 2025) | 缺乏公开数据发布 |
| **WebSailor / WebSailor-V2** (Li et al., 2025c) | 仅提供极小部分数据 |
| **WebLeaper** (Tao et al., 2025) | 仅开源模型，数据未公开 |
| **AgentFounder** (Su et al., 2026) | - |
| **DeepDive** (Lu et al., 2025) | 数据保真度不足，性能竞争力有限 |
| **MiroThinker** (MiroMind AI Team, 2025) | 仅开源部分数据 |

## 4. 技术范式基础
- **ReAct范式** (Yao et al., 2023)：当前大多数搜索代理采用的"推理-行动-观察"（Reasoning-Action-Observation）循环架构基础

## 5. 并行工作（Concurrent Works）
论文附录A中讨论了两个同期进行的竞争工作：
- **OpenResearcher** (Li et al., 2025d)：主要通过聚合现有开源数据集并在模拟环境中构建轨迹，使用96k样本进行训练
- **RedResearcher** (Chu et al., 2026)：采用多阶段训练（mid-training、SFT、RL），但仅部分开源SFT和RL数据，且训练协议透明度不足

这些工作的共同特点是**缺乏完整的训练数据开源**，或**依赖企业资源**进行大规模训练，而OpenSeeker旨在通过完全开源数据和纯学术团队开发，打破这一"闭门造车"（closed-door game）的局面。

## 解决方案

论文通过两项核心技术创新解决高质量训练数据的合成与开源问题，具体方案如下：

## 1. 总体框架

将网络建模为有向图 $\mathcal{G} = (\mathcal{V}, \mathcal{E})$，其中 $\mathcal{V}$ 表示网页节点，$\mathcal{E}$ 表示超链接边。目标是合成数据集 $\mathcal{D} = \{(q, y, \tau^*)\}$，包含复杂查询 $q$、标准答案 $y$ 和最优工具使用轨迹 $\tau^*$。

## 2. 基于事实的可扩展可控QA合成（Fact-Grounded Scalable Controllable QA Synthesis）

该技术解决**复杂多跳推理任务生成**的问题，确保查询无法通过简单检索或参数记忆解决。

### 2.1 生成式构建流程

- **图拓扑扩展（Graph Expansion）**  
  从大规模网络语料中采样种子节点 $v_{\text{seed}} \sim \mathcal{V}$，通过遍历出边扩展生成局部依赖子图 $\mathcal{G}_{\text{sub}} = \{v_{\text{seed}}\} \cup \{v_i | (v_{\text{seed}}, v_i) \in \mathcal{E}\}_k$。这种基于真实网络拓扑的方法确保任务具有事实依据（fact-grounded）。

- **实体提取与结构化（Entity Extraction）**  
  从 $\mathcal{G}_{\text{sub}}$ 中提取与中心主题 $y_{\text{theme}}$ 相关的关键实体，构建**实体子图** $\mathcal{G}_{\text{entity}}$。该步骤将原始文本噪声抽象为密集的关系结构，保留逻辑路径的同时去除干扰信息。

- **强制多跳的问题生成（Question Generation）**  
  生成器 $P_{\text{gen}}$ 基于 $\mathcal{G}_{\text{entity}}$ 的结构约束合成初始问题 $q_{\text{init}}$。通过硬性结构约束强制要求：从 $q_{\text{init}}$ 推导 $y_{\text{theme}}$ 必须遍历 $\mathcal{G}_{\text{entity}}$ 中的多条边，从而确保问题需要序列化的多节点演绎推理而非单步检索。

- **实体混淆与问题重构（Entity & Question Obfuscation）**  
  为防止代理利用关键词捷径（keyword shortcuts），应用混淆算子 $\Phi$ 将具体实体 $e$ 映射为模糊描述 $\tilde{e} = \Phi(e)$，构建**模糊实体子图** $\tilde{\mathcal{G}}_{\text{entity}}$。最终问题 $\tilde{q}$ 基于 $\tilde{\mathcal{G}}_{\text{entity}}$ 重构，保持推理逻辑不变但增加消歧难度。

### 2.2 双重标准验证（Dual-Criteria Verification）

通过拒绝采样（rejection sampling）确保数据质量：
- **难度验证（Difficulty Check）**：验证强基座模型在闭卷设置（无外部工具）下无法正确回答，即 $\mathbb{I}[\pi_{\text{base}}(\tilde{q}) \neq y]$。若模型仅凭参数记忆即可回答，则丢弃该样本。
- **可解性验证（Solvability Check）**：验证模型在提供完整实体子图 $\mathcal{G}_{\text{entity}}$ 作为上下文（oracle设置）时能够推导出 $y$，即 $\mathbb{I}[\pi_{\text{base}}(\tilde{q} | \mathcal{G}_{\text{entity}}) = y]$。若逻辑链条断裂或存在幻觉，则拒绝该样本。

## 3. 去噪轨迹合成（Denoised Trajectory Synthesis）

该技术解决**长周期工具使用轨迹中的噪声干扰**问题，确保训练信号稳定且可复现。

### 3.1 动态上下文去噪机制

定义搜索轨迹为 $\tau = [q, (r_1, a_1, o_1), \ldots, (r_T, a_T, o_T), y]$，其中 $r_t$ 为推理步骤，$a_t$ 为动作（工具调用），$o_t$ 为观察（工具响应）。

采用**"摘要化长期历史 + 原始近期上下文"**（Summarized History + Raw Recent）的上下文构建协议：

$$
H_t = \{\underbrace{q, (r_1, a_1, s_1), \ldots, (r_{t-2}, a_{t-2}, s_{t-2})}_{\text{Summarized Long-Term History}}, \underbrace{(r_{t-1}, a_{t-1}, o_{t-1})}_{\text{Raw Short-Term Context}}\}
$$

其中 $s_i = \text{Summarize}(o_i | \text{context})$ 为对历史观察 $o_i$ 的压缩语义摘要。

该机制通过两阶段循环运作：
- **决策阶段**：生成当前决策 $(r_t, a_t)$ 时，提供包含完整原始观察 $o_{t-1}$ 的 $H_t$，确保代理能获取最新观察中的全部潜在信号。
- **压缩阶段**：步骤 $t$ 完成后，回顾式调用摘要器将 $o_{t-1}$ 压缩为 $s_{t-1}$，替换长期历史中的原始观察，用于下一步的 $H_{t+1}$。

### 3.2 非对称上下文训练（Asymmetric Context Training）

为培养模型的鲁棒性，刻意制造合成阶段与训练阶段的**上下文不对称性**：

- **合成阶段（教师模型）**：基于去噪后的干净上下文 $H_t$（含摘要）生成"黄金"推理路径和动作。
- **训练阶段（学生模型）**：剥离摘要，恢复为完整原始上下文：

$$
H_t^{\text{train}} = \{q, (r_1, a_1, o_1), \ldots, (r_{t-1}, a_{t-1}, o_{t-1})\}
$$

学生模型被监督学习去预测教师模型生成的最优 $r_t, a_t$，但条件是基于含噪的原始历史 $H_t^{\text{train}}$。这迫使学生模型**内化去噪与信息提取能力**，学会从噪声中识别关键信号。

## 4. 开源实现

通过上述方法合成11.7k高质量样本（10.3k英文，1.4k中文），基于Qwen3-30B-A3B进行单轮SFT训练。所有合成代码、完整训练数据（含QA对及完整轨迹）及模型权重均完全开源，从根本上解决了数据封闭问题，使学术社区能够复现并改进前沿搜索代理。

## 实验验证

论文在第4节（Experiments）及附录A中进行了系统的实验验证，涵盖实验设置、多维度性能对比及数据分析，具体如下：

## 1. 实验设置

**模型配置**
- 基座模型：Qwen3-30B-A3B-Thinking-2507（30B总参数，3B激活参数）
- 训练方式：单次监督微调（SFT），默认超参数，无启发式过滤或超参数调优
- 训练数据量：11,700条合成样本（10.3k英文 + 1.4k中文）
- 推理限制：最大工具调用次数200次，上下文窗口256k tokens

**评测基准**
- **BrowseComp** / **BrowseComp-ZH**：测试英文/中文多步导航与困难信息定位能力（因资源限制，BrowseComp在200样本子集上评测）
- **xbench-DeepSearch**：评估复杂深度研究能力（规划与综合）
- **WideSearch**：衡量在广泛信息源中的可靠性（报告英文子集的Item F1分数）

**基线分类**
- **闭源商业模型**：Claude-4系列、OpenAI-o3、OpenAI Deep Research、GPT-5-High
- **大规模开源模型(>30B)**：Kimi-K2、DeepSeek-V3.1/V3.2、GLM-4.6/4.7、Minimax-M2
- **同规模(~30B)开源模型**：MiroThinker、DeepDive-32B、WebDancer、WebSailor-V2、WebLeaper、Tongyi DeepResearch

## 2. 主要实验结果

### 2.1 与工业界资源密集型基线对比（Table 1）
在仅使用单次SFT、11.7k样本的条件下，OpenSeeker展现出与大量企业资源支持的模型相媲美的竞争力：
- 在**BrowseComp-ZH**上取得**48.4**分，超越采用持续预训练(CPT)+SFT+RL复杂流程的**Tongyi DeepResearch**（46.7分）
- 在xbench（74.0分）和WideSearch（59.4分）上达到或超越多数工业界模型

### 2.2 相同训练范式下的性能对比（Table 2）
在仅使用SFT训练的~30B参数模型类别中，OpenSeeker实现决定性优势：
- **BrowseComp**：29.5分（第二名DeepDive-32B为15.3分）
- **BrowseComp-ZH**：48.4分，领先第二名WebSailor-V2-SFT（28.3分）近**20个百分点**
- 证明数据质量（而非数量）是决定性因素：MiroThinker使用147k样本仍仅得13.8分，远低于OpenSeeker的11.7k样本效果

### 2.3 同等数据量下的质量验证（Table 3）
与使用10k-15k样本的WebSailor-V2和WebLeaper数据配置对比：
- **BrowseComp**：29.5分 vs 最佳基线27.67分
- **xbench**：74.0分 vs 最佳基线66.0分（提升约8%）
- **WideSearch-EN**：59.4分 vs 最佳基线44.07分（提升约15%）

验证了所提出数据合成方法在样本效率上的显著优势。

## 3. 数据统计分析（Figure 4 & Figure 5）
通过使用相同开源模型在合成数据与基准测试数据上执行推理，量化对比数据难度：
- **中文数据难度显著更高**：OpenSeeker-v1-Data-ZH平均需要**46.35次工具调用**和**76.1k tokens**，而BrowseComp-ZH平均仅**26.98次调用**和**15.1k tokens**
- **英文数据难度相当**：OpenSeeker-v1-Data-EN与BrowseComp-EN的难度水平相近（由于资源限制，英文数据尚未更新至最新QA标准）

这表明尽管中文数据仅1.4k样本，但其高保真度和复杂性直接贡献了对中文基准的优越性能。

## 4. 与并行工作的对比（Appendix A, Table 4 & Figure 6）
与同期进行的OpenResearcher和RedResearcher对比：
- **样本效率**：OpenSeeker使用11.7k样本，在三个基准上全面超越使用96k样本的OpenResearcher
- **性能优势**：在BrowseComp-ZH上超越RedResearcher **21.6%**（48.4% vs 26.8%）
- **任务复杂度**：工具调用次数统计显示，OpenSeeker-v1-Data-EN平均45.92次 vs RedResearcher-EN的36.91次；OpenSeeker-v1-Data-ZH平均46.35次 vs RedResearcher-ZH的20.02次，证明合成数据具有显著更高的任务复杂度。

## 未来工作

基于论文第5节（Discussions）及相关实验分析，可进一步探索的研究方向包括：

## 1. 数据质量与分布优化
- **严格的数据过滤策略**：当前工作因资源限制仅进行单次训练运行，未实施启发式数据过滤或分布优化。未来可探索基于模型反馈的数据筛选机制，以及针对任务难度和领域的分层采样策略，构建渐进式课程学习（curriculum learning）方案。
- **更高复杂度的数据合成**：尽管当前数据已展现较高难度，仍可进一步提升实体子图的拓扑复杂度（增加跳数 $k$）、引入更多模态的噪声干扰，或设计需要跨语言、跨领域推理的复合任务。

## 2. 训练方法与规模化
- **强化学习（RL）增强**：当前模型仅通过SFT达到前沿性能，尚未探索RL阶段。结合PPO、GRPO等算法进行多轮在线探索，有望进一步提升长周期决策的稳定性和工具调用的效率。
- **多轮迭代优化**：采用类似"合成-训练-评估-再合成"的迭代闭环（iterative bootstrapping），利用模型自身生成的失败案例反向指导QA合成过程，实现数据与模型的协同进化。

## 3. 工具生态扩展
- **异构工具集成**：当前工作聚焦于网络搜索工具，未来可整合代码解释器、数据库查询、科学计算工具、API调用等更多样化的工具集，向通用型代理（generalist agent）架构演进。
- **多模态感知能力**：扩展代理处理网页中的视觉信息（如图表、图像验证码、视频内容）的能力，构建视觉-语言-行动（Vision-Language-Action）统一的搜索代理。

## 4. 评估与基准测试
- **完整基准评估**：受限于计算资源，BrowseComp评估仅在200样本子集上进行。未来需在完整测试集上验证模型性能，并探索在动态变化的实时网络环境中的鲁棒性测试。
- **人工验证与对齐**：引入人工专家评估，衡量合成数据与真实人类信息需求的分布差异，优化QA合成流程以更好地对齐实际应用场景。

## 5. 效率与可解释性
- **上下文压缩机制优化**：当前采用简单的摘要器进行去噪，可探索可学习的动态上下文压缩模块（如基于注意力的选择性记忆机制），在保持关键信息的同时进一步延长有效上下文窗口。
- **推理路径的可视化与验证**：开发工具对代理的多跳推理路径进行可视化追踪和逻辑验证，提升黑盒模型的可解释性和错误诊断能力。

## 总结

论文介绍了**OpenSeeker**，这是首个由纯学术团队开发的、完全开源（包括完整训练数据与模型权重）且达到前沿性能水平的深度搜索代理。以下是主要内容概括：

## 1. 研究背景与问题
深度搜索能力已成为大语言模型（LLM）代理的核心竞争力，但该领域长期被工业巨头垄断。现有开源工作要么仅开源模型而不公开数据，要么数据质量不足导致性能落后，形成"数据护城河"（data moat），严重阻碍了学术社区的创新与复现。

## 2. 核心贡献
- **完全开源的搜索代理**：首次实现了模型权重、全部11.7k训练样本（含复杂QA对及完整工具调用轨迹）以及合成代码的全面开源。
- **前沿级性能**：仅凭单次监督微调（SFT），在BrowseComp、BrowseComp-ZH、xbench-DeepSearch和WideSearch等基准上达到或超越众多工业界模型（如在BrowseComp-ZH上以48.4%超越Tongyi DeepResearch的46.7%）。

## 3. 关键技术方法

### (1) 基于事实的可扩展可控QA合成（Fact-Grounded Scalable Controllable QA Synthesis）
通过逆向工程网页图结构 $\mathcal{G} = (\mathcal{V}, \mathcal{E})$ 生成复杂查询：
- **图拓扑扩展**：从种子节点 $v_{\text{seed}}$ 扩展局部子图 $\mathcal{G}_{\text{sub}}$，确保任务基于真实网络拓扑。
- **实体提取与混淆**：提取实体构建子图 $\mathcal{G}_{\text{entity}}$，并通过混淆算子 $\Phi$ 将具体实体映射为模糊描述 $\tilde{e} = \Phi(e)$，强制模型进行多跳推理而非关键词匹配。
- **双重验证**：通过闭卷测试（确保必须调用工具）和开卷测试（确保逻辑一致性）的拒绝采样，保证数据难度与有效性。

### (2) 去噪轨迹合成（Denoised Trajectory Synthesis）
解决原始网页噪声干扰问题：
- **动态上下文去噪**：在轨迹生成阶段采用"摘要化长期历史 + 原始近期上下文"的混合协议：
  $$
  H_t = \{\underbrace{\text{Summarized History}}_{\text{历史摘要}}, \underbrace{(r_{t-1}, a_{t-1}, o_{t-1})}_{\text{原始最近观察}}\}
  $$
- **非对称训练**：教师模型基于去噪上下文生成高质量动作，而学生模型在训练时被强制基于原始噪声上下文 $H_t^{\text{train}}$ 预测专家动作，从而内化信息提取与去噪能力。

## 4. 实验验证
- **数据效率**：仅用11.7k样本（10.3k英文+1.4k中文）即超越使用96k样本的同期工作（OpenResearcher）及采用持续预训练(CPT)+RL的工业模型（Tongyi DeepResearch）。
- **难度可控性**：合成数据平均需要46.35次工具调用，显著高于标准基准（如BrowseComp-ZH的26.98次），验证了合成方法生成高复杂度任务的能力。

## 5. 研究意义
OpenSeeker通过**数据透明度**打破了工业界对高质量搜索数据的垄断，证明了战略性的数据合成方法足以弥合学术资源与工业级大规模训练之间的差距，为自主搜索代理领域建立了一个开放、协作、健康的技术生态基础。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
