# Temperature

默认为1

如果temperature很高，它会使得所有Token的得分变得接近于0，这意味着这个Token更有可能被选中。会有更多的多样性，更多的内容可以被预测，这会使得模型变得更有创造性。但如果temperature太高，神经网络只会预测出无意义的东西。

如果temperature非常低，最高的概率得分会被乘以一个非常大的数，这会使得最高得分的Token有更大的被选中的概率，也就是说，模型的行为会更加确定。

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>The garden was full of beautiful</p></figcaption></figure>



一个更直观的解释，Temperature, “温度”，顾名思义，好比煮沸的一锅水，温度越高，就会有越多的水滴溅出锅，温度越低能溅出出锅就那么几个水滴。

也就是说Temperature 这个参数越高就拉平模型预测在词表上的概率分布，这样在解码时结合采样策略会采样到不同的Token。



{% hint style="info" %}
Reference:

[Google Generative AI learning Path - Encoder-Decoder Architecture](https://www.cloudskillsboost.google/course\_templates/543)
{% endhint %}

