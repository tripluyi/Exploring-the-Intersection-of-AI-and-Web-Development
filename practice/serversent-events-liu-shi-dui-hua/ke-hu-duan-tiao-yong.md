# 客户端调用

以下为 react 中点击按钮之后调用 sse 的请求

{% embed url="https://gist.github.com/tripluyi/81c1fc6311766b3e84f499a7ecc034eb" %}

可以看到，和普通的接口调用区别并不大。但原生的 EventSource 只支持 GET 请求，当 request body的消息体有更多要求时，无法满足。

这时候可以使用 Azure 的 [fetch-event-source](https://github.com/Azure/fetch-event-source) 实现 POST

{% embed url="https://gist.github.com/tripluyi/ffa75e91df298b2077244e16f759e5de" %}

