# Context-Fidelity Boosting: Enhancing Faithful Generation through Watermark-Inspired Decoding

**arXiv**: [2604.22335](https://arxiv.org/abs/2604.22335) · [PDF](https://arxiv.org/pdf/2604.22335)  
**领域**: Hallucination  
**作者**: Zhang, Ye, Gao, Li, Wu, Tian, Duan, Du 等 10 人  
**综合评分**: 8.05  （novelty: 8.5 · method: 9.0 · evidence: 9.0 · clarity: 7.5）

---

## 摘要

> 本文提出了一种名为Context-Fidelity Boosting（CFB）的解码时轻量级框架，旨在减少大语言模型生成内容时出现的忠实性幻觉问题。该方法受水印技术中的logit整形原理启发，通过基于输入上下文对token的支持程度进行加性logit调整，有效提升了生成内容的忠实度。实验在摘要和问答任务上验证了其有效性，且代码已开源。作者团队未明确标注所属机构，故省略背景介绍。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

这篇论文旨在解决**忠实性幻觉（faithfulness hallucination）**问题，即大型语言模型（LLMs）生成的内容与用户提供的输入上下文相矛盾、忽略或歪曲上下文信息的现象。

具体而言，论文关注以下核心问题：

- **上下文与参数知识的冲突**：当外部证据（输入上下文）与模型在预训练期间获得的内部参数记忆发生冲突时，模型往往倾向于依赖其参数知识而非给定上下文，导致生成与输入不一致的内容。

- **与高利益场景的关联**：在医疗、法律、金融等高风险领域，模型输出必须严格忠实于给定的输入上下文，而非依赖看似合理但与上下文无关或冲突的内部知识。

- **现有方法的局限性**：现有缓解方法主要分为三类，但各有不足：
  - **训练时方法**：需要微调或架构修改，计算成本高且跨域泛化能力有限；
  - **提示技术**：依赖手工设计的输入，在不同任务或模型间表现不一致；
  - **解码时干预**：虽具有效率和模型无关性优势，但常在强制上下文忠实度与保持生成流畅性之间面临困难权衡，或依赖复杂调整的对比目标和启发式控制规则。

论文提出的**Context-Fidelity Boosting (CFB)**框架通过**解码时的加性对数调整（additive logit shaping）**来解决上述问题，即通过选择性提升受上下文支持的标记的生成概率，在不牺牲流畅性的前提下增强模型对输入上下文的忠实度，且无需重新训练或修改模型架构。

## 相关工作

这篇论文的相关研究主要涵盖以下三个方面：

### 1. 大语言模型中的忠实性幻觉（Faithfulness Hallucinations in LLMs）

研究区分了两种主要形式的幻觉：

- **事实性幻觉（Factuality Hallucination）**：模型输出与可验证的真实世界事实不符（如错误的历史日期或虚构的引用）
- **忠实性幻觉（Faithfulness Hallucination）**：生成内容相对于提供的输入上下文存在矛盾、忽略或虚构信息（如摘要中的无支持细节）

相关工作包括：
- **Hase et al. (2024)**：关于模型编辑与信念修正的基础问题研究
- **Chuang et al. (2024)**：利用注意力图检测和缓解上下文幻觉的"Lookback Lens"方法
- **Wu et al. (2024)**：量化LLM内部先验与外部证据间冲突的ClashEval框架
- **Qiu et al. (2024)**：基于熵的检索增强语言模型解码方法
- **Ming et al. (2024)**：用于评估语言模型上下文忠实度的Faitheval基准

### 2. 现有的幻觉缓解方法

现有方法按干预阶段分为三类：

**训练时方法（Training-time Approaches）**
- 通过架构修改或目标函数调整（如增强注意力机制、知识感知训练）来增强模型对上下文的依赖
- **局限性**：需要大量计算资源，跨域泛化能力有限（Tonmoy et al., 2024）
- 代表工作：**Hu et al. (2024)** 提出通过忠实微调缓解幻觉

**提示技术（Prompting Techniques）**
- 包括思维链推理（Chain-of-Thought, Wei et al., 2023）、自我一致性（Self-Consistency）等
- **局限性**： effectiveness 在不同模型和任务间变化较大，依赖手工设计的输入（Hou et al., 2024）

**解码时干预（Decoding-time Interventions）**
- 直接修改推理时的生成行为，如约束解码、对比解码、自适应 token 概率重加权
- 代表工作：
  - **CAD (Context-Aware Decoding, Shi et al., 2024)**：使用固定超参数调整输出概率
  - **ADACAD (Adaptive Context-Aware Decoding, Wang et al., 2024)**：基于 Jensen-Shannon 散度动态推断调整强度
  - **COIECD (Contextual Information-Entropy Constraint Decoding, Yuan et al., 2024)**：对冲突和非冲突 token 采用不同策略
- **局限性**：常在强制忠实度与保持流畅性间面临权衡，或依赖复杂的启发式控制规则（Gema et al., 2024; Mo et al., 2026）

### 3. LLM 中的文本水印技术（Watermarking in LLMs）

论文的方法受水印技术中的 **logit-shaping（对数塑形）** 机制启发：

- **Kirchenbauer et al. (2024)**：提出将词汇表划分为"绿色"和"红色" token 集合，通过调整 token 概率嵌入可检测的统计模式
- **Liu et al. (2024a, 2024b)**：提出基于语义不变性约束的鲁棒水印方法
- **Liu & Bu (2024)**：自适应文本水印，根据上下文动态调整 token 概率
- **Golowich & Moitra (2024)**：从理论上分析水印强度与自然性之间的权衡

**关键区别**：水印技术旨在嵌入可检测信号以便后续检测，而本文的 CFB 方法 repurposes（重新利用）类似的 logit-shaping 机制，通过加性对数调整来偏向上下文支持的 token，从而减少忠实性幻觉。

### 4. 其他相关技术

- **缓存与记忆增强解码**：通过提升近期生成历史中的 token 来改善语言建模或长程依赖建模（与 CFB 的 token-aware 变体相关）
- **相关性重加权方法**：改进注意力或排序信号（如 Tian et al., 2026 的 ReAttn），但 CFB 明确针对外部输入上下文中的 token 进行提升，而非一般性语言建模质量改进

## 解决方案

论文通过提出 **Context-Fidelity Boosting (CFB)** 框架来解决忠实性幻觉问题。这是一个轻量级、模型无关的**解码时（decoding-time）**干预方法，通过选择性提升受上下文支持的 token 的生成概率，增强模型对输入上下文的忠实度。

### 核心机制：加性对数调整（Additive Logit Shaping）

CFB 受文本水印技术中的 logit-shaping 原理启发，在解码过程中对原始 logits 进行加性调整：

$$
\tilde{l}_t(w) = 
\begin{cases} 
l_t(w) + \Delta_t(w), & \text{if } w \in V_S \\
l_t(w), & \text{otherwise}
\end{cases}
$$

其中 $l_t(w)$ 是原始 logit，$V_S$ 是从输入上下文中提取的源支持 token 集合，$\Delta_t(w)$ 是基于上下文支持程度的提升因子。

### 三级提升策略

CFB 提供三种由粗到精的控制策略：

#### 1. 静态提升（Static Boosting）
对所有源支持 token 应用固定偏置 $\delta$：

$$
\Delta_t(w) = \delta
$$

该方法简单高效，但可能过度提升不相关的源 token。

#### 2. 上下文感知提升（Context-Aware Boosting）
根据上下文对模型预测的影响程度动态调整提升强度。计算上下文感知与无上下文 next-token 分布间的 **Jensen-Shannon 散度（JSD）**：

$$
D = \text{JSD}(P_w \| P_w^o)
$$

其中 $P_w$ 和 $P_w^o$ 分别表示有上下文和无上下文时的 next-token 分布。基于 $D$ 自适应缩放提升：

$$
\Delta_t(w) = \delta(D) = \delta_{\min} + (\delta_{\max} - \delta_{\min}) \cdot D
$$

当上下文显著改变模型预测时（$D$ 较大），自动增强提升强度。

#### 3. Token 感知提升（Token-Aware Boosting）
在上下文感知的基础上，进一步根据 token 级相关性重新分配自适应提升。结合两种局部相关性信号：

- **注意力信号**：聚合当前解码步骤对源位置的注意力分数
  $$
  \alpha_t(w) = \text{Agg}(\{a_t(p) : p \in P(w, C)\})
  $$

- **语义相似度**：计算 token 与源跨度的语义相似度
  $$
  s(w) = \frac{1}{|S|} \sum_{c \in S} \text{cosine}(e_w, e_c)
  $$

综合相关性分数：
$$
r_t(w) = \lambda_1 \alpha_t(w) + \lambda_2 s(w)
$$

归一化后得到最终提升：
$$
\hat{r}_t(w) = \frac{r_t(w)}{\frac{1}{|V_S|} \sum_{u \in V_S} r_t(u)}
$$

$$
\Delta_t(w) = \delta(D) \cdot \hat{r}_t(w)
$$

该策略将更大的提升分配给在当前解码状态下估计更相关的源支持 token。

### 实现流程

算法执行步骤如下：

1. **源跨度解析**：从输入提示中解析源支持跨度 $S$，将提升限制在此跨度支持的 token 上，避免干扰提示脚手架和指令文本
2. **预处理**：计算 token 级语义相关性（每样本一次）
3. **解码循环**：
   - 计算上下文感知与无上下文分布的 JSD（上下文感知和 token 感知模式）
   - 获取当前解码状态的源位置注意力（token 感知模式）
   - 计算最终 logit 调整 $\tilde{l}_t$
   - 应用 Softmax 并采样下一个 token

### 关键优势

- **无需训练**：不需要微调或架构修改，兼容各种开源 LLM
- **计算轻量**：静态和上下文感知变体的计算开销极低（小于基模型 FLOPS 的 0.003%）
- **灵活控制**：三级策略提供从简单固定偏置到细粒度 token 级重分布的连续谱系
- **保持流畅性**：通过加性调整而非硬性约束，在增强忠实度的同时保持生成质量

## 实验验证

论文在**摘要（Summarization）**和**问答（Question Answering）**任务上进行了系统评估，并辅以人工评估、效率分析和消融实验。具体实验内容如下：

---

### 1. 实验设置（Experiment Setup）

**评估模型**
- Llama2-13B-chat-hf
- Llama3-8B-Instruct  
- Mistral-7B-Instruct

**数据集**
- **摘要任务**：
  - **CNN-DM**：新闻文章摘要
  - **XSum**：抽取式摘要（随机采样500条评估）
- **问答任务**：
  - **NQ-Synth**：上下文与模型参数知识互补的设置
  - **NQ-SWAP**：包含合成知识冲突的设置（上下文与参数知识矛盾）

**对比基线**
- **CAD** (Context-Aware Decoding, Shi et al., 2024)
- **ADACAD** (Adaptive Context-Aware Decoding, Wang et al., 2024)
- **COIECD** (Contextual Information-Entropy Constraint Decoding, Yuan et al., 2024)

**评估指标**
- **ROUGE-L**：生成质量/词汇重叠
- **FactKB**：知识一致性/事实性
- **BERT-P**：语义保留度
- **Accuracy**（仅QA任务）：回答正确率

---

### 2. 主要实验结果

#### 2.1 摘要任务性能（Table 2）
- **CNN-DM**：CFB 在所有三个模型上均超越基线。Token-aware CFB 在 Mistral-7B 上取得最佳 ROUGE-L (34.52) 和 FactKB (96.87)；Context-aware CFB 在 Llama2-13B 上取得最佳 ROUGE-L (37.52)；Llama3-8B 上 Static 和 Context-aware 在不同指标上各占优势。
- **XSum**：结果更具模型依赖性。CFB 在 Llama2-13B 和 Llama3-8B 上大幅超越基线，但在 Mistral-7B 上 CAD 仍保持 ROUGE-L 优势，而 CFB 在 FactKB 和 BERT-P 上表现更强。

#### 2.2 问答任务性能（Table 3）
- **NQ-Synth**（互补知识）：CFB 表现优异，Token-aware CFB 在所有三个模型上均取得最佳准确率（Mistral-7B: 60.10, Llama2-13B: 64.00, Llama3-8B: 73.40）。
- **NQ-SWAP**（知识冲突）：ADACAD 在准确率上表现最佳（处理显式冲突更强），但 CFB 在某些忠实度相关指标（如 FactKB、BERT-P）上仍具竞争力。

#### 2.3 模型特定分析
- CFB 在摘要任务和互补知识 QA 中表现稳健，但在显式知识冲突场景下效果受模型架构影响较大。

---

### 3. 人工评估与 LLM 评估（Table 4）

在 CNN-DM 和 NQ-SWAP 上随机采样 100 例，对比 CAD、ADACAD 和 Token-aware CFB：

**人工评分**（1-5分制，3维度）：
- **忠实度（Faithfulness）**：Token-aware CFB 最优 (4.31)，超越 CAD (3.82) 和 ADACAD (4.03)
- **流畅度（Fluency）**：CFB (4.18) 与基线相当（4.15-4.21）
- **信息丰富度（Informativeness）**：CFB 最优 (4.12)

**LLM 自动评估**（GPT-4o）：
- **一致性（Consistency）**：CFB 最高 (0.91)
- **幻觉数量（Hallucination）**：CFB 最低 (0.67)
- **矛盾率（Contradiction）**：CFB 最低 (0.05)

---

### 4. 计算效率分析

**FLOPS 估计**（Table 5）：
- Static CFB 和 Context-aware CFB 的额外开销极低（< 基模型 FLOPS 的 0.003%）
- Token-aware CFB 计算量较高但仍属轻量级（$2.86 \times 10^8$ FLOPS vs 基模型 $3.40 \times 10^{12}$ FLOPS）

**实际运行时间**（Appendix Table 8）：
- Static CFB 最快（CNN-DM: 1.38秒/样本），快于 COIECD (3.22秒)
- Context-aware CFB 次之 (2.00秒)
- Token-aware CFB 最慢 (10.39秒)，因需逐步计算注意力和语义相关性

---

### 5. 消融实验与参数分析

#### 5.1 组件消融（Table 6）
在 Llama3-8B/CNN-DM 上消融 Token-aware CFB 的组件：
- **移除语义相似度（w/o semantic）**：性能严重下降（ROUGE-L 从 35.81 降至 4.45），表明语义组件是关键稳定因素
- **移除 JSD（w/o JSD）**：性能一致下降，证实样本级自适应缩放的重要性
- **移除注意力（w/o attention）**：影响较小，注意力信号提供有限但有益的补充

#### 5.2 参数敏感性（Figure 3）
分析 boost 值 $\delta$ 的影响：
- **CNN-DM**：中等 boost 值效果最佳，过大导致性能下降
- **NQ-Synth**：在更宽的 boost 值范围内性能稳定，表明当上下文补充参数知识时，更强的上下文强化更可接受

---

### 6. 案例研究（Case Studies）

**案例 1：高知识冲突**（Table 7）
- 上下文：2012年测定长城实际长度为 21,196 公里（此前认为是 8,850 公里）
- 贪婪解码与 COIECD：错误输出 8,850 公里（依赖参数知识）
- CAD：部分正确但不够完整
- **CFB**：正确引用 21,196 公里并明确对比旧估计，最接近标准答案

**案例 2 & 3**：展示了在互补上下文和低冲突场景下，CFB 能保持流畅性同时更好地整合上下文细节。

---

### 7. 补充实验：推理任务（Appendix Table 9）

在**多跳推理**（HotpotQA）和**阅读理解**（TriviaQA）上的初步评估：
- **HotpotQA**：CFB 表现不如 CAD（39.5% vs 34.7-37.1%），表明简单上下文提升对复杂多步推理任务 insufficient
- **TriviaQA**：CFB 显著优于 CAD（Token-aware: 71.5% vs CAD: 40.2%），说明当答案可直接从上下文 grounding 时，CFB 有效

**结论**：CFB 更适合直接上下文对齐任务，而非复杂推理任务。

## 未来工作

基于论文的局限性分析与实验发现，未来研究可从以下方向进一步探索：

### 1. 黑盒模型适配（Black-Box Adaptation）
当前 CFB 依赖模型内部访问（logits、注意力权重、token 嵌入），限制了其在封闭 API（如 GPT-4、Claude）上的应用。未来可探索：
- **基于概率估计的近似方法**：仅通过 API 返回的 top-k 概率或采样结果近似估计上下文支持度；
- **外部判别器机制**：训练轻量级分类器或利用 LLM-as-a-Judge 来识别源支持 token，替代内部注意力信号；
- **提示工程替代方案**：设计 meta-prompt 引导模型在解码前显式标记或优先选择上下文相关 token。

### 2. 高冲突场景下的鲁棒性增强
实验表明，在 NQ-Swap 等**显式知识冲突**场景下，CFB 效果不如专门抑制参数先验的方法（如 ADACAD）。可探索：
- **动态冲突检测机制**：在解码前自动识别上下文与参数知识的高冲突区域，切换至对比式解码（contrastive decoding）或知识抑制策略；
- **矛盾感知的 Boost 调整**：当检测到强冲突时，不仅提升上下文 token，同时抑制高概率的参数知识 token，实现双向调节；
- **置信度校准**：结合模型对自身预测的不确定性（entropy、logit 分布锐度），在低置信度区域增强上下文依赖。

### 3. 复杂推理任务的扩展
CFB 在 HotpotQA 等**多跳推理**任务上表现有限，表明简单 token-level boosting 不足以支持复杂推理。未来可：
- **结构化上下文建模**：将 CFB 与链-of-thought (CoT) 或思维图 (Graph-of-Thought) 结合，在推理路径规划阶段引入上下文忠实度约束；
- **跨步依赖性增强**：设计跨解码步骤的累积相关性评分，使当前步的 boost 不仅依赖当前注意力，还考虑历史推理链与源文本的累积对齐度；
- **检索-推理联合优化**：在 multi-hop QA 中，将 CFB 与动态检索结合，确保每一步推理都基于最新检索到的上下文片段。

### 4. 更细粒度的相关性建模
消融实验显示**语义相似度**是关键，而**注意力信号**贡献有限。可改进：
- **注意力机制优化**：探索更复杂的注意力聚合方式（如基于 span 的 attention pooling、多头注意力选择性聚合），替代简单的求和；
- **上下文感知嵌入**：使用上下文动态调整的 token 嵌入（如 contextualized embeddings）计算语义相似度，而非静态嵌入；
- **句法与实体感知**：引入实体链接、共指消解等 NLP 工具，优先提升指代明确、句法角色重要的源 token。

### 5. 自适应超参数学习
当前方法依赖手工设定的 boost 值 ($\delta$) 和权重 ($\lambda_1, \lambda_2$)。未来可研究：
- **样本级自适应**：基于输入复杂度、上下文长度、冲突强度等特征，通过轻量级元网络（meta-network）预测最优 boost 强度；
- **在线学习/反馈机制**：在生成长文本时，根据已生成内容的忠实度自动调整剩余步骤的 boost 策略；
- **帕累托最优搜索**：在多目标优化框架下自动权衡忠实度（faithfulness）与流畅度（fluency），提供可配置的解码 frontier。

### 6. 多模态与结构化数据扩展
当前 CFB 针对文本 token，可扩展至：
- **视觉-语言模型**：在图像描述、视觉问答中，将图像区域特征与文本 token 对齐，"boost" 与视觉证据对齐的描述 token；
- **表格/代码生成**：针对结构化数据（表格、SQL、代码），设计基于结构化匹配（如单元格对齐、语法树匹配）的 boosting 机制，而非纯文本 token 匹配；
- **长文档处理**：结合分层注意力（hierarchical attention）或块级（block-level）相关性，处理超长上下文中的忠实度问题。

### 7. 理论基础与可解释性
- **理论边界分析**：建立数学框架分析 logit boosting 对输出分布的影响，推导保持 fluency 的最大可行 boost 值理论边界；
- **因果干预视角**：将 CFB 形式化为对上下文变量的因果干预，量化其对减少特定类型幻觉（如实体替换、数字错误）的因果效应；
- **可解释性工具**：开发可视化工具展示哪些 token 被 boost 以及为何被 boost，帮助用户理解模型的上下文依赖机制。

### 8. 效率进一步优化
- **缓存与增量计算**：优化 token-aware 变体中的注意力计算，利用 KV-cache 避免重复计算源位置注意力；
- **早停与稀疏化**：仅在检测到潜在幻觉风险的高风险解码步骤启用 CFB，而非每一步都计算；
- **蒸馏与近似**：将 CFB 的行为蒸馏到标准解码过程中，或训练小型插件网络预测 boost 值，减少运行时开销。

## 总结

这篇论文提出 **Context-Fidelity Boosting (CFB)**，一种无需重新训练的解码时框架，用于缓解大型语言模型（LLMs）中的**忠实性幻觉（faithfulness hallucination）**问题，即模型生成内容与用户提供的输入上下文相矛盾或忽略上下文的现象。

### 核心问题
在检索增强生成、摘要和问答等场景中，当外部上下文与模型的参数知识冲突时，LLMs 往往倾向于依赖内部记忆而非给定证据，导致生成与输入不一致但仍看似合理的内容。现有方法常需在忠实度与流畅性间进行困难权衡，或依赖复杂的训练/提示工程。

### 方法论：Context-Fidelity Boosting (CFB)
受文本水印技术中的 logit-shaping 机制启发，CFB 通过在解码过程中对**受源支持的 token（source-supported tokens）**施加加性对数调整（additive logit adjustment），提升其生成概率，从而增强上下文对齐。框架包含三个递进层级：

1. **静态提升（Static Boosting）**：对所有源支持 token 施加固定偏置 $\Delta_t(w) = \delta$，简单高效但缺乏适应性。

2. **上下文感知提升（Context-Aware Boosting）**：根据上下文对模型预测的影响程度动态调整。通过计算上下文感知与无上下文 next-token 分布间的 **Jensen-Shannon 散度（JSD）** $D = \text{JSD}(P_w \| P_w^o)$，自适应缩放提升强度：
   $$ \Delta_t(w) = \delta_{\min} + (\delta_{\max} - \delta_{\min}) \cdot D $$
   当上下文显著改变模型预测时，自动增强上下文偏向。

3. **Token 感知提升（Token-Aware Boosting）**：进一步结合局部相关性信号重新分配自适应提升：
   - **注意力信号** $\alpha_t(w)$：聚合当前解码步骤对源位置的注意力质量；
   - **语义相似度** $s(w)$：计算 token 与源跨度的余弦相似度。

   综合归一化后得到 token 级提升：
   $$ \Delta_t(w) = \delta(D) \cdot \hat{r}_t(w), \quad \text{其中 } r_t(w) = \lambda_1 \alpha_t(w) + \lambda_2 s(w) $$

### 实验验证
论文在 **Llama2-13B、Llama3-8B、Mistral-7B** 上进行了系统评估：

- **摘要任务（CNN-DM、XSum）**：CFB 在 ROUGE-L、FactKB（事实一致性）和 BERT-P（语义保留）指标上普遍优于 CAD、ADACAD 和 COIECD 等基线，尤其在 Llama2-13B 和 Llama3-8B 上提升显著。

- **问答任务（NQ-Synth、NQ-SWAP）**：
  - 在**互补知识场景（NQ-Synth）**中，Token-aware CFB 在所有模型上均取得最佳准确率（最高达 73.40%）；
  - 在**知识冲突场景（NQ-SWAP）**中，CFB 在忠实度相关指标（FactKB、BERT-P）上表现竞争力，但准确率略逊于专门抑制参数先验的 ADACAD。

- **人工与 LLM 评估**：Token-aware CFB 在忠实度（4.31/5）、一致性（0.91）和幻觉率（0.67 次/样本）上均优于基线，同时保持相当的流畅度。

- **效率分析**：Static 和 Context-aware 变体计算开销极低（<0.003% 基模型 FLOPS），Token-aware 变体虽较慢（约 10 秒/样本）但仍属实用范围。

- **消融研究**：语义相似度组件被证明对 Token-aware CFB 的稳定性至关重要，而 JSD 自适应机制对维持性能平衡不可或缺。

### 主要贡献
- **轻量级框架**：无需重新训练或架构修改，兼容广泛的开源 LLMs；
- **三级控制机制**：从固定偏置到样本自适应再到 token 级细粒度重分配，提供灵活的忠实度-流畅性权衡；
- **有效性验证**：在摘要和问答任务上证明能一致提升上下文忠实度，同时最小化生成开销。

### 局限与展望
CFB 目前需要访问模型内部（logits、注意力、嵌入），难以应用于黑盒 API；在显式知识冲突场景下效果受限；对多跳推理任务的提升有限。未来可探索黑盒近似、冲突检测机制、以及向多模态和结构化数据的扩展。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
