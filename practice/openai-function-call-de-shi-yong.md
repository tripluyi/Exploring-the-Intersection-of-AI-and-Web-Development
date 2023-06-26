# OpenAI Function Call的使用

目前OpenAI新提供了 function call的功能。我们可以看一个实际的例子来更直观地知晓它的作用。



当我们询问AI现在几点时，他会回答无法获取当前时间：

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

由于没有联网它无法获取当前正确的时间，现在我们可以通过prompt + function call 让它实现这个功能。

先编写一个获取时间的方法





{% hint style="info" %}
Reference:\
[https://github.com/JohannLai/openai-function-calling-nodejs](https://github.com/JohannLai/openai-function-calling-nodejs)
{% endhint %}
