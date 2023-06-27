# 通过Embeddings实现PDF检索

先来看一段实现，

{% tabs %}
{% tab title="From Bilibili" %}
{% embed url="https://www.bilibili.com/video/BV1jW4y1D734" %}
PDF Embeddings Search
{% endembed %}
{% endtab %}

{% tab title="From Youtube" %}
{% embed url="https://www.youtube.com/watch?v=xmwZrRwD05Q" %}
PDF Embeddings Search
{% endembed %}
{% endtab %}
{% endtabs %}

这段视频实现的功能是，使用 Openai API，基于用户上传的PDF来回答回答，并且回答的内容范围限定在此PDF。

简单的思路是，

1. 上传PDF，并识别文本
2. 分割PDF为多个Documents
3. 通过Openai embeddings 对每段分割后的内容进行向量化
4. 将多组向量存储与向量数据库
5. 获取用户提问，并且将提问的内容也向量化
6. 将两者进行相似度对比，按给定规则获取对应的Document内容
7. 再次将获取到的最有关联性的Document内容以及用户的提问一起丢给 Openai
8. 得到 Openai 的回答

下面我们来一步步实现









