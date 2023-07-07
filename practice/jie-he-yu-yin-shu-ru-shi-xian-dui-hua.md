# 结合语音输入实现对话



{% tabs %}
{% tab title="From Bilibili" %}
{% embed url="https://www.bilibili.com/video/BV1Hk4y1T7aA/" %}
Speech to text by Azure and chat with Openai
{% endembed %}
{% endtab %}

{% tab title="From Youtube" %}
{% embed url="https://www.youtube.com/watch?v=NCZkQYauaKA" %}
Speech to text by Azure and chat with Openai
{% endembed %}
{% endtab %}
{% endtabs %}



这里结合了语音输入的功能来实现对话，ai 作为一个面试者在回答面试官的问题。

openai有自己的语音接口 Whipser，但实际使用下来，whisper对中文的处理不如英文。另外 Whipser本身也带了一部分ai的功能，如果是使用它作为翻译功能的话，效果不过。不过这里我们直接使用微软的[语音服务](https://azure.microsoft.com/en-us/products/cognitive-services/speech-services/)。

同样，简单的步骤说明：

1. 根据用户选择的面试职位类型生成对应的SystemMessage
2. 用户点击按钮之后获取到浏览器麦克风权限监听
3. 实时识别面试官语音提问并转为文字( Azure Speech-To-Text)
4. 将面试官提问内容作为HumanMessage传入接口
5. 结合之前的System message，以及前面几轮的对话内容一同发送给openai
6. 得到openai的AIMessage并展示。

