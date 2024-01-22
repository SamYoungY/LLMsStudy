# Self-RAG Learning to Retrieve, Generate, and Critique through Self-Reflection

论文链接：https://arxiv.org/abs/2310.11511

论文代码：https://github.com/AkariAsai/self-rag

## 1.论文背景

尽管 LLM 的模型和数据规模不断增加，但它们仍然面临事实错误的问题。现有的 RAG 方法可以通过增强 LLM 的输入来减少知识密集任务中的事实错误，但还是有以下问题存在：1）如何保证检索内容是有效，或有用的？2）如何验证检索的内容对输出的结果是支持的？3）如何验证输出的结果是来自检索还是模型的生成？


## 2.论文解决了什么问题

作者引入了自反思的检索增强生成方法（Self-RAG），该方法通过按需检索和自我反思来改进 LLM 的生成质量。Self-RAG 会训练一个任意的 LM，使其能够反思自己的生成过程，并生成任务输出和中间的特殊 tokens（reflection tokens）。这些反思 tokens 被分类为检索 tokens 和评论 tokens，分别表示需要检索的需求和其生成质量。


## 3.论文方法

1.RAG vs Self-RAG

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/RAG%20vs%20Self-RAG.png)

对比 RAG，Self-RAG 框架的不同之处就是：在生成过程中利用特殊的 token 达到更精细的控制——要不要检索、检索内容相关性怎样、利用检索内容生成的质量怎样。达到这些目的，就会让 RAG+LLM 生成的内容在质量、事实性、验证性上得到提升。

2.核心算法

以下是 Self-RAG 框架中使用的四种反思 tokens 的类型：

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/Self-RAG%20tokens.png)

以下是推理算法的伪代码，其中涉及三个主要组件：生成器语言模型（LM）、检索器（R）、以及大型文本段落集合（D）

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/Self_RAG%20%E7%AE%97%E6%B3%95.png)

对于每一个 x 和前序生成结果 y_n (n < t)，模型都会解码一个检索标记，以评估检索的效用。如果不需要检索，模型就会像标准 LM 一样预测下一个输出段落。如果需要检索，模型就会生成一个评论标记，用于评估检索段落的相关性，然后生成下一个回复段落以及一个评论标记，用于评估回应段中的信息是否得到段落的支持。

3.Self-RAG 训练

Self-RAG 的训练包括三个模型：检索器（Retriever）、评论家（Critic）和生成器（Generator）。

首先，训练评论家，使用检索器检索到的段落以及反思 token 增强指令-输出数据。

然后，使用标准的下一个 token 预测目标来训练生成器 LM，以学习生成 自然延续(continuations)以及特殊 tokens (用来检索或批评其自己的生成内容).

4.Self-RAG 推理

在推理阶段，SELF-RAG通过生成反射标记来自我评估输出结果，从而使其行为适应不同的任务要求。对于要求事实准确性的任务，目标是让模型更频繁地检索段落，以确保输出结果与可用证据密切吻合。在开放性较强的任务中，如撰写个人经历文章，重点则转向减少检索次数，优先考虑整体创造性或实用性得分。

因此，在推理过程中需要实施控制以满足这些不同目标。方法包括：

1）基于阈值的自适应检索。

2）基于评论 tokens 的树解码


## 4.实验分析

1.实验结果

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/Self_RAG%20%E7%BB%93%E6%9E%9C.png)

Self-RAG在六项任务中均超越了原始的 ChatGPT 或 LLama2-chat，并且在大多数任务中，其表现远超那些广泛应用的检索增强方法。

2.消融实验

![](https://github.com/Kayin211/LLMsStudy/blob/master/%E8%AE%BA%E6%96%87%E8%A7%A3%E8%AF%BB/pic/Self_RAG%20%E6%B6%88%E8%9E%8D.png)

每一个组件和技术在Self-RAG中都起到了至关重要的作用。调整这些组件可以显著影响模型的输出性质和质量，这证明了它们在模型中的重要性。


## 5.总结

Self-RAG 框架给 LLM+RAG 提供了一种新的结合方式——在生成过程中加入多维、更细粒度的控制与评价标签，让 LLM 对检索内容的利用，以及利用效果有了更直接的操作。但也有缺点，在输出的时候要多次生成和判断标签，会增加推理成本。

优化空间，1）比如标签的优化（用更少的标签，或者代表其他含义的标签）；2）在召回相关文档后，用一个小模型来判断，选择top1作为最终结果，减少循环计算。
