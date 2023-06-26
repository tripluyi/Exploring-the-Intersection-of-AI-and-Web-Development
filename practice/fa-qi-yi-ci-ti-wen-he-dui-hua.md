# 发起一次提问和对话

这里我们通过一段简单的代码来实现提问和对话



### 引入 openai

在这个例子中，我们使用OpenAI来实现。Azure OpenAI的使用方式类似

```typescript
import { Configuration, OpenAIApi } from 'openai'
```



### 新建配置

你需要在OpenAI获取到API Key以及 Organization，并且将他们存储在环境变量。\
之后新建一个OpenAI的配置

```typescript
const configuration = new Configuration({
    organization: process.env.OPENAI_ORGANIZATION,
    apiKey: process.env.OPENAI_API_KEY,
})
const openai = new OpenAIApi(configuration)
```



### 发起一次提问

这里使用的是Completion，它的实际作用是根据给定的输入生成一个自然语言的输出，而不是进行对话。Completion应用通常需要一个能够自动完成某种任务的系统，例如自动文本摘要、机器翻译或文本自动生成。Completion系统需要能够理解输入的上下文，并能够生成合适的输出。

```typescript
const MODELS = {
    textDavici: `text-davinci-003`
};

export const QuetstionToOpenAI = async ({
    question
}: {
    question: string
}): Promise<String> =>{
    let result: string = ``
    if(!question) return result;
    

    try{
        const response = await openai.createCompletion({
            model: MODELS.textDavici,
            prompt: question,
            max_tokens: 1000,
            temperature: 0,
        });
        result = response?.choices?.[0]?.text || ``;
    } catch(e){
        console.log(`QuetstionToOpenAI error Info`, e);
    }
    
    return result;
}
```

这里几个参数的意义如下：

<table><thead><tr><th width="187">参数</th><th>含义</th></tr></thead><tbody><tr><td>model</td><td>当前提问使用的模型</td></tr><tr><td>prompt</td><td>发起提问的内容</td></tr><tr><td>max_tokens</td><td>此次回答返回的最大tokens。这将会影响返回的结果。当token值越大时，模型尝试匹配的内容会越多。</td></tr><tr><td>temperature</td><td><p>控制生成的文本的多样性和创造性。</p><p>当temperature设置为0时，API将只生成最有可能的文本，即每个token的概率分布中最高的概率对应的文本。这将导致生成的文本非常保守和可预测，缺乏多样性和创造性。</p><p>当temperature设置为1时，API将在每个令牌的概率分布中随机选择一个令牌，并将其用作生成的文本的下一个令牌。这将导致生成的文本更加随机和创造性，但也可能会导致生成的文本出现语法错误或不合理的内容。</p></td></tr></tbody></table>



### 发起一次对话

Chat的作用是实现人机对话，一般是为了解决用户的问题或提供信息。Chat应用通常需要一个能够理解和回答自然语言问题的系统，例如聊天机器人。Chat系统需要能够处理大量的自然语言输入，并能够以自然且流畅的方式与用户进行交互。

```typescript
import { 
    Configuration, 
    OpenAIApi, 
    ChatCompletionRequestMessage 
} from 'openai'

const MODELS = {
    gpt35Turbo: `gpt-3.5-turbo`
};

export const chatWithOpenAI = async ({ 
    content, question 
}: { 
    content: string; question: string 
}): Promise<string> => {
    let result = ``
    if (!question || !content) return result;
    try {
        const messages: Array<ChatCompletionRequestMessage> = [
            {
                role: 'system',
                content: `
                You are a helpful assistant. Answer the question as truthfully as possible using the provided text, and if the answer is not contained within the text below, say "I don't know."

                Context:
                ${content}`,
            },
            { role: 'user', content: question },
        ]
        const response = await openai.createChatCompletion({
            model: MODELS.gpt35Turbo,
            messages: messages,
        })

        const { choices } = response?.data || {}
        result = choices[0].message || ``
    } catch (e) {
        console.log(`chatWithOpenAI error Info`, e)
    }

    return result
}
```

#### Chat模式和Completion一般根据场景的不同来区别使用。

在这里Chat例子中，实际是通过给定一段限定的文本(content)，让 AI 在此文本的范围内回答用户的提问。

这里实际有3个角色，system/user/assistant

1. 系统（system）：系统是对话系统中的核心组件，负责处理用户的输入信息，并给出合适的回答或响应。系统需要能够理解自然语言输入，进行语义分析和推理，并生成合适的自然语言输出。系统的角色是处理用户的请求并给出回复的一方。
2. 用户（user）：用户是对话系统的使用者，向系统提出问题或请求，系统需要理解用户的问题或请求，并给出合适的回答或响应。用户的角色是发起对话的一方，主要作用是提供输入信息和反馈。
3. 助手（assistant）：助手是对话系统中的辅助组件，主要作用是帮助系统更好地理解用户的输入信息，并提供额外的上下文信息。助手通常使用额外的信息（如用户的历史记录、知识库等）来增强系统的响应和回答。助手的角色是协助系统理解用户输入并提供更好的回答或响应。

