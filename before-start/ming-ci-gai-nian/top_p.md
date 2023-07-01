# Top\_p

默认为1

模型将会使用这个值来获取对应概率质量的Token结果。0.1表示仅考虑前10％概率质量的Token。

> 在 _Top-p_ 中，采样不只是在最有可能的 _K_ 个单词中进行，而是在累积概率超过概率 _p_ 的最小单词集中进行。然后在这组词中重新分配概率质量。这样，词集的大小 (_又名_ 集合中的词数) 可以根据下一个词的概率分布动态增加和减少。
>
> \
> 假设 p=0.92，_Top-p_ 采样对单词概率进行降序排列并累加，然后选择概率和首次超过 p=92% 的单词集作为采样池，定义为 _V top-p_。在 第一个示例中_V top-p_ ​有 9 个词，而在第二个示例它只需要选择前 3 个词就超过了 92%。其实很简单吧！可以看出，在单词比较不可预测时，它保留了更多的候选词，_如_ _P(w∣"The”)_，而当单词似乎更容易预测时，只保留了几个候选词，_如_ _P(w∣"The","car")_。

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>





{% hint style="info" %}
Reference:

[How to generate text](https://huggingface.co/blog/how-to-generate)
{% endhint %}

