# 发起一次提问和对话

这里我们通过一段简单的代码来实现一个提问请求



#### 引入 openai

在这个例子中，我们使用OpenAI来实现。Azure OpenAI的使用方式类似

```typescript
import { Configuration, OpenAIApi } from 'openai'
```



#### 新建配置

你需要在OpenAI获取到API Key以及 Organization，并且将他们存储在环境变量。\
之后新建一个OpenAI的配置

```typescript
const configuration = new Configuration({
    organization: process.env.OPENAI_ORGANIZATION,
    apiKey: process.env.OPENAI_API_KEY,
})
const openai = new OpenAIApi(configuration)
```



#### 发起一次提问

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



#### 发起一次对话

```typescript
import { Configuration, OpenAIApi, ChatCompletionRequestMessage } from 'openai'

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







