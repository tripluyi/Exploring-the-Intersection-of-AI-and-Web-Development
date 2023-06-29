---
description: Chain of Thought
---

# CoT and ReAct

&#x20;这里来说一下什么是Chain of Thought

一般的模型问答，都倾向于或者说只能一步作答，也就是缺少推理的过程，这样的结果是，答案错误百出。只要问题多两个弯模型就没法玩转。

CoT的模式是，把人类思考的过程，用自然语言的形式，显性地放在prompt message中。

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



{% hint style="info" %}
Reference:

[GPT Best Practices - Strategy: Give GPTs time to "think"](https://platform.openai.com/docs/guides/gpt-best-practices/strategy-give-gpts-time-to-think)[\
](https://platform.openai.com/docs/guides/gpt-best-practices/tactic-instruct-the-model-to-work-out-its-own-solution-before-rushing-to-a-conclusion)[GPT Best Practices - Tactic: Use inner monologue or a sequence of queries to hide the model's reasoning process](https://platform.openai.com/docs/guides/gpt-best-practices/tactic-use-inner-monologue-or-a-sequence-of-queries-to-hide-the-model-s-reasoning-process)

[Azure - Chain of thought prompting](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/advanced-prompt-engineering?pivots=programming-language-chat-completions#chain-of-thought-prompting)

[Chain-of-Thought Prompting Elicits Reasoning in Large Language Models](https://arxiv.org/abs/2201.11903)

[ReAct: Synergizing Reasoning and Acting in Language Models](https://react-lm.github.io/)
{% endhint %}

