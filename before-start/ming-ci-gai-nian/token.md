# Token

Token（令牌）通常指的是文本中的基本单位，可以是单词、数字、标点符号或其他语言学上的单位。在自然语言处理中，文本通常需要被分割成一个个Token，以便进行后续的处理和分析。

例如，对于以下句子：

> "This is an example sentence."

这个句子可以被分割成以下Token：

* This
* is
* a
* example
* sentence
* .

在自然语言处理中，Token可以用于多种任务，例如文本分类、情感分析、机器翻译、自然语言生成等。Token也可以用于构建语言模型，以预测给定文本的下一个Token，从而实现自动文本生成等任务。

目前，各大AI 平台也是根据Token数来进行计费。



一个有趣的例子

询问GPT将一个单词以相反的顺序返回，看似简单，但GPT两次都回答错了。

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

这里的原因是GPT是通过Token来读取文本的，在GPT官方[Tokenizer页面](https://platform.openai.com/tokenizer)上，我们可以清楚地看到，lollipop这个单词被分割成了3个Token，Token ID分别是 \[75, 692, 42800]，所以它实际无法正确地处理这个字符串反转的操作。

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

那么应该如何处理这种情况呢？有几个办法可以参考。

1. 事先使用分割符将单词以字母的形式分割，这样每一个字母就是一个token。
2. 告诉GPT分割的规则，需要逐字母地识别单词并且分割。

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

这里第二个方法也恰巧说明了一段正确的prompt是如何重要。同时这里也带入了一点CoT(Chain of Thought).





{% hint style="info" %}
Reference：

[吴恩达OpenAI课程 - Building System with ChatGPT](https://www.deeplearning.ai/short-courses/building-systems-with-chatgpt/)
{% endhint %}
