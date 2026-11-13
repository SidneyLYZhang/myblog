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

所以，对于给AI/LLM的“Prompt”，我们也可以这样理解，就是告诉AI/LLM，需要它做的是什么，提示它继续下去的起始文本。用正式的说法，就是：

{{</* admonition note "定义 Prompt" true */>}}
Prompt（提示词）是指用户输入给大型语言模型（LLM）或AI工具的文本指令、问题或任务描述。
{{</* /admonition */>}}

所以，以一个最直觉的角度来说，你的需求写下来或说出来，给到一个AI或者LLM，那你写或者说的这几句话是一个Prompt了。那么，为什么有时发给AI的Prompt并不能总是按照我们的预期工作呢？这并非是因为你不会写Prompt，而是现实局限所致的结果，或者说，曾经流行一时的Prompt工程，只是人本身为了让AI可以更好地按需求工作，而被临时提高了优先级，并非需要配合AI工作的必要方式。

为什么会这么说呢？这就要从头说起了。

##  从来处到去处

一般来说，LLM的起点是Transformer。在其发布之后很长时间，人们都是围绕自然语言处理来看待这个模型，而且需要一事一议，也就是对于不同任务需要不同的预训练和针对性的微调。这个方法其实延续了很久的时间，人们才意识到模型本身的能力或者预训练得到的知识也并不伐可迁移性。

在达到让模型超出自然语言处理这个范畴之前，Petroni(2019)等人[^1]发现了一个提取模型内知识的方法，即“完形填空”。这在我看来，是一个非常早期的Prompt的雏形——通过一个输入控制模型的输出，来尽可能完成我们需要的目标（也就是完形填空中的空缺信息）。不过，这时候一切还只是停留在实验室的范围，也受限于模型能力，大众更是难以使用。

2020年OpenAI发布的GPT-3，是一切的分水岭。它不仅仅有了能力上、知识上的跃迁，也在一定程度上正式让Prompt被大家了解。Brown等人(2020)[^2]在他们当年的研究中，展示了GPT-3在各种任务上的能力，包括文本生成、文本分类、问答等。这时候，人们才了解到，通过Prompt的上下文学习（In-Context Learning）能力，实现了不修改模型参数，仅靠输入中的任务描述和少量示例，就可以让模型完成新任务。

从这时开始，Prompt的价值就开始被人们看见，也渐渐成为AI使用上所必要的一部分。并直接催生了工程范式的转折——从“微调模型适配任务”转向了“优化prompt以适配任务”，_Prompt Engineering_ （提示词工程）作为一个独立方向正式诞生，甚至出现了大量的提示词工程师需求。

零样本、单样本、少样本成为当时最基础的三种Prompt范式[^10]，但实践仍以手工试错为主，复杂推理任务效果依然很差。这也推动了研究者兴趣的迁移，开始探索如何通过Prompt来实现复杂推理任务，同时也推动了模型对齐技术的发展。

然后，思维链（Chain-of-Thought, CoT）[^3]被提出，它通过分析任务提示和示例，让模型先输出中间推理步骤再给答案，大幅提升了数学和逻辑任务的准确率。紧接着，零样本CoT[^4]发现只需一句“Let’s think step by step”就能唤醒推理能力，将使用门槛降到几乎为零；随后自我一致性（Self-Consistency）[^5]通过多路径采样投票进一步降低推理出错率。至此，Prompt工程的核心关切，从“指令的清晰度”转向了“推理过程的可引导性”。

简单来说，Prompt在思维链出现之后，已经可以更好地梳理用户的目标，以及完善实现目标的过程。不再是被动的，具有了更多的主动性。实际使用上，用户逐渐可以把相对复杂或者关联模糊的事情交给AI或者LLM，而不用特别提供复杂的示例或者繁杂的设定。当然，这一定带来了一些好处，也带来了一些新的特点。同时，这一段时间也带来了一次LLM能力上的突飞猛进，但人与AI之间的协同方式，还维持相对稳定。

直到ReAct模式[^6]的出现，让AI/LLM有了可以在“思考”、“行动”之间交替进行的能力[^7] [^8]，这在一定程度上更接近人类的思考模式，并深刻的改变了模型、人之间的协作模式。相应的，Prompt的重点也因这一改变而继续改变。Prompt不再是一次性的静态指令，而成为驱动多步执行的交互协议，我们现在熟知的Agent概念，也由此开始萌芽。

