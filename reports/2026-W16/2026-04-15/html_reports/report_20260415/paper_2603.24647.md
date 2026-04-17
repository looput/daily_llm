# Can LLMs Beat Classical Hyperparameter Optimization Algorithms? A Study on autoresearch

**arXiv**: [2603.24647](https://arxiv.org/abs/2603.24647) · [PDF](https://arxiv.org/pdf/2603.24647)  
**领域**: Agent  
**作者**: Ferreira, Wobbe, Krishnakumar, Hutter, Zela  
**综合评分**: 8.00  （novelty: 8.5 · method: 8.5 · evidence: 9.0 · clarity: 8.0）

---

## 摘要

> 本文由Frank Hutter教授团队（来自弗莱堡大学和博世人工智能中心）提出了一种名为Centaur的混合超参数优化方法，将经典优化算法CMA-ES与大型语言模型相结合。研究通过autoresearch平台系统比较了经典HPO算法与LLM-based方法在语言模型超参数调优上的性能，发现纯LLM方法在固定搜索空间下仍落后于经典方法，但提出的Centaur混合方法取得了最佳效果。论文创新性地探索了LLM在自动化机器学习中的角色定位，实验设计严谨，代码开源，为AI for AI领域提供了有价值的见解。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

该论文旨在解决以下核心问题：

**在固定的计算预算和约束条件下，系统性地比较经典超参数优化（HPO）算法与基于大语言模型（LLM）的方法在语言模型训练任务上的性能，并探索如何有效结合两者的优势以实现更优的优化效果。**

具体而言，论文围绕以下三个层面展开研究：

1. **固定搜索空间下的算法对比**  
   验证在预定义的14维超参数搜索空间内，经典HPO方法（如CMA-ES、TPE、SMAC）是否优于基于LLM的优化方法（如LLAMBO、Karpathy Agent），并分析LLM在约束搜索空间中的局限性。

2. **无约束代码编辑的可行性**  
   评估LLM agent通过直接编辑训练源代码（而非在固定空间内选择参数）进行优化的竞争力，特别是在使用开源权重模型（Qwen3.5-27B）时的表现，以及模型规模（0.8B vs 27B）对此类方法的影响。

3. **混合优化架构的设计**  
   提出名为 **Centaur** 的混合方法，解决纯LLM方法难以跟踪优化状态、纯经典方法缺乏领域知识的互补性问题。该方法通过将CMA-ES的内部状态（均值向量 $\mu$、步长 $\sigma$、协方差矩阵 $C$）显式共享给LLM，实现优化轨迹学习与Transformer训练领域知识的融合，从而在实验中达到最佳性能（val_bpb $\approx$ 0.9763）。

## 相关工作

论文在第2节（Related Work）中系统梳理了相关研究，主要涵盖以下三个层面：

### 1. 经典超参数优化（HPO）方法

- **基础方法**：随机搜索（Random Search）[Bergstra and Bengio, 2012]、基于高斯过程的贝叶斯优化 [Snoek et al., 2012]
- **序列模型方法**：SMAC（基于随机森林）[Hutter et al., 2011]、TPE（树结构Parzen估计）[Bergstra et al., 2011]
- **进化策略**：CMA-ES（协方差矩阵自适应进化策略）[Hansen, 2016]
- **多保真度与迁移方法**：Hyperband [Li et al., 2017]、BOHB [Falkner et al., 2018]、迁移HPO [Wistuba and Grabocka, 2021; Arango et al., 2024]、零样本HPO [Öztürk et al., 2022]（本文未涵盖这些方法，但指出其可应用于该基准）

### 2. 基于LLM的HPO方法

- **LLAMBO** [Ye et al., 2024]：使用LLM作为代理模型替代高斯过程，通过LLM进行性能预测
- **SLLMBO** [Mahammadli and Ertekin, 2024]：将LLM与TPE集成到联合采样器中
- **Zhang et al. [2023]**：直接提示LLM生成超参数建议
- **LLaMA-ES** [Kramer, 2024]：使用LLM调整CMA-ES自身的超参数
- **HOLLM** [Schwanke et al., 2026a]：通过bandit机制将搜索空间划分为子区域，在各区域内使用LLM提出候选；后续扩展至多目标优化 [Schwanke et al., 2026b]

### 3. 开放端智能体发现（Open-ended Agentic Discovery）

- **AlphaEvolve** [Novikov et al., 2025]、**ShinkaEvolve** [Lange et al., 2026] 等方法原则上可用于优化autoresearch任务，但本文聚焦于专门面向HPO的方法
- 其他相关工作包括 [Zhang et al., 2026; Liu et al., 2024; Wang et al., 2026]

### 4. 本文与已有工作的核心区别

论文明确指出其差异化贡献在于：

1. **基准测试平台**：使用autoresearch [Karpathy, 2025a] 作为真实世界基准，天然支持固定搜索空间与无约束代码编辑两种模式，实现同等条件下的头对头比较
2. **自动化搜索空间构建**：通过抽象语法树（AST）解析自动从训练脚本中提取超参数，减少人工先验对搜索空间设计的影响
3. **状态共享机制**：提出**Centaur**方法，首次将经典优化器的完整内部状态（CMA-ES的均值向量 $\mu$、步长 $\sigma$、协方差矩阵 $C$）显式暴露给LLM，实现优化器状态感知的建议生成。相比之下：
   - LLAMBO将LLM作为代理模型，由采集函数决策
   - SLLMBO仅合并LLM与TPE提议，不暴露优化器内部状态
   - HOLLM通过空间划分约束LLM，而非用优化器状态信息丰富LLM

## 解决方案

论文通过构建严格的基准测试框架、自动化搜索空间提取，以及设计新型的混合优化架构来解决该问题，具体方案如下：

### 1. 统一基准测试框架
为消除比较中的混淆因素，研究建立了标准化的评估协议：
- **任务与预算**：在 **nanochat**（约50M参数的解码器-only Transformer）上优化验证集bits-per-byte（val_bpb），所有方法统一获得 **24小时GPU训练预算** 和 **3个随机种子**
- **硬件隔离**：在NVIDIA H200 GPU上运行，为训练进程分配80GB显存，剩余内存用于托管LLM推理服务（vLLM），确保经典方法与LLM方法的资源消耗可比
- **失败处理**：将内存溢出（OOM）等失败试验标记为 $val\_bpb = 100.0$（有限惩罚值），使优化器能够从失败中学习并避免不可行区域

### 2. 自动化搜索空间构建
为减少人工先验对搜索空间设计的干扰：
- 采用**抽象语法树（AST）解析**自动从训练脚本 `train.py` 中提取超参数
- 识别所有全大写变量名且赋值为字面量的顶层赋值语句，得到**14维搜索空间**（13个连续/整数型 + 1个分类型 `WINDOW_PATTERN`）
- 虽然范围设定仍需领域知识，但超参数选择过程完全自动化，避免了手动搜索空间策划的主观性

### 3. 系统性方法对比
在相同条件下评估了**9种方法**（见Table 2）：
- **经典方法**：TPE、CMA-ES、SMAC、随机搜索
- **LLM-based方法**：LLAMBO（两种实现）、Karpathy Agent（固定14HPs）、Karpathy Agent（无约束代码编辑）
- **混合方法**：Centaur（本文提出）

所有LLM-based方法使用自托管的开源模型 **Qwen3.5**（对比0.8B与27B参数规模），以隔离模型能力对优化效果的影响。

### 4. Centaur混合架构设计
论文提出的 **Centaur** 方法（Algorithm 1）通过**显式状态共享**解决纯LLM方法难以跟踪优化状态、纯经典方法缺乏领域知识的互补性问题：

**核心机制**：
- **状态共享**：在30%的试验中（由概率 $r=0.3$ 控制），LLM接收CMA-ES的完整内部状态，包括：
  - 均值向量 $\mu$（当前最优估计）
  - 步长 $\sigma$（搜索范围）
  - 协方差矩阵 $C$（参数间相关性）
  - 历史最优5个配置及最近20次试验记录
- **决策融合**：LLM可基于上述状态覆盖CMA-ES的提议（实践中27B模型100%覆盖，0.8B模型95%覆盖）
- **状态更新**：无论是否被LLM覆盖，**所有试验结果**均用于更新CMA-ES的内部状态（$\mu, \sigma, C$），确保经典优化器持续从完整轨迹中学习

**算法流程**：
$$
\begin{array}{ll}
\textbf{Algorithm 1: Centaur} \\
\text{Input: Search space } S, \text{ budget } T, \text{ LLM ratio } r=0.3 \\
1: \text{ Initialize CMA-ES, history } H \leftarrow \emptyset; \\
2: \text{ for } t = 1, \ldots, T \text{ do} \\
3: \quad \text{if LLM turn (with probability } r \text{) then} \\
4: \quad\quad \text{Extract } \mu, \sigma, C \text{ from CMA-ES;} \\
5: \quad\quad x \leftarrow \text{LLM}(\mu, \sigma, C, H, S); \\
6: \quad \text{else} \\
7: \quad\quad x \leftarrow \text{CMA-ES.Propose()}; \\
8: \quad \text{end} \\
9: \quad y \leftarrow \text{Evaluate}(x); \\
10: \quad \text{CMA-ES.Update}(x, y); \\
11: \quad H \leftarrow H \cup \{(x, y)\}; \\
12: \text{end}
\end{array}
$$

**设计选择依据**：
选择CMA-ES作为基础优化器是因为其内部状态具有**可解释性**（$\mu$为具体配置，$\sigma$为标量，$C$为带标签的矩阵），便于LLM通过自然语言理解；相比之下，TPE的密度估计器或GP-BO的高维后验难以简洁地传达给LLM。

通过该架构，Centaur实现了：
- **稳定性**：保留CMA-ES的快速收敛特性（步长自适应、协方差学习）
- **领域知识注入**：LLM利用Transformer训练经验（如将批次大小调整为2的幂、选择合适的注意力窗口模式）修正提议，将CMA-ES的跨种子方差从0.0036降低至0.0005
- **计算效率**：仅需0.8B参数的LLM即可达到优于27B参数的效果，因为LLM仅需在经典优化器建立的轨迹上进行局部精炼，而非从零开始探索。

## 实验验证

论文开展了以下系统性实验：

### 1. 基准对比实验（9种方法 head-to-head 比较）
在 **nanochat**（约50M参数Transformer）上，以验证集bits-per-byte（val_bpb）为目标，对比了9种方法在**24小时GPU预算**（3个随机种子）下的表现：

- **经典方法**：TPE、CMA-ES、SMAC、随机搜索
- **LLM-based方法**（固定14维搜索空间）：LLAMBO（Optuna实现）、LLAMBO（论文复现）、Karpathy Agent（14 HPs）
- **LLM-based方法**（无约束代码编辑）：Karpathy Agent（Code）
- **混合方法**：Centaur（CMA-ES + LLM）

所有LLM方法使用自托管的 **Qwen3.5-27B** 作为优化器，通过vLLM部署，训练VRAM限制为80GB。实验记录了val_bpb随累积训练时间的变化（Figure 1），以及按试验次数的收敛曲线（Figure 3）。

### 2. 模型规模消融实验（0.8B vs 27B）
为评估LLM规模对优化效果的影响，对比了 **Qwen3.5-0.8B** 与 **Qwen3.5-27B** 在以下场景的表现（Figure 2、Figure 4）：
- **固定搜索空间**：Karpathy Agent（14 HPs）、LLAMBO（Paper）、LLAMBO（Optuna）、Centaur
- **无约束代码编辑**：Karpathy Agent（Code）

关键发现：0.8B模型不足以支持无约束代码编辑（val_bpb 0.9910 vs 27B的0.9814），但在混合优化（Centaur）中 sufficient，甚至 Centaur[0.8B]（0.9766）略优于 Centaur[27B]（0.9763）。

### 3. Centaur LLM比例消融实验
针对混合方法Centaur中LLM介入比例 $r$（即LLM接管试验的概率），进行了系统消融（Table 4、Figure 8）：
- 测试了 $r \in \{0.1, 0.2, 0.3, 0.5, 0.8\}$
- 分别在 **0.8B** 和 **27B** 模型上验证

结果表明：$r=0.2$（0.8B模型）和 $r=0.5$（27B模型）在单种子上取得最佳结果，但过高的 $r=0.8$ 会导致性能下降（27B模型降至0.9903，差于纯CMA-ES），证实CMA-ES需保持对优化轨迹的主导控制。

### 4. 搜索多样性与失败率分析
量化了各方法的探索行为与可靠性（Table 3、Table 5），指标包括：
- **OOM率**：内存溢出失败试验占比（随机搜索56%，LLAMBO 48-61%，而CMA-ES/TPE仅11-16%）
- **Spread**：各超参数跨试验的标准差均值
- **Pairwise**：配置对之间的平均 $L_2$ 距离
- **Step**：连续试验间的平均 $L_2$ 跳跃距离
- **Cells**：5分箱离散化后覆盖的唯一网格单元数（理论最大 $5^{13} \approx 1.2 \times 10^9$）

分析表明：低OOM率比高搜索多样性更能预测优化成功；LLM方法虽有完整历史记录，但OOM率与随机搜索相当，显示其在中小规模模型上难以有效学习避免失败区域。

### 5. 前沿模型探索性实验
使用 **Gemini 2.5 Flash** 替代Qwen3.5-27B，测试了Centaur、Karpathy Agent（Code）和LLAMBO（Paper）（Figure 5）。初步结果显示，在该任务上Gemini 2.5 Flash并未显著优于Qwen3.5-27B，表明简单替换为前沿模型并不能自动弥合与经典方法的差距。

### 6. 定性行为分析
通过案例分析（Section A.4）揭示了Centaur中LLM如何利用CMA-ES状态：
- 在seed 0的第136次试验中，LLM将CMA-ES提议的 `WINDOW_PATTERN=LLLL` 覆盖为 `SSSS`（利用Transformer领域知识选择内存高效的短注意力模式）
- 将批次大小调整为2的幂次（64, 131072），对齐GPU内存约束
- 在seed 0中，LLM试验（占32%）贡献了25%的历史最优改进，显示其在精炼CMA-ES提议时的增值作用

此外，论文提供了所有方法的**per-trial对话日志**（incumbent traces，Figures 6-7），展示各优化器发现改进的时间点与轨迹特征。

## 未来工作

基于论文结论与讨论部分，可进一步探索的研究方向包括：

### 1. 前沿模型在无约束代码编辑中的潜力验证
当前研究使用开源权重模型（Qwen3.5-27B）已使无约束代码编辑方法接近经典方法性能。未来可系统评估 **Gemini、GPT-4、Claude等前沿闭源模型** 在该任务上的表现，验证模型能力的提升是否足以使LLM agent完全超越混合方法与经典方法。 preliminary实验（Appendix A.2）显示Gemini 2.5 Flash未显著优于Qwen3.5-27B，但更强或更大规模的模型可能产生不同结果。

### 2. 混合优化架构的扩展与变体
- **替代基础优化器**：探索以TPE、SMAC、贝叶斯优化或其他进化策略替代CMA-ES作为混合基础，验证状态共享机制在不同优化器架构中的通用性。
- **代码编辑与CMA-ES的融合**：将CMA-ES与直接编辑源代码的LLM agent结合，使**搜索空间随优化轨迹动态演化**（co-evolve），而非固定在预定义的14维空间或完全无约束的二元选择。

### 3. 搜索空间自动化程度的提升
当前研究通过AST解析自动提取超参数，但**搜索空间范围的设定**仍依赖人工领域知识。未来可探索：
- 基于训练脚本分析自动推断合理的参数范围
- 动态调整搜索空间边界以适应发现的优质配置区域
- 自动识别应纳入优化的潜在超参数（如网络结构更深层的配置）

### 4. 多保真度与迁移学习的集成
论文明确排除了Hyperband、BOHB等多保真度方法以及迁移HPO、零样本方法的范围。未来可将这些方法引入autoresearch基准，验证：
- 早期终止策略在LLM-based优化中的有效性
- 跨任务知识迁移对LLM优化器性能的影响
- 预训练模型在零样本HPO场景下的适用性

### 5. LLM介入策略的自适应优化
Centaur采用固定的LLM比例 $r=0.3$。未来可研究：
- 根据优化阶段动态调整 $r$（如早期探索阶段减少LLM介入，后期精炼阶段增加）
- 基于不确定性估计或CMA-ES的协方差矩阵特征自适应触发LLM调用
- 多LLM协作架构（如小模型用于快速筛选，大模型用于关键决策）

### 6. 更大规模与复杂度的验证
当前实验局限于**50M参数**的小语言模型。未来需在**更大规模模型**（如1B+参数）和**更复杂的训练场景**（如多模态训练、分布式训练配置）中验证：
- 经典方法与LLM方法的扩展性差异
- 混合方法在更高维搜索空间中的有效性
- 计算预算量级变化对方法相对排名的影响

### 7. 失败预测与主动学习机制
针对LLM方法OOM率较高的问题，可探索：
- 训练LLM-based**失败预测器**，在配置评估前预判内存溢出风险
- 将硬件约束（显存、计算图优化）显式编码到LLM的提示模板中
- 利用CMA-ES的协方差矩阵识别并规避高失败概率区域

## 总结

该论文系统比较了经典超参数优化（HPO）算法与基于大语言模型（LLM）的方法在语言模型训练任务上的性能，并提出了一种结合两者优势的新型混合优化器。以下是论文的主要内容总结：

---

### 1. 研究背景与问题设定
研究以 **autoresearch** [Karpathy, 2025a] 为基准平台，在 **nanochat**（约50M参数的Transformer）上优化验证集bits-per-byte（val_bpb）。所有方法在**相同的24小时GPU预算**和**3个随机种子**下运行，LLM推理开销不计入训练时间。通过抽象语法树（AST）自动从训练脚本中提取**14维搜索空间**（13个连续/整数型+1个分类型），减少人工先验干扰。

### 2. 方法对比框架
论文评估了**9种HPO方法**：
- **经典方法**：TPE、CMA-ES、SMAC、随机搜索
- **LLM-based方法**：LLAMBO（两种实现）、Karpathy Agent（固定14维空间）、Karpathy Agent（无约束代码编辑）
- **混合方法**：**Centaur**（本文提出）

所有LLM方法使用自托管的 **Qwen3.5**（对比0.8B与27B参数规模）。

### 3. 核心发现
#### 3.1 固定搜索空间：经典方法占优
在预定义的14维超参数空间内，经典方法（CMA-ES、TPE、SMAC）一致性地优于纯LLM方法。最佳方法为：
- **Centaur**：$0.9763 \pm 0.0005$
- **TPE**：$0.9768 \pm 0.0019$  
- **CMA-ES**：$0.9785 \pm 0.0036$

LLM-based方法在固定空间中表现不佳，部分甚至差于随机搜索，表明**限制LLM在固定搜索空间内无法发挥其优势**。

#### 3.2 无约束代码编辑：模型规模至关重要
**Karpathy Agent（Code）** 通过直接编辑训练源代码（而非选择预定义参数）展现出竞争力（val_bpb $\approx 0.9814$），显著缩小了与经典方法的差距。然而：
- **27B模型**可实现有效代码编辑
- **0.8B模型**完全不足（val_bpb $\approx 0.9910$，接近基线）

#### 3.3 Centaur混合方法：最佳性能
提出的 **Centaur** 方法将 **CMA-ES的完整内部状态**（均值向量 $\mu$、步长 $\sigma$、协方差矩阵 $C$）在30%的试验中共享给LLM，允许LLM基于优化器状态覆盖提议：
$$
x \leftarrow \text{LLM}(\mu, \sigma, C, \text{history}, S)
$$
所有试验结果（无论是否被LLM覆盖）均用于更新CMA-ES状态。

**关键结果**：
- 达到**最佳平均性能**（0.9763）并**显著降低方差**（std从0.0036降至0.0005）
- **Centaur[0.8B]（0.9766）优于Centaur[27B]（0.9763）**，表明当与强经典优化器配对时，廉价小模型足够用于精炼建议，无需大模型
- LLM试验占30%，贡献了25%的历史最优改进

#### 3.4 可靠性优于多样性
分析显示，**OOM（内存溢出）失败率**是比搜索多样性更强的性能预测指标：
- 顶尖方法（TPE、CMA-ES、Centaur）OOM率 $\leq 16\%$
- 表现较差的方法（LLAMBO、随机搜索）OOM率 $\geq 48\%$

LLM方法虽有完整试验历史，但OOM率与随机搜索相当（48-61%），表明**中小规模LLM难以有效学习避免失败区域**，而经典方法通过显式优化状态维持低失败率。

### 4. 消融实验
- **LLM比例 $r$**：过高比例（如$r=0.8$）会损害性能，确认CMA-ES应保持对优化轨迹的主导控制（推荐 $r=0.3$）
- **模型规模**：0.8B模型在固定空间方法中无优势，但在混合架构中 sufficient；27B模型对无约束代码编辑 essential

### 5. 贡献总结
1. 在autoresearch上建立了经典与LLM-based HPO的标准化比较基准
2. 证明经典HPO在固定搜索空间内优于LLM方法，而无约束代码编辑可显著缩小差距
3. 提出**Centaur**，通过共享CMA-ES内部状态与LLM，实现最佳性能且允许使用更小LLM
4. 开源所有方法的per-trial对话日志供后续研究

### 6. 局限与未来方向
- 仅评估单一任务与开源模型（Qwen3.5），未使用Gemini/GPT-4等前沿模型进行完整基准测试
- 搜索空间范围仍需人工设定
- 未来可探索动态搜索空间演化（代码编辑与CMA-ES结合）、自适应LLM介入策略，以及在更大规模模型上的验证



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
