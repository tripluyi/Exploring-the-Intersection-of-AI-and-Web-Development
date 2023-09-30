# Openai Function Calling

开发人员现在可以将函数描述给 gpt-4-0613 和 gpt-3.5-turbo-0613，模型可以智能地选择输出一个包含调用这些函数的参数的JSON对象。这是一种更可靠地将GPT的能力与外部工具和API连接起来的新方法。

这些经过微调的模型可以检测到何时需要调用函数（根据用户的输入），也可以以符合函数签名的JSON进行响应。函数调用使得开发人员可以更可靠地从模型中获取结构化数据。



Openai 本身并不具备调用外部 API 和 Function 的功能，也因此只能基于模型本身的数据来回答

<figure><img src="../../.gitbook/assets/image (19).png" alt="" width="563"><figcaption></figcaption></figure>



但 function calling 相当于扩展了其能力，通过调用方自身的 API 或者 Function，来给Openai 更实时的信息，并据此返回正确的内容。\


<figure><img src="../../.gitbook/assets/image (21).png" alt="" width="563"><figcaption></figcaption></figure>





我们可以看两个实际的例子来更直观地知晓它的作用。

