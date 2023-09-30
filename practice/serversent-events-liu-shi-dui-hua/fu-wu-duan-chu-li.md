# 服务端处理

依托OpenAI API对 stream 的支持，可以实现连续的结果输出。

{% embed url="https://gist.github.com/tripluyi/0dcfb7e001ba80f97a60fb22348209bd" %}

在使用 langchain 的基础上，只需要开启 streaming 为 true，并且在 callbacks中调用 handleLLMNewToken 既可处理流式的token返回



在处理连续 token 时，node返回的repsonse 也需要符合 text/event-stream格式

{% embed url="https://gist.github.com/tripluyi/8f00a8437bc0880bfb9911310053c26a" %}

👆以上经过 Next Response 处理，当 Openai 的stream 内容全部返回时，返回一个 `__completed__` 给客户端用于标记内容已完全收到。
