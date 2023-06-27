# OpenAI Function Calling的使用

目前OpenAI在最新 gpt-3.5-turbo-0613 和 gpt-4-0613`两个模型版本中`提供了 function calling的功能。我们可以看一个实际的例子来更直观地知晓它的作用。



当我们询问AI现在几点时，他会回答无法获取当前时间：

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

由于没有联网它无法获取当前正确的时间，现在我们可以通过prompt + function calling 让它实现这个功能。

先尝试在一次ChatCompletion传入functions 参数，然后做第一次请求。

```typescript
const MODELS = {
    gpt35Turbo0613: "gpt-3.5-turbo-0613",
    gpt4Turbo0613: "gpt-4-0613",
}

const getChatCompletion = async ({messageList}: {
    messageList: Array<ChatCompletionRequestMessage>
}) => { 
    if(!messageList) return;
    let result = ``
    const getCurrentTimeFunObj = {
        name: `getCurrentTime`,
        description: `Get the current time`,
        parameters: {
            type: `object`,
            properties: {},
            required: []
        },
    }
    try {
        const response = await openai.createChatCompletion({
            model: MODELS.gpt35Turbo0613,
            messages: messages,
            functions: [getCurrentTimeFunObj],
            temperature: 0,
        })

        const { choices } = response?.data || {}
        result = choices[0].message || ``
    } catch (e) {
        console.log(`getChatCompletion error Info`, e)
    }
    return result
}

// prompt: "please tell me current time"
const firstResponse = await getChatCompletion({messageList})
```

当我们在messageList中出现询问当前时间的时候，GPT会







函数调用的基本步骤如下：

1. 通过用户查询和在函数参数中定义的一组函数来调用模型。
2. 模型可以选择调用函数；如果调用，则内容将是一个符合你预定义模式的字符串化 JSON 对象。（注意：模型可能会生成无效的 JSON 或产生虚假参数）。
3. 在你的代码中将字符串解析为 JSON，并通过入参来调用调用你写的函数。
4. 将函数返回的结果作为新消息附加到模型中，然后让模型将结果汇总回用户。





{% hint style="info" %}
Reference:

[openai-function-calling-nodejs](https://github.com/JohannLai/openai-function-calling-nodejs)

[OpenAI Doc - Function calling](https://platform.openai.com/docs/guides/gpt/function-calling)

[OpenAI Cookbook - How to call functions with chat models](https://github.com/openai/openai-cookbook/blob/main/examples/How\_to\_call\_functions\_with\_chat\_models.ipynb)
{% endhint %}
