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

这里是通过读取PDF，分割PDF为多个Documents，embeddings -> 相似性搜索 来实现问答。&#x20;



