---
description: Chain of Thought and ReAct
---

# CoT and ReAct

这里来大致说一下什么是Chain of Thought 和 ReAct思维，了解AI及大模型的处理方式，方便开发中达到更好的效果。



### Chain of Thought

ChatGPT的作者之一 Jason发表的一篇论文中讲述了什么是CoT：

一般的模型问答，都倾向于或者说只能一步作答，也就是缺少推理的过程，这样的结果是，答案错误百出。只要问题多两个弯模型就没法玩转。

CoT的模式是，把人类思考的过程，用自然语言的形式，显性地放在prompt message中。

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

这样普通的 input prompt, output message 就被转化成 \
input prmopt(include CoT) and output(include CoT) message

当Chain of Thought放在prompt之后，模型在给出答案前也会强制输出CoT，从条件概率分布的角度来讲，答案在chain of thought后，其准确的可能性更大。这也佐证了一点，模型其实本身没有思考，只在乎输出。

当然Jason也提到，CoT式prompt不会对小模型的性能产生积极影响，只有在使用大约1000亿参数的模型时才会产生性能提升。小规模的模型产生的思维链流畅但不合逻辑，因此性能低于标准提示。

在ChatGPT之前，也有很多其他大语言模型，但GPT有如此突出的表现，基于Jason和ChatGPT的联系，针对用户的输入和任务，猜测GPT包含了一些隐式的CoT。







{% hint style="info" %}
Reference:

[GPT Best Practices - Strategy: Give GPTs time to "think"](https://platform.openai.com/docs/guides/gpt-best-practices/strategy-give-gpts-time-to-think)

[GPT Best Practices - Tactic: Use inner monologue or a sequence of queries to hide the model's reasoning process](https://platform.openai.com/docs/guides/gpt-best-practices/tactic-use-inner-monologue-or-a-sequence-of-queries-to-hide-the-model-s-reasoning-process)

[Azure - Chain of thought prompting](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/advanced-prompt-engineering?pivots=programming-language-chat-completions#chain-of-thought-prompting)

[Chain-of-Thought Prompting Elicits Reasoning in Large Language Models](https://arxiv.org/abs/2201.11903)

[Large Language Models are Zero-Shot Reasoners](https://arxiv.org/abs/2205.11916)

[Sverige\_ Dong-seok](https://twitter.com/realrenmin) - [talk about CoT](https://twitter.com/realrenmin/status/1643241565031366657)

[ReAct: Synergizing Reasoning and Acting in Language Models](https://react-lm.github.io/)
{% endhint %}

