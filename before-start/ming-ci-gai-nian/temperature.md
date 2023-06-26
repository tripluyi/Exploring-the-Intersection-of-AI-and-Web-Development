# Temperature

默认为1

如果temperature很高，它会使得所有Token的得分变得接近于0，这意味着这个Token更有可能被选中。会有更多的多样性，更多的内容可以被预测，这会使得模型变得更有创造性。但如果temperature太高，神经网络只会预测出无意义的东西。

如果temperature非常低，最高的概率得分会被乘以一个非常大的数，这会使得最高得分的Token有更大的被选中的概率，也就是说，模型的行为会更加确定。





{% hint style="info" %}
Reference:\
[https://huggingface.co/blog/how-to-generate](https://huggingface.co/blog/how-to-generate)
{% endhint %}

