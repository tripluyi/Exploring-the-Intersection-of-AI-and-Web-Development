# 服务端处理

依托OpenAI API对 stream 的支持，可以实现连续的结果输出。

{% embed url="https://gist.github.com/tripluyi/0dcfb7e001ba80f97a60fb22348209bd" %}

在使用 langchain 的基础上，只需要开启 streaming 为 true，并且在 callbacks中调用 handleLLMNewToken 既可处理流式的token返回

