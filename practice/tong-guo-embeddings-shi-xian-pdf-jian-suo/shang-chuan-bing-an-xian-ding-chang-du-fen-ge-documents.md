# 上传并按限定长度分割Documents

从这里开始，我们需要编写api来接收客户端处理过的语句。

先按固定长度以及向量数据库特有的类型来处理。



声明Document的类型

{% code title="./types.ts" %}
```typescript
export interface DocumentParams {
    pageContent: string
    metadata: Record<string, any>
}
/**
 * Interface for interacting with a document.
 */
export declare class Document implements DocumentParams {
    pageContent: string
    metadata: Record<string, any>
    constructor(fields?: Partial<DocumentParams>)
}
```
{% endcode %}



编写一个分割Document的方法

{% code title="./common.ts" %}
```typescript
import { Document } from './types'
import _ from 'lodash'

const defaultDocLength = 500;
const splitTextInDoc = ({
    flattenSentenceList,
    docLength,
}: {
    flattenSentenceList: Array<{ sentence: string; pageList: number[] }>
    docLength?: number
}) => {
    if (!flattenSentenceList?.length) return false

    let docs: Array<Document> = [],
    _doc_text = '';

    const splitDocLength = docLength || defaultDocLength
    
    let currentPageList: any = []
    _.map(flattenSentenceList, sentenceItem => {
        const { sentence, pageList } = sentenceItem || {}
        if (sentence.length + _doc_text.length > splitDocLength) {
            docs.push({
                pageContent: _doc_text,
                metadata: { page: currentPageList.join(','), pageContent: _doc_text },
            })
            _doc_text = sentence
            currentPageList = [...pageList]
        } else {
            _doc_text += sentence
            currentPageList = _.uniq(currentPageList.concat([...pageList]))
        }
    })

    // 补上最后一段
    if (_doc_text.length) {
        docs.push({
            pageContent: _doc_text,
            metadata: { page: currentPageList.join(','), pageContent: _doc_text },
        })
    }
    return docs
}
```
{% endcode %}



