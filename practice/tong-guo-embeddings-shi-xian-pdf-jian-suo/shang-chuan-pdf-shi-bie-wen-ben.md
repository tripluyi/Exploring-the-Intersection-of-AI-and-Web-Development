# 上传PDF, 识别文本

这里我们通过前端来实现上传PDF并识别的功能。需要用到[ react-pdf](https://www.npmjs.com/package/react-pdf) 这个包

由于需要在客户端处理PDF，客户端需要下载worker来工作，也就是需要暴露 pdfworker js文件路径给客户端。

<details>

<summary>一个基于NextJS路由的pdfworker下载方法</summary>

{% code title="api/pdf.worker" fullWidth="false" %}
```typescript
import type { NextApiRequest, NextApiResponse } from 'next'
import fs from 'fs'
import path from 'path'
import { findSpecificDir } from '@/utils/serverUtil'

const PdfWorker = async (req: NextApiRequest, res: NextApiResponse) => {
    const rootDir = await findSpecificDir({ startPath: __dirname, specificFile: 'package.json' })
    const pdfworkerjs = path.resolve(rootDir, './node_modules/pdfjs-dist/build/pdf.worker.js')

    fs.readFile(pdfworkerjs, (err, data) => {
        if (err) {
            res.setHeader('Content-Type', 'text/plain')
            res.status(404)
            res.end('404 Not Found\n')
            return
        }
        res.setHeader('Content-Type', 'application/javascript')
        res.write(data)
        res.end()
    })
}

export default PdfWorker
```
{% endcode %}

</details>



先引入 react-pdf，并且指定 pdfworker.js

{% code title="客户端" %}
```tsx
import { Document, Page, pdfjs } from 'react-pdf' //'react-pdf'

// 设置 PDF.js 的 workerSrc
pdfjs.GlobalWorkerOptions.workerSrc = '/api/pdf.worker'
```
{% endcode %}

这里Document 为上传PDF的主体，Page为每一页。



{% code title="客户端" %}
```tsx
const PdfReader = ({
    file,
    pageCallback,
    loadCallback,
}: {
    file: any
    pageCallback?: (arg?: any) => void
    loadCallback?: (arg?: any) => void
}) => {
    const [numPages, setNumPages] = useState(0)

    const onDocumentLoadSuccess = (documentArgs: DocumentCallback) => {
        const { numPages } = documentArgs || {}
        setNumPages(numPages)
        if (typeof loadCallback == `function`) {
            loadCallback()
        }
    }

    let tempCollection: PageContentCollection = []
    const onPageTextLoadSuccess = (page: PageCallback) => {
        page.getTextContent().then(textContent => {
            tempCollection.push({
                page: page.pageNumber,
                contentList: textContent?.items,
            })
            if (tempCollection.length >= numPages && numPages > 0) {
                // page Load Success
                const longContentBlock = getLongContentFromPage(tempCollection)
                const { longParagraph } = longContentBlock
                if (longParagraph && typeof pageCallback == `function`) {
                    pageCallback(longParagraph)
                }
            }
        })
    }

    return (
        <div>
            <Document file={file} onLoadSuccess={onDocumentLoadSuccess}>
                {_.map(new Array(numPages), (el, index) => (
                    <div className="inside_carousel" key={`pdf_page_${index}`}>
                        <Page
                            key={`page_${index + 1}`}
                            pageNumber={index + 1}
                            renderTextLayer={false}
                            renderAnnotationLayer={false}
                            onLoadSuccess={page => onPageTextLoadSuccess(page)}
                        />
                        <br />
                    </div>
                ))}
            </Document>
        </div>
    )
}

```
{% endcode %}

这里客户端的PDF view渲染的时候，每一页渲染都会触发 Page.onLoadSuccess。需要在这里收集每一页的内容整理之后处理。\
页面上的文本内容在 page.getTextContent()中，实际上我们获得的是一个PDF上每个文字的点位transform，我们通过这个字段来判断文字是否在同一行，并且将它们收集在一起处理。\


处理方法：

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

