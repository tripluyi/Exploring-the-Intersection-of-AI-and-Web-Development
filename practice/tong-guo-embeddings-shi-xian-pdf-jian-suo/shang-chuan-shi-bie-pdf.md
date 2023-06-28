# 上传识别PDF

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
页面上的文本内容在 page.getTextContent()中，但实际上他识别到的并不是一段话，而是PDF上每个文字的点位。

下一节我们就需要通过根据这个点位来处理/识别文字，并且进行分割。



