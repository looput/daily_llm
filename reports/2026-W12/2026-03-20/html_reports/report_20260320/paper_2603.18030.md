# Quine: Realizing LLM Agents as Native POSIX Processes

**arXiv**: [2603.18030](https://arxiv.org/abs/2603.18030) · [PDF](https://arxiv.org/pdf/2603.18030)  
**领域**: Agent  
**作者**: Ke  
**综合评分**: 8.64  （novelty: 9.0 · method: 9.5 · evidence: 7.5 · clarity: 8.5）

---

## 摘要

> 本文提出了一种创新的LLM智能体运行时架构Quine，将LLM智能体实现为原生POSIX进程。该方法通过将智能体抽象映射到操作系统进程模型，直接从内核继承了隔离、组合和资源控制等机制，同时自然支持递归委托、通过exec进行上下文更新以及与shell原生组合。论文指出了POSIX进程模型在认知运行时方面的局限性，并提出了超越进程语义的两个扩展方向。参考实现已在GitHub上公开。

---
