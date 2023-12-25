# PromptAgent Strategic Planning with Language Models Enables Expert-level Prompt Optimization

论文链接：https://arxiv.org/abs/2310.16427

github：https://github.com/xinyuanwangcs/promptagent

## 1.论文背景

在特定任务上发挥潜力的 Prompt 往往需要专家进行大量的手工调整，而自动化生成这种专家级 Prompt 尚未取得成功，现有的方法往往无法充分考虑到领域知识的深度，也难以有效地探索 Prompt 的广阔空间，而且现有的 Prompt 优化技术往往集中于短期的、浅层次的搜索，而忽略了长期的、深入的领域知识。这些方法的局限性在于，它们不能自动地生成与人类专家手工制作同等质量的 Prompt。

## 2.论文解决了什么问题

作者比较了一些启发式的 Prompt 生成方法：比如蒙特卡洛搜索、Gibbs采样，发现这些方法忽略了 Prompt 的本质是通过多次人机交互来修改校验的过程。基于此作者提出模拟人机交互的过程，利用大模型的反思能力，不断试错迭代从而生成更专业的 Prompt。PromptAgent这个新的优化方法，可以自动地生成与专家手工制作同等质量的 Prompt。

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/expert_prompt.png)

## 3.论文方法

作者提出可以自动优化 Prompt 的框架—— PromptAgent，结合大模型的自我反思特点与蒙特卡洛树搜索规划算法，自动迭代检查 Prompt，发现不足，并根据反馈对其进行改进，寻找通往最优 Prompt 的路径，可以将简单的初始 Prompt 打造成媲美人类专家手工设计的 Prompt。

整个方法基于 MCTS，具体步骤如下：

1.给定当前状态（即 Prompt），使用基本模型（gpt-3.5）收集错误。

2.使用优化模型（GPT-4）提供错误反馈

3.根据错误和反馈来优化 Prompt

4.循环1-3，最终导向专家级 Prompt。

策略优化过程：对 Prompt 优化的过程可以将 PromptAgent 与规划算法蒙特卡洛树搜索（MCTS）相结合，从而产生专家级Prompt。

基于蒙特卡洛树搜索（MCTS）逐步构建树状结构来实现策略搜索，每个节点表示一个状态，每个边表示一个动作。算法通过四个操作：选择、扩展、模拟和反向传播，来更新状态-动作的价值函数，并扩展树结构。迭代过程在达到预定义的迭代次数后结束，从所有路径中挑选一条最优的路径和节点（Prompt）作为最终结果。

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/MCTS_PromptAgent.png)

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/example_PromptAgent.png)

## 4.实验分析

作者在三个不同领域的12个任务进行实验： 6个BIG-Bench Hard (BBH)任务，3个生物医学领域特定任务，3个自然语言理解任务。

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/BBH_result.png)

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/NLU_result.png)

泛化性：作者在GPT-3.5, GPT-4， PaLM2 上进行实验发现指标都有较大的提升。

## 5.其它

APE:自动指令生成和选择的框架，首先大模型生成候选指令池，然后把指令交给大模型打分，最后选择打分高的作为最后的指令。算力消耗大

## 6.总结

这篇论文介绍了 PromptAgent，一种 Prompt 优化框架，结合 LLMs 的自我反思能力将任务的领域特定知识纳入到新生成的 Prompt 中，并使用 MCTS 规划能力遍历复杂的 Prompt 空间找到专家级 Prompt，PromptAgent 优化后的 Prompt 表现出专家级的特征。

现在大部分研究聚焦于通用 Prompt 设计激发模型潜力以及人工专家 Prompt 实现特定领域效能，我觉得在某些任务上效果较好的 Prompt，可能是因为该类型的数据在训练数据分布上和 prompt 存在较大的相似性，从而在某些 prompt 上处理指定任务会达到相对较好的效果。自动构建专家级 Prompt 则能在一定程度上找到最适合的 Prompt。
