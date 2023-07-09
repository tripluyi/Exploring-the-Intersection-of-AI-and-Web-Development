# 通过麦克风识别语音输入

Web Speech API 可以实现语音转文字的功能，目前大部分新款浏览器都可以[支持](https://caniuse.com/?search=SpeechRecognition)。一个简单示例：

```typescript
// 客户端
// 判断浏览器是否支持 Web Speech API
if ('SpeechRecognition' in window || 'webkitSpeechRecognition' in window) {
  // 创建 SpeechRecognition 对象
  const recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
  recognition.lang = 'zh-CN'; // 设置语言为中文

  // 监听语音识别结果
  recognition.addEventListener('result', event => {
    const transcript = event.results[0][0].transcript;
    console.log(transcript); // 打印识别结果
  });

  // 开始语音识别
  recognition.start();
} else {
  console.log('Web Speech API is not supported');
}
```



当然我们为了更好地支持语音识别的效果以及兼容不同的浏览器，更推荐使用微软的 speech SDK

我们需要预先获取到token，需要在服务端获取校验信息

```typescript
export const speechCheck = async (): Promise<IAzureSpeechCheckResult> => {
    if (!azureSpeechKey || !azureSpeechregion) {
        return {
            status: false,
            error: {
                message: 'auth failed',
            },
        }
    }

    const params = {
        method: 'POST',
        headers: {
            'Ocp-Apim-Subscription-Key': azureSpeechKey,
            'Content-Type': 'application/x-www-form-urlencoded',
        },
        body: null,
    }

    try {
        const tokenResponse: any = await fetch(
            `https://${azureSpeechregion}.api.cognitive.microsoft.com/sts/v1.0/issueToken`,
            params
        )
        const tokenResponseResult = await tokenResponse.text()
        if (tokenResponseResult) {
            return {
                status: true,
                token: tokenResponseResult,
                region: azureSpeechregion,
                // tokenResponse
            }
        }

        return {
            status: false,
            error: {
                message: 'There was an error authorizing your speech key. no tokenResponse data',
            },
        }
    } catch (err) {
        console.log(`AzureSpeechCheck`, { err })
        return {
            status: false,
            error: {
                message: 'There was an error authorizing your speech key.',
            },
        }
    }
}
```



在客户端使用 speech SDK实现实时监听并且获取语音转换之后的文字

<pre class="language-typescript"><code class="lang-typescript"><strong>// 客户端
</strong><strong>const sttFromMic = async (
</strong>    stateRecognizer: any,
    speechToken: SpeechToken,
    recording: (arg: any) => void,
    callback?: (arg?: any) => void
) => {
    const speechConfig = speechsdk.SpeechConfig.fromAuthorizationToken(speechToken.authToken, speechToken.region)
    speechConfig.speechRecognitionLanguage = 'zh-CN'

    let recognizer
    if (!stateRecognizer) {
        const audioConfig = speechsdk.AudioConfig.fromDefaultMicrophoneInput()
        recognizer = new speechsdk.SpeechRecognizer(speechConfig, audioConfig)
    } else {
        recognizer = stateRecognizer
        recognizer.startContinuousRecognitionAsync()
        callback &#x26;&#x26; callback()
        return
    }

    recognizer.recognizing = function (s: any, e: AnyObj) {
        recording(e?.result)
    }

    recognizer.startContinuousRecognitionAsync()

    if (callback) {
        callback(recognizer)
    }
}
</code></pre>

