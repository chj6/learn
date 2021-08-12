# PDF预览
### 基于react-pdf组件实现

主要还是基于[pdf.js](https://www.github.com/mozilla/pdf.js)来实现

```javascript
import React, { useState } from "react";
import { Document, Page, pdfjs } from "react-pdf";

// 注意：pdf.worker.js版本需要和pdfjs的版本保持一致
pdfjs.GlobalWorkerOptions.workerSrc = "/pdfjs/pdf.worker.js";

function PDFViewer({filePath: string}) {
  // pdf文件的总页数
  const [numPages, setNumPages] = useState<number>(0);

  const onDocumentLoadSuccess = ({ numPages }: any) => {
    setNumPages(numPages);
  };

  return (
    <Document
      options={{
        // bcmaps可以用pdfjs的external目录中获取
        // 使用bcmaps防止pdf中的部分字符不能正常显示
        cMapUrl: "/pdfjs/bcmaps/",
        cMapPacked: true,
      }}
      file={filePath}
      onLoadSuccess={onDocumentLoadSuccess}
      loading={<div>pdf加载中...</div>}
    >
      {/* 加载pdf中所有的页面内容 */}
      {new Array(numPages).fill("page").map((item, index) => (
        <Page
          key={index}
          width={document.body.offsetWidth || 375}
          pageNumber={index + 1}
        />
      ))}
    </Document>
  );
}

export default PDFViewer;
```

#### pdf.worker.js说明
因为解析和渲染PDF需要较长的时间，可能会阻塞其它JS代码的运行，
所以pdf.js引入了WebWorkers（通过从主线程中移除大量CPU操作（如解析和渲染）来提升性能）。

#### 签名或者盖章图片不能显示
处理方法：
```javascript
if (data.fieldType === "Sig") {
  // 注释"pdf.worker.js"中下面的2行代码
  // data.fieldValue = null;
  // this.setFlags(_util.AnnotationFlag.HIDDEN);
}
```