与此同时，ChatGPT的普及使Prompt从技术圈，更深入的进入了大众视野。到2024—2025年，实践走向结构化与可编程化：XML标签、JSON Schema、模板化系统提示成为企业级应用的标配，Prompt本身越来越像一种“配置语言”，而不是一个简单的文本指令。但天花板也随之显现。Prompt无法注入私有知识、管理跨会话状态或约束工具调用，单靠措辞优化已经略显不足。需要更多的方法来约束、限定AI/LLM的执行。“Harness”这个概念也随之开始被人了解。

最先被广泛接受的，就是Context Engineering（上下文工程）[^9]。核心问题也从“怎么表达任务”变为“模型在决策时应该看到什么信息环境”。2026年，则更进一步，从单纯的约束，正式出现Harness Engineering等概念，关注的不再是单次输入，而是模型运行其中的系统级约束、工具编排与验证机制。

这时候，Prompt本身没有“消失”，它被整合成了更宏观工程体系中的一个基础层，因为构成上下文的信息，依然是Prompt。我们在前台提交给AI/LLM的信息，也就变成了整体上下文信息的一个部分，可以是任务起始，中间修正，也可以是决定任务是否继续的判断依据。

在当下，Prompt对于AI的使用，既重要也不再那么重要。重要的地方在于它塑造了AI/Agent所见的“环境”，它也连接了用户和AI/LLM，不重要的原因，也是因为最开始的那个用户动机，并不会那么显著的影响后续人物的执行，AI依然还是在做它擅长的事情，寻找满足这一动机的后续信息（Token）。但在现有的Agent/Harness环境下，动机这个起点可以有更多的后续条件让人和AI可以互动，从而逐步贴近那个人的期望。

## 在当下的Prompt

Prompt的构建，或者说，上下文的构建，这要复杂的多，需要专门来探讨上下文构建。那么，我们先来把这个内容限缩一下，想想抛开一切因素，只面对Prompt，我们怎么把自己的需求更好的让AI/LLM理解和执行。

Prompt从诞生之初，就作为驱动AI工作的基本内容，过去更像是一次性的静态指令，即使现在，在驱动AI绘图/视频生成等任务上，依然有这样的特点，就是抽卡。LLM本质依然是建立在概率基础上的，为了增加我们需要内容的出现，这就是Prompt的意义所在。但确定性才是工作的意义，Harness、Context Engineering等概念，就是为了这个确定性，而逐渐发展起来的。

在我眼中，当下的Prompt，就是一份模型/Harness/Agent能理解的任务清单，包含需求、上下文、约束与验收标准等等信息。请注意，这个清单中的上下文，并不是构成LLM互动过程的上下文，任务清单中的上下文，是帮助LLM理解任务需求的组成部分。怎么理解这一点呢？

让我先把我自己常用的Prompt的框架**TCCAE**列出来，以此为基础，再来深入讨论Prompt的构建：

{{</* admonition note "Prompt框架" true */>}}
【任务需求|Task】 ； 【任务上下文|Context】 ； 【任务约束|Constraints】 ； 【验收标准|Acceptance Criteria】 ； 【任务示例|Task Examples】
{{</* /admonition */>}}

当然，也不是没有别的框架可以选择，比如：

| 框架 | 核心要素 | 适用场景与特点 |
| :--- | :--- | :--- |
| **TRIRO** | **T**ask, **R**ole, **I**teration, **R**eference, **O**utput 【T任务、R角色、I迭代、R参考、O输出】 | 通用性强的对话式框架，特别强调通过**迭代（Iteration）** 来逼近理想结果，适合创意、写作和逐步优化的任务。 |
| **CTCO** | **C**ontext, **T**ask, **C**onstraints, **O**utput 【C上下文、T任务、C约束、O输出】 | 2026年生产环境中替代纯对话式Prompt的框架，更强调**约束（Constraints）** 和**输出（Output）** 的精确性，适合工程化和自动化场景。 |
| **GCOB** | **G**oal, **C**ontext, **O**utput, **B**oundary 【G目标、C上下文、O输出、B边界】 | 面向研发任务的实用框架，突出**边界（Boundary）** 定义（如“哪些代码不能修改”），有助于将AI限制在安全的操作范围内。 |

看了这四个框架的对比之后，是不是已经有些理解这个任务清单的作用了呢？

