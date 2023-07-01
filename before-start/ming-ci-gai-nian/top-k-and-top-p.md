# Top K and Top P



除了temperature之外，还有两个参数会控制返回的结果



### Top K(number)

表示从最高的N个结果里面随机的选择1个，top\_k设置允许其他高分值的词又被选中的可能性。

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

然而如果模型命中的词的概率非常偏科，比如第概率最大的那个词有0.9，剩下的词是0.03，0.02，那么在这种情况下，当你设置top\_k > 1时，极大可能是返回的结果完全不是你想要的。这也造成了设置一个最佳top\_k的难度很高。



### Top P(probability)

由此又引出了另一个参数，Top P。模型将会使用这个值来获取对应概率质量的Token结果。0.1表示仅考虑前10％概率质量的Token。

> 在 _Top-p_ 中，采样不只是在最有可能的 _K_ 个单词中进行，而是在累积概率超过概率 _p_ 的最小单词集中进行。然后在这组词中重新分配概率质量。这样，词集的大小 (_又名_ 集合中的词数) 可以根据下一个词的概率分布动态增加和减少。
>
> \
> 假设 p=0.92，_Top-p_ 采样对单词概率进行降序排列并累加，然后选择概率和首次超过 p=92% 的单词集作为采样池，定义为 _V top-p_。在 第一个示例中_V top-p_ ​有 9 个词，而在第二个示例它只需要选择前 3 个词就超过了 92%。其实很简单吧！可以看出，在单词比较不可预测时，它保留了更多的候选词，_如_ _P(w∣"The”)_，而当单词似乎更容易预测时，只保留了几个候选词，_如_ _P(w∣"The","car")_。

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

在这个例子中，第一个词 The 之后的预测结果由一堆小概率的词，这些小概率的词集合超过0.92后组成新的词集，然后在Top K的影响下随便选中一个。

在 car 被随机选中之后，新的预测结果就由前3个大概率的词和一堆极小概率的词组成，由于前3个词的累加已经超过了Top P 0.92，因此在这里只有前3个词被组成新的词集再通过Top K在这3个词中随机选择。





{% hint style="info" %}
Reference:

[How to generate text](https://huggingface.co/blog/how-to-generate)
{% endhint %}

