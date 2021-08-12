# Html转换成pdf
### 基于jspdf、html2canvas组件实现

```html
<!-- html布局，建议使用如下方式布局，方便后续生成pdf时的换页计算  -->
<!-- 主要当时处理的都是图表，所以要特殊处理，否则图片可能会被切割成2页 -->
<div className="xs-wrap-content" ref={contentRef}>
  <div className={styles.wrapRow}>
    <ChartComponent />
  </div>
  <div className={styles.wrapRow}>
    <ChartComponent />
  </div> 
</div>
```

```javascript
import html2canvas from 'html2canvas';
import { jsPDF } from 'jspdf';

const exportPdf = async () => {
  // a4尺寸：595.28、841.89
  const a4Width = 595.28;
  const a4Height = 841.89;
  // 导出pdf的预留padding
  const pdfPadding = 10;
  // 需要导出的元素
  const divToDisplay = contentRef?.current;
  // 需要导出元素的所有子元素
  const totalDivChildrenEle = divToDisplay.children || [];
  // 每个子元素的高度转换成a4高度，用于后续计算分页
  const totalDivChildrenHeight: number[] = [];
  totalDivChildrenEle.forEach((item: HTMLElement) => {
    totalDivChildrenHeight.push((a4Width / item.offsetWidth) * item.offsetHeight);
  });

  let positionY = pdfPadding;
  // 限制导出的pdf页数
  let pageCountLimit = 30;
  const pdf = new jsPDF('p', 'pt', 'a4');
  for (let i = 0; i < totalDivChildrenEle.length; i += 1) {
    if (pageCountLimit < 0) {
      break;
    }
    const ele = totalDivChildrenEle[i];
    await html2canvas(ele, { backgroundColor: '#fff', scale: 3 }).then(function (canvas) {
      const contentWidth = canvas.width;
      const contentHeight = canvas.height;
      // html页面生成的canvas在pdf中图片的宽高
      const imgWidth = a4Width;
      const imgHeight = (imgWidth / contentWidth) * contentHeight;
      const pageData = canvas.toDataURL('image/jpeg', 1.0);
      // 如果超出当前a4页的高度
      if (positionY + imgHeight > a4Height) {
        pdf.addPage();
        pageCountLimit += 1;
        positionY = pdfPadding;
      }
      pdf.addImage(pageData, 'JPEG', 0, positionY, imgWidth, imgHeight);
      positionY += imgHeight + pdfPadding;
    });
  }
  pdf.save('demo.pdf');
};
```