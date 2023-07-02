---
description: Chain of Thought and ReAct
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# CoT and ReAct

在Openai最佳实践系列的官方文档中提到，给予GPT时间去思考。那么这是什么意思呢？

官方举了一个例子，询问学生的回答是否正确。在常规的问答场景下，GPT错误地认为学生的回答是正确。

第二次修改prompt，让GPT先尝试根据提问自己计算一遍，再通过计算的结果和学生的答案做对比，这样就可以得出正确的判断。

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

这其实就是让GPT思考的一种方式。那么我们来更详细地说一下什么是Chain of Thought 和 ReAct思维，进一步了解AI及大模型的处理方式，方便在开发过程中达到更好的效果。



### Chain of Thought

ChatGPT的作者之一 Jason发表的一篇论文中讲述了什么是CoT：

一般的模型问答，都倾向于或者说只能一步作答，也就是缺少推理的过程，这样的结果是，答案错误百出。只要问题多两个弯模型就没法玩转。

CoT的模式是，把人类思考的过程，用自然语言的形式，显性地放在prompt message中。

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

这样普通的 _input prompt, output message_ 就被转化成 \
_input prmopt(include CoT) and output(include CoT) message_

当Chain of Thought放在prompt之后，模型在给出答案前也会强制输出CoT，从条件概率分布的角度来讲，答案在chain of thought后，其准确的可能性更大。这也佐证了一点，模型其实本身没有思考，只在乎输出。

当然Jason也提到，CoT式prompt不会对小模型的性能产生积极影响，只有在使用大约1000亿参数的模型时才会产生性能提升。小规模的模型产生的思维链流畅但不合逻辑，因此性能低于标准提示。

在ChatGPT之前，也有很多其他大语言模型，但GPT有如此突出的表现，基于Jason和ChatGPT的联系，针对用户的输入和任务，猜测GPT包含了一些隐式的CoT。

普通用户在没有CoT的想法之前，只能依靠大模型本身的思维来处理，这个纯粹倚赖大模型训练的结果(Few-Shot-CoT)。

#### Zero-shot CoT

在另一篇 LLM are Zero-Shot Reasoners 论文中，作者小岛武简化了CoT的过程，也就是无需模型训练，通过特定的prmopt让模型自动生成CoT的方式来加强AI的回答(zero-shot CoT)

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>compare with Few-shot, Zero-shot, Few-shot-CoT and Zero-shot-CoT</p></figcaption></figure>

他通过在中间过程中加了很简单的一句话：_**"Let's think step by step",**_  就可以让没有CoT思维的模型进化回答。

> _We propose Zero-shot-CoT, a zero-shot template-based prompting for chain of thought reasoning. It differs from the original chain of thought prompting \[Wei et al., 2022] as it does not require step-by-step few-shot examples, and it differs from most of the prior template prompting \[Liu et al., 2021b] as it is inherently task-agnostic and elicits multi-hop reasoning across a wide range of tasks with a single template. The core idea of our method is simple, as described in Figure 1: add **Let’s think step by step**, or a a similar text (see Table 4), to extract step-by-step reasoning._

那么这是怎么实现的呢？  Zero-shot CoT的操作这样的：

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Zero-shot CoT</p></figcaption></figure>

它通过两次prompt来实现。第一个通过 _**“Let’s think step by step”**_, 让模型进行思考；第二次将思考的过程联合第一次的问题，加上prmopt: "_**The answer is**_" 一起提交，得到最终正确答案。

当然我们在开发中也可以这么实现，隐式地将这两步变一步，可以获得更精准的回答。



### ReAct

ReAat表示的是Reason+Act，通过这个例子我们可以看出几种模式的区别。

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Standard, Reason only, Act Only, ReActR</p></figcaption></figure>

在这个例子中，用户提问的是，除了Apple Remote，还有别的什么设备可以控制Apple Remote用来交互的那个程序吗？

这个问题看似简单，实际包含了 device和program。并且针对性的问了关于Apple方面的问题。

Reason only其实就是上面提到的CoT。不过虽然它有 Step by Step思维，由于它本身的没有Apple相关的所有信息，且并没有通过外部搜索，仅通过模型自身的数据来回答，显然回答不正确，连device和program都没有区分出来。

Act only确实是通过外部资源来搜索了，但它没有思维链，导致答非所问。

ReAct就很好的解决了这个问题，先思考，再Act获取外部信息源Obs，往复循环，最终得到正确的答案：\
_“Keyboard function keys”_

总结作者的操作流程：

* round1: Thought, Act, Obs
* round2: Thought, Act, Obs
* round3: Thought, Act, Obs
* finally: Thought, Act



### What about AutoGPT?

这里仅针对早期的AutoGPT进行理解和分析

AutoGPT看上去也是通过step by step来完成任务，但实际是否符合CoT的理念呢。推主Sverige对AutoGPT的源码进行了解读。

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption><p>AutoGPT</p></figcaption></figure>



AutoGPT主要由4部分组成， User, Prompt postfix, Memory, Prompt Template

这里Prompt Template是固定的，而template的目的是为了规范模型回答，让其生成带有thought和command的json，方便commoand被解析出来操作。

这里其实分为了2个阶段，在阶段一中，也就是初始时，Memory的内容是空的。通过和用户的第一次交互，明确第一个command，并且将这些内容存储在Memory中；此时thought也同时和command一起出现。AutoGPT为 Prompt postfix设置了一个默认值：\
_Determine which next command to use, and respond using the format specified above:_

从第二阶段开始，在用户明确选择之后，Prompt postfix变更为：\
_GENERATE NEXT COMMAND JSON_

从这里开始，之后的每一步都按照阶段二的第一步开始循环执行。

这个看上去是有step by step的思维，但CoT的模式是，将CoT的内容带给下一步prompt。这里AutoGPT执行的都是上一步的command，而thought是和command一起被生产的，并没有被带入下一步中。

最终从运行效果来看，是生效的。但却被认为低效(😆)，也就是大量消耗了token(费钱)。



{% hint style="info" %}
Reference:

[GPT Best Practices - Strategy: Give GPTs time to "think"](https://platform.openai.com/docs/guides/gpt-best-practices/strategy-give-gpts-time-to-think)

[GPT Best Practices - Tactic: Use inner monologue or a sequence of queries to hide the model's reasoning process](https://platform.openai.com/docs/guides/gpt-best-practices/tactic-use-inner-monologue-or-a-sequence-of-queries-to-hide-the-model-s-reasoning-process)

[Azure - Chain of thought prompting](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/advanced-prompt-engineering?pivots=programming-language-chat-completions#chain-of-thought-prompting)

[Chain-of-Thought Prompting Elicits Reasoning in Large Language Models](https://arxiv.org/abs/2201.11903)

[Large Language Models are Zero-Shot Reasoners](https://arxiv.org/abs/2205.11916)

[Sverige\_ Dong-seok](https://twitter.com/realrenmin) - [talk about CoT](https://twitter.com/realrenmin/status/1643241565031366657)

[Sverige\_Dong-seok - Does AutoGPT use CoT?](https://twitter.com/realrenmin/status/1647383612931870720)

[ReAct: Synergizing Reasoning and Acting in Language Models](https://react-lm.github.io/)
{% endhint %}

