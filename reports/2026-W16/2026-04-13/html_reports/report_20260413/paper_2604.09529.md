# VL-Calibration: Decoupled Confidence Calibration for Large Vision-Language Models Reasoning

**arXiv**: [2604.09529](https://arxiv.org/abs/2604.09529) · [PDF](https://arxiv.org/pdf/2604.09529)  
**领域**: Multimodal  
**作者**: Xiao, Xu, Gan  
**综合评分**: 7.88  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种针对大型视觉语言模型（LVLMs）的置信度校准新方法VL-Calibration。该方法创新性地将整体置信度解耦为视觉置信度和推理置信度，解决了现有方法不匹配LVLMs特性的问题。通过引入基于图像扰动的视觉基础性测量和内部确定性测量来监督视觉置信度，并采用基于令牌的优化策略，有效抑制了幻觉并提升了视觉推理的准确性。在13个基准测试上的实验证明了其有效性。

---

## 详细分析

> **社区热度**: ⭐ 2 (来自 papers.cool)

## 问题定义

这篇论文旨在解决**大型视觉语言模型（LVLMs）在推理过程中存在的过度自信与置信度校准失衡问题**，具体表现为模型在产生幻觉或错误回答时仍表现出极高的确定性，从而限制了其在高风险领域的应用。

该研究主要针对以下核心挑战：

**1. 错误来源的混淆问题**
- 现有面向纯文本LLMs的言语化置信度校准方法通常优化单一的整体置信度分数，基于二元答案级正确性进行监督。
- 然而，LVLMs的错误可能源于两个不同层面：**感知幻觉**（如误读或忽略图像内容）或**推理错误**（在感知正确的前提下产生逻辑谬误）。单一置信度分数无法区分这两种错误来源，阻碍了精确的错误定位。

**2. 语言先验对视觉不确定性的掩盖**
- 研究表明，LVLM推理常被语言先验（language priors）主导，导致内在的视觉不确定性被掩盖。
- 传统方法无法有效识别模型是否真正基于视觉证据进行推理，还是依赖于预训练中的文本偏见生成答案。

**3. 缺乏视觉感知监督信号**
- 在解耦置信度框架中，视觉置信度的训练缺乏真实的感知标签（ground-truth perception labels），现有基于采样的方法（如Self-Consistency）计算开销巨大，而基于内部状态的方法（如logits分析）忽视了视觉 grounding 特性。

为应对上述问题，论文提出了**VL-Calibration**框架，通过强化学习将置信度解耦为视觉置信度与推理置信度，并引入基于图像扰动KL散度与token熵的内在视觉确定性估计作为伪监督信号，同时采用token级优势重加权策略抑制无根据的幻觉。

## 相关工作

根据论文第3页的Related Work章节，相关研究主要分为以下两大方向：

## 1. LLM与LVLM的不确定性估计（Uncertainty Estimation）

### 基于采样的方法（Sampling-based Methods）
- **Self-Consistency** (Wang et al., 2023)：通过多数投票选择最频繁的答案来推断不确定性。
- **Semantic Entropy** (Farquhar et al., 2024; Aichberger et al., 2025)：在语义层面测量响应簇之间的一致性，而非表面文本的一致性。
- **VL-Uncertainty** (Zhang et al., 2024)：针对LVLMs，通过对语义等价但扰动后的输入进行多次采样来估计不确定性。

### 基于内部状态的方法（Internal-state Approaches）
利用模型的内部信号量化确定性，包括：
- **统计指标**：如对数概率 (Vashurin et al., 2025)、困惑度 (Zhao et al., 2025)。
- **Self-Certainty** (Kang et al., 2025)：测量输出分布与均匀分布的KL散度。
- **真实token概率** (Kadavath et al., 2022)：评估模型分配给特定真实token的概率。

## 2. 言语化置信度校准（Verbalized Confidence Calibration）

### 监督微调方法（Supervised Fine-Tuning, SFT）
- **SaySelf** (Xu et al., 2024)：使用从GPT-4蒸馏的理性标签训练模型输出言语化置信度。
- **ConfTuner** (Li et al., 2025)：基于token级logit监督进行校准。

### 强化学习方法（Reinforcement Learning, RL）
- **LACIE** (Stengel-Eskin et al., 2024)：在说话者-听者框架内使用直接偏好优化（DPO）。
- **PPO-C** (Leng et al., 2025) 与 **Rewarding Doubt** (Stangel et al., 2025)：使用近端策略优化（PPO），通过Brier分数或对数惩罚等定制奖励函数激励准确的置信度表达。
- **RLCR** (Damani et al., 2025)：使用GRPO（Group Relative Policy Optimization）联合优化任务准确率和校准性能。

## 现有研究的局限性
上述方法虽在纯文本任务中有效，但存在以下关键局限：
- **未解耦错误来源**：现有方法通常优化单一的整体置信度分数，无法区分感知错误与推理错误。
- **忽视视觉特性**：现有不确定性估计方法或计算开销巨大（采样方法），或忽视LVLMs的视觉 grounding 特性（内部状态方法）。
- **LVLM校准探索不足**：现有言语化置信度校准研究主要针对纯文本LLMs，对多模态场景下的置信度校准探索有限。

## 解决方案

该研究提出**VL-Calibration**框架，通过以下三个核心机制解决大型视觉语言模型的置信度校准问题：

## 1. 解耦置信度框架（Decoupled Confidence Inference）

将单一的整体置信度解耦为**视觉置信度**与**推理置信度**，使模型能够分别表达对视觉感知和逻辑推理的确定性：

- **结构化生成轨迹**：模型生成过程被显式分为两个阶段，轨迹表示为：
  $$\tau = (\underbrace{z_{\text{vis}}, c_{\text{vis}}}_{\text{Visual Phase}}, \underbrace{z_{\text{reas}}, c_{\text{reas}}}_{\text{Reasoning Phase}}, y)$$
  其中 $z_{\text{vis}}$ 为视觉描述（如图像密集标注），$z_{\text{reas}}$ 为推理链，$c_{\text{vis}}, c_{\text{reas}} \in [0,1]$ 为对应的置信度分数。

- **保守的聚合策略**：采用**调和平均**（Harmonic Mean）计算整体置信度 $\Phi$，确保只有当两个阶段的置信度均较高时，整体置信度才高：
  $$\Phi(\hat{c}_{\text{vis}}, \hat{c}_{\text{reas}}) = \frac{2 \cdot \hat{c}_{\text{vis}} \cdot \hat{c}_{\text{reas}}}{\hat{c}_{\text{vis}} + \hat{c}_{\text{reas}}}$$

## 2. 内在视觉确定性估计（Intrinsic Visual Certainty Estimation）

针对缺乏真实感知标签的问题，提出无需外部标注的视觉确定性估计方法，从两个互补维度量化感知质量：

- **视觉接地性（Visual Grounding）**：通过测量模型对图像扰动的敏感性来检测幻觉。对原始图像 $I$ 和随机遮挡（mask ratio=0.8）后的图像 $I'$，计算输出分布的KL散度：
  $$D_{\text{KL}} = \frac{1}{T} \sum_{t=1}^{T} \text{KL}\left( \pi(\cdot|z_{\text{vis},<t}, I) \parallel \pi(\cdot|z_{\text{vis},<t}, I') \right)$$
  较高的 $D_{\text{KL}}$ 表明生成内容紧密依赖于视觉输入，而非语言先验。

- **内部确定性（Internal Certainty）**：通过视觉描述中token的平均熵 $H$ 衡量模型内部冲突：
  $$H = -\frac{1}{T} \sum_{t=1}^{T} \sum_{v \in \mathcal{V}} \pi(v|z_{\text{vis},<t}, I) \log \pi(v|z_{\text{vis},<t}, I)$$
  较低的熵表示概率分布尖锐，反映模型内部确信。

- **视觉确定性分数**：整合上述指标，通过对数比率压缩数值范围以保证训练稳定性：
  $$S_{\text{vis}} = \log(D_{\text{KL}} + \epsilon) - \log(H + \epsilon)$$
  经批次标准化与Sigmoid变换后映射至 $[0,1]$ 作为伪标签 $\tilde{S}_{\text{vis}}$。

## 3. 基于确定性感知的强化学习（Certainty-Aware RL）

基于Group Relative Policy Optimization (GRPO) 构建训练目标，通过以下机制实现精细校准：

- **视觉确定性奖励（Visual Certainty Reward）**：将估计的视觉确定性作为视觉置信度的监督信号：
  $$R_{\text{vis}} = - \left( \hat{c}_{\text{vis}} - \text{sg}(\tilde{S}_{\text{vis}}) \right)^2$$
  其中 $\text{sg}(\cdot)$ 为停止梯度算子，防止梯度回传至估计模块。

- **Token级优势重加权（Token-Level Advantage Reweighting, TAR）**：标准GRPO对所有token均匀分配信用，而TAR根据token级视觉确定性动态调整优势函数 $\hat{A}_t$：
  $$\hat{A}_t^{\text{TAR}} = 
  \begin{cases} 
  \hat{A}_t \cdot \left[ 1 + \lambda_{\text{TAR}}(1 - 2\tilde{S}_{\text{vis}}(t)) \right] & \text{if } t \in z_{\text{vis}} \land \hat{A}_t < 0 \\
  \hat{A}_t & \text{otherwise}
  \end{cases}$$

  该机制对**视觉不确定性高**（$\tilde{S}_{\text{vis}}(t) \to 0$）且**模型出错**（$\hat{A}_t < 0$）的token施加更强惩罚，有效抑制无根据的视觉幻觉，同时保留有效的视觉感知。

- **复合奖励函数**：总奖励结合准确性、整体校准与视觉校准：
  $$R(\tau, y^*) = \lambda_{\text{acc}} R_{\text{acc}} + \lambda_{\text{cal}} R_{\text{cal}} + \lambda_{\text{vis}} R_{\text{vis}}$$
  其中 $R_{\text{acc}} = \mathbb{1}_{y \equiv y^*}$，$R_{\text{cal}} = -(\Phi - \mathbb{1}_{y=y^*})^2$。

## 实验验证

根据论文第4-8页的实验章节，该研究进行了以下系统性实验：

## 1. 实验设置与评估

**训练配置**
- 从ViRL-39K中随机选取12,000个数据点构建训练集（VL-Calibration-12K）
- 应用于Qwen3-VL-4B、Qwen3-VL-8B和InternVL3.5-4B-MPO，验证不同规模和架构的适用性

**对比基线**
- **推理阶段方法**：Verbalize、P(True)、SteerConf
- **训练阶段方法**：RLVR、LACIE、ConfTuner、PPO-C、SaySelf、Rewarding Doubt、RLCR

**评估基准**（13个数据集，涵盖4类任务）：
- **数学与几何推理**：DynaMath、Geo3K、MathVerse、MathVision、MathVista、WeMath
- **逻辑推理**：LogicVista  
- **视觉主导推理**：CLEVR、MathVerseV
- **多学科推理**：A-OKVQA、MMK12、MMMU-Pro、ViRL-39K

**评估指标**：准确率（ACC）、期望校准误差（ECE）、ROC曲线下面积（AUROC）

## 2. 主要结果（Table 1 & Table 2）

**模型规模内性能**（Qwen3-VL 4B/8B）：
- **校准性能**：ECE从0.421降至0.098（4B模型），从0.401降至0.071（8B模型）
- **推理性能**：相比最强基线，平均准确率提升2.3%（4B）和3.0%（8B）
- **跨域泛化**：在MMMU-Pro等分布外基准上，准确率提升2.2%，ECE从0.112降至0.017（A-OKVQA）

**跨架构泛化**（Table 2）：
- **Qwen3-VL-30B**：AUROC提升至0.767，ECE降至0.082，准确率从0.652提升至0.803
- **InternVL3.5-4B**：相比RLCR基线，准确率提升至0.689，ECE降至0.103

## 3. 消融实验（Table 3）

验证各组件的有效性：
- **解耦置信度**：单独解耦而不加视觉监督（仅使用整体Brier分数）几乎无提升（ECE: 0.167→0.164）
- **视觉确定性估计（VCE）**：单独使用Entropy或KL散度均可降低ECE，但**组合使用**效果最佳（ECE: 0.167→0.121）
- **Token级优势重加权（TAR）**：在VCE基础上加入TAR，进一步提升准确率至0.727，ECE降至0.098

## 4. 深度分析实验

**视觉确定性估计验证**（Figure 3）：
- 使用Gemini-3-pro-preview作为裁判，评估1,500个图像描述的质量
- 相比Self-Certainty、VL-Uncertainty、Semantic Entropy等基线，该估计方法在AUROC（0.746）、Spearman相关系数（0.496）和Kendall's Tau（0.370）上均表现最优
- **扰动鲁棒性分析**（Table 7）：验证随机遮挡（80%比例）优于高斯模糊、噪声添加和中心裁剪

**可靠性图分析**（Figure 4 & Figure 11-12）：
- 基线模型表现出严重过度自信（高置信度区间准确率远低于置信度）
- VL-Calibration的置信度分布紧密贴合对角线（完美校准），ECE降低超过4倍

**解耦置信度效果**（Figure 5 & Table 4）：
- **视觉错误识别**：解耦视觉置信度能有效区分视觉正确与错误响应（基线对两者均赋予高置信度，而该方法显著降低视觉错误样本的置信度）
- **不可回答样本检测**：在DynaMath中去除图像构造不可回答样本，该方法实现最大的置信度差距（$\Delta=0.616$），显著优于RLCR（0.405）和基线（0.228）

**置信度分布分离性**（Figure 6）：
- 二维热力图显示视觉置信度与推理置信度呈明显分离分布，证实两者测量不同不确定性来源（模型可能视觉确定但推理不确定，或反之）

**Token级重加权可视化**（Figure 7）：
- 高视觉不确定性token不仅出现在视觉内容词（如"highest"），也出现在逻辑连接词（如"while"、"indicating"），验证了基于视觉确定性进行优势重加权的必要性

**训练动态**（Figure 14）：
- 基于视觉确定性的训练在100步内快速收敛（ECE降至0.1），展现稳定的训练信号

**失效模式分析**（Figure 9-10）：
- **高置信度错误**：主要源于视觉幻觉（Visual Hallucination），其次是推理偏见和捷径依赖
- **低置信度正确**：主要源于视觉模糊（Visual Ambiguity），表明解耦机制能有效捕捉感知驱动的不确定性

## 未来工作

基于论文的局限性分析与方法论延伸，以下方向值得进一步探索：

## 1. 更大规模模型的验证与扩展

当前评估受计算资源限制，仅覆盖至30B参数规模（Qwen3-VL-30B）。**超大规模模型（70B+）**上的有效性尚未验证：
- 随着模型容量增长，语言先验的主导作用可能呈现非线性变化，解耦置信度的必要性及重加权行为可能表现出与中小规模模型不同的特征
- 需要验证视觉确定性估计信号在超大规模模型中是否仍保持足够的区分度与训练稳定性

## 2. 视觉不确定性估计的精细化

现有方法依赖**随机遮挡（Random Patch Masking）**与**Token熵**的组合，存在优化空间：
- **自适应扰动策略**：探索基于注意力权重的选择性遮挡，或引入对抗性扰动以更精准地检测视觉脆弱性
- **多尺度视觉分析**：当前方法主要基于整体图像层面的KL散度，可探索细粒度区域级（region-level）或对象级（object-level）的视觉确定性估计
- **动态阈值机制**：当前使用固定的掩码比例（0.8），可研究根据图像复杂度自适应调整扰动强度的策略

## 3. 跨模态与复杂场景的泛化

- **视频语言模型**：将解耦置信度框架扩展至时序维度，引入**时序置信度**（temporal confidence）以处理视频帧间的不一致性
- **多图像推理**：在需要对比多张图像的场景（如视觉问答中的多图推理）中，探索图像间置信度的聚合与冲突检测机制
- **文档理解**：针对富含结构化信息的文档（表格、图表、流程图），研究布局感知的视觉置信度建模

## 4. 计算效率与实时应用

视觉确定性估计引入**11%的额外计算开销**（第二次前向传播）：
- **轻量化估计器**：训练一个小型辅助网络预测视觉确定性，避免完整的前向传播
- **稀疏采样策略**：仅在关键token（如视觉描述中的实体词）上计算视觉确定性，而非完整序列
- **缓存机制**：利用视觉特征的跨样本共享特性，设计确定性估计的缓存与复用策略

## 5. 下游任务的具体适配

论文提及医疗、法律等高风险领域，但未进行专门验证：
- **领域特定校准**：在医学影像诊断中，结合专业医生的视觉注意力模式调整视觉确定性计算
- **人机协作接口**：利用解耦置信度设计分层确认机制——当视觉置信度低时触发图像增强或人工复核，当推理置信度低时触发知识检索

## 6. 与模型可解释性的深度结合

- **归因分析**：利用解耦的置信度分数进行**错误溯源**——区分是"看错了"（感知错误）还是"想错了"（推理错误），并据此生成针对性的改进建议
- **可视化工具**：开发交互式可视化界面，实时展示模型在视觉理解与逻辑推理两个维度上的置信度热力图

## 7. 理论层面的深化

- **校准误差的理论界限**：分析在视觉-语言多模态场景下，解耦置信度相比单一置信度的**贝叶斯最优性**改进
- **语言先验的量化研究**：建立数学框架量化语言先验对视觉不确定性的压制效应，并分析解耦机制如何理论上缓解此问题

## 总结

**VL-Calibration: Decoupled Confidence Calibration for Large Vision-Language Models Reasoning** 针对大型视觉语言模型（LVLMs）在推理过程中存在的**过度自信与幻觉问题**，提出了一种解耦式置信度校准框架。以下为核心内容总结：

## 1. 研究问题与动机

现有言语化置信度校准方法（主要针对文本LLMs）存在两个关键局限：
- **错误来源混淆**：单一整体置信度无法区分**感知幻觉**（视觉误读）与**推理错误**（逻辑谬误），阻碍精确错误定位
- **语言先验主导**：LVLM推理常被语言先验主导，导致视觉不确定性被掩盖，产生错误校准

## 2. 方法论：VL-Calibration 框架

### 2.1 解耦置信度架构
将传统单一置信度解耦为两个维度：
- **视觉置信度** ($\hat{c}_{\text{vis}}$)：对图像感知描述的确信程度
- **推理置信度** ($\hat{c}_{\text{reas}}$)：对逻辑推理链的确信程度

通过**调和平均**（Harmonic Mean）聚合为整体置信度 $\Phi$，确保保守性：
$$\Phi(\hat{c}_{\text{vis}}, \hat{c}_{\text{reas}}) = \frac{2 \cdot \hat{c}_{\text{vis}} \cdot \hat{c}_{\text{reas}}}{\hat{c}_{\text{vis}} + \hat{c}_{\text{reas}}}$$

### 2.2 内在视觉确定性估计（无需真实标签）
针对视觉置信度缺乏监督信号的问题，提出基于模型内部状态的估计方法：
- **视觉接地性**：通过**KL散度**衡量模型输出对图像扰动（随机遮挡）的敏感性，检测是否依赖视觉而非语言先验：
  $$D_{\text{KL}} = \frac{1}{T} \sum_{t=1}^{T} \text{KL}\left( \pi(\cdot|z_{\text{vis},<t}, I) \parallel \pi(\cdot|z_{\text{vis},<t}, I') \right)$$
- **内部确定性**：通过**Token熵** $H$ 衡量视觉描述生成时的概率分布尖锐度

整合为视觉确定性分数：
$$S_{\text{vis}} = \log(D_{\text{KL}} + \epsilon) - \log(H + \epsilon)$$

### 2.3 Token级优势重加权（TAR）
在GRPO强化学习中引入细粒度优化策略：
- 对视觉不确定性高（$\tilde{S}_{\text{vis}}(t) \to 0$）且模型出错（优势 $\hat{A}_t < 0$）的token施加更强惩罚
- 公式表达：
  $$\hat{A}_t^{\text{TAR}} = \hat{A}_t \cdot \left[ 1 + \lambda_{\text{TAR}}(1 - 2\tilde{S}_{\text{vis}}(t)) \right]$$
- 有效抑制无根据的视觉幻觉，同时保留有效感知

## 3. 实验结果

在**13个基准测试**（涵盖数学、几何、逻辑、视觉主导及多学科推理）上的验证表明：

- **校准性能显著提升**：Qwen3-VL-4B的期望校准误差（ECE）从0.421降至0.098，降低超过4倍；AUROC提升至0.763
- **准确率同步提升**：相比最强基线，平均准确率提升2.3%（4B模型）和3.0%（8B模型），在DynaMath、MathVerse等复杂视觉推理任务上提升显著
- **跨规模与架构泛化**：在Qwen3-VL-30B和InternVL3.5-4B上均保持有效性（ECE分别降至0.082和0.103）
- **不可回答样本识别**：在去除图像的DynaMath测试中，相比基线实现更大的置信度差距（$\Delta=0.616$），有效识别视觉信息缺失场景

## 4. 关键贡献

- **范式创新**：首次为LVLMs提出**解耦式言语化置信度校准**，实现感知不确定性与推理不确定性的显式分离
- **无监督估计**：提出无需外部标注的内在视觉确定性估计方法，结合视觉接地性与内部一致性
- **训练稳定性**：通过Token级优势重加权实现细粒度幻觉抑制，解决二进制结果奖励的均匀信用分配问题

该框架为LVLMs在高风险领域（医疗、法律）的应用提供了可靠的置信度表达机制，同时提升了视觉推理的准确性与可解释性。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
