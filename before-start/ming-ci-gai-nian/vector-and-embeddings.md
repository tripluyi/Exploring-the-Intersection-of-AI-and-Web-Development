# Vector & Embeddings

### 向量和Embeddings的作用

想象一下，现在数据库中有一段文本，内容是“这只老鼠正在寻找食物”。\
用户输入一个查询：奶酪

如果我们仅仅是使用文本搜索，将无法识别该段落，它不包含任何相同的文字。\
但通过使用embeddings，这两段文字都将转化为向量，然后可以在这两段文字的向量上执行相似性搜索。\
由于 “老鼠” 和 "奶酪" 在上下文中存在某种关系，尽管两段文字没有匹配的词语，用户还是能够通过向量间的相似性得出这段文字。

这正说明了向量嵌入的价值 - 它能够捕捉到上下文之间的关系，而不是只依靠精确匹配。这样就可以超出文字本身，基于更广泛的意义找到相似的内容。





### 什么是向量

从数学角度来说，向量是一个具有大小和方向的数学构造。例如,我们可以把向量想象成空间中一个点,“方向”就是从(0,0,0)到该点的矢量空间中的一个箭头。

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

从开发人员来看，我们可以更容易地将向量看作包含数值的数组。例如:

_**vector = \[0,-2,...4]**_



当我们查看同一空间中的许多向量时，我们可以说某些向量彼此近在咫尺，而其他向量相距甚远。 一些向量似乎会聚集在一起,而另一些向量可能在空间中分布稀疏。

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



通过一个可视化的三维的Embeddings，来理解嵌入模型的工作原理。这里中标出了多个数据点，每个点代表一个单词的向量embedding。距离近的单词在语义上相似，而距离远的单词语义不同。

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

一旦训练完成，嵌入模型可以将我们的原始数据转换为向量embedding。这意味着它知道如何将新的数据点放置在向量空间中。

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

这种embedding使得可以比较单词之间的相似性，且具有逻辑意义 - 如同义词出现在一起，不同义词分开。通过这种方法，嵌入模型学习了数据之间的隐含关系，并将其反映在向量空间之中。这正是保留原始数据含义的关键。



正如我们在图中看到的，在模型层面上，距离较近的向量在上下文中具有相似性，而相距较远的向量则相互不同。这正是向量具有真正含义的地方 - 它与向量空间中的其他向量的关系取决于嵌入模型是如何"理解"它所被训练的数据集的。嵌入模型根据上下文将近似的向量嵌入在一起。

向量embedding的目的是传达数据之间的关系，而不仅仅是将数据转换为向量。嵌入模型利用它从数据中学习到的模式，将具有上下文相似性的数据点嵌入在一起。



### 向量的相似性

一般我们通过余弦相似性来做向量相似的判断。

余弦相似性通过测量两个向量的夹角的余弦值来度量它们之间的相似性。0度角的余弦值是1，而其他任何角度的余弦值都不大于1；并且其最小值是-1。从而两个向量之间的角度的余弦值确定两个向量是否大致指向相同的方向。两个向量有相同的指向时，余弦相似度的值为1；两个向量夹角为90°时，余弦相似度的值为0；两个向量指向完全相反的方向时，余弦相似度的值为-1。这结果是与向量的长度无关的，仅仅与向量的指向方向相关。余弦相似度通常用于正空间，因此给出的值为0到1之间。

这里是一个余弦相似性的判断方法：

```typescript
/* 向量相似度对比
 * 使用示例：
 *     
 *    const a: number[] = [1, 2, 3];
 *    const b: number[] = [4, 5, 6];
 *    
 *    const similarity: number = cosineSimilarity(a, b);
 *    console.log(similarity); // Output: 0.9746318461970762
 */
export const cosineSimilarity = (a: number[], b: number[]): number => {
    if (a.length !== b.length) {
        throw new Error('Vectors must have same length')
    }

    let dotProduct = 0
    let normA = 0
    let normB = 0

    for (let i = 0; i < a.length; i++) {
        dotProduct += a[i] * b[i]
        normA += a[i] * a[i]
        normB += b[i] * b[i]
    }

    return dotProduct / (Math.sqrt(normA) * Math.sqrt(normB))
}
```



### 为什么更推荐使用 embeddings而不是 fine-tuning

Openai CookBook中提到，更推荐使用使用 embeddings。这是因为embeddings具有zero-shot的优势。

> Zero-shot（零样本）是指模型能够在没有经过特定任务的训练数据的情况下，对该任务进行推理或执行。这意味着，即使模型从未见过某个特定的任务或领域，它也可以通过利用其对其他相关任务或领域的学习，来完成新任务或领域的处理。
>
> 例如，在自然语言处理中，Zero-shot能力可以用来解决新词汇的问题。假设我们的模型在训练时没有见过“COVID-19”这个词汇，但是它可以通过了解相关的知识，比如“冠状病毒”、“病毒传播”等，来理解并在相关上下文中正确地使用这个词汇。&#x20;
>
> Zero-shot能力是一种非常有用的技术，因为它可以提高模型的泛化能力，使其能够更好地适应各种不同的任务和场景，而不需要进行大量的任务特定训练。

GPT有两种方式学习知识:

* 通过模型权重（比如：在一个训练数据集上微调模型）
* 通过模型输入（比如：通过input消息插入一段知识）

尽管微调感觉更自然（毕竟GPT学习其他知识就是通过训练数据）- 但一般不推荐它来教授模型知识。微调更适用于教授专项任务或类似风格，在召回事实知识时可靠性较低。

类比来说，模型权重就像长期记忆。当你微调模型时，就像为了一周后的考试而学习。当快要考试时，模型可能会忘记一些细节，或误记从未读到的事实。

相比之下，消息输入就像短期记忆。当你通过输入来传递知识时，就像带着资料去参加开卷考试。有资料在手，模型更可能得出正确答案。

相对于微调，文本搜索的一个缺点是每个模型都有一个最大文本长度限制：

| 模型            | 最大文本长度                    |
| ------------- | ------------------------- |
| gpt-3.5-turbo | 4,096 tokens(\~5 pages)   |
| gpt-4         | 8,192 tokens(\~10 pages)  |
| gpt-4-32k     | 32,768 tokens(\~40 pages) |

继续这个类比，你可以把模型看成一个只能看几页笔记的学生，即使他有整个书架可供选择。

因此，要构建一个能够利用大量文本回答问题的系统,我们推荐使用 搜索-问 的方法。





{% hint style="info" %}
Reference

[Vector Embeddings for Developers](https://www.pinecone.io/learn/vector-embeddings-for-developers/)

[What is a Vector Database?](http://localhost:5000/s/aC0LjN8LnoW1EQtmZMbv/about-us/vision-mission-and-focus/vision)

[Cosine Similarity - Wikipedia](https://en.wikipedia.org/wiki/Cosine\_similarity)

[Question answering using embeddings - OpenAI Cookbook](https://github.com/openai/openai-cookbook/blob/main/examples/Question\_answering\_using\_embeddings.ipynb)
{% endhint %}