现在，先来回答第一个问题，任务清单中的上下文是什么？可能是目标的背景、任务的背景，也可能是目标受众，还可能是之前的计划、想法、思考等等围绕这个目标或者任务的信息。这个信息可以是直接相关的，也可以是间接相关的，而这个上下文的意义就在于它可以让LLM/AI更好的理解这个任务，以便它更好的实现你的目标。

延伸来说，在狭隘的Prompt中，也就是我说的这个任务清单里，不止上下文，任务需求、约束、验收标准和示例，都是为了让LLM提供我们期望的结果。对于我来说，迭代的基础是模型/AI已经提供了他的理解，在这个理解基础上，继续完善任务清单，就是在迭代，可能比单纯的在Prompt中设定迭代方法要简单的多，当然，你也可以在验收标准中把你想要的迭代一股脑写入Prompt，这也是合适的方法。

但不要忘记，LLM的注意力可能比人还是要差很多，Prompt很长的时候它就会忽略很多东西，这也是概率性的，所以迭代的另一个价值就是帮你恢复LLM忽略的地方。同时，这也说明，你说的越多，LLM/AI越有可能出现浪费你的努力的情况，甚至是很自信的浪费。

那么，在框架基础上，可以进一步帮助我们整理任务清单的原则，就可以这样总结出来：

**1. 清晰与直接，将“思维需求”转为“文本需求”**

避免使用华丽辞藻或委婉暗示，直接陈述核心指令。你脑海中的需求再清晰，AI收到的也只有打出来的文字。**示例**：与其写“能不能麻烦你，如果方便的话，帮我看看这段代码有没有什么可以改进的地方？”，不如直接说：“优化以下Python函数的时间复杂度至O(n)，并解释关键改动。”

**2. 提供完整上下文与“Why”**

明确告知任务背景、目标受众、项目环境及约束条件。解释**约束背后的动机**（例如，“请使用平实语气，因为目标读者是非技术背景的管理层”），能让模型更好地遵循指令。

**3. 将复杂任务拆解为子任务（Prompt Chaining）**

不要试图用一个巨大的Prompt让模型一次性完成翻译、摘要、分析和格式化。应将流程拆解，变成不同的子任务，每个子任务都有自己的任务清单，并将上一步的输出作为这一步的输入，这样更可控、可调试，也能更好的让AI来迭代、优化。

**4. 定义明确的输出约束**

清晰地规定输出的**格式**（如Markdown表格、JSON schema）、**长度**和**结构**。这能极大减少后续的解析成本，尤其对于需要程序化处理的场景至关重要。同时，也能给AI提供合适的方案让他自我检查。

**5. 迭代与评估，而非一次性赌博**

Prompt质量决定了约**80%** 的输出结果。应采用迭代策略：先要求AI提供多个方向或选项，再从中筛选并深化，这往往比直接索要完整答案更高效。同时，建立**回归测试**来验证Prompt变更的效果。



-----

[^1] Petroni et al., Language Models as Knowledge Bases? (2019) arXiv: https://arxiv.org/abs/1909.01066

[^2] Brown et al., Language Models are Few-Shot Learners (2020) arXiv: https://arxiv.org/abs/2005.14165

[^3] Wei et al., Chain-of-Thought Prompting Elicits Reasoning in Large Language Models (2022) arXiv: https://arxiv.org/abs/2201.11903

[^4] Kojima et al., Large Language Models are Zero-Shot Reasoners (2022) arXiv: https://arxiv.org/abs/2205.11916

[^5] Wang et al., Self-Consistency Improves Chain of Thought Reasoning in Language Models (2022) arXiv: https://arxiv.org/abs/2203.11171

[^6] Yao et al., ReAct: Synergizing Reasoning and Acting in Language Models (2022/2023) arXiv: https://arxiv.org/abs/2210.03629

[^7] Yao et al., Tree of Thoughts: Deliberate Problem Solving with Large Language Models (2023) arXiv: https://arxiv.org/abs/2305.10601

[^8] Besta et al., Graph of Thoughts: Solving Elaborate Problems with Large Language Models (2023) arXiv: https://arxiv.org/abs/2308.09687

[^9] Mei et al., A Survey of Context Engineering for Large Language Models (2025) arXiv: https://arxiv.org/abs/2507.13334

[^10] Liu et al., Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing (2021) arXiv: https://arxiv.org/abs/2107.13586