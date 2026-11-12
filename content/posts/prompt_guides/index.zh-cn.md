---
title: "关于Prompt你所需要知道的一切"
subtitle: "撰写Prompt的入门指南"
date: 2026-11-12T15:36:34+08:00
lastmod: 2026-11-12T15:36:34+08:00
draft: true
author: "Sidney Zhang"
authorLink: "https://lyzhang.me"
description: ""
license: ""
images: []

tags: ["Prompt","指南","撰写"]
categories: ["经验分享"]

featuredImage: "chris-abney-ugQb6PFLPWI-unsplash.jpg"
featuredImagePreview: "chris-abney-ugQb6PFLPWI-unsplash.jpg"

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
  keepStatic: false
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  enable: true
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
---

【AI Summary】

<!--more-->

## 什么是PROMPT？

Prompt 在[字典](https://dictionary.cambridge.org/zhs/%E8%AF%8D%E5%85%B8/%E8%8B%B1%E8%AF%AD-%E6%B1%89%E8%AF%AD-%E7%B9%81%E4%BD%93/prompt)里，是这样描述的：

> verb. to make something happen 引起；导致；激起
> 
> noun. a sign on a computer screen that shows that the computer is ready to receive your instructions 电脑提示符
> 
> noun. words that are spoken to an actor who has forgotten what he or she is going to say during the performance of a play （给演员的）题词；提白

所以，对于给AI/LLM的“Prompt”，我们也可以这样理解，就是告诉AI/LLM，需要它做的是什么，提示它说下的起始文本。用正式的说法，就是：

{{</* admonition note "定义 Prompt" true */>}}
Prompt（提示词）是指用户输入给大型语言模型（LLM）或AI工具的文本指令、问题或任务描述。
{{</* /admonition */>}}

所以，从最直觉的角度来说，你的需求写下来或说出来，给到一个AI或者LLM，这就是一个Prompt了。

##  从来处到去处

LLM的起点是Transformer，在其发布之后很长时间，人们都是围绕自然语言处理来看待这些模型，而且需要一事一议，也就是对于不同任务需要不同的预训练和针对性的微调。这个方法其实延续了很久的时间，人们才意识到模型本身的能力或者预训练得到的知识也并不伐可迁移性。

在达到让模型超出自然语言处理这个范畴之前，Petroni(2019)等人[^1]发现了一个提取模型内知识的方法，即“完形填空”。这在我看来，是一个非常早期的Prompt的雏形，通过一个输入控制模型的输出，来尽可能完成我们需要的目标（也就是完形填空中的空缺信息）。不过，这时候一切还只是停留在实验室的范围，也受限于模型能力，大众更是难以使用。

2020年OpenAI发布的GPT-3，是一切的分水岭。它不仅仅有了能力上、知识上的跃迁，也在一定程度上正式让Prompt被大家了解。Brown等人(2020)[^2]在他们当年的研究中，展示了GPT-3在各种任务上的能力，包括文本生成、文本分类、问答等。这时候，人们通过Prompt的上下文学习（In-Context Learning）能力，实现了不修改模型参数，仅靠输入中的任务描述和少量示例，模型就完成了新新任务。

从这时开始，Prompt的价值就开始被人们看见，也渐渐成为AI使用上所必要的一部分。并直接催生了工程范式的转折——从“微调模型适配任务”转向了“优化prompt以适配任务”，_Prompt Engineering_ 作为一个独立方向正式诞生，甚至出现了大量的提示词工程师需求。

零样本、单样本、少样本成为当时最基础的三种Prompt范式，但实践仍以手工试错为主，复杂推理任务效果依然很差。这也推动了研究者兴趣，开始探索如何通过Prompt来实现复杂推理任务，也推动了模型对齐技术的发展。

然后，思维链（Chain-of-Thought, CoT）[^3]被提出，它通过分析任务提示和示例，让模型先输出中间推理步骤再给答案，大幅提升了数学和逻辑任务的准确率。紧接着，零样本CoT[^4]发现只需一句“Let’s think step by step”就能唤醒推理能力，将使用门槛降到几乎为零；随后自我一致性（Self-Consistency）[^5]通过多路径采样投票进一步降低推理出错率。至此，Prompt工程的核心关切，从“指令的清晰度”转向了“推理过程的可引导性”。

简单来说，Prompt在思维链出现之后，已经可以更好地梳理用户的目标，以及实现目标的的过程。不再是被动的，具有了更多的主动性。实际使用上，用户逐渐可以把相对复杂或者关联模糊的事情交给AI或者LLM，而不用特别提供复杂的示例或者繁杂的设定。当然，这一定带来了一些好处，也带来了一些新的特点。



[^1] Petroni et al., Language Models as Knowledge Bases? (2019) arXiv: https://arxiv.org/abs/1909.01066
[^2] Brown et al., Language Models are Few-Shot Learners (2020) arXiv: https://arxiv.org/abs/2005.14165
[^3] Wei et al., Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022) arXiv: https://arxiv.org/abs/2201.11903
[^4] Kojima et al., Large Language Models are Zero-Shot Reasoners (2022) arXiv: https://arxiv.org/abs/2205.11916
[^5] Wang et al., Self-Consistency Improves Chain of Thought Reasoning in Language Models (2022) arXiv: https://arxiv.org/abs/2203.11171