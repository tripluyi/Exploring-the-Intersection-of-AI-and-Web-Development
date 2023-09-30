# Server-Sent Events流式对话

当涉及到AI的流式回答问题时，SSE（Server-Sent Events）是一种常用的技术。它允许服务器主动向客户端发送数据，实现实时的、持续的通信。

通过使用SSE，我们可以实现实时的、交互式的AI回答系统，使用户能够即时获取到问题的回答结果。



SSE的工作原理：

1\. 客户端通过建立与服务器的连接来订阅事件流。2. 服务器将事件作为文本流发送给客户端，每个事件都包含一个标识符和数据。3. 客户端通过监听接收到的事件，对数据进行处理和展示。

使用SSE进行AI流式回答问题的步骤：

1\. 客户端发送问题给服务器。2. 服务器将问题传递给AI模型进行处理。3. AI模型生成回答，并将其作为事件发送给客户端。 客户端接收到回答事件后，即时展示给用户。



实现的效果

{% tabs %}
{% tab title="From Bilibili" %}
{% embed url="https://www.bilibili.com/video/BV1134y1G77d/" %}
{% endtab %}

{% tab title="From Youtube " %}
{% embed url="https://youtu.be/P8vkZ-GrSA4" %}
{% endtab %}
{% endtabs %}

