# FunctionCalling实现实时搜索

另一个例子，通过 function calling 实现的实时产品结果搜索，基于用户的提问，获取到对应的搜索参数。

{% tabs %}
{% tab title="From Bilibili" %}
{% embed url="https://www.bilibili.com/video/BV1Sj411t7mo/?vd_source=c61707bf7dc2af9f3cd0e3e19cc3f2d3" %}
{% endtab %}

{% tab title="From Youtube" %}
{% embed url="https://youtu.be/9zfQ9cPLHU0" %}
{% endtab %}
{% endtabs %}

👆上面的视频，用户的提问为：

我想去厦门玩一周，但我是穷学生。

实际过程是调用了2次 Openai，第一次根据提供的 functions 列表，提取用户提问中的信息来命中可用的 function，这里 openai 摘取到了function需要的3个参数：

`keyword: 厦门`，`时间范围: 一周-> [7]`，`排序: 穷学生 -> priceAsc`

在调用实时接口获取到产品信息之后，再将产品列表传至 openai，最终生成答案。

