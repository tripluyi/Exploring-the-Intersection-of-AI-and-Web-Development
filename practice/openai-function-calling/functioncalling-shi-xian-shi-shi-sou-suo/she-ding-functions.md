---
description: functions
---

# 设定 functions

设定可以被openai识别的 functions (上一个页面传入的参数)

```typescript
const functions = [
    {
        name: 'getTravelProductList',
        description: 'Get a list of travel products based on the given location',
        parameters: {
            type: 'object',
            properties: {
                keyword: {
                    type: 'string',
                    description: 'The destination keyword, e.g. Shanghai, China',
                },
                travelDays: {
                    type: 'array',
                    items: {
                        type: 'number',
                    },
                    description: 'The number of days of the trip, e.g. [3, 5, 7]',
                },
                sortType: {
                    type: 'string',
                    enum: ['priceDesc', 'priceAsc', 'ratingDesc', 'default', 'sales'],
                    description: 'The sort type of product list show, e.g. priceDesc',
                },
            },
            required: ['keyword'],
        },
    },
]
```

这里我们需要描述清楚 function 的名称，以及入参类型和说明，这样才能正确地被识别并且从prompt中提取到合理的参数。



至于这里的 getTravelProductList 实际的内容：

```typescript
const getTravelProductList = async ({
    keyword,
    sortType,
    travelDays,
}: {
    keyword: string
    sortType: string
    travelDays: number[]
}): Promise<String> => {
    const sortTypeNumber = sortTypeMap[sortType] || sortTypeMap.default
    const requestParams = getProductSearchRequestByCondition({ keyword, sortType: sortTypeNumber, travelDays })
    const params = {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify(requestParams),
    }

    const url = `https://your.api.com/productSearch`
    let productList: any = []
    try {
        const productListRes = await fetch(url, {
            ...params,
        })
        const productListResult = await productListRes.json()
        if (!_.isEmpty(productListResult?.products)) {
            productList = _.map(productListResult?.products, (product: any) => {
                const { basicInfo, priceInfo, tagGroups } = product || {}
                const tags = tagGroups?.[0]?.tags
                return {
                    id: product?.id,
                    name: basicInfo?.mainName,
                    subName: basicInfo?.subName,
                    price: priceInfo?.price,
                    description: basicInfo?.name,
                    tags: _.isEmpty(tags)
                        ? []
                        : _.map(tags, (tag: any) => {
                              return tag?.tagName || ''
                          }),
                }
            })
        }
    } catch (e) {
        console.log(`getTravelProductList error`, e)
    }

    return JSON.stringify(_.take(productList, 3))
}
```



在调用 openai api 时，我们需要判断当前返回的结果是否需要走自己的 function，openai 会在返回的内容里面出现  `function_call`

```typescript
if (message?.additional_kwargs?.function_call) {
            const availableFunctions: Record<string, Function> = {
                getTravelProductList: getTravelProductList,
            }
            const { name: functionName, arguments: functionArguments } = message?.additional_kwargs?.function_call || {}
            const functionToCall = availableFunctions[functionName]
            const functionArgs = JSON.parse(functionArguments)
            const functionResponse: string = await functionToCall(functionArgs)

            const messages = [
                new HumanMessage(humanMessage),
                new AIMessage(message),
                new FunctionMessage({
                    name: functionName,
                    content: functionResponse,
                }),
            ]
            getFunctionCallingMessage({
                mseeages: messages,
                streamHanler: (token: string) => {
                    writeSSEMessage({ text: token, writer: writer })
                },
                getAllHandler: (message: BaseMessage) => {
                    writeSSEMessage({ text: '__completed__', writer: writer })
                },
            })
        }
```

当返回的内容中有 function\_call， 并且命中之前已有的 function 时，就可以走自己的方法去获取数据然后二次传给 openai 做语言整合。
