# 获取文本，分割Documents

在客户端展示PDF view之后，我们需要获取到PDF内的文本，并将它们重新组合/分割。

实际上我们获得的是一个PDF上每个文字的点位transform，我们通过这个字段来判断文字是否在同一行，并且将它们收集在一起处理。

处理方法

{% code title="客户端" %}
```typescript
const getLongContentFromPage = (pageContentCollection: PageContentCollection) => {
    const endRegs = /[\.\!！。]/
    let longContextList: Array<LONG_CONTEXT_TYPE> = []
    _.map(pageContentCollection, pageContent => {
        const { page, contentList } = pageContent || {}
        let longContext: LONG_CONTEXT_TYPE = {
            page,
            textlines: [],
        }
        let thisLine = '',
            lastTransformString = ''
        _.map(contentList, (content, contentIndex: number) => {
            const { transform, str } = content || {}
            const sameLineTrans = transform
                .slice(0, 4)
                .concat([transform[transform.length - 1]])
                .join(',')
            if (!lastTransformString || sameLineTrans == lastTransformString) {
                thisLine += str
                if (contentIndex == contentList.length - 1) {
                    longContext.textlines.push(thisLine)
                }
            } else {
                longContext.textlines.push(thisLine)
                thisLine = str
            }
            lastTransformString = sameLineTrans
        })

        longContextList.push({
            ...longContext,
        })
    })

    // sort by page number
    longContextList = _.sortBy(longContextList, ['page'])

    // get sentence list
    let sentence = '',
        longParagraph = '',
        pageList: number[] = [],
        flattenSentenceList: {sentence: string, pageList: number[]}[] = [];
    _.map(longContextList, longContext => {
        const { textlines, page } = longContext || {}
        pageList.push(page)
        _.map(textlines, (textline, lineIndex) => {
            const is_end_index = textline.match(endRegs)?.index || -1
            if (is_end_index >= 0) {
                // TODO 根据段落来分隔更好
                const endSentence = sentence + textline.slice(0, is_end_index + 1)
                flattenSentenceList.push({
                    sentence: endSentence,
                    pageList: [...pageList],
                })
                longParagraph += `\n${endSentence}`
                sentence = textline.slice(is_end_index + 1)
                // pageList 重新计算
                // 如果是当页的最后一行，而且句子在此行末尾结束，下一句为新的一页，则不要把当页加入pageList。
                pageList = lineIndex + 1 == textlines.length && is_end_index + 1 == textline.length ? [] : [page]
            } else {
                sentence += textline
            }
        })
    })

    // 补上最后一句
    if (sentence.length) {
        flattenSentenceList.push({
            sentence,
            pageList: [...pageList],
        })
    }

    console.log(`flattenSentenceList`, flattenSentenceList)
    return { longContextList, flattenSentenceList, longParagraph }
}

```
{% endcode %}

至此，我们获取到了整个PDF文档的所有文本内容，并且通过 pageCallback 存入当前的状态中供后续使用。这里我们获得了3个字段：

* longContextList - 以页面纬度，按照PDF上展示的位置保存每一行的内容
* flattenSentenceList - 将整个PDF以句子拆分，并且标记上句子所在的页号，存储成数组
* longParagraph - 将所有的整句都拼凑在一起

