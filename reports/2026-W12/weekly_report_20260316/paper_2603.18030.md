# Quine: Realizing LLM Agents as Native POSIX Processes

**arXiv**: [2603.18030](https://arxiv.org/abs/2603.18030) · [PDF](https://arxiv.org/pdf/2603.18030)  
**领域**: Agent  
**作者**: Ke  
**综合评分**: 8.43  （novelty: 9.5 · method: 9.0 · evidence: 7.5 · clarity: 8.5）

---

## 摘要

> 本文提出了一种创新的LLM智能体运行时架构Quine，将LLM智能体实现为原生POSIX进程。该方法通过将智能体抽象直接映射到操作系统进程模型（身份对应PID，接口对应标准流和退出状态，状态对应内存、环境变量和文件系统，生命周期对应fork/exec/exit），避免了在应用层重复实现操作系统已提供的隔离、调度和通信机制。Quine通过单个可执行文件递归生成自身新实例来实现这一模型。设计不仅继承了内核提供的隔离、组合和资源控制能力，还自然支持递归委托、通过exec进行上下文更新以及与shell原生组合。论文还指出了POSIX进程模型的局限性，并提出了超越进程语义的两个扩展方向：任务相对世界和可修改时间。参考实现已在GitHub上开源。

---
