# 获取当前时间 - function calling

当我们询问AI现在几点时，他会回答无法获取当前时间：

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

由于没有联网它无法获取当前正确的时间，现在我们可以通过prompt + function calling 让它实现这个功能。

我们先定一个 getCurrentTime的函数

```typescript
const functions = {
    getCurrentTime: function(){
        return new Date().toLocaleTimeString()
    }
}
```



然后在ChatCompletion传入functions参数，写上getCurrentTime函数名以及描述，这个目的是让GPT知道我们有哪些函数可以使用。然后做第一次请求

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
        result = choices[0].message || {}
    } catch (e) {
        console.log(`getChatCompletion error Info`, e)
    }
    return result
}

// prompt: "please tell me current time"
let messageList = [{
    role: "user", 
    content: "现在几点了？"
}]
const firstResponse = await getChatCompletion({messageList})
```



当我们在messageList中出现询问当前时间的时候，由于在调用中含有functions，根据描述GPT判断可以使用function。GPT就会以 "function\_call" 来终止并返回function\_call字段，让我们调用 getCurrentTime方法。

<pre class="language-typescript"><code class="lang-typescript"><strong>{
</strong>    id: `chatcmpl-1`,
    object: 'chat.completion',
    created: 1623662570,
    model: 'gpt-3.5-turbo-0613',
    choices: [{
        index: 0,
        message: {
            role: 'assistant',
            content: null,
            function_call: { name: "getCurrentTime", argumentss: '{}' }
        },
        finish_reason: 'function_call',
    }],
    user: { prompt_tokens: 43, completion_tokens: 7, total_tokens: 50 },
}
</code></pre>



在通过GPT指定要调用的方法来处理数据后，我们需要将结果以对应的role类型再次存入messageList进行二次调用

```typescript
const responseCallback = async ({respsone, messageList}: {
    respsone: ChatCompletionResponse,
    messageList: Array<ChatCompletionRequestMessage>
}) => {
    let result = ``;
    const { choices } = respsone?.data || {}
    const {finish_reason, message} = choices?.[0] || {}
    if(finish_reason == "function_call" && message?.function_call){
        const {name: fnName, arguments} = message.function_call || {}
        const fn = functions[fnName]
        if(fn){
            const fnResult = fn(arguments)
            messageList.push({
                role: 'assistant',
                content: null,
                function_call: { name: fnName, argumentss: arguments }
            });
            messageList.push({
                role: 'function',
                name: fnName,
                content: JSON.stringify({result: fnResult}),
            })

            result = await openai.createChatCompletion({messageList})
        }
    } 
    return result
}
```



最后，我们就能得到一个通过 function calling来实现的获取本地时间的结果了

```typescript
{
    message: {
        role: "assistant",
        content: "现在时间是13:57:50",
      }
}
```





总结一下函数调用的基本步骤：

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
